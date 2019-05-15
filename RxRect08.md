# RxSwfitRect
RxSwfit Recture

8강 RXCococa
===========
1. 텍스트필드 데이터의 유무에 따라 변화하는 화면 구성
<pre><code>

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
</pre></code>
* * *
2. 텍스트 데이터 받기
<pre><code>
    private func bindUI() {
        idField.rx.text.subscribe(onNext:{ s in
            print(s)
        }).disposed(by: disposeBag)
    }
</pre></code>
* * *
3. map을 통한 텍스트 데이터의 Valid Check
<pre><code>
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
</pre></code>
* * *
4. combineLastest : 텍스트 부분 둘을 결합, 둘중 하나라도 데이터 들어오면 결합
<pre><code>
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
</pre></code>
* * *
5. Subject : 외부에서 통제하는 옵저버블을 생성, bind함수를 통해 통제 권한을 넘겨 준다.
* Behavior 는 초기값을 가지는 옵져버블을 만든다.
<pre><code>
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
</pre></code>
