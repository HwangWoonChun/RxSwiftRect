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



2. Observarble 생성

     * Just : 특정 아이템 하나를 가지는 Observable을 생성
     
     * of : 다양한 데이터, 객체를 가지는 Observable을 생성 / 하나씩 데이터 추출
     
     * From   : 다양한 데이터, 객체를 가지는 Observable을 생성 / 하나씩 데이터 추출 / 배열만 가능
     
``` swift

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


3. Observarble 구독 : subscribe 함수를 이용하여 이벤트를 받는다. 

     * subscribe() : escaping 클로져로 이벤트의 데이터 타입을 가진다. escaping에 대한 리턴 값은 없으며, 함수 리턴 타입은 Disaposable 이다.
 
 ``` swift
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
 
     * subscribe(onNext:) : next 이벤트만을 argument로 취한뒤 데이터만 핸들링 가능

 ``` swift
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
               completed
            */

        }
    }
}
```
