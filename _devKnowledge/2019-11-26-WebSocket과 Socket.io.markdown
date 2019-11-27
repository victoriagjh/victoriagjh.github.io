---
title:  "WebSocket과 Socket.io"
date: 2019-11-26 22:30:00
description: The history of Development Study
categories: [devKnowledge]
resource: true
comments: true
---
# WebSocket과 Socket.io
##### 웹 페이지의 한계에서 벗어나 실시간으로 상호작용하는 웹서비스를 만드는 표준 기술인 WebSocket.
##### 그리고 다양한 방식의 실시간 웹 기술을 손쉽게 사용할 수 있는 Socket.io
두 기술에 대해 알아봅시다!<br>

### `WebSocket이 있기까지`
전형적인 브라우저 렌더링 방식은 HTTP 요청에 대한 HTTP 응답을 받아서 브라우저의 화면을 깨끗하게 지우고 새롭게 표현하는 방식이었다. <br>
그 이후, 내용을 새롭게 다 그리는 것이 아니라 원하는 부분만 다시 랜더링하며 실시간으로 사용자와 상호작용하는 방식이 나타나고 사용자와 상호작용하는 웹 서비스를 선호하는 사용자가 증가하면서 RIA(Rich Internet Application)기술의 발달이 촉진되었다.<br>

상호작용하는 웹 서비스를 위해 숨겨진 프레임(Hidden Frame), Long Polling, Stream 등 다양한 방식이 존재하였으나 이 방식은 모두 사용자의 요청이 있고 응답을 보내준 방식이었다. 그렇기 때문에 상호작용하는 웹 페이지를 복잡하고 어려운 코드로 구현해야 했다. <br>

상호작용하는 웹페이지의 핵심은 **브라우저와 웹 서버 사이에 더 자유로운 양방향 메시지 송수신** 이 있어야 한다는 것이다. 그래서 HTML5 표준안의 일부로 **WebSocket** 이 등장하게 되었다.<br>

### `WebSocket 프로토콜`
표준 WebSocket은 **Upgrade: WebSocket** 을 사용하여 웹 서버에 요청한다. 웹 서버 역시 WebSocket 기능을 지원하며, 브라우저는 이 헤더와 함께 랜덤하게 생성한 키를 서버에 보낸다. 웹 서버는 이 키를 바탕으로 토큰을 생성한 후 브라우저에 돌려준다.<br>
👉 이런 과정으로 WebSocket 핸드쉐이킹이 이루어진다.<br>

##### WebSocket 프로토콜을 나타내는 **ws://** 은 URI 스키마를 사용한다. 암호화 소켓은 httpscjfja wss를 사용한다.

소켓에 연결할 때는 onopen, 연결을 종료할 때 onclose, 메시지를 받았을 때 onmessage 등의 이벤트를 정의할 수 있다. 서버에 메시지를 보내고 싶을 때에는 send() 메서드를 이용한다. <br>

HTTP Polling 방식을 사용하는 것보다 overhead가 적어진다. 클라이언트가 먼저 서버에게 요청하지 않고, 서버가 클라이언트에게 content를 보내고 이 연결이 계속 열린채로 유지되기 때문에 가능하다.<br>

### `Socket.io는 무엇인가?`
Socket.io는 JavaScript를 이용하여 브라우저 종류에 상관없이 실시간 웹을 구현할 수 있도록 한 기술이다. <br>
브라우저와 웹 서버의 종류와 버젼을 확인한 뒤, 적합한 기술을 사용하는 방식이다. <br>
Web Socket과 달리 Socket.io는 표준 기술이 아니고, Node.js 모듈로서 다른 프레임워크에서도 사용가능하다. <br>

### `참고자료`
WebSocket과 Socket.io : [심상민](https://d2.naver.com/helloworld/1336)<br>
