# RxSwfitRect
RxSwfit Recture

7강 Scheduler
===========
1. Concurrency 프로그램을 짜기위한 기능, 종류는 observeOn, subscribeOn
* * *
2. observeOn : 다음 스트림들을 지정된 스케쥴러로 동작하게 한다.
<pre><code>
    @IBAction func exMap3() {
        Observable.just("")
            .observeOn(ConcurrentDispatchQueueScheduler(qos: .default)) 	//qos 디폴트는 백그라운드 스레드
            .map { $0.replacingOccurrences(of: "x", with: "/") }
            .map { "https://picsum.photos/\($0)/?random" }
            .map { URL(string: $0) }
            .filter { $0 != nil }
            .map { $0! }
            .map { try Data(contentsOf: $0) }
            .map { UIImage(data: $0) }
            .observeOn(MainScheduler.instance)
            .subscribe(onNext: { image in
                self.imageView.image = image
            })
            .disposed(by: disposeBag)
    }

</pre></code>
* * *
3. subscribeOn : subscribe되는 부분을 지정된 스케쥴러를 사용, 콜 위치는 상관 없다.
<pre><code>
    @IBAction func exMap3() {
        Observable.just("")
            .map { $0.replacingOccurrences(of: "x", with: "/") }
            .map { "https://picsum.photos/\($0)/?random" }
            .map { URL(string: $0) }
            .filter { $0 != nil }
            .map { $0! }
            .map { try Data(contentsOf: $0) }
            .map { UIImage(data: $0) }
            .subscribeOn(MainScheduler.instance)
            .subscribe(onNext: { image in
                self.imageView.image = image
            })
            .disposed(by: disposeBag)
    }
</pre></code>
* * *
4. subsribe, Do : 외부 변수(self 등)가 접근 하는 부분은 subscribe, do에 사용해야한다.
<pre><code>
    @IBAction func exMap3() {
        Observable.just("")
            .map { $0.replacingOccurrences(of: "x", with: "/") }
            .map { "https://picsum.photos/\($0)/?random" }
            .map { URL(string: $0) }
            .filter { $0 != nil }
            .map { $0! }
            .map { try Data(contentsOf: $0) }
            .map { UIImage(data: $0) }
            .subscribeOn(MainScheduler.instance)
            .do(onNext : {image in
                print(image?.size)
            })
            .subscribe(onNext: { image in
                self.imageView.image = image
            })
            .disposed(by: disposeBag)
    }
</pre></code>


