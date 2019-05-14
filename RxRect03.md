# RxSwfitRect
RxSwfit Recture

3강 Disposable, DisposeBag
===========
1. Disposable : 메모리에서 Observable를 해제하는 함수, subscribe의 리턴 타입이다.
* * *
2. DisposeBag : 여러 Dispose를 가방에 담을 수 있는 함수
* * *
3. 네트워크 취소를 한방에 처리 못하는 기존의 코드는 OperationQueue를 직접 구현 하거나, 캔슬플래그를 타이머로 계속 돌려 체크하는 방법이 있다.
* * *
4. Insert를 통해 DisposeBag에 담기
<pre><code>
    var disposable : Disposable?
    
    @IBAction func onLoadImage(_ sender: Any) {
        imageView.image = nil

        let disposable = rxswiftLoadImage(from: LARGER_IMAGE_URL)
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
        disposeBag.insert(disposable)
    }

    @IBAction func onCancel(_ sender: Any) {
        disposable?.dispose()
    }

    // MARK: - RxSwift
    func rxswiftLoadImage(from imageUrl: String) -> Observable<UIImage?> {
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
5. Disposed 함수 사용
<pre><code>
    @IBAction func onLoadImage(_ sender: Any) {
        imageView.image = nil

        rxswiftLoadImage(from: LARGER_IMAGE_URL)
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
        .disposed(by : disposeBag)
    }
</pre></code>
