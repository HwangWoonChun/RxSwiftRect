# RxSwfitRect
RxSwfit Recture

6강 Combining Operator
===========
## A. 시작하기

* 다양한 방법으로 sequence를 모으고 sequence 내의 데이터를 병합하는 방법

## B. 앞에 붙이기 - append

1. startWith(_:)

	* observer가 초기값을 받을 수 있도록 해줌

	``` swift
        example(of: "startWith") {
            // 1
            let numbers = Observable.of(2, 3, 4)
            
            // 2
            let observable = numbers.startWith(1)
            observable.subscribe(onNext: {
                print($0)
            })
            
            /* Prints:
             1
             2
             3
             4
             */
        }
	```

2. observable.concat(_:)
	* 두개의 Sequence를 하나로 묶음
	* 두 Observable의 요소들의 타입이 같아야 한다.

	``` swift
        example(of: "Observable.concat") {
            // 1
            let first = Observable.of(1, 2, 3)
            let second = Observable.of(4, 5, 6)
            
            // 2
            let observable = Observable.concat([first, second])
            
            observable.subscribe(onNext: {
                print($0)
            })
            
            /* Prints:
             1
             2
             3
             4
             5
             6
             */
        }
	```

3. concatMap(_:)

	* flatMap은 Observable에서 발행한 아이템을 다른 Observable 로 만드는 것이다.

	* concatMap은 각각의 sequence가 다음 sequence가 구독되기전에 합쳐지는 것을 보증 하는 것이다.

	``` swift
        example(of: "concatMap") {
            // 1. 두개의 시퀀스 준비
            let sequences = ["Germany": Observable.of("Berlin", "Münich", "Frankfurt"),
                             "Spain": Observable.of("Madrid", "Barcelona", "Valencia")]
            
            // 2. 도시명에 맵핑 되는 국가명을 내보냄
            let observable = Observable.of("Germany", "Spain")
                .concatMap({ country in
                    sequences[country] ?? .empty() })
            
            // 3
            _ = observable.subscribe(onNext: {
                print($0)
            })
            /*
             Berlin
             Münich
             Frankfurt
             Madrid
             Barcelona
             Valencia
             */
        }
	```

## C. 합치기 - Combining

1. merge()

	* 합치기 가장 쉬운방법
	
	``` swift
        example(of: "merge") {
            // 1
            let left = PublishSubject<String>()
            let right = PublishSubject<String>()
            
            // 2
            let source = Observable.of(left.asObservable(), right.asObservable())
            
            // 3
            let observable = source.merge()
            let disposable = observable.subscribe(onNext: {
                print($0)
            })
            
            // 4
            var leftValues = ["Berlin", "Münich", "Frankfurt"]
            var rightValues = ["Madrid", "Barcelona", "Valencia"]
            
            repeat {
                if arc4random_uniform(2) == 0 {
                    if !leftValues.isEmpty {
                        left.onNext("Left: " + leftValues.removeFirst())
                    }
                } else if !rightValues.isEmpty {
                    right.onNext("Right :" + rightValues.removeFirst())
                }
            } while !leftValues.isEmpty || !rightValues.isEmpty
            
            // 5
            disposable.dispose()
        }
        /*
         Left: Berlin
         Right :Madrid
         Left: Münich
         Right :Barcelona
         Left: Frankfurt
         Right :Valencia
         */
    }
	```
2. merge(maxConcurrent:)
	* 합칠 수 있는 sequence의 수를 제한하기 위해 사용

## D. 요소 결합하기

1. combineLatest(::resultSelector:)

	* 결합된 Sequence들은 값을 방출 할때마다 제공한 클로저를 호출하며 각각 내부 Sequence들의 최종값을 받는다.
	
	``` swift
        example(of: "combineLast") {
            let left = PublishSubject<String>()
            let right = PublishSubject<String>()
            
            // 1
            let observable = Observable.combineLatest(left, right, resultSelector: { lastLeft, lastRight in
                "\(lastLeft) \(lastRight)"
            })
            
            let disposable = observable.subscribe(onNext: {
                print($0)
            })
            
            // 2
            print("> Sending a value to Left")
            left.onNext("Hello,")
            print("> Sending a value to Right")
            right.onNext("world")
            print("> Sending another value to Right")
            right.onNext("RxSwift")
            print("> Sending another value to Left")
            left.onNext("Have a good day,")
            
            // 3
            disposable.dispose()
            
            /* Prints:
             > Sending a value to Left
             > Sending a value to Right
             Hello, world
             > Sending another value to Right
             Hello, RxSwift
             > Sending another value to Left
             Have a good day, RxSwift
             */
        }
	```
	``` swift
        example(of: "combine user choice and value") {
            let choice:Observable<DateFormatter.Style> = Observable.of(.short, .long)
            let dates = Observable.of(Date())
            
            let observable = Observable.combineLatest(choice, dates, resultSelector: { (format, when) -> String in
                let formatter = DateFormatter()
                formatter.dateStyle = format
                return formatter.string(from: when)
            })
            
            observable.subscribe(onNext: { print($0) })
        }
	```
	``` swift
	//array 내 최종 값을 결합할 수 있다.
	let observable = Observable.combineLatest([left, right]) { strings in
	strings.joined(separator: " ")
	}
	```

