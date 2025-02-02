# RxSwfitRect
RxSwfit Recture

8강 RXCococa Beggning
===========
1. 텍스트필드 데이터의 유무에 따라 변화하는 화면 구성
``` swift

class ViewController: UIViewController {
    var disposeBag = DisposeBag()

    override func viewDidLoad() {
        super.viewDidLoad()
        bindUI()
    }

    // MARK: - IBOutler

    @IBOutlet var idField: UITextField!
    @IBOutlet var pwField: UITextField!
    @IBOutlet var loginButton: UIButton!
    @IBOutlet var idValidView: UIView!
    @IBOutlet var pwValidView: UIView!

    // MARK: - Bind UI

    private func bindUI() {
    }

    // MARK: - Logic

    private func checkEmailValid(_ email: String) -> Bool {
        return email.contains("@") && email.contains(".")
    }

    private func checkPasswordValid(_ password: String) -> Bool {
        return password.count > 5
    }
}
```
* * *
2. 텍스트 데이터 받기
``` swift
    private func bindUI() {
        idField.rx.text.subscribe(onNext:{ s in
            print(s)
        }).disposed(by: disposeBag)
    }
```
* * *
3. map을 통한 텍스트 데이터의 Valid Check
``` swift
    private func bindUI() {
        idField.rx.text
            .filter {$0 != nil}
            .map{$0!}
            .map(checkEmailValid)
            .subscribe(onNext:{ s in
            print(s)
        }).disposed(by: disposeBag)
    }
    //Optional Check이 귀찮다면
     private func bindUI() {
        idField.rx.text.orEmpty
            .map(checkEmailValid)
            .subscribe(onNext:{ s in
            print(s)
        }).disposed(by: disposeBag)

    }
```
* * *
4. combineLastest : 텍스트 부분 둘을 결합, 둘중 하나라도 데이터 들어오면 결합
``` swift
    private func bindUI() {
    
        let idInputOb : Observable<String> = idField.rx.text.orEmpty.asObservable()
        let idVaildOb = idInputOb.map(checkEmailValid)
        
        let pwInputOb : Observable<String> = pwField.rx.text.orEmpty.asObservable()
        let pwValidOb = pwInputOb.map(checkPasswordValid)
        
        idVaildOb.subscribe(onNext : {b in
            self.idValidView.isHidden = b
        }).disposed(by: disposeBag)
        pwValidOb.subscribe(onNext : {b in
            self.pwValidView.isHidden = b
        }).disposed(by: disposeBag)
        
        Observable.combineLatest(idVaildOb,pwValidOb,resultSelector : {b1, b2 in b1 && b2})
            .subscribe(onNext : {b in
                self.loginButton.isEnabled = b
            }).disposed(by: disposeBag)
        
    }


    }
```
* * *
5. Subject : 우리는 실시간으로 Observable에 값을 추가하고 Subscriber에게 방출하는 것이 필요하다. 이 때 Observable이자 Observer인 Subject 를 사용한다. 시간에 민감한 데이터를 모델링 할 때 사용한다 (subscribe 되기 이전의 값이 필요 없는 경우)
* Behavior 는 초기값을 가지는 옵져버블을 만든다.
``` swift
import RxCocoa
import RxSwift
import UIKit

class ViewController: UIViewController {
    var disposeBag = DisposeBag()
    
    let idValid : BehaviorSubject<Bool> = BehaviorSubject(value: false)
    let pwValid : BehaviorSubject<Bool> = BehaviorSubject(value: false)
    let idInputText : BehaviorSubject<String> = BehaviorSubject(value : "")
    let pwInputText : BehaviorSubject<String> = BehaviorSubject(value : "")
    
    override func viewDidLoad() {
        super.viewDidLoad()
        bindUI()
    }

    // MARK: - IBOutler

    @IBOutlet var idField: UITextField!
    @IBOutlet var pwField: UITextField!
    @IBOutlet var loginButton: UIButton!
    @IBOutlet var idValidView: UIView!
    @IBOutlet var pwValidView: UIView!

    // MARK: - Bind UI

    private func bindUI() {

        //input 처리
        idField.rx.text.orEmpty
            .map(checkEmailValid)
            .bind(to: idValid)
            .disposed(by: disposeBag)

        pwField.rx.text.orEmpty
            .map(checkPasswordValid)
            .bind(to: idValid)
            .disposed(by: disposeBag)
        
        //output
        idValid.subscribe(onNext : { b in
            self.idValidView.isHidden = b
        })
        .disposed(by: disposeBag)
        
        pwValid.subscribe(onNext : { b in
            self.pwValidView.isHidden = b
        })
        .disposed(by: disposeBag)
        
        Observable.combineLatest(idValid, pwValid, resultSelector : {$0 && $1})
            .subscribe(onNext : {b in self.loginButton.isEnabled = b})
        .disposed(by: disposeBag)
        
    }
    
    

    // MARK: - Logic

    private func checkEmailValid(_ email: String) -> Bool {
        return email.contains("@") && email.contains(".")
    }

    private func checkPasswordValid(_ password: String) -> Bool {
        return password.count > 5
    }
}
```
``` swift
let subject = PublishSubject<Int>()
subject.onNext(0)

let subscriptionOne = subject
    .subscribe(onNext: { num in
        print(num)
    })
subject.on(.next(1))
subject.onNext(2)

let subscriptionTwo = subject
    .subscribe({ event in
        print("2)", event.element ?? event)
    })

subject.onNext(3)

subscriptionOne.dispose()

subject.onNext(4)
subject.onCompleted()
subject.onNext(5)

subscriptionTwo.dispose()

let disposeBag = DisposeBag()

subject
    .subscribe {
        print("3)", $0.element ?? $0)
    }
    .disposed(by: disposeBag)

subject.onNext(9999)

/* Prints:
 1
 2
 3
 2) 3
 2) 4
 2) completed
 3) completed
*/
```

