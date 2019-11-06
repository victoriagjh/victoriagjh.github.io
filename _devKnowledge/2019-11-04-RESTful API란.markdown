---
title:  "RESTful API란?"
date: 2019-11-04 18:26:00
description: The history of Development Study
categories: [devKnowledge]
resource: true
comments: true
---

# RESTful API를 파헤쳐봅시다🤩

### `REST API의 개념과 탄생배경`
REST : Representational State Transfer = 대표적인 상태전달 <br>
  👉 웹에 존재하는 모든 자원(이미지, 동영상, DB자원)에 고유한 URI(인터넷 식별자)를 부여해 활용하는 것 <br>
  👉 자원을 정의하고 자원에 대한 주소를 지정하는 방법론을 의미한다. <br>
  👉 **자원[소프트웨어가 관리하는 모든 것]을 이름으로 구분하여 해당 자원의 상태(정보)를 주고받는 모든 것을 의미한다.** <br>
    상태 전달 : 데이터가 요청되어지는 시점에서 자원의 정보를 전달, JSON 또는 XML을 통해 데이터를 주고받는 것이 일반적이다. <br>
  **REST는 기본적으로 웹의 기존 기술과 HTTP 프로토콜을 그대로 활용하기 때문에, 웹의 장점을 최대한 활용할 수 있는 아키텍처 스타일이다.**
  REST는 네트워크상에서 Client와 Server간 통신 방식중 하나이다.
REST API : REST의 특징을 지키면서 API를 제공하는 것을 의미한다. <br>

### `왜 REST의 개념이 대두되었을까?`
가장 큰 이유는 **어플리케이션 분리 및 통합, 다향한 클라이언트의 등장** 이라고 할 수 있다. <br>
어플리케이션의 복잡도가 증가하면서, 어플리케이션을 어떻게 분리하고 통합하느냐가 주요 이슈가 되었고, 모바일과 같은 다양한 클라이언트가 등장하면서 Backend 하나로 다양한 Device를 대응하기 위해 REST의 필요성이 증대되었다.<br>

##### REST : HTTP URI(uniform resource identifier)를 통해 자원을 명시하고, HTTP Method(Post, Get, Put, Delete)를 통해 해당 자원에 대한 CRUD Operation을 적용하는 것이다.

CRUD Operation : Create(생성 - Post), Read(조회 - Get), Update(수정 - Put), Delete(삭제 - Delete), Head(Header 정보 조회)

### `REST 구성`
1. 자원(Resource) : URI
  - 모든 자원에 고유한 ID가 존재하고, 이 자원은 Server에 존재한다.
  - 자원을 구별하는 ID는 '/groups/:groud_id'와 같은 HTTP URI이다.
  - Client는 URI를 이용해서 자원을 지정하고, 해당 자원의 상태(정보)를 Server에 요청한다.
2. 행위(Verb) : Http Method
  - GET, POST, PUT, DELETE와 같은 4가지의 메소드로 구성되어 있다.
3. 표현(Representations)
  - Client가 자원의 상태(정보)에 대한 조작을 요청하면, Server는 이에 적절한 응답을 보낸다.
  - REST에서 하나의 자원은 JSON, XML, TEXT 등 여러 형태로 나타내어 질 수 있다.
  - JSON 또는 XML을 통해, 데이터를 주고받는 것이 일반적이다.

### `REST의 특징`
1. Server-Client 구조
  REST 서버 : API제공, 비즈니스 로직 처리 및 저장
  클라이언트 : 사용자 인증이나 컨텍스트(세션, 로그인 정보)등을 직접 관리
  **클라이언트와 서버에서 개발해야 할 내용이 명확해지고 서로간 의존성이 줄어든다.**
2. Stateless(무상태성)
  HTTP 프로토콜은 Stateless Protocol이므로, REST 역시 무상태성인 성격을 가지고 있다.
  Client의 context(세션, 로그인 정보)를 Server에 저장하지 않는다. 👉 구현이 단순해짐
  **Server는 각각의 요청을 별개의 것이라고 인식하고 처리한다.**
    각 API서버는 Client의 요청만을 단순 처리한다.
    이전 요청이 다음 요청의 처리에 연관되어 있어서는 안된다.(이전요청이 DB를 수정했을 경우는 노상관!)
    Server의 처리방식에 일관성을 부여하고, 부담이 줄어들어 서비스 자유도가 높아진다.
3. Cacheable(캐시 가능)
  HTTP라는 기본 웹 표준을 그대로 사용하기 때문에, 웹에서 사용하는 기존 인프라를 그대로 활용이 가능하다. 따라서 HTTP가 가진 캐싱 기능이 적용가능하다.
  Cache 사용을 통해, 응답시간이 빨라지고 REST Server 트랜잭션이 발생하지 않기 때문에 전체 응답시간, 성능, 서버의 자원 이용률을 향상 시킬 수 있다.
##### 4. Layered System(계층화)
  Client는 REST API Server만 호출한다.
  Rest API 서버는 다중 계층으로 구성될 수 있다.
    API Server는 순수 비즈니스 로직을 수행하고 그 앞단에 보안, 로드밸런싱, 암호화, 사용자 인증 등을 추가하여 구조상 유연성을 줄 수 있다.
    로드 밸런싱, 공유 캐시 등을 통해 확장성과 보안성을 향상시킬 수 있다.
  - Client는 REST API만을 호출하고, 해당 SERVER에서 보안이나 암호나 사용자 인증과 관련한 다른 API를 또 호출하여 결과를 받고 이를 응답으로 보내주는 과정이 계층적인 것이다.
