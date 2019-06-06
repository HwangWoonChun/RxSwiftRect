# RxSwfitRect
RxSwfit Recture

2강 Observable
===========
1. Observable : Observable<T> 는 T형태의 데이터를 전달 할 수 있는 일련의 이벤트를 비동기적으로 생성(방출) 하며 각각의 이벤트들은 숫자/인스턴스 값을 가지며 탭등의 제스쳐를 인식 가능 하다.
* * *
  
2. 생명주기
  <img src = "https://github.com/fimuxd/RxSwift/blob/master/Lectures/02_Observables/1.%20marble.png?raw=true" height = 50>
  
    next 이벤트를 통해 각각의 요소들을 방출 

  <img src = "https://github.com/fimuxd/RxSwift/blob/master/Lectures/02_Observables/2.%20lifecycle1.png?raw=true" height = 50>
  
     tap 이벤트를 방출한뒤 완전종료(completed)

  <img src = "https://github.com/fimuxd/RxSwift/raw/master/Lectures/02_Observables/3.%20lifecycle2.png?raw=true" height = 50>
  
     1,2 이벤트를 방출한뒤 에러 이벤트 후 완전종료(error)
  
* * *
