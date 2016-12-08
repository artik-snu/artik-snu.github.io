# ARTIK을 활용한 Tizen Framework 및 IoT 시나리오 개발

서울대학교의 '창의적통합설계1'에서 진행된 프로젝트입니다.

## 평가기준표

### 기능적 요구사항(총 40점)

#### Tizen GPIO API 구현(15)

1. **기존 프레임워크 분석 및 구현(10)**
	- [GPIO on Tizen](https://github.com/artik-snu/gpio)
	2. ~~새로운 프레임워크로 구현(5)~~
3. **테스트 코드 작성(3)**
	- [gpio/test.cpp](https://github.com/artik-snu/gpio/blob/master/test.cpp)

#### Node.js GPIO API 구현(15)

1. ~~Internal API 로 구현(7)~~
	- **Addons 로 구현 (2)**
		 - [GPIO on Node.js(Addon)](https://github.com/artik-snu/node-addon-gpio)
3. **Tizen Framework GPIO API 사용(3)**
	- [Tizen GPIO 사용](https://github.com/artik-snu/node-addon-gpio/blob/master/tizen-gpio.cc)
	- ~~Tizen Framework GPIO API 미사용(1)~~
5. **테스트 코드 작성(3)**
	- [node-addon-gpio/test](https://github.com/artik-snu/node-addon-gpio/tree/master/test)
6. **디자인 설계(2)**
	- *최종 보고서 참고*

#### IoT 시나리오 구현(10)

1. ~~Iotivity 활용한 모바일과 통신(5)~~
	- **모바일과 통신(Iotivity 미활용)(3)**
		- *데모 동영상 참고*
3. **ARTIK Cloud 통신(3)**

![ARTIK Cloud에 데이터를 쌓는다](https://dl.dropbox.com/s/3opqmnhbizeral6/photo_2016-12-08_16-34-58.jpg) 

4. **센서(2개 이상) 사용(2)**
	- 카메라 센서
	- 마이크 센서

### 비기능적 요구사항(총 30점)

#### 성능(20)

1. **GPIO API 반응 시간 < 0.1s (10)**
	- 100,000번 요청 0.3s 소요
	- ~~0.1s <= GPIO API 반응 시간 < 0.3s(4)~~
	- ~~0.3s <= GPIO API 반응 시간(1)~~
4. **앱 or Node.js 서비스 Memory Leak 없음(10)**
	- ~~앱 or Node.js 서비스 Memory Leak 존재(2)~~

#### 신뢰성(10)

1. **Tizen GPIO API 성공률 95% 이상(5)**
	- 100,000번 요청 100,000번 성공
	- ~~Tizen GPIO API 성공률 50% 이상,95% 이하(2)~~
	- ~~Tizen GPIO API 성공률 50% 이하(1)~~
4. **Node.js GPIO API 성공률 95% 이상(5)**
	- 100,000번 요청 100,000번 성공
	- ~~Node.js GPIO API 성공률 50% 이상, 95%이하(2)~~
	- ~~Node.js GPIO API 성공률 50% 이하(1)~~

### 프로젝트 관리(총 30점)

#### 개발 일정(10)

1. **개발 일정 준수(7)**
	- *최종 보고서 참고*
2. **요구사항 정리 및 개발 일정 수립(3)**
	- *최종 보고서 참고*

#### Github(10)

1. Review Process 진행(5)
2. **README, LICENSE 작성(5)**
	- [tizen-gpio, README & LICENSE](https://github.com/artik-snu/gpio/blob/master/README.md)
	- [nodejs-gpio, README & LICENSE](https://github.com/artik-snu/node-addon-gpio/blob/master/README.md)
	- [SOSCON-rvc, README](https://github.com/artik-snu/SOSCON-RVC/blob/master/README.md)
	- [SOSCON-dashboard, README](https://github.com/artik-snu/soscon-dashboard/blob/master/README.md)

#### Blog(10)

Blog: [artik-snu.github.io](https://artik-snu.github.io/)

1. **프레임워크 분석 및 개발 방법 작성(7)**
	2. [GPIO Research](https://artik-snu.github.io/artik/tizen/gpio/2016/09/29/gpio-research.html)
	3. [Memory Mapped GPIO](https://artik-snu.github.io/artik/tizen/gpio/2016/10/12/memory-mapped-gpio.html)
2. **Issue에 대한 블로그 작업(3)**
	3. [Error while using gbs to build package](https://lists.tizen.org/pipermail/application-dev/2016-October/000791.html)

### 기타(가점)

#### 공모전(가점)

1. **SOSCON 해커톤**

![청소로봇 트랙 참가](https://dl.dropbox.com/s/1g19ehmjgiy64co/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202016-12-08%2016.48.02.png)

![청소로봇 트랙 3등 수상](https://dl.dropbox.com/s/bkx9hc9l9gukvbz/2016-11-18%2017.39.10.jpg)

