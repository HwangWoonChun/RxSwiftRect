# RxSwiftRect
RxSwiftRect Recture

1강 RxSwift
===========
1. RxSwift : Observarble 스트림으로 비동기프로그래밍을 지원하는 API
2. Why Rx : 코드가 새로운 데이터에 반응하고 순차적으로 분리된 방식으로 처리 하기 때문에 비동기 프로그래밍이 간소화 된다.
* * *
3. 기존 비동기 코드를 지원 하는 Swift
* Notification Center
* Delegate
* GCD : 운영체제가 스레드 풀을 관리 하는 C 기반의 API
  - ConcurrencyQueue(Global, Main) : 스레드 작업을 동시 실행(비동기)
  - SerialQueue : 스레드 작업을 순서대로 실생
* Closure
* NSThread : 스레드 풀을 직접 관리하는 API
* OperationQueue : 스레드를 큐에 넣고 운영체제가 알아서 꺼내도록 하는 API
``` swift
    //동기
    @IBAction func onLoadSync(_ sender: Any) {
        
        guard let url = URL(string: imageUrl) else { return }
        guard let data = try? Data(contentsOf: url) else { return }
        
        let image = loadImage(from: IMAGE_URL)
        imageView.image = image
    }
    
    //비동기
    private func loadImage(from imageUrl: String) {
        DispatchQueue.global().async {
            guard let url = URL(string: imageUrl) else { return }
            guard let data = try? Data(contentsOf: url) else { return }
            
            let image = UIImage(data: data)
        
            DispatchQueue.main.async {
                self.imageView.image = image
            }
        }
    }
```
* * *
4. RxSwift의 기초
  * Observable : Observable<T> 는 T형태의 데이터를 전달 할 수 있는 일련의 이벤트를 비동기적으로 생성
    * Observable 프로토콜 준수한다는 의미는 next, completed, error 3가지 이벤트를 수신 한다는 의미
    * 유한한 이벤트와 무한한 이벤트를 관찰 할 수 있는 Observable이 존재 한다.
  * Operator : 비동기 작업을 추상화 하는 메소드들의 모임
  * Schedulers : DispatchQueue와 동일하며 메인, 백그라운드 스레드 처리
* * *
5. App Architecture : MVVM은 데이터 바인딩(UI와 데이터 연결)을 제공하는 플렛폼에서 이벤트 기반의 소프트웨어용으로 개발되어 Rx와 잘 어울린다.
* * *
6. RxCocoa : UIKit, Cocoa 프레임워크 기반으로 UI/UX 개발 지원
