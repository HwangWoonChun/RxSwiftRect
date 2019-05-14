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
* * *
4. Map : 데이터를 가공하여 전달 해주는 Operator
<pre><code>
    @IBAction func exMap1() {
        Observable.just("Hello")
            .map { str in "\(str) RxSwift" }
            .subscribe(onNext: { str in
                print(str)
            })
            .disposed(by: disposeBag)
    }
    //Hello RxSwift
    
    @IBAction func exMap2() {
        Observable.from(["with", "곰튀김"])
            .map { $0.count }
            .subscribe(onNext: { str in
                print(str)
            })
            .disposed(by: disposeBag)
    }
    //4
    //3
</pre></code>
* * *
5. Filter : 데이터가 조건문에 걸러져 참인 경우 내려보내는 Operator
<pre><code>
    @IBAction func exFilter() {
        Observable.from([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
            .filter { $0 % 2 == 0 }
            .subscribe(onNext: { n in
                print(n)
            })
            .disposed(by: disposeBag)
    }
    //2, 4, 6, 8, 10
</pre></code>
* * *
6. 응용
<pre><code>
    @IBAction func exMap3() {
        Observable.just("800x600")
            .map { $0.replacingOccurrences(of: "x", with: "/") }
            .map { "https://picsum.photos/\($0)/?random" }
            .map { URL(string: $0) }
            .filter { $0 != nil }
            .map { $0! }
            .map { try Data(contentsOf: $0) }
            .map { UIImage(data: $0) }
            .subscribe(onNext: { image in
                self.imageView.image = image
            })
            .disposed(by: disposeBag)
    }
</pre></code>
