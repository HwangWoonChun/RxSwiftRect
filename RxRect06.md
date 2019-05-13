# RxSwfitRect
RxSwfit Recture

6강 Memory Leak
===========
1. self.imageView 는 외부에서 블럭안에 접근했기때문에 메모리 릭이 발생할 수 있다. 클로져의 경우 [weak self] 처럼 캡쳐리스트를 사용 하지만 RX 경우 disposeBag = DisposeBag()으로 초기화 하면 자동으로 날라간다.
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
            .subscribeOn(ConcurrentDispatchQueueScheduler(qos : .default))
            .observeOn(MainScheduler.instance)  //쓰이기 바로직전에 메인 스레드 스케쥴러
            .subscribe(onNext: { image in
            		self.imageView.image = image
            })
            .disposed(by: disposeBag)
    }
</pre></code>
