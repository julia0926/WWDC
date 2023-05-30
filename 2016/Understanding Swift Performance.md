## Swift 성능
![](../2016/images/understanding%20swift%20performance/2023-05-29-10-31-08.png)
- 구조체와 클래스를 사용한 오버헤드에 따른 차이
- POP의 성능 영향

## Allocation
### Stack
![](../2016/images/understanding%20swift%20performance/2023-05-29-10-44-23.png)
- Swift는 자동으로 메모리 할당 및 해제한다.
- 스택의 할당하는 메모리 중 일부이다. 
- 함수를 호출할 때 (스택 끝의) 스택 포인터를 약간만 줄여서 공간 확보한 다음 필요한 메모리를 할당할 수 있다.
### Heap
![](../2016/images/understanding%20swift%20performance/2023-05-29-10-45-29.png)
- 스택보다 고급 데이터 구조가 필요함
- 적절한 크기의 사용되지 않은 블록을 찾기 위해 힙의 데이터 구조를 검색해야함
- 할당을 해제하려면 해당 메모리의 적절한 위치에 다시 삽입
- 동시에 힙에 메모리 할당할 수 있으므로 무결성을 보호해야 함
### Struct Example
![](../2016/images/understanding%20swift%20performance/2023-05-29-10-49-43.png)
- 코드 실행 시작도 전에 point1, point2를 위한 스택 공간을 할당했다.
- 스택에 이미 할당한 메모리를 x=0, y=0으로 초기화할 뿐

![](../2016/images/understanding%20swift%20performance/2023-05-29-10-51-40.png)
- point1을 point2에 할당할 때 포인트의 복사본을 만들고 스택에 이미 할당한 point2 메모리를 다시 초기화
- point1, point2는 각각 독립적인 인스턴스임. 각 값 변화에 영향을 끼치지 않음
- point1을 사용하고, point2 사용하면 함수 실행이 종료됨
- 스택 포인터를 함수에 입력했을 때 위치까지 다시 증가시키는 것만으로 각각 메모리 할당을 간단하게 해제 가능
### Class Example
![](../2016/images/understanding%20swift%20performance/2023-05-29-11-26-59.png)
1. (0, 0)에 포인트를 구성할 때 Swift는 힙을 잠그고 적절한 크기의 사용되지 않은 메모리 블록에 대한 데이터 구조를 검색
2. 메모리 차지하면 해당 메모리를 초기화할 수 있고, point1 참조를 초기화 할 수 있다.
3. `Struct`랑 다르게 4군데를 스토리지에 할당했다.

![](../2016/images/understanding%20swift%20performance/2023-05-29-11-30-47.png)
- 값을 복사하지 않고 참조를 복사한다.
- 정확히 동일한 포인트 인스턴스를 참조한다 -> 참조 시멘틱
- 의도하지 않은 상태 공유로도 이어질 수 있다.

![](../2016/images/understanding%20swift%20performance/2023-05-29-11-32-52.png)
- class는 힙 할당이 필요하기 때문에 struct보다 생성 비용이 더 많이 든다.
- 참조하기 때문에 ID, 간접 저장소 같은 몇가지 강력한 특성이 있다.
- 하지만 추상화를 위해 위 특성이 필요없다면 struct 써라.

### Balloon Example
![](../2016/images/understanding%20swift%20performance/2023-05-29-11-35-56.png)
- 문자 메세지의 말풍선을 만들 때, `makeBalloon()`는 할당 시작 및 사용자 스크롤 중에 자주 호출하기 때문에 매우 빨라야 한다.
- 캐싱 레이어를 추가해서 한번 생성하면 캐시에 꺼내서 사용할 수 있지만, 몇몇 문제가 있음
- 문자열은 강력한 타입은 아니다. 실제로 해당 문자의 내용을 힙에 간접적으로 저장하기 때문에 많은 것을 나타냄.
- `makeBalloon()` 함수를 호출할 때마다 cache hit이 있더라도 힙 할당이 발생된다.

![](../2016/images/understanding%20swift%20performance/2023-05-29-11-39-02.png)
- Struct를 사용해서 구성을 해보자! String보다 훨씬 안전한 방식
- 딕셔너리의 키에 struct 타입의 `Attributes` 으로 변경하면 힙 할당이 필요하지 않기 때문에 오버헤드가 발생하지 않는다.
- 스택에 할당할 수 있다. 훨씬 안전하고 빠름.

