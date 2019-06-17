# RxSwfitRect
RxSwfit Recture

4강 Transforming Operator
===========
## A. 시작하기

1. swift의 map, flatMap
map 은 배열이외에도 연산(함수를) 사상 할 수 있는 기능 이에 비해 flatMap 은 각 원소를 unwrapp 하고 하나의 배열로 만드는 기능

``` swift
        let words = ["123", "456.7", "eighty nine", "10", "100"]
        
        let numbers = words.map{ Int($0) }
        /*
         [Optional(123), nil, nil, Optional(10), Optional(100)]
         */
        let numbersWithMap = words.map{ Int($0) }.filter{ $0 != nil }.map{ $0! }
        /*
         [123, 10, 100]
         */
        let numbersFlatMap = words.flatMap{ Int($0) }
        /*
         [123, 10, 100]
         */
```

## B. 변환연산자의 요소들
1. toArray
    * 독립적인 요소들을 배열로 넣는 가장 간단한 방법
2. map
    * 각각의 요소들을 연산에 사상할 수 있는 방법
3. enumerated
    * Map with enumerated

## C. 내부의 Observable 변환하기
1. flatMap
2. flatMapLatest

## D. 이벤트 관찰하기
