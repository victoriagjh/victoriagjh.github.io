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

##### 왜 REST의 개념이 대두되었을까?
가장 큰 이유는 **어플리케이션 분리 및 통합, 다향한 클라이언트의 등장** 이라고 할 수 있다. <br>
어플리케이션의 복잡도가 증가하면서, 어플리케이션을 어떻게 분리하고 통합하느냐가 주요 이슈가 되었고, 모바일과 같은 다양한 클라이언트가 등장하면서 Backend 하나로 다양한 Device를 대응하기 위해 REST의 필요성이 증대되었다.<br>




### 참고자료
REST API 제대로 알고 사용하기 : [TOAST Meetup!](https://meetup.toast.com/posts/92)<br>
