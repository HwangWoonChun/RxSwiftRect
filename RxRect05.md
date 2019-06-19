# RxSwfitRect
RxSwfit Recture

4강 Transforming Operator
===========
## A. 시작하기

1. swift의 map, flatMap
map 은 배열이외에도 연산(함수를) 사상 할 수 있는 기능 이에 비해 flatMap 은 각 원소를 unwrapp 하고 하나의 배열로 만드는 기능

``` swift
        let words = ["123", "456.7", "eighty nine", "10", "100"]
        
        let numbers = words.map{ Int($0) }
        /*
         [Optional(123), nil, nil, Optional(10), Optional(100)]
         */
        let numbersWithMap = words.map{ Int($0) }.filter{ $0 != nil }.map{ $0! }
        /*
         [123, 10, 100]
         */
        let numbersFlatMap = words.flatMap{ Int($0) }
        /*
         [123, 10, 100]
         */
```

## B. 변환연산자의 요소들
1. toArray
    * 독립적인 요소들을 배열로 넣는 가장 간단한 방법, onNext이벤트를 통해 방출 하는 것이 아니라 onSuccess 이벤트를 이용
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
        
        example(of: "toArray") {
            
            let disposeBag = DisposeBag()
            
            Observable.of("1","2")
                .toArray()
                .subscribe(onSuccess : {
                    print($0)
                })
                .disposed(by: disposeBag)
        }
        
        /*
         [“1”,”2”]
         */
        
    }
}
```
2. map
    * 각각의 요소들을 연산에 사상할 수 있는 방법
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
        
        example(of: "map") {
            let disposeBag = DisposeBag()
            
            // 1
            let formatter = NumberFormatter()
            formatter.numberStyle = .spellOut
            
            // 2
            Observable<NSNumber>.of(123, 4, 56)
                // 3
                .map {
                    formatter.string(from: $0) ?? ""
                }
                .subscribe(onNext: {
                    print($0)
                })
                .disposed(by: disposeBag)
        }
        
        /*
         one hundred twenty-three
         four
         fifty-six
         */
        
    }
}
```
3. enumerated
    * Map with enumerated
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
        
        example(of: "enumerated and map") {
            let disposeBag = DisposeBag()
            
            // 1
            Observable.of(1, 2, 3, 4, 5, 6)
                // 2
                .enumerated()
                // 3 index가 2보다 크면, 2배 곱
                .map { index, interger in
                    index > 2 ? interger * 2 : interger
                }
                // 4
                .subscribe(onNext: {
                    print($0)
                })
                .disposed(by: disposeBag)
            
        }
        
        /*
         1 2 3 8 10 12
         */
        
    }
}
```

## C. 내부의 Observable 변환하기
1. flatMap
    * Observable에서 발행한 아이템을 다른 Observable 로 만듬
    <img src = "https://farm8.staticflickr.com/7567/26230104214_635e66ac0b_z.jpg" height = 50>
    
``` swift
import UIKit
import RxSwift

struct Student {
    var score: BehaviorSubject<Int>
}

class ViewController: UIViewController {
    
    public func example(of description: String,
                        action: () -> Void)
    {
        action()
    }
    
    override func viewDidLoad() {
        
        example(of: "flatMap") {
            let disposeBag = DisposeBag()
            
            // 1
            let ryan = Student(score: BehaviorSubject(value: 80))
            let charlotte = Student(score: BehaviorSubject(value: 90))
            
            // 2
            let student = PublishSubject<Student>()
            
            // 3
            student
                .flatMap{
                    $0.score
                }
                // 4
                .subscribe(onNext: {
                    print($0)
                })
                .disposed(by: disposeBag)
            
            // 5
            student.onNext(ryan)    // Printed: 80
            
            // 6
            ryan.score.onNext(85)   // Printed: 85
            
            // 7
            student.onNext(charlotte)   // Printed: 90
            
            // 8
            ryan.score.onNext(95)   // Printed: 95
            
            // 9
            charlotte.score.onNext(100) // Printed : 100
        }
    }
}
```
2. flatMapLatest
    * 가장 최신 값만을 유지하고 확인 하고 싶을때 사용 
``` swift
import UIKit
import RxSwift

struct Student {
    var score: BehaviorSubject<Int>
}

class ViewController: UIViewController {
    
    public func example(of description: String,
                        action: () -> Void)
    {
        action()
    }
    