## Reference Counting
![](../2016/images/understanding%20swift%20performance/2023-05-29-14-17-44.png)
1. Class는 point1, point2가 하나씩 참조
2. 사용이 끝났으면 각각 참조를 해제한다.
3. 참조가 더이상 없으므로 Heap을 잠구고 안전하게 메모리 블록 반환

![](../2016/images/understanding%20swift%20performance/2023-05-29-14-21-16.png)
1. Struct 타입이지만 구성요소에 String, UIFont는 Class Type이므로 힙에 저장된다.
2. 하나의 인스턴스에 두 개의 참조가 존재한다.
3. (참조) 사본 만든다면 두 개의 참조가 더 추가되서 오버헤드가 두 배 발생된다.
### Performance
![](../2016/images/understanding%20swift%20performance/2023-05-29-14-26-01.png)
- Class는 힙에 할당되기 때문에 Swift는 힙 할당의 수명을 관리해야 한다.

![](../2016/images/understanding%20swift%20performance/2023-05-29-14-27-25.png)
- Struct에 참조가 포함되어 있으면 참조를 계산하는 오버헤드도 발생한다.

![](../2016/images/understanding%20swift%20performance/2023-05-29-14-28-31.png)
- Struct 내 RC가 많다면 비례하는 참조 계산 수많은 오버헤드가 존재.

![](../2016/images/understanding%20swift%20performance/2023-05-29-14-33-42.png)
- Struct 내에 3가지 속성 모두 전달할 때 RC 오버헤드가 발생한다. 힙 할당에 대한 참조가 있기 때문에

1. `String -> UUID` : UUID는 128비트를 struct에 직접 저장
2. `String -> Enum` : mineType을 각 타입에 맞게 적절하게 매핑할 수 있고, 간접적으로 힙에 저장할 필요가 없기 때문에 더 나은 성능
![](../2016/images/understanding%20swift%20performance/2023-05-29-14-40-14.png)

## Method Dispatch
### Static
![](../2016/images/understanding%20swift%20performance/2023-05-29-14-42-06.png)
- 컴파일 단계에 실행할 구현을 결정
- 런타임에 올바른 구현으로 바로 이동 가능
- 인라인(inlines) 같은 것으로 적극적 코드 최적화 가능

### Dynamic
![](../2016/images/understanding%20swift%20performance/2023-05-29-14-49-53.png)
- 컴파일 타임에 결정 불가
- 런타임에 실제 구현을 조회한 후 해당 구현으로 건너뜀
- Static보다 비용이 많이 들진 않음 (RC, Heap 할당, 스레드 동기화 오버헤드 없음)
- 컴파일러의 가시성(visiblity) 차단하므로 동적 디스패치는 다른 최적화를 수행할 수 있지만 컴파일러는 이를 추론할 수 없다.
 
 ### Inlining
 ![](../2016/images/understanding%20swift%20performance/2023-05-29-14-53-50.png)
 - `drawAPoint()`와 `point.draw()` 모두 정적으로 전달됨.

 ![](../2016/images/understanding%20swift%20performance/2023-05-29-14-55-33.png)
 - 컴파일러가 어떤 구현이 실행될지 정확히 알고 있어 `drawAPoint()` 디스패치를 가져와  `point.draw()` 대체 가능

 ![](../2016/images/understanding%20swift%20performance/2023-05-29-14-57-54.png)
 - `draw()`의 실제 구현으로 (정적 디스패치이기 때문에) 대체할 수도 있다.
 - 런타임에 이 코드를 실행하면 Point를 구성하고 구현을 실행할 수 있는 작업이 완료
> 왜 static이 dynamic보다 빠른가? 
- static dispatch는 전체 체인(whole chain)을 통해 가시성을 갖게됨. 
- 반면, dynamic dispatch는 전체 체인 없이 고차원에서 추론하는 모든 단계에서 차단됨.
- 호출 스택 오버헤드가 없는 단일 구현처럼 정적 메소드 디스패치 체인을 축소할 수 있다.

![](../2016/images/understanding%20swift%20performance/2023-05-29-15-13-15.png)
- for문으로 `d.draw()`를 호출할 때 Point일 수도 Line일 수도 있다.
- 어떤 것을 호출할지 어떻게 결정하나? 컴파일러는 해당 클래스의 타입 정보에 대한 포인터인 다른 필드를 클래스에 추가하고 정적 메모리에 저장

