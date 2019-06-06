# RxSwfitRect
RxSwfit Recture

2강 Observable
===========
## A. Observable
Observable<T> 는 T형태의 데이터를 전달 할 수 있는 일련의 이벤트를 비동기적으로 생성(방출) 하며 각각의 이벤트들은 숫자/인스턴스 값을 가지며 탭등의 제스쳐를 인식 가능 하다.

## B. 생명주기

``` swift
public enum Event<Element> {
     /// element 인스턴스를 가진다.
     case next(Element)
     /// 에러 인스턴스를 가진다.
     case error(Swift.Error)
     /// 아무것도 가지지 않는다.
     case completed
}
```

1. 이벤트 : next 이벤트를 통해 계속 데이터 방출할 수 있으며, error, complete를 통해 완전 종료 될 수 있다.

     * next 이벤트를 통해 각각의 요소들을 방출<p>
     <img src = "https://github.com/fimuxd/RxSwift/blob/master/Lectures/02_Observables/1.%20marble.png?raw=true" height = 50>
     
     * tap 이벤트를 방출한뒤 완전종료(completed)<p> 
     <img src = "https://github.com/fimuxd/RxSwift/blob/master/Lectures/02_Observables/2.%20lifecycle1.png?raw=true" height = 50>
    
     * 1,2 이벤트를 방출한뒤 에러 이벤트 후 완전종료(error)<p> 
    <img src = "https://github.com/fimuxd/RxSwift/raw/master/Lectures/02_Observables/3.%20lifecycle2.png?raw=true" height = 50>



2. Observable 생성 : Observable은 반드시 타입을 가진다.

     * Just : 특정 아이템 하나를 가지는 Observable을 생성
     
     * of : 다양한 데이터, 객체를 가지는 Observable을 생성 / 하나씩 데이터 추출
     
     * From   : 다양한 데이터, 객체를 가지는 Observable을 생성 / 하나씩 데이터 추출 / 배열만 가능
     
     * Create : 개발자가 입맛대로 Observable 개발
     
     * empty : element를 가지지 않고 completed 이벤트만 방출, 즉시 종료 할 수 있는 Observable을 리턴하고 싶을 때 사용
     
     * never : element를 가지지 않고 어떠한 이벤트도 방출 하지 않는다.
     
     * throw : element를 가지지 않고 어떠한 에러 이벤트만 방출 한다.
     
     * range : start로 부터, count 만큼의 크기의 데이터를 가지는 Observable을 만든다. 
     
``` swift
/*** 
     Just, Of, From
***/
import UIKit
import RxSwift

class ViewController: UIViewController {

    public func example(of description: String,
                        action: () -> Void) {
        action()
    }
    
    override func viewDidLoad() {
        example(of : "just, of, from"){
            let one = 1
            let two = 2
            let three = 3
            
            //Just
            let observable : Observable<Int> = Observable<Int>.just(one)
            
            //Of
            let observable2 = Observable.of(one, two, three)
            let observable3 = Observable.of([one, two, three]) //just와 동일한 효과

            //From
            let observable4 = Observable.from([one, two, three])

            
        }
    }
}
```
``` swift
/*** 
     Create
***/
import UIKit
import RxSwift

enum MyError : Error{
    case anError
}

class ViewController: UIViewController {

    let disposeBag = DisposeBag()
    
    public func example(of description: String,
                        action: () -> Void) {
        action()
    }
    
    override func viewDidLoad() {
        
        example(of: "create"){
            
            Observable<String>.create({ (observable) -> Disposable in
                
                observable.onNext("1")
                observable.onError(MyError.anError)
                observable.onCompleted()
                observable.onNext("?")
                return Disposables.create()
                
            }).subscribe(
                onNext:{print($0)},
                onError:{print($0)},
                onCompleted:{print("completed")},
                onDisposed:{print("disposed")}
            ).disposed(by: disposeBag)
        }
        /*
               1
               anError
               disposed
         */
        
    }
}
```
3. Observarble 구독 : subscribe 함수를 이용하여 이벤트를 받는다. 

     * subcribe() : escaping 클로져로 이벤트의 데이터 타입을 가진다. escaping에 대한 리턴 값은 없으며, 함수 리턴 타입은 Disaposable 이다.
     
     * subscribe(onNext:) : next 이벤트만을 argument로 취한뒤 데이터만 핸들링 가능

``` swift
/*** 
     subscribe()
***/
import UIKit
import RxSwift

class ViewController: UIViewController {
    
    public func example(of description: String,
                        action: () -> Void) {
        print("\n--- Example of:", description, "---")
        action()
    }
    
    override func viewDidLoad() {
        example(of : "just, of, from"){
            let one = 1
            let two = 2
            let three = 3
            
            let observable : Observable<Array> = Observable<Array>.just(["a","a","a"])

            observable.subscribe({(event) in
                print(event)
            })
            /* 
               next(["a", "a", "a"])
               completed
            */
        }
    }
}
```
     
 ``` swift
/*** 
     subscribe(onNext : )
***/
import UIKit
import RxSwift

class ViewController: UIViewController {
    
    public func example(of description: String,
                        action: () -> Void) {
        print("\n--- Example of:", description, "---")
        action()
    }
    
    override func viewDidLoad() {
        example(of : "just, of, from"){
            let one = 1
            let two = 2
            let three = 3
            
            let observable : Observable<Array> = Observable<Array>.just(["a","a","a"])

            observable.subscribe(onNext : {(element) in
                print(element)
            })
            /* 
               ["a", "a", "a"]
            */

        }
    }
}
```

4. Disposing과 종료 : subscribe가 Observable이 이벤트를 방출 해준다면, disposing은 수동적으로 subscribe를 취소하는 것 이다.

     * disposing() : 구독을 취소
     
     * disposeBag(by : ) : 구독에 대해서 일일이 취소 하지 않고 Bag 에 담아 한번에 취소
     
 ``` swift
/*** 
     disposing
***/     
import UIKit
import RxSwift

class ViewController: UIViewController {

    public func example(of description: String,
                        action: () -> Void) {
        action()
    }
    
    override func viewDidLoad() {
        
        example(of: "range"){
            let observable = Observable.of("A","B")
            let subscribtion = observable.subscribe({ (event) in
                print(event)
            })
            subscribtion.dispose()
        }
    }
}

```

 ``` swift
/*** 
     disposeBag
***/    
import UIKit
import RxSwift
import RxCocoa

class ViewController: UIViewController {

    let dispseBag = DisposeBag()
    
    public func example(of description: String,
                        action: () -> Void) {
        action()
    }
    
    override func viewDidLoad() {
        
        example(of: "range"){
            let observable = Observable.of("A","B")
            let subscribtion = observable.subscribe({ (event) in
                print(event)
            })
            .disposed(by: dispseBag)
        }
    }
}
```
     
     
