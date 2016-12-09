---
layout: post
title:  "GPIO API draft"
date:   2016-10-20 01:02:00 +0900
categories: artik tizen gpio
---

##### *by [Taehee Jeong](https://github.com/FredJeong)*{: style="color: #888"}

### GPIO API Based on memory-mapped GPIO registers

GPIO API 구현을 위해, 다음의 네 가지 함수를 구현하였다.

* int set_pin_mode (gpio_pin_e pin, gpio_direction_e mode)
* int get_pin_mode (gpio_pin_e pin)
* set_pin_value (gpio_pin_e pin, gpio_value_e value)
* get_pin_value (gpio_pin_e pin)

set/get_pin_mode 함수는 각 GPIO 핀의 모드(IN/OUT)을 변경하고 읽어오며,
set/get_pin_value 함수는 GPIO 핀에 값을 쓰거나(OUT mode일 때), GPIO 핀의 현재 값을 읽어온다.

또한, GPIO 상수들을 정의한 몇 가지 enum을 정의하였다.

* gpio_direction_e
* gpio_value_e
* gpio_port_e
* gpio_pin_e

{% highlight c %}
typedef enum {
    GPIO_IN = 0,
    GPIO_OUT = 1
} gpio_direction_e;

typedef enum {
    LOW = 0,
    HIGH = 1
} gpio_value_e;

typedef enum {
  GPX0 = 0x300,
  GPX1 = 0x308,
  GPA0 = 0x00,
  GPA1 = 0x08,
  GPA2 = 0x10,
  GPD0 = 0x28,
  GPB2 = 0x28
} gpio_port_e;

typedef enum {
  GPX0_0 = (GPX0 << 3) + 0,
  GPX0_1 = (GPX0 << 3) + 1,
  /* More to be added */
  GPX1_0 = (GPX1 << 3) + 0,
  GPX1_5 = (GPX1 << 3) + 5,
  GPX1_6 = (GPX1 << 3) + 6,
  J27_11 = GPX1_0,
  J27_12 = GPX1_5,
  J27_13 = GPX1_6
  /* contunued */
} gpio_pin_e;

{% endhighlight %}

`gpio_port_e`는 하나의 레지스터로 묶이는 GPIO pin 묶임을 의미하며, `gpio_pin_e`는 LSB 3개에 각 핀의 offset을, 그리고 그 위의 비트에 port address를 저장한다. GPIO pin들은 ARTIK 10 개발보드 내에서의 이름을 alias로 가진다.

### Example code

{% highlight c %}
#include <unistd.h>
#include "gpio.h"

int main(int argc, char **argv)
{
    gpio_pin pin = J27_13;
    set_pin_mode(pin, GPIO_OUT);
    int i;
    for (i = 0; i < 10; i++) {
        set_pin_value(pin, HIGH);
        sleep(1);
        set_pin_value(pin, LOW);
        sleep(1);
    }

    return 0;
}
{% endhighlight %}

[코드](https://github.com/artik-snu/gpio/tree/master/gpio_client/deprecated)
