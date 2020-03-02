---
title:  "장고 Project V.S. App"
date: 2020-02-25 18:00:00
description: The history of Development Study
categories: [devKnowledge]
resource: true
comments: true
---
# 장고 Project V.S. App
### Django Project
하나의 프로젝트는 전체 어플리케이션과 모든 파트들을 나타낸다.

### Django App
하나의 앱은 프로젝트의 서브 모듈을 나타낸다. 하나의 앱은 그 자체로 충분하고, 프로젝트 안에서 다른 앱들과 꼬이지 않는다. 앱의 경우, 특정 앱을 하나 골라 다른 프로젝트에 어떠한 수정 없이 사용할 수 있다. 그래서 하나의 앱은 하나의 models.py를 가지고 있다(물론 이 models.py는 상황에 따라 빈 파일일 수도 있다🙂) 그냥 독립적인 파이썬 모듈이라고 생각해도 된다.

각 앱들이 서로 데이터를 주고받아야 할 경우(통신), 공개된 클래스 및 접근자 메서드를 이용해 수행할 수 있습니다.

##### 각 앱에서 의존성을 주의하자!
👉 하나의 앱에서 두가지 문제를 해결하면 이를 둘로 나누는 것이 좋다.
👉 두가지 앱이 서로 얽혀있으면, 다른 앱 없이 재사용할 수 없을 땐 이를 단일앱으로 결합시키세요.

### `참고자료`
스택오버플로우 : [stackoverflow](https://stackoverflow.com/questions/19350785/what-s-the-difference-between-a-project-and-an-app-in-django-world)  <br>
