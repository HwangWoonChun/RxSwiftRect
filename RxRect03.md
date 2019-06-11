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
```
