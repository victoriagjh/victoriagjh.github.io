---
title:  "Java Script 배경지식"
date: 2019-11-21 02:30:00
description: The history of Development Study
categories: [devKnowledge]
resource: true
comments: true
---
# 자바스크립트 공부를 시작해봅시다.
##### "자바스크립트를 잘 이해하고 있다는 것" === "자바스크립트 함수를 잘 이해하고 있다는 것"

### `자바스크립트의 버전`
- 자바스크립트 버젼은 ECMAScript(ES, 이크마스크립트)의 버젼에 따라서 결정된다.

### `함수`
> 자바스크립트에서는 **함수도 객체** 이다.
  따라서 다른 객체와 마찬가지로 넘기거나 할당할 수 있으며, 함수를 객체 프로퍼티에 할당할 수도 있다.
  **함수 호출** 은 functionName()으로 실행할 수 있고, **함수 참조** 는 그냥 함수 이름만 할당하는 것으로 (const f = functionName)이라고 했을 때, f()으로 함수를 호출할 수 있다.

##### `원시값 매개변수 V.S. 객체 매개변수`
- 함수를 호출하면 함수 매개변수는 변수 자체가 아니라 그 값을 전달받는다.
  - 넘겨받은 원시값 매개변수를 함수내에서 변경하더라도 밖에서는 변경되지 않는다.
  - 하지만 객체를 넘겨받았을 시, 이 객체 자체를 변경하면 그 객체는 함수밖에서도 바뀐 점이 반영된다.

> Java Script에서 함수는 반드시 return 값이 존재하며, 리턴값이 없을 때는 'undefined'가 반환된다.

### `arguments 객체`
- 함수 호출시에 넘겨진 실제 인자값을 가지는 객체
- 함수가 실행되면 그 안에는 arguments라는 특별한 지역변수가 자동으로 생성된다***
- 자바스크립트 함수는 선언한 파라미터보다 더 많은 인자를 보낼 수 있고, arguments로 하나씩 접근이 가능하다.
- arguments는 배열의 형태(array-like)를 가지고 있다. 하지만 **배열 타입** 은 아니다.

> function a() {
    console.log(arguments);
  }
  a(1, 2, 3); // { '0':1, '1':2, '2':3 }

이런식으로 3개의 인자를 추가적으로 보내는 것이 가능하며, arguments['0'] 또는 arguments[0] 을 통해 접근이 가능할 것 같다. <br>
- arguments도 남용하면 변경에 약한 코드가 된다. 또한 함부로 수정하는 것을 조심해야 한다. 수정이 안된다해도 해당값을 바꾸려고 하는 것은 좋지 않다.

### `함수 선언문 V.S. 함수 표현식`
1. 함수 선언문
{% highlight javascript %}
function functionName(){
  ~~
}
{% endhighlight %}

2. 함수 표현식
- 변수에 함수 표현을 담아놓는 형태
- 함수표현식은 익명 함수표현식과 기명함수 표현식으로 나눌 수 있다.
  - 일반적으로 함수표현식이라고 부르면, 익명이 생략된 형태라고 할 수 있다. (함수명-함수 식별자)가 없음
- **클로저로 사용** 가능하고 **콜백으로 사용** 가능하다.

{% highlight javascript %}
var test1 = function() { // (익명) 함수표현식
  return '익명 함수표현식';
}

var test2 = function functionName() { // 기명 함수표현식
  return '기명 함수표현식';
}
{% endhighlight %}

##### 차이점
- 함수 선언문은 호이스팅에 영향을 받지만, 함수표현식은 호이스팅에 영향을 받지 않는다.

{% highlight javascript %}
function printName(firstname) { // 함수선언문
    console.log(inner); // "undefined": 선언은 되어 있지만 값이 할당되어있지 않은 경우
    var result = inner(); // ERROR!!
    console.log("name is " + result);

    var inner = function() { // 함수표현식
        return "inner value";
    }
}
printName(); // TypeError: inner is not a function

**JS Parser 내부의 호이스팅(Hoisting)의 결과**
function printName(firstname) { // 함수선언문
    var inner; // Hoisting - 변수값을 끌어올린다. (선언은 되어 있지만 값이 할당되어있지 않은 경우)
    console.log(inner); // "undefined"
    var result = inner(); // ERROR!!
    console.log("name is " + result);

    inner = function() { // 함수표현식
        return "inner value";
    }
}
printName();
{% endhighlight %}

이런 코드의 경우, inner is not a function이라는 TypeError가 발생한다. 그 이유는 JS Parser가 inner를 미리 undefined라고 지정했기 때문에, inner를 함수로 인식하지 않아서 에러가 난다. <br>

**함수 표현식의 선언이 호출보다 위에 있는 경우에는 정상적으로 작동된다.**<br>
또한 저 inner라는 변수를 let을 이용하여 선언하면, 그냥 'defined' 자체가 되지 않기때문에 에러가 발생한다.<br>

### `Java Script 변수`
- 변수는 var,let,const로 선언할 수 있다.
- 어떤 것을 사용하는가에 의해서 scope, 즉 변수의 유효범위가 달라진다.
- ES6 이전 버전에서는 var를 사용해서 변수를 선언할 수 있다.
  - var로 선언한 변수는 끌어올린다는 뜻의 호이스팅(hoisting)이라는 매커니즘을 따른다.

##### ES6 - const,let
- 블록 단위 {}로 변수의 범위가 제한되었다.
- const : 한번 선언한 값에 대해서 변경할 수 없다(상수 개념)
- let : 한번 선언한 값에 대해서 다시 선언할 수 없다. 변경은 가능하다 **(메모리에 할당하면 다시 할당하지 못한다)**

### `연산자`
- 연산자 우선순위를 표현하기 위해서는 ()를 사용하면 된다
- 수학연산자, 논리연산자(&&,||), 삼항연산자(?), 비교연산자 등이 있다.

##### 비교연산자
- 비교는 == 보다 ===을 사용한다
- '==='은 Type까지 확인하는 연산자이다
- '=='은 임의적으로 Type을 바꿔서 비교하기 때문에 원하는 결과와 다른 값이 나올 수 있다

### `자바스크립트 타입`
##### undefined, null, boolean, number, string, object, function, array, Date, RegExp 등등..

##### 기본형과 참조형?
- 기본형(Primitive) : Number, String, Boolean, null, undefined
  - 값을 그대로 할당함
- 참조형(Reference) : Array, Function, RegExp
  - 기본형 데이터의 집합으로 값이 저장된 주소값을 할당한다. (그래서 매개변수로 객체타입이 오면 실제로 그 객체 값이 변한다)

##### 자바스크립트 Type 체크
  - 컴파일 단계가 없는 자바스크립트의 Type은 선언할 때가 아니라, 실행타임(Dynamic Type)에 결정된다.

### `참고자료`
함수표현식 v.s. 함수 선언문 : [heejeong Kwon](https://gmlwjd9405.github.io/2019/04/20/function-declaration-vs-function-expression.html)<br>
자바스크립트의 변수, 연산자, 타입: [heejeong Kwon](https://gmlwjd9405.github.io/2019/04/18/javascript-variable-and-type.html)<br>
