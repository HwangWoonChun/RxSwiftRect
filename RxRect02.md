# RxSwfitRect
RxSwfit Recture

2강 Observable
===========
1. Observable : 하나의 일련의 사건을 말한다. 
        * 기본 흐름 : 데이터를 배출할떄 마다 전달받고(onNext), 옵저버와 연결되어(subscribe) 결과를 받는다.

<pre><code>
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
