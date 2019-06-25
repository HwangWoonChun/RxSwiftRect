# RxSwfitRect
RxSwfit Recture

7강 RxCocoa Beginning
===========
## A. 더미 데이터를 UI에 뿌려보기

``` swift
import UIKit
import RxSwift
import RxCocoa

class ViewController: UIViewController {
    
    @IBOutlet weak var tempLabel: UILabel!
    @IBOutlet weak var humidityLabel: UILabel!
    @IBOutlet weak var iconLabel: UILabel!
    @IBOutlet weak var cityNameLabel: UILabel!
    
    var bag = DisposeBag()
    
    public func example(of description: String,
                        action: () -> Void)
    {
        action()
    }
    
    override func viewDidLoad() {
        ApiController.shared.currentWeather(city: "RXSwift")
        .observeOn(MainScheduler.instance)
            .subscribe(onNext:{data in
                self.tempLabel.text = "\(data.temperature)"
                self.iconLabel.text = "\(data.icon)"
                self.humidityLabel.text = "\(data.humidity) %"
                self.cityNameLabel.text = "\(data.cityName)"
            })
        .disposed(by: bag)
    }

}
```

``` swift
import Foundation
import RxSwift

class ApiController {

    struct Weather {
        let cityName: String
        let temperature: Int
        let humidity: Int
        let icon: String
    }
    
    
    public func currentWeather(city : String) -> Observable<Weather>{
        return Observable.just(Weather(cityName: city, temperature: 20, humidity: 90, icon: iconNameToChar(icon: "02n")))
    }
}

public func iconNameToChar(icon: String) -> String {
    switch icon {
    case "01d":
        return "\u{f11b}"
    case "01n":
        return "\u{f110}"
    case "02d":
        return "\u{f112}"
    case "02n":
        return "\u{f104}"
    case "03d", "03n":
        return "\u{f111}"
    case "04d", "04n":
        return "\u{f111}"
    case "09d", "09n":
        return "\u{f116}"
    case "10d", "10n":
        return "\u{f113}"
    case "11d", "11n":
        return "\u{f10d}"
    case "13d", "13n":
        return "\u{f119}"
    case "50d", "50n":
        return "\u{f10e}"
    default:
        return "E"
    }
}
```

## B. Textfield와 연동하여 데이터 뿌려보기

1. SearchNameTextField -> filter{length > 0} -> flatMap{currentWeather} -> subscribe{...}

2. catchErrorJustReturn 
    
        * API에서 받은 에러를 통해 Observable이 dispose되는 것을 막는 역할

``` swift
import UIKit
import RxSwift
import RxCocoa

class ViewController: UIViewController {
    
    @IBOutlet weak var tempLabel: UILabel!
    @IBOutlet weak var humidityLabel: UILabel!
    @IBOutlet weak var iconLabel: UILabel!
    @IBOutlet weak var cityNameLabel: UILabel!
    //
    @IBOutlet weak var searchCityName: UITextField!
    
    var bag = DisposeBag()
    
    public func example(of description: String,
                        action: () -> Void)
    {
        action()
    }
    
    override func viewDidLoad() {

        searchCityName.rx.text
            .filter { (string) -> Bool in (string ?? "").count > 0}
            .flatMapLatest { (text) in
                return ApiController.shared.currentWeather(city: text ?? "Error")
                .catchErrorJustReturn(ApiController.Weather.empty)
            }
                .observeOn(MainScheduler.instance)
                .subscribe(onNext :{ data in
                    self.tempLabel.text = "\(data.temperature)"
                    self.iconLabel.text = "\(data.icon)"
                    self.humidityLabel.text = "\(data.humidity) %"
                    self.cityNameLabel.text = "\(data.cityName)"
                    })
                .disposed(by: bag)
        
    }

}
``` 

``` swift
import Foundation
import RxSwift

class ApiController {
    
    struct Weather {
        let cityName: String
        let temperature: Int
        let humidity: Int
        let icon: String
        
        static let empty = Weather(
            cityName: "Unknown",
            temperature: -1000,
            humidity: 0,
            icon: iconNameToChar(icon: "e")
        )
    }
    
    
    public func currentWeather(city : String) -> Observable<Weather>{
        return Observable.just(Weather(cityName: city, temperature: 20, humidity: 90, icon: iconNameToChar(icon: "02n")))
    }
}

public func iconNameToChar(icon: String) -> String {
    switch icon {
    case "01d":
        return "\u{f11b}"
    case "01n":
        return "\u{f110}"
    case "02d":
        return "\u{f112}"
    case "02n":
        return "\u{f104}"
    case "03d", "03n":
        return "\u{f111}"
    case "04d", "04n":
        return "\u{f111}"
    case "09d", "09n":
        return "\u{f116}"
    case "10d", "10n":
        return "\u{f113}"
    case "11d", "11n":
        return "\u{f10d}"
    case "13d", "13n":
        return "\u{f119}"
    case "50d", "50n":
        return "\u{f10e}"
    default:
        return "E"
    }
}
```


