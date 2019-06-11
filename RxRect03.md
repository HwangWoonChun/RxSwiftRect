# RxSwfitRect
RxSwfit Recture

3강 Subject
===========
## A. Subject
2강의 기존코드는 실시간으로 데이터를 구독 하진 않는다. 필요에 따라 데이터를 추가하고 방출 할 수 있게 하는 것이 subject 이다.
Observerable이면서 Observer인 녀석이 subject 이다.

## B. Subject의 종류
1. publish Subject : 빈 상태로 시작 새로운 값만 subscriber에 방출

<img src = "https://github.com/fimuxd/RxSwift/blob/master/Lectures/03_Subjects/1.%20publishsubject.png?raw=truehttps://github.com/fimuxd/RxSwift/blob/master/Lectures/02_Observables/1.%20marble.png?raw=true" height = 100>

* 첫번쨰 줄 : 배포
* 두번째 줄, 세번쨰 줄 : subscriber
* 위로 향하는 화살표는 구독
* 아래로 향하는 화살표는 이벤트 방출