    override func viewDidLoad() {
        
        example(of: "flatMap") {
            let disposeBag = DisposeBag()
            
            // 1
            let ryan = Student(score: BehaviorSubject(value: 80))
            let charlotte = Student(score: BehaviorSubject(value: 90))
            
            // 2
            let student = PublishSubject<Student>()
            
            // 3
            student
                .flatMapLatest{
                    $0.score
                }
                // 4
                .subscribe(onNext: {
                    print($0)
                })
                .disposed(by: disposeBag)
            
            // 5
            student.onNext(ryan)    // Printed: 80
            
            // 6
            ryan.score.onNext(85)   // Printed: 85
            
            // 7
            student.onNext(charlotte)   // Printed: 90
            
            // 8
            ryan.score.onNext(95)   // 이미 charlotte로 Observable이 전환되어 ryan은 이제 반영되지 않고 charlotte만 반영된다.
            
            // 9
            charlotte.score.onNext(100) // 100
            
            charlotte.score.onNext(101) // 101
            
            charlotte.score.onNext(102) // 102
            
            /*
             80
             85
             90
             100
             101
             102
             */
        }
    }
}
```

## D. 이벤트 관찰하기
1. materialize
    * 외부적으로 observable이 종료되는 것을 방지하기 위해 error 이벤트를 처리하고 싶을 떄 사용 할 수 있다.
    * Observable을 Observable의 이벤트로 변환하는 기능이다.
``` swift
import UIKit
import RxSwift

struct Student {
    var score: BehaviorSubject<Int>
}

class ViewController: UIViewController {
    
    public func example(of description: String,
                        action: () -> Void)
    {
        action()
    }
    
    override func viewDidLoad() {
        
        example(of: "materialize and dematerialize") {
            
            // 1
            enum MyError: Error {
                case anError
            }
            
            let disposeBag = DisposeBag()
            
            // 2
            let ryan = Student(score: BehaviorSubject(value: 80))
            let charlotte = Student(score: BehaviorSubject(value: 100))
            
            let student = BehaviorSubject(value: ryan)
            
            // 3
            let studentScore = student
                .flatMapLatest{
                    $0.score.materialize()
            }
            
            // 4
            studentScore
                .subscribe(onNext: {
                    print($0)
                })
                .disposed(by: disposeBag)
            
            // 5
            ryan.score.onNext(85)
            ryan.score.onError(MyError.anError)
            ryan.score.onNext(90)
            
            // 6
            student.onNext(charlotte)
            
            /* Prints:
             next(80)
             next(85)
             error(anError)
             next(100)  //에러는 여전히 studentscroe를 종료를 발생시키나 바깥은 student observable은 그대로 살려놓는다. 그래서 100 출력
             */
        }
    }
}
```
2. dematerialize
    * materialize는 event를 받지만 element를 받을 수 없다 그렇기 때문에 dematerialize를 통해 기존의 모양으로 돌려주는 역할 을 한다.
``` swift
import UIKit
import RxSwift

struct Student {
    var score: BehaviorSubject<Int>
}

class ViewController: UIViewController {
    
    public func example(of description: String,
                        action: () -> Void)
    {
        action()
    }
    
    override func viewDidLoad() {
        
        example(of: "materialize and dematerialize") {
            
            // 1
            enum MyError: Error {
                case anError
            }
            
            let disposeBag = DisposeBag()
            
            // 2
            let ryan = Student(score: BehaviorSubject(value: 80))
            let charlotte = Student(score: BehaviorSubject(value: 100))
            
            let student = BehaviorSubject(value: ryan)
            
            // 3
            let studentScore = student
                .flatMapLatest{
                    $0.score.materialize()
            }
            
            // 4
            studentScore
                .filter{
                    //에러가 발생되면 필터하고 프린트 그래서 100 출력이 된다.
                    guard $0.error == nil else{
                        print($0.error!)
                        return false
                    }
                    return true
                }
                .dematerialize()
                .subscribe(onNext: {
                    print($0)
                })
                .disposed(by: disposeBag)
            
            // 5
            ryan.score.onNext(85)
            ryan.score.onError(MyError.anError)
            ryan.score.onNext(90)
            
            // 6
            student.onNext(charlotte)
            
            /* Prints:
             80
             85
             anError
             100

             */
        }
    }
}
```
