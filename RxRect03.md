# RxSwfitRect
RxSwfit Recture

3강 Disposable, DisposeBag
===========
1. Disposable : 메모리에서 Observable를 해제하는 함수, subscribe의 리턴 타입이다.

2. DisposeBag : 여러 Dispose를 가방에 담을 수 있는 함수

3. 아래 취소 함수 처럼 한방에 처리 못하는 기존의 코드는 OperationQueue를 직접 구현 하거나, 캔슬플래그를 타이머로 계속 돌려 체크하는 방법이 있다.
<pre><code>
    var disposable : Disposable?
    
    @IBAction func onLoadImage(_ sender: Any) {
        imageView.image = nil

        disposable = rxswiftLoadImage(from: LARGER_IMAGE_URL)
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