## C. 서버 API를 통한 데이터 노출

``` swift
import UIKit
import RxSwift
import RxCocoa

class ViewController: UIViewController {
    
    @IBOutlet weak var tempLabel: UILabel!
    @IBOutlet weak var humidityLabel: UILabel!
    @IBOutlet weak var iconLabel: UILabel!
    @IBOutlet weak var cityNameLabel: UILabel!

    @IBOutlet weak var searchCityName: UITextField!
    
    var bag = DisposeBag()
    
    public func example(of description: String,
                        action: () -> Void)
    {
        action()
    }
    
    override func viewDidLoad() {

        searchCityName.rx.text
            .filter { (string) -> Bool in (string ?? "").count > 0}
            .flatMapLatest { (text) in
                return ApiController.shared.currentWeather(city: text ?? "Error")
                .catchErrorJustReturn(ApiController.Weather.empty)
            }
                .observeOn(MainScheduler.instance)
                .subscribe(onNext :{ data in
                    self.tempLabel.text = "\(data.temperature)"
                    self.iconLabel.text = "\(data.icon)"
                    self.humidityLabel.text = "\(data.humidity) %"
                    self.cityNameLabel.text = "\(data.cityName)"
                    })
                .disposed(by: bag)
        
    }

}
``` 

``` swift

import Foundation
import RxSwift
import SwiftyJSON


class ApiController {
    
    static var shared = ApiController()
    let baseURL = URL(string: "http://api.openweathermap.org/data/2.5")!

    private let apiKey = "c253847886ad63efff4bd0422b6d471c"

    struct Weather {
        let cityName: String
        let temperature: Int
        let humidity: Int
        let icon: String
        
        static let empty = Weather(
            cityName: "Unknown",
            temperature: -1000,
            humidity: 0,
            icon: iconNameToChar(icon: "e")
        )
    }
    
    func currentWeather(city: String) -> Observable<Weather> {
        return buildRequest(pathComponent: "weather", params: [("q", city)]).map() { json in
            return Weather(
                cityName: json["name"].string ?? "Unknown",
                temperature: json["main"]["temp"].int ?? -1000,
                humidity: json["main"]["humidity"].int  ?? 0,
                icon: iconNameToChar(icon: json["weather"][0]["icon"].string ?? "e")
            )
        }
    }

    private func buildRequest(method: String = "GET", pathComponent: String, params: [(String, String)]) -> Observable<JSON> {
        
        let url = baseURL.appendingPathComponent(pathComponent)
        var request = URLRequest(url: url)
        let keyQueryItem = URLQueryItem(name: "appid", value: apiKey)
        let unitsQueryItem = URLQueryItem(name: "units", value: "metric")
        let urlComponents = NSURLComponents(url: url, resolvingAgainstBaseURL: true)!
        
        if method == "GET" {
            var queryItems = params.map { URLQueryItem(name: $0.0, value: $0.1) }
            queryItems.append(keyQueryItem)
            queryItems.append(unitsQueryItem)
            urlComponents.queryItems = queryItems
        } else {
            urlComponents.queryItems = [keyQueryItem, unitsQueryItem]
            
            let jsonData = try! JSONSerialization.data(withJSONObject: params, options: .prettyPrinted)
            request.httpBody = jsonData
        }
        
        request.url = urlComponents.url!
        request.httpMethod = method
        
        request.setValue("application/json", forHTTPHeaderField: "Content-Type")
        
        let session = URLSession.shared
        
        return session.rx.data(request: request).map {
            do {
                return try JSON(data: $0)
            }
            catch {
                fatalError("unable to convert data to JSON")
            }
        }
    }
}

public func iconNameToChar(icon: String) -> String {
    switch icon {
    case "01d":
        return "\u{f11b}"
    case "01n":
        return "\u{f110}"
    case "02d":
        return "\u{f112}"
    case "02n":
        return "\u{f104}"
    case "03d", "03n":
        return "\u{f111}"
    case "04d", "04n":
        return "\u{f111}"
    case "09d", "09n":
        return "\u{f116}"
    case "10d", "10n":
        return "\u{f113}"
    case "11d", "11n":
        return "\u{f10d}"
    case "13d", "13n":
        return "\u{f119}"
    case "50d", "50n":
        return "\u{f10e}"
    default:
        return "E"
    }
}

``` 

## D. Observable 바인딩 bind(to:)

1. Observable 바인딩
        
        * Observable에 데이터를 바인딩하면 Observable이 변경될때마다 UI를 업데이트 하는 구독이 자동으로 업데이트 한다.
        * Producer는 값을 만들고 Recevier는 만들어진 값을 수신하고 처리 한다. 단 Recevier는 값을 반환 할 수 없다.
        