5. Uniform Interface
  URI로 지정한 Resource에 대한 조작을 통일되고 한정적인 인터페이스로 수행한다.
  HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 사용이 가능하다.

##### 이러한 5가지의 규칙을 잘 지킨 구조를 **REST Ful** 하다고 한다.
REST 구성의 이유 -> 내가 굳이 상황을 뜯어보지 않아도, 명확한 요청과 응답을 주도록 만드는 것

### `REST 구성`
RMM - Richardson Maturity Model<br>
![Image]({{ site.baseurl }}/images/rmm.png)<br>
<br>
1. Level 0. The Swamp of POX<br>
  ![Image]({{ site.baseurl }}/images/rmm0.png)<br>
  <br>
  웹의 기본 매커니즘을 전혀 사용하지 않는 단계로, HTTP를 통해 데이터를 주고 받지만, 모든 데이터 요청 및 응답과 관련한 정보를 HTTP의 body 정보를 활용한다. POX(Plain Old XML)로 요청과 응답을 주고받는 RPC 스타일의 시스템이다. 이때 HTTP method는 POST만 사용하며, 특정 서비스를 위해 클라이언트에서 접근할 endpoint는 하나이다. <br>
  **하나의 URI를 가지고, 하나의 HTTP method(POST)를 사용한다. 내용에 대한 구분은 XML을 Payload로 사용해서 요청을 구분하는 방식을 취한다. 모든 걸 하나의 리소스를 가지고 처리한다.** <br>
  **0단계에서는 내 마음대로 서버에 요청을 보낸다** <br>
  - endpoint도 하나야! POST에 내 요청을 감싸서, 서버는 이를 받아 If문으로 조건을 체크해 맞는 함수를 실행시키는 단계라고 할 수 있다.<br>

2. Level 1. Resources<br>
  ![Image]({{ site.baseurl }}/images/rmm1.png)<br>
  <br>
  RMM에서 REST를 위한 첫 단계는 리소스를 도입한다는 것이다. 그래서 이제는 모든 요청을 단일 서비스 endpoint로 보내는 것이 아니라 개별 리소스와 통신한다.<br>
  **함수를 호출하고 인자들을 넘기는 것이 아니라 다른 정보를 위해 인자들을 제공하는 특정 리소스(URI)로 요청을 보낸다. 이럴 경우, 자원이 외부로 보여지는 방식과 내부에 저장되는 방식을 분리할 수 있다는 것이다.**<br>
  **1단계에서는, resource를 구분시켜서, 서버에 URI를 통해 요청을 구분해서 보낸다. 요청만 봐도 어떤 작업인지 알 수 있도록!**<br>
  - 단일 서비스 Endpoint로 보내는 것이 아니라, 개별 리소스와 통신한고 URI에 어떤 리소스인지 구분시켜서 서버에 요청을 보낸다.<br>

3. Level 2. HTTP Method<br>
  ![Image]({{ site.baseurl }}/images/rmm2.png)<br>
  <br>
  Level 1의 URL + HTTP Method 조합으로 리소스를 구분하는 것으로 응답 상태를 HTTP Status code를 활용한다.<br>
  발생한 에러의 종류를 커뮤니케이션하기 위해 상태코드를 사용하는 것, 그리고 안전한 오퍼레이션(GET)과 안전하지 않은 오퍼레이션(?)간의 강한 분리를 제공하는 것이 레벨 2의 핵심이다<br>
  Status Code를 사용한다 👉 기존에는 유저 생성 요청을 했을 경우, Redirect Request을 서버에서 내려주는 방식이었다<br>
  - 단일 서비스 Endpoint가 아니고, 명확하지 않은 요청을 원하는 행위를 HTTP Method를 이용해서 명확히 표현하고, 행위를 당하는 리소스는 URI로 표현하여 서버에 요청하는 것.<br>
  - Status Code를 사용한다는 건, 각 함수에 대한 여러 상황에 정확한 응답을 준다.<br>

4. Level 3. HYPERMEDIA CONTROL<br>
![Image]({{ site.baseurl }}/images/rmm3.png)<br>
<br>
  API를 호출했으면, 그 다음에 호출할 수 있는 API가 기다리고 있음.<br>
  Update를 보냈으면, 응답으로 API를 쓸수있는 링크가 온다. -> 리퀘스트를 보내면, 그 다음 리퀘스트에 대한 엔드포인트를 응답으로 보내준다.<br>
  EX) 내가 카카오톡에 어떤 친구를 차단 요청을 보냈을 때, 그 응답으로 해당 친구를 채팅방에서 다 지울 수 있는 API의 링크가 응답으로 나에게 돌아오고, 나는 그 응답을 이용하여 다시 api를 호출함으로 내가 있는 채팅방에 그 친구들 다 없앨 수 있다.<br>

### 참고자료
REST API 제대로 알고 사용하기 : [TOAST Meetup!](https://meetup.toast.com/posts/92)<br>
REST API란? : [ijbgo](https://ijbgo.tistory.com/20)<br>