6. MVVM으로 적용해보기 : 인풋과 아웃풋을 연결하고 수정해야될 부분 및 버그가 일어날 곳은 오로지 비즈니스 로직 뿐이다.
``` swift
import Foundation
import RxCocoa
import RxSwift

struct ViewModel {
    
    //
    let emailText = BehaviorSubject(value: "")
    let pwdText = BehaviorSubject(value: "")

    //
    let isEmailValid = BehaviorSubject(value: false)
    let isPasswordValid = BehaviorSubject(value: false)
    
    
    //
    init(){
        _ = emailText.distinctUntilChanged()
            .map(checkEmailValid)
            .bind(to: isEmailValid)
        
        _ = pwdText.distinctUntilChanged()
            .map(checkPwdValid)
            .bind(to: isPasswordValid)
    }
    
    //business Logic
    private func checkEmailValid(_ email : String) -> Bool{
        return email.contains("@") && email.contains(".")
    }
 
    private func checkPwdValid(_ pwd : String) -> Bool{
        return pwd.count > 5
    }
}
```

``` swift
import RxCocoa
import RxSwift
import UIKit

class ViewController: UIViewController {
    var disposeBag = DisposeBag()

    override func viewDidLoad() {
        super.viewDidLoad()
        bindUI()
    }
    
    // MARK: - IBOutler
    
    @IBOutlet var idField: UITextField!
    @IBOutlet var pwField: UITextField!
    @IBOutlet var loginButton: UIButton!
    @IBOutlet var idValidView: UIView!
    @IBOutlet var pwValidView: UIView!

    let viewModel : ViewModel! = ViewModel()
    
    // MARK: - Bind UI
    
    private func bindUI() {
        
        //input 처리
        idField.rx.text.orEmpty
            .bind(to: viewModel.emailText)
            .disposed(by: disposeBag)
        
        pwField.rx.text.orEmpty
            .bind(to: viewModel.pwdText)
            .disposed(by: disposeBag)
        
        //output
        viewModel.isEmailValid.bind(to: idValidView.rx.isHidden)
            .disposed(by: disposeBag)
        
        viewModel.isPasswordValid.bind(to: pwValidView.rx.isHidden)
            .disposed(by: disposeBag)
        
        //
        Observable.combineLatest(viewModel.isEmailValid, viewModel.isPasswordValid, resultSelector : {$0 && $1})
            .bind(to: loginButton.rx.isEnabled)
            .disposed(by: disposeBag)
        
    }
    
}
``` 
