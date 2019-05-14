# RxSwiftRect
RxSwiftRect Recture

1강 RxSwift
===========
1. Reactive X : Observable 스트림으로 비동기 프로그래밍 할 수 있도록 해주는 API

2. 주요 기능 : Observable, Operator, Subject, Single

3. Swift의 병렬 프로그래밍
* NSThread : 스레풀을 직접 관리 해야하는 기본 클래스
* OperationQueue : 스레드를 큐에 넣고 운영체제가 알아서 꺼내어 쓰도록 하는 클래스
* GCD : 운영 체제가 스레드 풀을 관리하는 C 기반 API, DispatchQueue 클래스를 이용
    1. ConcurrencyQueue : 스레드 작업을 동시실행(global, main)
    2. SerialQueue : 스레드 작업을 순서대로 실행
