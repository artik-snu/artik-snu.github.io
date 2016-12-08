---
layout: post
title:  "GPIO를 위한 nodejs Addon 만들기"
date:   2016-12-08 21:17:00 +0900
categories: artik tizen nodejs rpm addon
---

##### *by [Taekmin Kim](https://github.com/tantara)*{: style="color: #888"}

*Ubuntu 14.04에서 작업한 결과물입니다.*

# GPIO를 위한 nodejs Addon 만들기

## Development Environment

### ARM Cross compiler

1. 다음 링크를 참고해서 ARM Cross Compiler를 설치합니다.
	- [ARM toolchain](http://www.acmesystems.it/arm9_toolchain)
2. Addon 빌드에 필요한 환경 변수를 설정합니다.

```
$ export AR=arm-linux-gnueabi-ar
$ export CC=arm-linux-gnueabi-gcc
$ export CXX=arm-linux-gnueabi-g++
$ export LINK=arm-linux-gnueabi-g++
```

### node-gyp

Addon 개발을 위한 Node.js 패키지를 설치합니다. node-gyp는 Addon 개발을 도와주는 다양한 기능을 제공합니다.

```
$ npm install -g node-gyp
```

## Configure & Build

### configure

Node.js 4.4.3 버전을 사용하는 ARTIK10 보드에서 테스트하기 때문에 몇가지 옵션이 더 필요합니다.(*tizen 3.0 based on ARTIK 10 requires specific nodejs version(v4.4.3)*)

```
$ node-gyp --target=4.4.3 --arch=arm configure
$ vi binding.gyp # refer to 'addon example'
```

### Develop

Addon 구현을 위한 설명은 따로 하지 않습니다. Addon을 만들면서 사용할 수 있는 모듈이나 함수는 다음의 링크에서 자세히 확인할 수 있습니다.

- [Node.js 공식 문서:Addon](https://nodejs.org/api/addons.html)
- [Node.js Addon 예제 코드](https://github.com/nodejs/node-addon-examples)
- [ARTIK-SNU의 Addon 프로젝트](https://github.com/artik-snu/node-addon-gpio/tree/master/examples/hello)

### build

```
$ node-gyp build
```

## Test

아래는 다음 Addon([hello.node](https://github.com/artik-snu/node-addon-gpio/tree/master/examples/hello))에 대한 테스트입니다.

```
$ scp /path/build/hello.node name@arm-host:/path/test/addon
```

in ARM host(/path/test)

```
.
|- addon
|	`- hello.node
`- hello.js
```

```
// hello.js
var addon = require('./addon/hello');
console.log(addon.hello());
```

run like below

```
$ node hello.js # 'world'
```
