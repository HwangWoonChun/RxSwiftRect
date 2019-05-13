# RxSwfitRect
RxSwfit Recture

5강 Scheduler
===========
1. Scheduler : 오퍼레이터에 멀티 스레딩을 구현하고자 할때 사용
* * *
2. Observer on : 다음 작업을 특정 스케쥴러를 변경
3. Subscribe on : Observable 에서 동작하는 함수의 스케쥴러 변경(on next)
<pre><code>
	let backgroundScheduler = SerialDispatchQueueScheduler(globalConcurrentQueueQOS: .Default)

	[1,2,3,4,5].toObservable()
		.subscribeOn(MainScheduler.instance) 	// 1. Observable이 동작하는 스케쥴러 메인스레드
		.doOnNext {
			UIApplication.sharedApplication().networkActivityIndicatorVisible = true
			return $0
		} 		// 2. 1에서 메인으로 지정했기때문에 메인스레드로
		.observeOn(backgroundScheduler) // 3. 다음 동작은 백그라운드로
		.flatMapLatest {
			HTTPBinDefaultAPI.sharedAPI.get($0)
		}		// 4. 백그라운드
		.observeOn(MainScheduler.instance) 		// 5. subscribe를 메인스레드로
		.subscribe {
			UIApplication.sharedApplication().networkActivityIndicatorVisible = false
			print($0)
		}		// 6
</pre></code>
