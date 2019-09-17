# RxSwfitRect
RxSwfit Recture

10강 RxSwift + MVVM
===========
1. MVC : View , ViewController , Model

  * 컨트롤러를 가운데 두고 모델과 뷰를 업데이트
  * 뷰는 화면에 보여줌
  * 모델은 앱의 상태 지속을 위해 데이터를 쓰고 읽음
  
2. MVVM : View , ViewController, ViewModel, Model

  * 모델은 다른 클래스들이 데이터 변경을 노티해도 직접통신 하지 않는다.
  * 뷰모델은 모델과 통신하며 여기서의 데이터들을 ViewController 로 내보낸다.
  * 뷰컨트롤러는 뷰의 생명주기 관리 UI구성요소에 바인딩 할때만 ViewModel 및 View와 통신
  * 뷰는 화면에 보여줌
 
``` swift
import Foundation

struct Repository {
    
    let repoName: String
    let repoURL: String
}
``` 

``` swift
import RxSwift
import RxCocoa

class ViewModel {
    
    let searchText = Variable("")
    
    //lazy : 사용되기전까지 로딩되지 않는 타입, let 불가
    lazy var data: Driver<[Repository]> = {
        
        return self.searchText.asObservable()
            .throttle(0.3, scheduler: MainScheduler.instance)   //가장 마지막 값 받아 쓸때 없는 네트워킹 방지
            .distinctUntilChanged() //값이 변경됬을때만 데이터 받음
            .flatMapLatest(ViewModel.repositoriesBy)    //데이터를 특정 형태로 맵핑하는데 최신 데이터를 완전 다른 Observable로 변환
            .asDriver(onErrorJustReturn: [])    //driver로 변경
    }()
    
    
    //repositoriesBy, parse 는 API Manager에 두는 것이 좋다.
    static func repositoriesBy(_ githubID: String) -> Observable<[Repository]> {
        guard !githubID.isEmpty,
            let url = URL(string: "https://api.github.com/users/\(githubID)/repos") else {
                return Observable.just([])
        }
        
        return URLSession.shared.rx.json(url: url)
            .retry(3)   //에러 발생시 3번 리트라이
            //.catchErrorJustReturn([]) //에러 시 빈 배열 리턴
            .map(parse)
    }
    
    static func parse(json: Any) -> [Repository] {
        guard let items = json as? [[String: Any]]  else {
            return []
        }
        
        var repositories = [Repository]()
        
        items.forEach{
            guard let repoName = $0["name"] as? String,
                let repoURL = $0["html_url"] as? String else {
                    return
            }
            repositories.append(Repository(repoName: repoName, repoURL: repoURL))
        }
        return repositories
    }
}
``` 

``` swift
import UIKit
import RxSwift
import RxCocoa

class ViewController: UIViewController {

    @IBOutlet weak var tableView: UITableView!
    
    let searchController = UISearchController(searchResultsController: nil)
    var searchBar: UISearchBar { return searchController.searchBar }
    
    var viewModel = ViewModel()
    let disposeBag = DisposeBag()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        configureSearchController()
        
        //데이터 > 뷰모델 > 뷰컨트롤러 > (드라이브) 뷰
        viewModel.data
            .drive(tableView.rx.items(cellIdentifier: "Cell")) { _, repository, cell in
                cell.textLabel?.text = repository.repoName
                cell.detailTextLabel?.text = repository.repoURL
            }
            .addDisposableTo(disposeBag)
        
        //뷰 > 데이터 > 뷰컨트롤러 > (바인드) 뷰모델
        //viewModel의 searchText를 SearchBar에 바인딩
        //orEmpty는 닐값 체크
        searchBar.rx.text.orEmpty
            .bind(to: viewModel.searchText)
            .disposed(by: disposeBag)
        
        viewModel.data.asDriver()
            .map { "\($0.count) Repositories" }
            .drive(navigationItem.rx.title)
            .disposed(by: disposeBag)
    }
    
    func configureSearchController() {
        searchController.obscuresBackgroundDuringPresentation = false
        searchBar.showsCancelButton = true
        searchBar.text = "NavdeepSinghh"
        searchBar.placeholder = "Enter GitHub ID, e.g., \"NavdeepSinghh\""
        tableView.tableHeaderView = searchController.searchBar
        definesPresentationContext = true
    }
}
``` 
