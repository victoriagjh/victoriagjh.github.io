---
title:  "장고 ViewSet이란?"
date: 2020-02-26 18:00:00
description: The history of Development Study
categories: [devKnowledge]
resource: true
comments: true
---
# 장고 ViewSet이란?
### ViewSets
Django의 REST Framework는 단일 클래스에서 관련있는 view들의 집합을 위해 logic의 결합을 허용합니다. 
ViewSets은 resource 또는 controller와 비슷한 개념입니다.
클래스는 단순하게 class 기반 view타입이며, .get(), .post()같은 모든 method 핸들러를 제공하지 못하지만, .list(), .create()와 같은 액션을 지원합니다.

View Set을 위한 method 핸들러들은 as_view()함수를 이용해 view가 끝나는 시점에 해당하는 행동이 취해질때 바인딩합니다.
일반적으로 url설정의 viewset안에서 view들을 명시적으로 등록하는 것보다 router 클래스를 가지고 있는 viewSet을 등록하고, 이 viewSet이 자동으로 사용자를 위해 url 설정을 결정합니다.

> Serializer(직렬화) : queryset과 모델 인스턴스와 같은 복잡한 데이터를 JSON, XML 또는 다른 콘텐츠 유형으로 쉽게 변환할 수 있습니다. 또한 serializer는 받은 데이터의 유효성(validation)을 검사한 다음, 복잡한 타입으로 형변환을 할 수 있도로고 serialization을 제공합니다. 

### ViewSet을 이용하여 래팩터링 하기
- viewsets.ReadOnlyModelViewSet : 읽기 전용 기능을 자동으로 지원한다
- queryset과 serializer_class 

