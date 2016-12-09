---
layout: post
title:  "GPIO API for non-previliged user"
date:   2016-11-11 00:53:00 +0900
categories: artik tizen gpio
---

##### *by [Taehee Jeong](https://github.com/FredJeong)*{: style="color: #888"}

### Non-root user의 GPIO 사용 문제

GPIO access는 /dev/mem 또는 sysfs 디렉토리인 /sys/class/gpio에 접근할 수 있는 권한을 가지고 있어야 가능하다. 그러나 미 두 시스템 디렉토리는 `root` user의 접근만을 허용한다. 따라서, Tizen GPIO API를 사용해 실제로 개발한 application을 구동해야 하는 `owner` user는 이 두 디렉토리에 대한 접근 권한이 없고, 그대로 /dev/mem에 접근을 수행하면 segmetation fault가 뜨는 것을 확인하였다. 이를 해결하기 위해 root권한을 가진 service와 API의 프로세스가 inter-process communication을 통해 GPIO I/O요청을 수행하는 모델을 구상하였다.


### Tizen sensord의 해결 방법

먼저 GPIO와 가장 쓰임이 비슷할 것으로 여겨지는 sensor 부분의 API를 참고하여, sensor의 권한 문제를 어떻게 해결하고 있는지 보고자 했다. Tizen SDK에 들어있는 sensor API 함수들이 실제로 핸들을 얻고 값을 얻어올 때 `sensord_`로 시작하는 함수를 호출하는 것을 발견했고, dameon process로 추정되는 sensord의 존재를 알았다.

 Sensord는 아래와 같이 user단에서 동작하는 client process와 dameon으로 작동하는 server process 둘로 나눠진 구조를 가지며, command socket interface를 통해 서로 명령을 주고받고, polling을 사용한 event listener를 통해 server process로부터 각종 event를 받아온다.

![Tizen Sensord architecture](https://wiki.tizen.org/w/images/thumb/2/27/Sensor_framework_architecture.png/800px-Sensor_framework_architecture.png)

### Sensord를 모방한 gpiod 구현

위의 sensord의 IPC pattern을 참조해서, service로 동작하는 gpiod와 client의 GPIO API로 나누어 구현하였다.

GPIO API의 non-root user를 위해 개선된 구조는 아래와 같이 root process(service)와 user process(실제 application)으로 나누어진다.

주고받는 데이터가 간단하므로, 두 프로세스 사이의 통신은 system V messagq queue를 사용해 구현하였다. target ID는 server가 1, 각 client는 각자의 PID를 사용한다.

1. server process
  * server process는 user process의 요청을 받아 GPIO pin들의 방향을 설정하고, 값을 읽거나 쓴 후 그 결과를 돌려준다.
  * handle을 관리함으로서 여러 프로세스가 같은 핀에 접근하지 못하도록 관리한다 (suggested)
2. client process
  * sensord의 구조를 따 와 handle과 listener가 있으며, handle을 얻어온 후 listener를 통해 실제 gpio data의 I/O를 진행한다.
  * listener 는 일정 주기로 server 에 GPIO value read request를 보낸다. (IN mode일 때)
  * server에서 PIN VALUE가 돌아오면, message handler가 이를 처리하고 해당 listener의 상태를 업데이트한다. 이 때, 값이 달라졌다면 callback을 호출한다.
  
GPIO API 의 핵심 함수들은 아래와 같이 변경되었다.
{% highlight c %}
int gpio_get_default_gpio(gpio_pin_e pin, gpio_h *gpio, gpio_direction_e mode);
int gpio_create_listener(gpio_h gpio, gpio_listener_h *listener);
int gpio_destroy_listener(gpio_listener_h listener);

//IN mode only
int gpio_listener_start(gpio_listener_h listener);
int gpio_listener_stop(gpio_listener_h listener);

int gpio_listener_set_event_cb(gpio_listener_h listener, unsigned int interval_ms, gpio_event_cb callback, void *data);
int gpio_listener_unset_event_cb(gpio_listener_h listener);

int gpio_listener_read_data(gpio_listener_h listener, gpio_event_s *event);
int gpio_listener_set_interval(gpio_listener_h listener, unsigned int interval_ms);

//OUT mode only
int gpio_listener_set_data(gpio_listener_h listener, gpio_value_e data);
{% endhighlight %}

그리고, 다음과 같은 자료형들이 추가로 정의되었다.
{% highlight c %}
typedef void (*gpio_event_cb)(gpio_h gpio, gpio_event_s *event, void *data);

typedef struct
{
    unsigned long long timestamp;  /**< Time when the gpio data was observed */
    gpio_value_e value;  /**< GPIO data values */
} gpio_event_s;

struct gpio_listener_s {
	int id;
	gpio_pin_e pin;
	gpio_direction_e direction;
	gpio_value_e data;
	int pause;
	std::atomic<unsigned int> batch_latency;
	unsigned int magic;
	std::thread *read_thread;
	std::atomic<bool> active; //if listener is started
	gpio_h gpio;
	gpio_event_cb callback;
	void *user_data;
	void *accu_callback;
	void *accu_user_data;
};

typedef struct {
  gpio_pin_e pin;
  gpio_direction_e direction;
} gpio_t;


typedef struct gpio_listener_s *gpio_listener_h;
typedef gpio_t* gpio_h;
{% endhighlight %}

gpio_h와 gpio_listener_h를 통해 GPIO pin과 그에 해당하는 listener 를 관리한다. 각각은 pin과 direction 정보를 가지고 있으며, listener는 추가로  callback, data read interval 등의 정보를 가지고 있다. sensord의 listener에서 그대로 가져와 쓰지 않는 필드가 많은데, 추후 cleanup 할 예정이다.

[Code](https://github.com/artik-snu/gpio/tree/master/gpio_client)
