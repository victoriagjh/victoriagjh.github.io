---
title:  "Semaphore와 Mutex"
date: 2019-11-26 2:30:00
description: The history of Development Study
categories: [devKnowledge]
resource: true
comments: true
---
# Semaphore와 Mutex
### `Semaphore와 Mutex`
프로세스 간 메시지를 전송하거나, 공유 메모리를 통해 특정 데이터를 공유하게 되는 경우, 문제가 발생할 수 있다.
그래서 공유된 자원에 여러 개의 프로세스가 동시에 접근하면서 문제가 발생하기 때문에 공유된 자원 속 하나의 데이터는 한번에 하나의 프로세스만 접근할 수 있도록 제한해두어야 하기 때문에, **Semaphore** 가 고안되었다. <br>

- Semaphore : 공유된 자원의 데이터를 여러 프로세스가 접근하는 것을 막는 것
- Mutex : 공유된 자원의 데이터를 여러 쓰레드가 접근하는 것을 막는 것

### `Critical Section`
OS에서 Critical Section은 아주 중요한 부분이다. <br>
- 다중 프로그래밍 운영체제에서 여러 프로세스가 데이터를 공유하면서 수행될 때, **각 프로세스에서 공유 데이터를 액세스하는 프로그램 코드 부분** 을 가리킨다. <br>
- 공유 데이터를 여러 프로세스가 동시에 액세스하면 시간적인 차이 때문에 잘못된 결과를 만들어 낼 수 있기 때문에, 한 프로세스가 위험 부분을 수행하고 있을 때, 즉 공유 데이터를 액세스하고 있을 때는 다른 프로세스들은 절대로 그 데이터를 액세스하지 못하게 해야한다. <br>

1. Mutex란? - 상호 배제
**Mutual Exclusion으로 상호배제라고도 한다.** Critical Section을 가진 쓰레드들의 Running Time이 서로 겹치지 않게 각각 단독으로 실행되게 하는 기술이다. 다중 프로세스들의 공유 리소스에 대한 접근을 조율하기 위해 locking과 unlocking을 사용한다. <br>

2. Semaphore란?
세마포어는 리소스 상태를 나타내는 간단한 카운터라고 생각할 수 있다. 일반적으로 비교적 긴 시간을 확보하는 리소스에 대해 이용하게 되며, 유닉스 시스템 프로그래밍에서 세마포어는 운영체제의 리소스를 경쟁적으로 사용하는 다중 프로세스에서 행동을 조정하거나 또는 동기화 시키는 기술이다. <br>

### `Mutex V.S. Semaphore`
1. Semaphore는 Mutex가 될 수 있으나, Mutex는 Semaphore가 될 수 없다.
- Mutex는 상태가 0,1 두 개뿐인 binary Semaphore

2. Semaphore는 소유할 수 없는 반면, Mutex는 소유가 가능하며 소유주가 이에 대한 책임을 진다.
- Mutex의 경우 lock을 가질 수 있다

3. Mutex의 경우, Mutex를 소유하고 있는 쓰레드가 이 Mutex를 해제할 수 있다. 하지만 Semaphore의 경우, 이러한 Semaphore를 소유하지 않는 쓰레드가 Semaphore를 해제할 수 있다.

4. Semaphore는 시스템 범위에 걸쳐있고, 파일형태로 존재한다. 반면, Mutex는 프로세스 범위를 가지며 프로세스가 종료될 때 자동으로 clean up된다.

##### 가장 큰 차이점은 관리하는 동기화 대상의 갯수이다. Mutex는 동기화 대상이 오직 하나뿐일 때, Semaphore는 동기화 대상이 하나 이상일 때 사용한다.

EX) Semaphore는 1개 이상의 열쇠이다.
만약 화장실칸이 4개이고, 열쇠가 4개이면 4명까지는 대기없이 바로 사용할 수 있고 그 이상부터는 대기해야한다.
![Image]({{ site.baseurl }}/images/semaphore.png)<br>

뮤텍스는 무조건 1개의 열쇠만 가질 수 있다.
![Image]({{ site.baseurl }}/images/mutex.png)<br>



### `참고자료`
Semaphore란? : [jwprogramming](https://jwprogramming.tistory.com/13)<br>
세마포어와 뮤텍스 : [sycho-lego](https://sycho-lego.tistory.com/11)<br>