![](../2016/images/understanding%20swift%20performance/2023-05-29-15-20-18.png)
- 그래서 `draw()`를 호출할 때 실행할 올바른 구현에 대한 포인터가 포함된 유형 및 정적 메모리에 대한 가상 메서드 테이블이라는 항목에 대한 유형을 통한 조회
- 실질적으로 순환을 암시하는 self-parameter로 전달

![](../2016/images/understanding%20swift%20performance/2023-05-29-15-23-18.png)
- 클래스는 기본적으로 dynamic dispatch를 전달
- 메서드 체인이나 여러 것들과 관련해서 인라인과 같은 최적화 방지할 수 있다.
- `final`을 붙여 static dispatch로 할 수 있다.
![](../2016/images/understanding%20swift%20performance/2023-05-29-15-25-32.png)

--------
## Protocol Types
![](../2016/images/understanding%20swift%20performance/2023-05-29-15-42-18.png)
- struct 타입들은 V-Table dispatch 수행하는데 남아있는 관계를 공유하지 않는다.
- 어떻게 올바른 메서드로 디스패치하는가? Protocol Witness Table(테이블 기반 메커니즘)
### Protocol Witness Table
![](../2016/images/understanding%20swift%20performance/2023-05-29-15-45-37.png)
- 배열의 고정 오프셋에 요소를 가리켜 저장하도록 한다. 어떻게 작동?
- Swift가 `Existential Container`라는 특별한 구성을 사용
## Existential Container
![](../2016/images/understanding%20swift%20performance/2023-05-29-15-48-50.png)
- 처음에는 3 word가 valueBuffer에 예약되있음
- Point같이 2 word가 필요한 작은 타입이라면 적합
- Line은 4 word가 필요한데 어떻게 저장할까?

![](../2016/images/understanding%20swift%20performance/2023-05-29-15-50-45.png)
- Swift는 힙에 메모리를 표시하고 여기에 값을 저장하고 존재하는 것과 상응하는 메모리에 대한 포인터를 저장
- Line(valueBuffer보다 큼)과 Point(valueBuffer보다 작음)는 차이가 있다.

![](../2016/images/understanding%20swift%20performance/2023-05-29-15-55-52.png)
- 이를 VWT(Value Witness Table)로 수명 관리할 수 있다.
- 프로토콜 타입의 로컬 변수의 lifetime이 시작될 때, Swift는 해당 테이블 내에서 `allocate()` 호출
- LineVWT이 복구된 힙에 메모리를 표시, 고정 상태의 valueBuffer 내부에 해당하는 메모리에 대한 포인터를 저장

![](../2016/images/understanding%20swift%20performance/2023-05-29-15-58-49.png)
- Swift는 로컬 변수를 초기화하는 할당 소스에서 Existential Container로 값을 복사해야 합니다.
- VWT는 힙에 고정된 value LineBuffer로 복사

![](../2016/images/understanding%20swift%20performance/2023-05-29-16-06-03.png)
- destruct를 호출해 타입에 포함될 수 있는 값에 대한 RC를 줄인다.
- Line에는 아무것도 없었으므로 뭐 하진 않음

![](../2016/images/understanding%20swift%20performance/2023-05-29-16-07-35.png)
- Swift는 해당 테이블의 deallocate를 호출
- 우리는 Line에 대한 VWT를 가지고 다니며 이것이 value에 대한 힙에 할당된 메모리를 deallocate 함

![](../2016/images/understanding%20swift%20performance/2023-05-29-16-10-33.png)
- existential container는 VWT에 대한 참조가 존재
- PWT(Protocol witeness table)도 참조

### Existential Container Example
![](../2016/images/understanding%20swift%20performance/2023-05-29-16-23-13.png)
- Swift는 힙에 존재하는 컨테이너에 할당
- 컨테이너에서 VWT와 PWT를 읽고 로컬 컨테이너의 필드를 초기화

![](../2016/images/understanding%20swift%20performance/2023-05-30-10-08-50.png)
- local.draw()가 실행되면 컨테이너에서 PWT를 찾고 해당 테이블에서 draw 메서드를 찾고 구현한다.
- projectBuffer는 인라인 버퍼에 맞는 값 여부에 따라 이 주소가 Existential Container의 시작이거나 인라인 valueBuffer에 맞지 않는 큰 값이 있는경우 주소가 시작
- value witness function 기능은 타입에 따라 차이를 추상화한다.

