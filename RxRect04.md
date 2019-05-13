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
* * *
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
* * *
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
* * *
