---
title:  "Firebase Authentication"
date: 2019-11-06 15:00:00
description: The history of Development Study
categories: [devKnowledge]
resource: true
comments: true
---
# Firebase Authentication
### `Authentication`
대부분의 앱에서 사용자의 신원 정보를 필요로 한다. 사용자의 신원을 알면 앱이 사용자 데이터를 클라우드에 안전하게 저장할 수있고, 사용자의 모든 기기에서 개인에게 맞춘 동일한 경험을 제공할 수 있기 때문이다. <br>
Firebase Authentication은 앱에서 사용자 인증 시 필요한 백엔드 서비스와 사용하기 쉬운 SDK, 기성 UI 라이브러리를 제공한다. 비밀번호, 전화번호, 인기 ID제공업체(Google, Facebook, Twiiter 등)을 통한 인증이 지원된다. <br>
Firebase 인증은 OAuth 2.0 및 OpenId Connect 등의 산업 표준을 활용함으로 맞춤 백엔드와 쉽게 통합할 수 있다. <br>

### `작동원리`
사용자를 앱에 로그인시키려면, 우선 사용자에게서 인증 정보를 받아야한다. 이 사용자 인증정보는 사용자의 이메일 주소와 비밀번호일 수도 있고, 제휴 ID 공급업체에서 받는 OAuth 토큰 일수도 있다. 그 다음 사용자 인증 정보를 Firebase 인증 SDK로 전달한다. 그러면 Firebase의 백엔드 서비스가 정보를 확인하여 클라이언트에게 응답을 반환한다. <br>
로그인 이후,
  - 사용자의 기본 프로필 정보에 액세스할 수 있다.
  - 다른 Firebase 제품에 저장된 데이터에 대한 사용자의 액세스 권한을 제한할 수 있다.
  - 인증 토큰을 이용하여 자체 백엔드 서비스에서도 사용자의 신원을 확인할 수 있다.

  {% highlight python %}
  {
 displayName: null,
 email: “chris@quiver.is”,
 emailVerified: false,
 isAnonymous: false,
 photoURL: null,
 providerData: {
  refreshToken: “ANflqpEVNvT4iOyKVVRyybjcuwKqnca…”,
  uid: “P1yjH4GgcFQcJUHULmTgMLC68w64”
 },
 refreshToken: “ANflqpEVNvT4iOyKVVRyybjcuwKqnca…”,
 uid: “P1yjH4GgcFQcJUHULmTgMLC68w64”
}
  {% endhighlight %}
이러한 User와 관련한 정보를 firebase.auth().currentUser를 통해 얻을 수 있다. 불필요한 정보도 여럿 섞여 있다.

다른 Google이나 Facebook같은 인증 제공자들은 사용자가 승인하도록 요청한 범위에 따라 제공자 데이터 노드에 더 많은 데이터를 제공한다.

{% highlight python %}
firebase.auth().createUserWithEmailAndPassword(email, password)
 .catch(function (err) {
   // Handle errors
 });

// Sign in existing user
firebase.auth().signInWithEmailAndPassword(email, password)
 .catch(function(err) {
   // Handle errors
 });

// Sign out user
firebase.auth().signOut()
 .catch(function (err) {
   // Handle errors
 });
{% endhighlight %}

위 함수들 같은 경우, promises를 반환하고 새로운 유저 등록은 자동으로 사용자에게 sign한다. 세 함수 모두, onAuthStateChanged observer를 호출한다. 그래서 handler에서 성공적인 auth changes에 대한 경우를 처리해주어야한다.

### `Auth Service Linking`
대부분의 어플리케이션들에서는 email,password, Google, Facebook 등 다수 로그인 방식을 제공한다. 유저는 지난번에 그들이 사용하는 앱중 어떤 계정으로 로그인했는지 잊어버리는 경우가 있다. 그래서 Firebase는 다수의 인증 방식을 링크해주는 기능을 제공한다. <br>
예를 들어, email/password 계정을 만들고, 다른 디바이스에서 Google로 로그인을 하려 하면 파이어베이스에서 존재하는 email/password 계정과 해당 Google 계정을 즉시 링킹해준다. <br>
링크된 Auth 계정들은 Unique Identifier(UID)를 공유하며 Firebase Auth Console 페이지에서 싱글 유저 진입에 대해 띄워줄 것이다.

### `참고자료`
How to Firebase? : [Chris Esplin](https://howtofirebase.com/firebase-authentication-for-web-d58aad62cf6d)<br>
