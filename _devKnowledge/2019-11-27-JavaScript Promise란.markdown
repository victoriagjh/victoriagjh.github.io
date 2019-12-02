---
title:  "JavaScript Promise란?"
date: 2019-11-27 13:30:00
description: The history of Development Study
categories: [devKnowledge]
resource: true
comments: true
---
# Promise
##### A promise is an object that may produce a single value some time in the future

Promise란 자바스크립트 비동기 처리에 사용되는 객체입니다. 여기서 자바스크립트의 비동기란 **'특정 코드의 실행이 완료될 때까지 기다리지 않고, 다음 코드를 먼저 수행하는 자바스크립트이 특성'** 을 의미합니다.  <br>

### `Promise의 필요성?`
- 프로미스는 주로 서버에서 받아온 데이터를 화면에 표시할 때 사용한다.
- 일반적으로 웹 어플리케이션을 구현할 때 서버에서 데이터를 요청하고 받아오기 위해, 아래와 같은 API를 사용한다.
> $.get('url 주소/products/1', function (response) {
  // ...
});

그런데 여기서 데이터를 받아오기도 전에 마치 데이터를 다 받아온 것 처럼 화면에 표시하려고 하면, 오류가 나거나 빈 화면이 뜬다. 이런 문제를 해결하기 위한 방법 중 하나가 프로미스이다.<br>

### `Promise의 3가지 상태`
- Pending : 비동기 처리 로직이 아직 완료되지 않은 상태
- Fulfilled : 비동기 처리가 완료되어 프로미스가 결과 값을 반환해준 상태
- Rejected : 비동기 처리가 실패하거나 오류가 발생한 상태

1. Pending
**new Promise()** 라는 코드를 통해 대기 상태가 될 수 있다.
또한 콜백 함수의 인자로 **resolve, reject** 에 접근할 수 있다.

resolve를 실행하면, Fulfilled 상태가 되고, 이 상태가 되면 then함수를 통해 결과값을 받을 수 있다. <br>

rejected 상태가 되면, catch()를 통해 받을 수 있다.<br>

![Image]({{ site.baseurl }}/images/promise.png)<br>


### `참고자료`
자바스크립트 Promise 쉽게 이해하기 : [joshua1988](https://joshua1988.github.io/web-development/javascript/promise-for-beginners/)<br>
