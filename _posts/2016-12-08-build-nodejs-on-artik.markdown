---
layout: post
title:  "node.js on ARTIK"
date:   2016-12-08 20:17:00 +0900
categories: artik tizen nodejs rpm
---

##### *by [Taekmin Kim](https://github.com/tantara)*{: style="color: #888"}

*Ubuntu 14.04에서 작업한 결과물입니다.*

# Tizen용 Node.js rpm 만들기

## Environment
 
### gbs 설치
 
 gbs는 Tizen에 개발한 패키지를 설치할 수 있도록 rpm을 만들어주는 도구입니다.
 
 다음 파일을 열어서 ```$ sudo vim /etc/apt/sources.list``` 가장 아래에

 ```deb http://download.tizen.org/tools/latest-release/Ubuntu_14.04 /```
 
 을 추가합니다. 그리고 빌드를 위한 관련 패키지를 설치합니다.
 
 ```
 $ sudo apt-get update
 $ sudo apt-get install gbs mic
 ```

### ARM cross compiler 설치

현재 x86_64 기반의 Ubuntu 환경에서 개발을 하고 ARTIK10 보드에서 테스트를 하기 때문에 ARM cross compiler가 필요합니다.

```
$ sudo apt-get install libc6-armel-cross libc6-dev-armel-cross
$ sudo apt-get install binutils-arm-linux-gnueabi
$ sudo apt-get install libncurses5-dev
$ sudo apt-get install gcc-arm-linux-gnueabi
$ sudo apt-get install g++-arm-linux-gnueabi
```

ARM cross compiler를 사용하기 위해서 다음과 같이 환경변수를 설정합니다.

```
$ export AR=arm-linux-gnueabi-ar
$ export CC=arm-linux-gnueabi-gcc
$ export CXX=arm-linux-gnueabi-g++
$ export LINK=arm-linux-gnueabi-g++
```

## Develoment

### Setup

Tizen용 Node.js를 다운받고 Node.js 버전을 4.4.3으로 변경합니다. 현재 ARTIK10에서 4.4.3이 안정적이라고 알려져있기 때문에 해당 버전으로 진행했습니다.

```
$ git clone git://git.tizen.org/platform/upstream/nodejs nodejs-tizen
$ cd nodejs-tizen && git checkout b9b28b06449b0d6bac92ebc6c232839321e3d469
```

### Customize

개발자의 필요에 따라서 관련 코드를 수정합니다.

### Build

```gbs``` 툴을 이용해서 Tizen에 설치가 가능한 rpm 파일을 생성합니다. ```gbs```를 처음 사용한다면 설정 파일을 다운로드([설정 파일 받기](https://gist.github.com/tantara/6710819d341af3600aebc27b187de2cb)) 받아야합니다.

```
$ gbs build -P "tizen_common_3.0b_artik_armv7l" --arch armv7l --include-all
```

## Test

```gbs```를 통해 만들어진 rpm을 Tizen 환경으로 가져가서 설치합니다.

```
$ rpm -ivh nodejs-4.4.3-1.armv7l.rpm
```
