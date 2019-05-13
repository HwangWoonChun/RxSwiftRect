# RxSwfitRect
RxSwfit Recture

3강 Observerable
===========
1. Observerable : 하나의 연속된 사건을 말한다.
* Reactive X 의 표현 : Observable은 리턴 값을 배출하거나 observable의 메서드 호출을 통해 옵저버에게 알림을 보낸다.
* * *
2. Observerable 생성
1) 비동기 메소드 정의
2) Observable 비동기 메소드 호출 정의
3) Subsribe를 통해 겍체 연결
4) Operator를 통해 리턴 값 연산 시작
* * *
3. Observerable 라이프 사이클
* Subscribe : Observer와 Observable 을 연결
* onNext : 새로운 리턴 값이 생성 될때 마다 onNext 호출
* onCompleted : 오류 발생시 호출, 중간에 발생시 더 이상 onNext 호출 하지 않는다.
* onError : 오류를 발생 하지 않았다면 모든 값을 리턴 받았을 때 호출 된다.
* dispose : Subsribe 취소
* * *
<pre><code>
    func rxswiftLoadImage(from imageUrl: String) -> Observable<UIImage?> {
        return Observable.create { seal in
            self.asyncLoadImage(from: imageUrl) { image in
                seal.onNext(image)
                seal.onCompleted()
            }
            return Disposables.create()
        }
    }
    
    @IBAction func onLoadImage(_ sender: Any) {
        self.imageView?.image = nil
        
        self.disposable = rxswiftLoadImage(from: loadingImageUrl!)
            .subscribe({ result in
                switch result {
                case let .next(image):
                    self.imageView?.image = image
                    
                case let .error(err):
                    print(err.localizedDescription)
                    
                case .completed:
                    break
                }
            })
    }
</pre></code>
* * *
4. disposeBag : Bag에 취소 할 애들을 담는 함수
<pre></code>
var disposeBag : DisposeBag = DisposeBag()

    func rxswiftLoadImage(from imageUrl: String) -> Observable<UIImage?> {
        return Observable.create { seal in
            self.asyncLoadImage(from: imageUrl) { image in
                seal.onNext(image)
                seal.onCompleted()
            }
            return Disposables.create()
        }
    }
    
    @IBAction func onLoadImage(_ sender: Any) {
        self.imageView?.image = nil
        
        rxswiftLoadImage(from: loadingImageUrl!)
            .subscribe({ result in
                switch result {
                case let .next(image):
                    self.imageView?.image = image
                    
                case let .error(err):
                    print(err.localizedDescription)
                    
                case .completed:
                    break
                }
            })
            .disposed(by:disposeBag)
    }
    
    @IBAction func onCancel(){
        disposeBag = DisposeBag() //초기화 
    }
        
</pre></code>