3. zip
	* 일련의 Observable이 새 값을 각자 방출 할 때까지 기다리다가, 둘 중 하나의 Observable이라도 완료되면 zip이 완료 된다.

	* 더 긴 Observable이 남아있더라도 기다리지 않는다.
	
	* indexed sequencing : sequence에 따라 단계별로 작동하는 방법을 가르킨다.
	
	``` swift
        example(of: "zip") {
            // 1
            enum Weatehr {
                case cloudy
                case sunny
            }
            
            let left:Observable<Weatehr> = Observable.of(.sunny, .cloudy, .cloudy, .sunny)
            let right = Observable.of("Lisbon", "Copenhagen", "London", "Madrid", "Vienna")
            
            // 2
            let observable = Observable.zip(left, right, resultSelector: { (weather, city) in
                return "It's \(weather) in \(city)"
            })
            
            observable.subscribe(onNext: {
                print($0)
            })
            
            /* Prints:
             It's sunny in Lisbon
             It's cloudy in Copenhagen
             It's cloudy in London
             It's sunny in Madrid
             */
	     //Vienna는 출력되지 않는다.
        }
	```

## E. Trigger

* 여러개의 Observable을 한번에 받을 수 있는 방아쇠 역할을 해줌

1. withLatestFrom(_:)
	* 새 이벤트 추가되기 전에 최신 값을 출력
	``` swift
	example(of: "withLatestFrom") {
            // 1
            let button = PublishSubject<Void>()
            let textField = PublishSubject<String>()
            
            // 2
            let observable = button.withLatestFrom(textField)
            _ = observable.subscribe(onNext: { print($0) })
            
            // 3
            textField.onNext("Par")
            textField.onNext("Pari")
            textField.onNext("Paris")
            button.onNext(())
            button.onNext(())
            
            /*
             Paris
             Paris
             */
        }
	```
2. sample(_:)
	* 새 이벤트 추가되기 전에 최신 값을 한번만 출력
	``` swift
        example(of: "withLatestFrom") {
            // 1
            let button = PublishSubject<Void>()
            let textField = PublishSubject<String>()
            
            // 2
            let observable = textField.sample(button)
            _ = observable
                .subscribe(onNext: { print($0) })
            
            // 3
            textField.onNext("Par")
            textField.onNext("Pari")
            textField.onNext("Paris")
            button.onNext(())
            button.onNext(())
            
            /*
             Paris
             */
        }
	```
## F. Switches
1. amb(_:)
	* 두 가지 sequence의 이벤트 중 어떤 것을 구독할지 선택한다. 두개 중 어떤 것이든 요소를 모두 방출 하는 것을 기다리다가 하나를 방출 하기 시작하면 나머지는 구독을 중단한다.  

	``` swift
        example(of: "amb") {
            let left = PublishSubject<String>()
            let right = PublishSubject<String>()
            
            // 1
            let observable = left.amb(right)
            let disposable = observable.subscribe(onNext: { value in
                print(value)
            })
            
            // 2
            left.onNext("Lisbon")
            right.onNext("Copenhagen")
            left.onNext("London")
            left.onNext("Madrid")
            right.onNext("Vienna")
            
            disposable.dispose()
            
            /*
             Lisbon
             London
             Madrid
             */
        }
	```
2. switchLatest()
	* 두 가지 sequence의 이벤트 중 어떤 것을 구독할지 선택한다. 두개 중 어떤 것이든 요소를 모두 방출 하는 것을 기다리다가 최신 값을 구독한다.
	``` swift
	example(of: "switchLatest") {
            // 1
            let one = PublishSubject<String>()
            let two = PublishSubject<String>()
            let three = PublishSubject<String>()
            
            let source = PublishSubject<Observable<String>>()
            
            // 2
            let observable = source.switchLatest()
            let disposable = observable.subscribe(onNext: { print($0) })
            
            // 3
            source.onNext(one)
            one.onNext("Some text from sequence one")
            two.onNext("Some text from sequence two")
            
            source.onNext(two)
            two.onNext("More text from sequence two")
            one.onNext("and also from sequence one")
            
            source.onNext(three)
            two.onNext("Why don't you see me?")
            one.onNext("I'm alone, help me")
            three.onNext("Hey it's three. I win")
            
            source.onNext(one)
            one.onNext("Nope. It's me, one!")
            
            disposable.dispose()
            
            /* Prints:
             Some text from sequence one
             More text from sequence two
             Hey it's three. I win
             Nope. It's me, one!
             */
        }
	```
## G. Sequence 내 요소들 간의 결합
1. reduce(::)
	* 스위프트의 표준라이브러리인 reduce는 초기값을 정하고 배열을 조합하여 새로운 하나의 데이터를 생성하는 것이다.
	* 초기값부터 시작하여 값이 방출 할때마다 그 값을 가공한다.
	``` swift
        example(of: "reduce") {
            let source = Observable.of(1, 3, 5, 7, 9)
            
            // 1
            let observable = source.reduce(0, accumulator: +)
            observable.subscribe(onNext: { print($0) } )
            
            // 주석 1은 다음과 같은 의미다.
            // 2
            let observable2 = source.reduce(0, accumulator: { summary, newValue in
                return summary + newValue
            })
            observable2.subscribe(onNext: { print($0) })
            
            /*
             25
             25
             */
            
        }
	```
2. scan(_:accumulator)
	* reduce와 동일 하게 동작하나 리턴 값이 Observable이다.
	* accumlator의 과정을 모두 출력 할 수 있다.
	``` swift
	example(of: "scan") {
            let source = Observable.of(1, 3, 5, 7, 9)
            
            let observable = source.scan(0, accumulator: +)
            observable.subscribe(onNext: { print($0) })
            
            /* Prints:
             1
             4
             9
             16
             25
             */
        }
	```
	
	
