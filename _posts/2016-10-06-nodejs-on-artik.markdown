---
layout: post
title:  "node.js on ARTIK"
date:   2016-10-06 20:17:00 +0900
categories: artik tizen nodejs
---

##### *by [Taekmin Kim](https://github.com/tantara)*{: style="color: #888"}

*이 포스트는 Tizen이 깔린 ARTIK 10 보드에서 테스트하였습니다.*

### node.js 환경 구성하기

ARTIK에서 node.js를 원활히 개발하기 위해서는 node와 npm의 설치가 필요합니다. Tizen on ARTIK에는 node가 기본으로 패키징되어 있기 때문에 npm만 수동으로 깔도록 하겠습니다.

설치를 위해서는 rpm을 이용하는데 npm 설치 rpm을 SD카드 혹은 네트워크로 받아서 설치하면 됩니다.

제가 설치한 Tizen에는 curl, wget 등 흔히 쓰이는 툴들이 없었기 때문에 다음과 같은 코드를 이용했습니다.

```
# 추후에 추가
```

### 아주 간단한 서버 만들어보기

다음은 간단한 HTML을 보여주는 node 코드입니다.

```
추후에 추가
```

### 채팅 서버 만들어보기

ARTIK 프로젝트를 하면서 여러 디바이스 간의 데이터 전송이 꼭 필요한데요. 위의 예처럼 http 기반의 html, json 데이터 전송 방법도 가능하지만 소켓 기반의 데이터 전송 방법도 꼭 필요합니다.

socket.io의 예제를 이용해서 채팅 서버를 만들어 보았습니다.

{% highlight bash %}
$ git clone git@github.com:socketio/socket.io.git
$ cd socket.io
$ npm install
$ cd examples/chat
$ npm install
$ node .
{% endhighlight %}
