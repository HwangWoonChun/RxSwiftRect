# RxSwfitRect
RxSwfit Recture

4강 Filtergint Operator
===========
## A. Ignoring Operator

1. .ignoreElements() : .next 이벤트를 무시하고, completed, error 이벤트만 허용 한다.

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
        
        example(of: "igoring elements") {
            
            let subject = PublishSubject<String>()
            let disposeBag = DisposeBag()
            
            subject
                .ignoreElements()
                .subscribe{(element) in
                    print(element)
                }
            .disposed(by:disposeBag)
            
            subject.onNext("X")
            subject.onCompleted()
            
        }
        
        /*
         completed
         */
        
    }
    

}
```

2. .elementAt() : Observable에서 방출된 n번째 요소만 처리

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
        
        example(of: "element at") {
            
            let subject = PublishSubject<String>()
            let disposeBag = DisposeBag()
            
            subject
                .elementAt(1)
                .subscribe{(element) in
                    print(element)
                }
            .disposed(by:disposeBag)
            
            subject.onNext("1")
            subject.onNext("2")
            subject.onNext("3")
            subject.onCompleted()
            
        }
        
        /*
         next(2)
         completed
         */
        
    }
}
```
3. .filter() : 요구사항이 참인 경우 필터링하여 방출

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
        
        example(of: "filter") {
            
            let disposeBag = DisposeBag()
            
            Observable.of(1,2,3,4,5,6,7)
                .filter({ (int) -> Bool in
                    int % 2 == 0
                })
                .subscribe(onNext : {
                    print($0)
                })
                .disposed(by: disposeBag)
        }
        
        /*
         2
         4
         6
         */
        
    }
}
```
## B. Skip Operator
1. .skip(n) : 첫번째 부터 n 번까지의 요소를 skip
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
        
        example(of: "skip") {
            
            let disposeBag = DisposeBag()
            
            Observable.of(1,2,3,4,5,6,7)
                .skip(3)
                .subscribe(onNext : {
                    print($0)
                })
                .disposed(by: disposeBag)
        }
        
        /*
         4
         5
         6
         7
         */
        
    }
}

```
2. .skipWhile(조건) : 조건이 참인 경우 skip 거짓인 경우 부터 허용
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
        
        example(of: "skip while”) {
            
            let disposeBag = DisposeBag()
            
            Observable.of(2,4,6,8,7,1,2,3,4,5)
                .skipWhile({ (int) -> Bool in
                    int % 2 == 0
                })
                .subscribe(onNext : {
                    print($0)
                })
                .disposed(by: disposeBag)
        }
        
        /*
         7
         1
         2
         3
         4
         5
         */
        
    }
}
```
3. .skipUntil(observable) : 다른 Observable이 불린 경우 까지 skip 다음 부터 허용
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
        
        example(of: "skip") {
            
            let disposeBag = DisposeBag()
            
            let subject = PublishSubject<String>()
            let trigger = PublishSubject<String>()
            
            subject
            .skipUntil(trigger)
                .subscribe(onNext : {
                    print($0)
                })
            .disposed(by: disposeBag)
            
            subject.onNext("A")
            subject.onNext("B")
            trigger.onNext("X")
            subject.onNext("C")
            
        }
        
        /*
         C
         */
        
    }
}
```

## C. Taking Operator
1. .take(n) : 처음 부터 n 까지 데이터 허용
``` swift
```
2. .takeWhile(조건) : 조건이 참인 경우 데이터 허용

3. .enumerated() : index, value 형태의 튜플 허용
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
        
        example(of: "take") {
            
            let disposeBag = DisposeBag()
            
            Observable.of(2,2,4,4,6,6)
            .enumerated()
                .takeWhile({index, value in
                    value % 2 == 0 && index < 3
                })
                .map({$0.element})
                .subscribe(onNext :{
                    print($0)
                })
            .disposed(by: disposeBag)
        }
        
        /*
         2
         2
         4
         */
        
    }
}
```
4. .takeUntil(Observable) : 다른 Observable이 불릴 때까지 데이터 허용
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
        
        example(of: "takeUntil") {
            
            let disposeBag = DisposeBag()
            
            let subject = PublishSubject<String>()
            let trigger = PublishSubject<String>()
            
            subject
                .takeUntil(trigger)
                .subscribe(onNext : {
                    print($0)
                })
                .disposed(by: disposeBag)
            
            subject.onNext("A")
            subject.onNext("B")
            trigger.onNext("X")
            subject.onNext("C")
            
        }
        
        /*
         A
         B
         */
        
    }
}
```

## D. Distinct Operator
1. .distinctUntilChanged : 연달아 중복되는 값을 막아준다.
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
        
        example(of: "take") {
            
            let disposeBag = DisposeBag()
            
            Observable.of(1,1,2,2,2,1)
            .distinctUntilChanged()
                .subscribe(onNext :{
                    print($0)
                })
            .disposed(by: disposeBag)
        }
        
        /*
         1
         2
         1
         */
        
    }
}
```
2. .distinctUntilChanged(:) : 중복에 정규식 조건을 넣을 수 있다.
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
        
        example(of: "distinctuntil changed") {
            
            let disposeBag = DisposeBag()
            
            let formatter = NumberFormatter()
            formatter.numberStyle = .spellOut

            Observable<NSNumber>.of(10,110,20,200,210,310)
                .distinctUntilChanged({ a,b in
                    guard let awords = formatter.string(from: a)?.components(separatedBy: " "),
                        let bwords = formatter.string(from: b)?.components(separatedBy: " ") else {
                            return false
                    }
                    
                    print(awords)
                    print(bwords)
                    
                    var containsMatch = false
                    
                    for aword in awords{
                        for bword in bwords{
                            if aword == bword {
                                containsMatch = true
                                break
                            }
                        }
                    }
                    return containsMatch
                })
                .subscribe(onNext :{
                    print("d: \($0)")
                })
            .disposed(by: disposeBag)
        }
        
        /*
         d: 10
		["ten"]
		["one", "hundred", "ten"]
		["ten"]
		["twenty"]
		d: 20
		["twenty"]
		["two", "hundred"]
		d: 200
		["two", "hundred"]
		["two", "hundred", "ten"]
		["two", "hundred"]
		["three", "hundred", "ten"]
         */
        
    }
}

```
