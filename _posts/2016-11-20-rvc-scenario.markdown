---
layout: post
title:  "Samsung RVC Hackaton scenario"
date:   2016-11-20 13:10:00 +0900
categories: tizen powerbot
---

##### *by [Junmo Park](https://github.com/junmohi)*{: style="color: #888"}

# SOSCON을 위한 로봇 청소기(Powerbot) 시나리오

## Development Environment

### Tizen studio 1.0.1 for Hackaton
### Node.js


## Scenario

### 1. 방범 시나리오

#### Powerbot
```
파워봇의 전면 카메라로 일정 주기로 이미지 촬영
OpenCV를 사용해 이전 이미지와 이후 이미지의 차이 비교
차이가 일정 값 이상이면 Firebase에 침입자 발생 신호와 현재 이미지 전송
```

#### Web Controller
```
침입자 발생 신호가 감지되면 알람 소리 재생
침입자 이미지 확인 가능
```


### 2. 웹 기반 컨트롤러

#### Powerbot
```
일정 시간 주기로 현재 좌표 Firebase에 전송
웹에서 날아온 속도 신호대로 바퀴 속도 설정
```

#### Web Controller
```
전송된 좌표 로그 기반 맵 작성
터치 컨트롤러로 Powerbot의 바퀴별 속도를 조절해 움직이도록 구현
```