### Indirect Storage with Copy-on-Write
참조를 할 때 각각 힙에 할당하면 비용이 많이든다. 그래서 COW를 사용해 클래스 사용 전 참조 횟수를 확인하고 그에 따라 참조를 복사한다.
![](../2016/images/understanding%20swift%20performance/2023-05-30-10-37-19.png)
- Line 내부에 Storage라는 클래스를 갖고 값을 읽고싶을 때마다 해당 스토리지 내부의 값을 읽으면 된다.
- `isUniquelyReferance~~()` 참조 횟수를 확인해서 참조가 없을 때만 복사본을 만들어서 변경한다.
## Protocol Type
### Small Value
- 힙 할당 X
- 참조 카운트 X
- PWT를 통한 동적 디스패치

### Large Value
- 큰 struct가 참조에 포함된 경우 잠재적으로 힙 할당 -> 큰 비용
![](../2016/images/understanding%20swift%20performance/2023-05-30-10-48-46.png)

- COW를 통해 간접적으로 저장하면 힙 할당하는데 비용 절감, 클래스를 사용하는 것과 비슷하게 참조 횟수를 발생시킴
![](../2016/images/understanding%20swift%20performance/2023-05-30-10-53-44.png)

## Generic
![](../2016/images/understanding%20swift%20performance/2023-05-30-11-13-04.png)
- 하나의 call context가 있기 때문에, Existential container를 사용하지 않는다.
- 대신 함수의 매개변수의 추가 인수로 VWT와 PWT를 모두 전달
- 함수 실행되는 동안 로컬 변수 만들 때 Swift는 VWT를 사용해 필요한 버퍼 힙 할당, 값 복사가 이뤄짐.

![](../2016/images/understanding%20swift%20performance/2023-05-30-11-17-33.png)
- 어떻게 로컬 파라미터에 필요한 메모리를 할당 ?
- valueBuffer는 3 word이다. 즉 그래서 작은 value면 버퍼에 넣을 수 있지만, 큰 value라면 힙에 저장되고 Existential container 내부에 포인터 저장
- 이 모든건 VWT의 사용을 위해 관리됨.

### Whole Module Optimization
![](../2016/images/understanding%20swift%20performance/2023-05-30-11-23-59.png)
- 전체 모듈 최적화를 통해 컴파일러는 두 파일을 하나의 단위로 함께 컴파일해서 생성부의 정의에 대한 insight를 얻게 되어 최적화 발생.

### Generic Stored Properties
![](../2016/images/understanding%20swift%20performance/2023-05-30-11-30-12.png)
- 제네릭 타입으로 구현하면 런타임에 변경 불가
- Pair 내부에 Line(), Line() 인라인에 할당됨 -> 추가 힙 할당 필요 없음
- 내부 저장된 프로퍼티에 다른 유형의 값을 저장할 수 없다.
- Specialized Generic - Struct Type
    - struct를 사용하는 것과 같은 동일한 성능을 갖는다. 
    - 참조 카운트가 없다.
    - 추가 컴파일러 최적화를 가능하게 하고, 런타임을 줄이는 static dispatch 기능
- Unspecialized Generic - Small Value
    - Value Buffer에 맞기 때문에 힙 할당 없음.
    - 값에 참조 없어 RC 없음
    - witenss table을 사용해서 모든 잠재적 call-site에서 하나의 구현을 공유

## Summary
![](../2016/images/understanding%20swift%20performance/2023-05-30-11-50-05.png)
- dynamic runtime 요구사항이 적은 추상화를 선택해라. static이 더 빠르고 최적화 가능
    - struct는 참조하지 않으므로 의도하지 않은 상태 공유가 없다. 
    - class를 사용해야 된다면, 새로 struct로 정의하거나 OOP Style로 구현
    - Generic: 정적 다형성을 사용해 generic code와 value type을 결합할 수 있고, 코드의 구현부를 공유할 수 있다.
    - Protocol Type: 동적 다형성이 필요한 경우, protocol type을 value type과 결합할 수 있다. 클래스를 사용하는데 비교적 빠른 코드이면서 value semantics을 유지할 수 있다.
- 큰 값인 경우 COW를 이용해 indirect storage를 사용해 문제 해결