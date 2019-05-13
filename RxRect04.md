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
* create : 직접 Observable을 만들 수 있는 함수
<pre><code>
    func rxswiftLoadImage(from imageUrl: String) -> Observable<UIImage?> {
        return Observable.create { seal in
            asyncLoadImage(from: imageUrl) { image in
                seal.onNext(image)
                seal.onCompleted()
            }
            return Disposables.create()
        }
    }
    
    @IBAction func onLoadImage(_ sender: Any) {
        imageView.image = nil

        _ = rxswiftLoadImage(from: LARGER_IMAGE_URL)
            .observeOn(MainScheduler.instance)
            .subscribe({ result in
                switch result {
                case let .next(image):
                    self.imageView.image = image

                case let .error(err):
                    print(err.localizedDescription)

                case .completed:
                    break
                }
            })
    }
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
* FlatMap : 원본 옵져버블을 변형시키는데, 새로운 옵져버블을 만든다. sequenceInt에서 발행한 아이템에서 새로운 Observable을 만들고 발행한 아이템을 구독하여 출력
<pre><code>
        let sequenceInt = Observable.of(1,2,3)
        let sequenceStr = Observable.of("A","B","C","D")
        
        sequenceInt
            .flatMap({(x : Int) -> Observable<String> in
                print("Int : \(x)")
                return sequenceStr
        })
            .subscribe {
                print("String : \($0)")
        }
        
//        Int : 1
//        String : next(A)
//        Int : 2
//        String : next(B)
//        String : next(A)
//        Int : 3
//        String : next(C)
//        String : next(B)
//        String : next(A)
//        String : next(D)
//        String : next(C)
//        String : next(B)
//        String : next(D)
//        String : next(C)
//        String : next(D)
//        String : completed

</pre></code>
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
* Concat : 여러개의 Observable 을 순서대로 결합