2. bind(to:)

        * 업데이트 결과를 재사용 할 수 있으며, 일회용 데이터 소스를 여러번 사용하여 가독성 높은 코드의 Observable이 된다.


``` swift
import UIKit
import RxSwift
import RxCocoa

class ViewController: UIViewController {
    
    @IBOutlet weak var tempLabel: UILabel!
    @IBOutlet weak var humidityLabel: UILabel!
    @IBOutlet weak var iconLabel: UILabel!
    @IBOutlet weak var cityNameLabel: UILabel!
    //
    @IBOutlet weak var searchCityName: UITextField!
    
    var bag = DisposeBag()
    
    public func example(of description: String,
                        action: () -> Void)
    {
        action()
    }
    
    override func viewDidLoad() {
        
        let search = searchCityName.rx.text
            .filter { (string) -> Bool in (string ?? "").count > 0}
            .flatMapLatest { (text) in
                return ApiController.shared.currentWeather(city: text ?? "Error")
                .catchErrorJustReturn(ApiController.Weather.empty)
            }
            .share(replay : 1)
            .observeOn(MainScheduler.instance)
        
        
        search.map{ "\($0.temperature)" }
            .bind(to:tempLabel.rx.text)
            .disposed(by:bag)
        
        search.map{ "\($0.humidity)" }
            .bind(to:humidityLabel.rx.text)
            .disposed(by:bag)
        
        search.map{ "\($0.cityName)" }
            .bind(to:cityNameLabel.rx.text)
            .disposed(by:bag)
        
        search.map{ "\($0.icon)" }
            .bind(to:iconLabel.rx.text)
            .disposed(by:bag)
        
    }

}
``` 

## E. Traits

1. Traits

        * 직관적이며 작성하기 쉬운 코드를 만드는 특수 클래스, 특히 UI작업에 특화
        * error를 방출 할 수 없으며, 메인스케츌러에서 관찰 및 구독 한다. 부수 작용을 공유 한다.
        * 굳이 사용하지 않고 subject + observable을 사용해도 좋다.
        
2. Traits 의 주요 기능

        * controlProperty : 데이터와 UI를 연결
        * controlEvent : UI구성요소의 특정 이벤트를 구독(텍스트 필드의 입력완료 등)
        * Driver : 모든 과정을 메인 스레드에서 처리
        
3. asDriver() : 모든 과정을 메인스레드에서 처리 한다.

        * onErrorJustReturn 은 observable 에러를 방출 할때 어떻게 할 것인가 에 대한 기본 값 정의
        * onErrorDriveWith : 에러 직접 관리
        * onErrorRecover : 또 다른 드라이브와 함께 사용, 에러 노출 혹은 리트라이 목적으로 사용 가능 하다.

``` swift
import UIKit
import RxSwift
import RxCocoa


class ViewController: UIViewController {
    
    @IBOutlet weak var tempLabel: UILabel!
    @IBOutlet weak var humidityLabel: UILabel!
    @IBOutlet weak var iconLabel: UILabel!
    @IBOutlet weak var cityNameLabel: UILabel!
    //
    @IBOutlet weak var searchCityName: UITextField!
    
    var bag = DisposeBag()
    
    public func example(of description: String,
                        action: () -> Void)
    {
        action()
    }
    
    override func viewDidLoad() {
        
        //택스트를 입력받을 때마다 리퀘스트를 날리기 때문에 텍스트 입력이 완료 시 넘겨 받는다.
        //
        let search = searchCityName.rx.controlEvent(.editingDidEndOnExit).asObservable()
            .map{ self.searchCityName.text }
            .flatMapLatest { (text) in
                return ApiController.shared.currentWeather(city: text ?? "Error")
                .catchErrorJustReturn(ApiController.Weather.empty)
            }
            

            .asDriver(onErrorJustReturn: ApiController.Weather.empty)
        
        
        search.map{ "\($0.temperature)" }
            .drive(tempLabel.rx.text)
            .disposed(by:bag)
        
        search.map{ "\($0.humidity)" }
            .drive(humidityLabel.rx.text)
            .disposed(by:bag)
        
        search.map{ "\($0.cityName)" }
            .drive(cityNameLabel.rx.text)
            .disposed(by:bag)
        
        search.map{ "\($0.icon)" }
            .drive(iconLabel.rx.text)
            .disposed(by:bag)
        
    }

}
``` 


## F. Dispose()

1. Rx에서의 unOwned, weak
    
        * 쓰지말자 : 절대로 릴리즈 되지 않는 뷰컨트롤러 혹은 싱글턴 내부
        * unowned : 클로저 작업이 실행 된 후 릴리즈 되는 모든 컨트롤러
        * weak : 상기 두 상황을 제외한 경우
        
