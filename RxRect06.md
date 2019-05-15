# RxSwfitRect
RxSwfit Recture

6강 Next, Error, Complete
===========
1. Next, Error, Complete : subscribe의 이벤트 타입, 다른 오퍼레이터들은 Observable를 리턴하지만, Subscribe는 Dispose 타입을 리턴한다. Subscribe를 하면 dispose에 대한 처리를 해줘야한다.
* Next : 데이터를 subscribe함수를 통해 넘겨주는 것
* Error : 에러, 에러시 Observable 종료
* Complete : 완료, 모든 데이터가 컴플릿 되면 Observable 종료
<pre><code>
    @IBAction func exJust1() {
        Observable.just("Hello World")
            .subscribe{event in
                switch event{
                case .next(let str) :
                    print(str)
                case .error(let error) :
                    print(error)
                case .completed:
                    print("completed")
                }
        }
	.disposed(by : disposeBag)
    }
</pre></code>
* * *
2. Subscribe 에 대한 후처리가 필요없을 때
<pre><code>
    @IBAction func exJust1() {
        Observable.just("Hello World")
            .subscribe()
		.disposed(by : disposeBag)
    }
</pre></code>
* * *
3. Error : Single은 데이터 하나만 허용하니까 에러
<pre><code>
    @IBAction func exJust2() {
        Observable.from(["RxSwift", "In", "4", "Hours"])
            .single()
            .subscribe{event in
                switch event{
                case .next(let str) :
                    print(str)
                case .error(let error) :
                    print(error)
                case .completed:
                    print("completed")
                }
            }
            .disposed(by : disposeBag)
    }
    //Sequence contains more than one element.
</pre></code>
* * *
