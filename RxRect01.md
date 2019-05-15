# RxSwiftRect
RxSwiftRect Recture

1강 RxSwift
===========
1. Reactive X : Observable 스트림으로 비동기 프로그래밍 할 수 있도록 해주는 API
* * *
2. 주요 기능 : Observable, Operator, Subject, Single
* * *
3. Swift의 병렬 프로그래밍
* NSThread : 스레풀을 직접 관리 해야하는 기본 클래스
* OperationQueue : 스레드를 큐에 넣고 운영체제가 알아서 꺼내어 쓰도록 하는 클래스
* GCD : 운영 체제가 스레드 풀을 관리하는 C 기반 API, DispatchQueue 클래스를 이용
    * ConcurrencyQueue : 스레드 작업을 동시실행(global, main)
    * SerialQueue : 스레드 작업을 순서대로 실행
<pre><code>
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
</pre></code>
* * *
4. 다른 비동기 API는 없나? : PromiseKit, Bolts 등이 있지만, RXSwift의 코드가 훨씬 간결하며 다양한 기능을 Operator에서 제공하고 있다.
