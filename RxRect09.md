# RxSwfitRect
RxSwfit Recture

9강 Driver
===========
1. driver : RxCocoa에서 제공하는 메인스레드 스케쥴러 작업
<pre><code>
//1. ObserveOn
</pre></code>
let idEvent = idTextField.rx.text.orEmpty.asObservable()
    .observeOn(MainScheduler.instance)
    .share(replay: 1, scope: .whileConnected)
let passEvent = passTextField.rx.text.orEmpty.asObservable()
    .observeOn(MainScheduler.instance)
    .share(replay: 1, scope: .whileConnected)
 
let buttonEnableEvent = Observable.combineLatest(idEvent, passEvent)
    .map { !$0.0.isEmpty && !$0.1.isEmpty }
 
buttonEnableEvent
    .observeOn(MainScheduler.instance)
    .subscribe(onNext: { button.isEnabled = $0 })
    .disposed(by: disposeBag)

<pre><code>
//2. driver
let idEvent = idTextField.rx.text.orEmpty.asDriver()
let passEvent = passTextField.rx.text.orEmpty.asDriver()
        
let buttonEnableEvent = Driver.combineLatest(idEvent, passEvent)
    .map { !$0.0.isEmpty && !$0.1.isEmpty }
        
buttonEnableEvent
    .drive(button.rx.isEnabled)
    .disposed(by: disposeBag)

</pre></code>

2. Unfinished observable : subscribe 내에 클래스 변수가 접근하게되면 [weak self]로 해소해 준다. 하지만 이 마저 귀찮다면 화면에서 pop 될때 disposeBag을 날려 주면 해소 된다.
