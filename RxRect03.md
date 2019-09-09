# RxSwfitRect
RxSwfit Recture

3강 Subject
===========
## A. Subject
2강의 기존코드는 실시간으로 데이터를 구독 하진 않는다. 필요에 따라 데이터를 추가하고 방출 할 수 있게 하는 것이 subject 이다.
Observerable이면서 Observer인 녀석이 subject 이다.

## B. Subject의 종류
1. publish Subject : 빈 상태로 시작 새로운 값만 subscriber에 방출 / 시간에 민감한 데이터를 모델링 시 사용(경매 앱)
    * subscriber 가 dispose된 경우 새로운 subsciber에 이벤트 방출
    * subject가 completed, error이벤트 같이 완전종료 이벤트를 받으면 더 이상 next 이벤트 호출 하지 않음

    <img src = "https://github.com/fimuxd/RxSwift/blob/master/Lectures/03_Subjects/1.%20publishsubject.png?raw=truehttps://github.com/fimuxd/RxSwift/blob/master/Lectures/02_Observables/1.%20marble.png?raw=true" height = 100>

    * 첫번쨰 줄 : subjecgt 배포
    * 두번째 줄, 세번쨰 줄 : subscriber
    * 위로 향하는 화살표는 구독
    * 아래로 향하는 화살표는 이벤트 방출

``` swift
import UIKit
import RxSwift


class ViewController: UIViewController {

    public func example(of description: String,
                        action: () -> Void)
    {
        action()
    }
    
    override func viewDidLoad() {
        
        example(of: "PublishSubject") {
            
            let subject = PublishSubject<String>()
            
            subject.onNext("Is anyone listening?")
            
            let subcriptionOne = subject.subscribe(onNext : {(string) in
                print("subjectOne :  \(string)")
            })
            
            subject.onNext("1")
            subject.on(.next("2"))
            /*
             subjectOne :  1
             subjectOne :  2
             */
            
            let subcriptionTwo = subject.subscribe(onNext : {(string) in
                print("subjectTwo :  \(string)")
            })
            
            subject.onNext("3")
            /*
             subjectOne :  3
             subjectTwo :  3	//two 에 대해서도 출력
             */
            
            subcriptionOne.dispose()
            
            subject.onNext("4")
            /*
             subjectTwo :  4
             */
            
            subject.onCompleted()
            
            subject.onNext("5")
            
            subcriptionTwo.dispose()
            
            let disposeBag = DisposeBag()
            
            subject.subscribe{
                print("new Subject : \($0)")
            }.disposed(by: disposeBag)
            /*
             new Subject : completed
             */
            
            subject.onNext("?")
        }
        
    }
}

```

2. behavior Subject : 초기값을 가지고 시작, 새로운 값 또는 초기기값을 subscriber에 방출 / 프로필 화면 앱이 새로운 데이터를 가져오는 동안 미리 채워 넣을 수 있다.

    <img src = "https://github.com/fimuxd/RxSwift/blob/master/Lectures/03_Subjects/2.%20behaviorsubject.png?raw=true?raw=true" height = 100>

``` swift
import UIKit
import RxSwift



class ViewController: UIViewController {

    public func example(of description: String,
                        action: () -> Void)
    {
        action()
    }
    
    override func viewDidLoad() {
        
        example(of: "BehaviorSubject") {
            
            let subject = BehaviorSubject<String>(value: "Is anyone listening?")

            let subcriptionOne = subject.subscribe(onNext : {(string) in
                print("subjectOne :  \(string)")
            })
            
            subject.onNext("1")
            subject.on(.next("2"))
            
            let subcriptionTwo = subject.subscribe(onNext : {(string) in
                print("subjectTwo :  \(string)")
            })

            subject.onNext("3")

            subcriptionOne.dispose()
            
            subject.onNext("4")

            subject.onCompleted()
            
            subject.onNext("5")
            
            subcriptionTwo.dispose()
            
            let disposeBag = DisposeBag()
            
            subject.subscribe{
                print("new Subject : \($0)")
            }.disposed(by: disposeBag)

            subject.onNext("?")
            
            /*
            subjectOne :  Is anyone listening?
            subjectOne :  1
            subjectOne :  2
            subjectTwo :  2
            subjectOne :  3
            subjectTwo :  3
            subjectTwo :  4
            new Subject : completed
            */
        }
        
    }
}
```
3. Replay Subject : 버퍼를 두고 초기화, 버퍼 사이즈 만큼 값들을 유지하면서 새로운 subscriber에 방출 / 버퍼 사이즈를 넘겨 데이터를 방출하게 되면 최근 데이터만 노출하고 에러 방출 / 최근검색어(제한 몇개)에 사용가능

    <img src = "https://github.com/fimuxd/RxSwift/blob/master/Lectures/03_Subjects/2.%20behaviorsubject.png?raw=true?raw=true" height = 100>
    
``` swift
import UIKit
import RxSwift

enum MyError : Error{
    case anError
}

class ViewController: UIViewController {

    public func example(of description: String,
                        action: () -> Void)
    {
        action()
    }
    
    override func viewDidLoad() {
        
        example(of: "ReplaySubject") {
        
            let subject = ReplaySubject<String>.create(bufferSize: 2)
            let disposeBag = DisposeBag()
            
            subject.onNext("1")
            subject.onNext("2")
            subject.onNext("3")
            
            subject.subscribe{
                print("subject1 : \($0)")
            }.disposed(by: disposeBag)
            
            subject.onError(MyError.anError)
            
            subject.subscribe{
                print("subject2 : \($0)")
                }.disposed(by: disposeBag)
            
            /*
             subject1 : next(2)
             subject1 : next(3)
             subject1 : error(anError)
             subject2 : next(2)
             subject2 : next(3)
             subject2 : error(anError)
             */
            
        }
    }
}
```

4. Variable : Behavior subject를 래핑하고 최신 값만 subscriber에 방출

   * Observable의 현재의 값을 상태로 보유 한다.
   * value를 가지기 위해서는 일반적인 observable, subject 와는 다른 방법으로 추가
   * next, completed, error 추가 불가
   * 에러가 발생하지 않을 것임을 보증 / variable 할당해제 시 자동 완료


```swift
import UIKit
import RxSwift

class ViewController: UIViewController {

    public func example(of description: String,
                        action: () -> Void)
    {
        action()
    }
    
    override func viewDidLoad() {
        
        example(of: "Variable") {
            
            let variable = Variable("Initial Value")
            let disposeBag = DisposeBag()
            
            variable.value = "New Initial Value"
            
            variable.asObservable().subscribe{
                print("variable 1 : \($0)")
            }.disposed(by: disposeBag)
            
            variable.value = "1"
            
            
            variable.asObservable().subscribe{
                print("variable 2 : \($0)")
            }.disposed(by: disposeBag)
            
            variable.value = "2"
            
            /*
             variable 1 : next(New Initial Value)
             variable 1 : next(1)
             variable 2 : next(1)
             variable 1 : next(2)
             variable 2 : next(2)
             */
            
        }
    }
}
```
