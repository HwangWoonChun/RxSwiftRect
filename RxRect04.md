# RxSwfitRect
RxSwfit Recture

4강 기본 Operator
===========
1. Create : 개발자가 직접 Observable를 만드는 operator
<pre><code>
    func rxswiftLoadImage(from imageUrl: String) -> Observable {
        return Observable.create { seal in
            asyncLoadImage(from: imageUrl) { image in
                seal.onNext(image)
                seal.onCompleted()
            }
            return Disposables.create()
        }
    }
</pre></code>
* * *
2. Just : 직접 데이터를 그대로 내러 보내는 operator
<pre><code>
    @IBAction func exJust1() {
        Observable.just("Hello World")
            .subscribe(onNext: { str in
                print(str)
            })
            .disposed(by: disposeBag)
    }
    //hello World

    @IBAction func exJust2() {
        Observable.just(["Hello", "World"])
            .subscribe(onNext: { arr in
                print(arr)	
            })
            .disposed(by: disposeBag)
    }
    //[“Hello”,”World”] 배열 그대로 나옴
</pre></code>
* * *
3. From : 배열에서 데이터 하나하나 꺼내오는 operator
<pre><code>
    @IBAction func exFrom1() {
        Observable.from(["RxSwift", "In", "4", "Hours"])
            .subscribe(onNext: { str in
                print(str)
            })
            .disposed(by: disposeBag)
    }
    //RxSwift
    //In
    //4
    //Hours
</pre></code>
