---
title:  "Signed URL 뭐하는 녀석이니?"
date: 2020-02-26 20:00:00
description: The history of Development Study
categories: [devKnowledge]
resource: true
comments: true
---
# Signed URL이란?
### Signed URL : 요청을 수행하는 데 필요한 제한된 권한과 시간을 제공하는 URL <br>
> 서명된 URL은 쿼리 문자열에 인증 정보가 포함되어 있어 사용자 인증 정보가 없는 사용자도 리소스에 대한 특정 작업을 수행할 수 있습니다. 서명된 URL을 생성할 때는 서명된 URL이 수행할 요청에 필요한 권한이 있는 사용자 또는 서비스 계정을 지정합니다. 서명된 URL을 생성하면 서명된 URL을 소유한 모든 사람이 이를 사용하여 지정된 기간 내에 객체 읽기와 같은 지정된 작업을 수행할 수 있습니다. <br>
![Image]({{ site.baseurl }}/images/signed_url.png)<br>

위 아키텍처의 가장 중요한 특성은 파일 업로드부터 delivery까지 서버리스 플랫폼을 사용한다는 점입니다.
1. 앱엔진이 유저로부터 요청을 받으면, 앱엔진은 어플리케이션 도메인 로직을 이용하여 특정 클라우드 스토리지 버켓에 PUT request를 수행할 수 있는 Signed URL과 인증된 유저를 위한 객체를 생성해줍니다.
2. 유저는 특정 버켓과 객체에 따라서 파일을 업로드할 수 있습니다.
3. 클라우드 스토리지에 파일을 업로드하면, finalize라는 이벤트를 발생시킵니다. 그러면 클라우드 함수는 업로드된 파일의 유효성을 판단합니다. (적절한 사이즈인지, 이미지 포맷 등) 
4. 파일을 확인한 이후, 부적절한 컨텐츠를 필터링하기 위해 클라우드 비전 API에 포스팅함으로써,해당 이미지에 주석을 답니다.
5. 3번과 4번이 끝나면 해당 이미지를 Uploadable Bucket에서 Distribution Bucket으로 복사합니다.
6. 카피된 파일의 이미지는 public으로 이용가능합니다.




##### 각 앱에서 의존성을 주의하자!
👉 하나의 앱에서 두가지 문제를 해결하면 이를 둘로 나누는 것이 좋다.
👉 두가지 앱이 서로 얽혀있으면, 다른 앱 없이 재사용할 수 없을 땐 이를 단일앱으로 결합시키세요.

### `참고자료`
스택오버플로우 : [stackoverflow](https://stackoverflow.com/questions/19350785/what-s-the-difference-between-a-project-and-an-app-in-django-world)  <br>
