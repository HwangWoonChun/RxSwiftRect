# RxSwfitRect
RxSwfit Recture

5강 Marble Diagram
===========
1. 구슬 : 데이터
2. ->  : Observable의 타임라인
3. ⇣   : 데이터 emit(배출)
4. |   : Completed
5. x   : Error
![Alt text](http://reactivex.io/assets/operators/legend.png)
* * *
1. Just : 데이터가 그대로 넘어온다.
![Alt text](http://reactivex.io/documentation/operators/images/just.c.png)
* * *
2. Map : 데이터가 변형되어 넘어온다.
![Alt text](http://reactivex.io/documentation/operators/images/map.c.png)
* * *
3. Filter : 조거문의 참에 해당되는 데이터만 넘어온다.
![Alt text](http://reactivex.io/documentation/operators/images/filter.c.png)
* * *
4. First : 첫번째 데이터가 넘어온다.
![Alt text](http://reactivex.io/documentation/operators/images/first.c.png)
* * *
5. Single : First에서 파생된 함수, 단 하나의 데이터만 만족한다.
![Alt text](http://reactivex.io/documentation/operators/images/single.png)
* * *
6. Flatmap : 데이터가 변형되어 넘어오는데, 그 데이터는 Observable 형태이다.
![Alt text](http://reactivex.io/documentation/operators/images/flatMap.c.png)
* * *
7. Concat : 두개이상의 Observable을 결합하여 새로운 Observable 생성한다. 결과값은 하나의 Observable 완료 다음 Observable
![Alt text](http://reactivex.io/documentation/operators/images/concat.png)
