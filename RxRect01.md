# RxSwiftRect
RxSwiftRect Recture

1강 동기, 비동기 프로그래밍
===========
1. blocking IO Model : System Call이 끝날떄까지 프로그램은 Wait Queue 에 들어가고 콜이 끝나면 리턴 되는 방식
2. Non bloacking IO Model : System Call이 끝날떄까지 기다리지 않고 바로 리턴 해주는 방식
* 바로 리턴되냐 아니냐가 관심사
* * *
1. Sync Programming : 호출하는 함수가 호출되는 함수의 작업 완료를 신경 쓰는 방식
2. Async Programming : 호출하는 함수가 호출되는 함수의 작업 완료를 쓰지 않는 방식
* 호출함수가 리턴값을 신경 쓰는지가 관심사
* * *
1. Swift의 병렬 처리프로그래밍
* NSThread : 스레드 풀을 직접 관리해야하는 기본 클래스
* NSOperartion Queue : 개발자가 큐에 작업을 넣고 운영체제가 알아서 꺼내어 별도의 스레드에 넣어 동작하는 클랫흐
* GCD : C API 기반, 자동으로 스레드 풀을 관리 해주는 클래스, DispatchQueue 를 사용, ConcurrencyQueue(동시,실행-global,main) / SerialQueue(하나의 작업이 끝나야 다음 스레드 실행) 
<pre><code>
@IBAction func onLoadAsync(_ sender: Any) {
    DispatchQueue.global().async {
        guard let url = URL(string: loadingImageUrl) else { return }
        guard let data = try? Data(contentsOf: url) else { return }
      
        let image = UIImage(data: data)
        DispatchQueue.main.async {
            self.imageView.image = image
        }
    }
}
</pre></code>
