---
title:  "Java Script 동작원리"
date: 2019-11-25 18:30:00
description: The history of Development Study
categories: [devKnowledge]
resource: true
comments: true
---
# 자바스크립트 동작원리에 관한 공부를 시작해봅시다.
### `자바스크립트 작동 원리에 대해서 - Event Loop`
- 싱글스레드 기반으로 동작하는 자바스크립트
- 이벤트 루프를 기반으로 하는 싱글 스레드 Node.js

이런 말을 많이 볼 수 있었으나 구체적으로 내부 원리에 대해 간단하게라도 설명하는 글은 보기 힘들다.
1. "정말 싱글스레드인가?"
2. "싱글 스레드는 무엇이고, 어떻게 싱글 스레드인가?"
3. "이벤트 루프는 무엇인가?"

### `자바스크립트의 Engine`
우선 Javascript를 해석하는 **JavaScript Engine** 과 웹 브라우저에 화면은 그리는 **Rendering Engine** 은 다른 것이다. <br>
**Rendering Engine 또는 Layout Engine** 은 HTML과 CSS로 작성된 마크업 관련한 코드를 콘텐츠로 사용하여 웹 페이지에 'rendering'하는 역할을 한다. <br>
**JavaScript Engine** 이란 JavaScript로 작성한 코드를 해석하고 실행하는 인터프리터이다. 주로 웹 브라우저에서 이용되지만 최근에는 Node.js의 등장으로 server side에선 V8과 같은 Engine이 이용된다.<br>

> V8 : 웹 브라우저를 만드는 데, 기반을 제공하는 오픈 소스 자바스크립트 엔진이다. V8은 자바스크립트를 바이트코드로 컴파일하고 실행하는 방식을 사용한다.

구글에서 개발한 V8을 비롯하여, 대부분의 자바스크립트 엔진은 크게 다음의 세 영역으로 나뉜다. <br>
**Call Stack, Task Queue(Event Queue), Heap** <br>
추가적으로 **Event Loop** 라는 녀석이 존재하여 Task Queue에 들어가는 task들을 관리하게 된다.<br>
![Image]({{ site.baseurl }}/images/javascript_engine.png)<br>

##### Call Stack
자바스크립트는 단 하나의 호출 스택을 사용한다. 이러한 특징 때문에, 자바스크립트의 함수가 실행되는 방식을 **Run to Completion** 이라고 한다. <br>
이는 단 하나의 함수가 실행되면, 이 함수의 실행이 끝날 때까지 다른 어떤 task도 수행될 수 없다는 의미이다. 요청이 들어올 때마다 요청을 순차적으로 Call Stack에 담아 처리한다. 메소드가 수행될때, Call Stack에 새로운 프레임이 **Push되고** 메소드의 실행이 끝나면 해당 프레임은 **Pop 된다.**<br>

##### Heap
동적으로 생성된 객체(인스턴스)는 힙(Heap)에 할당된다. 대부분 구조화되지 않는 '더미'같은 메모리 영역을 'heap'이라고 표현한다.

##### Task Queue(Event Queue)
자바스크립트의 런타임 환경에서는 처리해야 하는 Task들을 임시 저장하는 대기 큐가 존재한다. 그런 대기큐를 Task Queue 또는 Event Queue라고 한다. 그리고 **Call Stack이 비워졌을 때**, 대기열에 들어온 순서대로 수행된다. <br>

자바스크립에서 비동기로 호출되는 함수들은 Call Stack에 쌓이지 않고, Task Queue에 enqueue된다. 자바스크립트에서는 이벤트에 의해 실행되는 함수(핸들러)들이 비동기로 실행된다. 자바스크립트 엔진이 아닌 Web API 영역에 따로 정의되어있는 함수들은 비동기로 실행된다. <br>

{% highlight javascript %}
function test1(){
  console.log("test1");
  test2();
}
function test2(){
  let timer = setTimeout(function(){
      console.log("test2");
    },0);
  test3();
}
function test3(){
  console.log("test3");
}
test1();
{% endhighlight %}

위의 코드를 실행하면, test1이 우선 console로 찍힌다. 그리고 test2가 호출되면서 setTimeout 함수가 실행되고, 콜스택에 들어간 다음 바로 빠져나온다. 내부에 걸려있던 핸들러(익명함수)는 콜스택에 들어가서 바로 실행되지 않는다. 이 핸들러는 event queue 영역에 들어간다. 그리고 test3 함수가 콜스택에 들어가고 test3가 console에 찍힌다음, Call Stack에서 test3 pop, test2 pop, test1 pop된다. 이렇게 Event Loop에 Call Stack이 비어지게 되면서, queue에서 하나의 event를 Call Stack에 넣는다. 그렇게 setTimeout이 실행되면서, console에 test2가 찍히게 된다.<br>
**이벤트에 걸려있는 핸들러는 절대 먼저 실행될 수 없다.** <br>

**호출 스택 처리에 어마어마한 시간이 걸리는 함수가 있으면, 브라우저는 페이지를 그리지도 못하고, 어느 코드도 실행을 못하는 대기 상태에 들어가버린다.**<br>
호출 스택의 많은 작업을 처리하다 보면, 화면이 오랫동안 응답을 하지 못하게 된다.
이러한 상황에서 렌더링 동작을 방해하지 않고, 브라우저의 응답도 끊지 않으면서, 연산량이 맣은 코드를 실행하려면 **비동기 콜백** 을 사용해야 한다. <br>


### `JavaScript의 처리속도`
자바스크립트는 싱글스레드로 한번에 하나의 작업만 가능하다. 하나의 Call Stack이 존재하여, 한번에 하나의 작업이 수행되는데, 어떻게 동시성이 가능한 것일까?<br>
- 자바스크립트는 함수가 어떤 함수가 실행되고 있을 때, 그 실행이 끝나기 전까지 다른 함수가 실행되지 못한다. **Run to Completion**<br>

1. Web API
Web API는 브라우저에서 제공되는 API이며, AJax나 Timeout같은 비동기 작업을 실행합니다. 자바스크립트에서 setTimeout함수를 실행하면, 자바스크립트 엔진은 Web API에 setTimeout을 요청하고, 동시에 setTimeout에 넣어준 Callback까지 전달된다. CallStack에서는 Web API 요청 이후, setTimeout이 종료되기 때문에 제거된다.
그래서 Web API는 방금 요청받은 setTimeout을 완료하고, 전달받은 Callback을 Task Queue에 넘겨준다.

2. Task Queue와 Event Loop
Task Queue는 Callback Queue라고도 하는데, 큐 형태로 Web API에서 넘겨받은 Callback 함수를 저장한다. 이 Callback 함수들은 자바스크립트 엔진의 CallStack이 비었을 떼, push된다.
이때, Event Loop에서 Call Stack이 비어있는지 아닌지, Task Queue가 존재하는지 아닌지 판단하는 역할을 맡는다.




### `참고자료`
JavaScript 작동 원리에 대해서 : [k39335](https://k39335.tistory.com/9)<br>
