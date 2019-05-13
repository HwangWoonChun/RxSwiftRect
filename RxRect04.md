# RxSwfitRect
RxSwfit Recture

4강 Operator
===========
1. Observable 생성과 관련된 연산자
* Just : 객체하나 혹은 집합을 그대로 Observable 로 변환
<pre><code>
    func exJust(){
        Observable.just(["RXSwift","In","4","Hour"])
            .subscribe(onNext : { s in
                print(s)
            }, onError : { err in
                print(err.localizedDescription)
            }, onCompleted : {
                print("onCompleted")
            }, onDisposed : {
                print("onDispsed")
            })
            .disposed(by : disposeBag)
    }
    //["RXSwift", "In", "4", "Hour"]
    //onCompleted
    //onDispsed
</pre></code>
* From : Array 등의 콜랙션에 있는 데이터를 하나 하나 처리
<pre><code>
    func exFrom(){
        Observable.from(["R","X"])
            .subscribe(onNext : { s in
                print(s)
            }, onError : { err in
                print(err.localizedDescription)
            }, onCompleted : {
                print("onCompleted")
            }, onDisposed : {
                print("onDispsed")
            })
            .disposed(by : disposeBag)
    }
     //R
     //X
    //onCompleted
    //onDispsed
</pre></code>
* * *
2. Observable 변환과 관련된 연산자
* Map : 받은 데이터를 가공하여 내려보낸다.
<pre><code>
    func exMap(){
        Observable.from(["R","X"])
            .map{ $0.replacingOccurrences(of: "R", with: "RR")}
            .subscribe(onNext : { s in
                print(s)
            }, onError : { err in
                print(err.localizedDescription)
            }, onCompleted : {
                print("onCompleted")
            }, onDisposed : {
                print("onDispsed")
            })
            .disposed(by : disposeBag)
    }
     //RR
     //X
    //onCompleted
    //onDispsed
</pre></code>
* FlatMap : 데이터를 넣으면 스트림이 나오는 함수
* * *
3. 필터링 : 각각의 옵져버블의 결과를 합쳐서 처리 할 수 있다.
* Filter : 받은 데이터에 조건문을 넣어 참 값 만 내려보낸다.
<pre><code>
    func exFilter(){
        Observable.from(["R","X"])
            .filter{$0 == "R"}
            .subscribe(onNext : { s in
                print(s)
            }, onError : { err in
                print(err.localizedDescription)
            }, onCompleted : {
                print("onCompleted")
            }, onDisposed : {
                print("onDispsed")
            })
            .disposed(by : disposeBag)
    }
     //R
    //onCompleted
    //onDispsed
</pre></code>
* First : 맨 처음 항목만 받고 Complete되는 시점에 받거나, 조건문과 결합하여 첫 번째로 참인 데이터만 받는 함수
* Single : Complete되는 시점에 항목 하나 받는 함수
* * *
4. 수학 및 집계 연산자 : Observable이 배출하는 항목 전체를 대상으로 동작하는 연산자들
* Concart : 여러개의 Observable 을 순서대로 결합
