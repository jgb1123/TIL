# collections framework
## 1. collections framework란?
### 컬렉션(collection)
   * 여러 객체를 모아 놓은 것을 의미한다. 
### 프레임웍(framework)
   * 표준화, 정형화된 체계적인 프로그래밍 방식이다.
   * 생산성이 늘어나고 유지보수에 용이해진다.

### 컬렉션 프레임웍(collection framework)
   * 켈렉션을 다루기 위한 표준화된 프로그래밍 방식이다.
   * 컬렉션을 쉽고 편리하게 다룰 수 있는 다양한 클래스를 제공해준다.
   * java.util패키지에 포함되어있다.

### 컬렉션 클래스 colection class
   * 다수의 데이터를 저장할 수 있는 클래스 (ex: Vector, ArrayList, HashSet 등)

## 2. 핵심 인터페이스
### List
* 순서가 있는 데이터의 집합이다.
* 데이터의 중복을 허용한다.
* ex) 대기자 명단
* ArrayList, LinkedList, Stack, Vector 등이 있다.

### Set
* 순서를 유지하지 않는 데이터의 집합이다.
* 데이터의 중복을 허용하지 않는다.
* 예) 양의 정수집합, 소수의 집합
* HashSet, TreeSet 등이 있다.

### Map
* 키(key)와 값(value)의 쌍(pari)으로 이루어진 데이터의 집합이다.
* 순서는 유지되지 않으며, 키는 중복을 허용하지 않고 값은 중복을 허용한다.
* 예)우편번호, 전화번호의 지역번호, id와 password
* HashMap, TreeMap, Hashtable, Properties 등이 있다.

## 3. Iterator, ListIterator, Enumeration
* 컬렉션에 저장된 데이터를 접근하는데 사용되는 인터페이스
* ListIterator는 Iterator의 접근성을 향상시킨 것이다.(단방향 -> 양방향)
* Enumeration은 Iterator의 구버전이고, 명령어만 조금 다르고 기능은 같다. (아직 남아있을 수 있기 때문에 알아만 두면 된다.)
### Iterator 메서드
* `hasNext()` 읽어 올 요소가 남아있는지 확인한다. 있으면 true, 없으면 false를 반환한다.
* `next()` 다음 요소를 읽어온다. (사용하기 전에 hasNext()를 호출해서 읽어올 요소가 있는지 확인하는것이 안전)
* `previous()` **ListIterator**에만 있는 메서드다

### Iterator 활용
* 컬렉션에 저장된 요소들을 읽어오는 방법을 표준화 한 것이다.
* 컬렉션에 iterator()를 호출해서 Iterator를 구현한 객체를 얻어서 사용한다.
* Collection 인터페이스의 자손인 List와 Set 모두 사용 가능하다. (컬렉션 프레임웍이 변경되어도 사용 가능하다.)
* Iterator는 끝까지 가고 나면 다시 못돌아온다.(1회용이다)
* Map에는 Iterator가 없기 때문에 keySet(), entrySet(), values()들로 호출한 후 Iterator를 사용하면 된다.


## 4. List
### 1) ArrayList
* 기존의 Vector를 개선한 것으로 구현원리와 기능적으로 동일하다.
* List인터페이스를 구현하므로 저장순서가 유지되고 중복을 허용한다.
* 데이터의 저장공간으로 배열을 사용한다.
#### ArrayList 메서드
* `ArrayList()`  기본 생성자
* `ArrayList(Collection c)`  컬렉션에 저장되어있는 객체들을 ArrayList로 만든다.
* `ArrayList(int initialCapacity)` 배열의 길이를 지정한다.
* `add(Object o)` 객체를 리스트에 추가한다.
* `add(int index, Object element)`  객체를 저장 시 저장위치를 정할 수 있다.
* `addAll(Collection)`  컬렉션에 저장되어있는 객체들을 리스트에 추가한다.
* `addAll(int index, Collection c)`  컬렉션을 저장시 저장위치를 정할 수 있다.
* `remove(Object o)`  객체를 리스트에서 삭제한다
* `remove(int index)`  특정 위치에있는 객체를 삭제한다.
* `removeAll(Collection c)`  컬렉션에있는 객체들을 모두 삭제한다.
* `clear()`  모든객체를 삭제한다.
* `indexOf(Object o)`  객체가 몇번 째 저장되어있는지 찾아준다. 못찾으면 -1을 반환한다.
* `lastIndexOf(Object o)`  indexOf와 달리 뒤에서 부터 찾는다.
* `contains(Object o)`  객체가 존재하는지를 여부를 boolean으로 반환한다.
* `get(int index)`  그 위치에 있는 객체를 읽는다.
* `set(int index, Object element)`  그 위치에있는 객체를 변경한다
* `subList(int fromIndex, int toIndex)`  from~to까지 있는 인덱스를 뽑아 새로운 리스트로 만든다 (from부터 to 이전까지)
* `toArray()`  ArrayList의 객체배열을 반환한다.
* `isEmpty()`  ArrayList가 비어있는지 여부를 boolean으로 반환한다
* `trimToSize()`  빈공간을 제거한다.
* `size()`  ArrayList에 저장된 객체의 갯수를 반환한다.

#### ArrayList에 저장된 객체의 삭제 과정
1. 삭제할 데이터 아래의 데이터를 한칸씩 위로 복사해서 삭제할 데이터를 덮어쓴다.
2. 데이터가 한칸씩 이동했기 떄문에 마지막 데이터는 null로 변경한다
3. 데이터가 삭제되어 데이터의 개수가 줄었으므로 size의 값을 감소시킨다.
* 되도록이면 1번이 일어나지 않도록 하는게 좋다.
* 마지막 객체부터 삭제하는게 좋다.

### 2) LinkedList 
#### 배열의 특징
* 구조가 간단하고 데이터를 읽는데 걸리는 시간이 짧다. (장점)
* 크기를 변경할 수 없다. 애초에 큰 배열을 생성하면 메모리가 낭비된다. (단점)
    * 크기를 변경하려면 더 큰 배열을 생성하고, 복사하고, 참조를 변경해야 한다.
* 비 순차적인 데이터의 추가 및 삭제에 시간이 많이 걸린다. (단점)
    * 그러나 끝에 있는 데이터를 추가하거나 삭제하는건 빠르다 
 
 
#### LinkedList의 특징
* LinkedList는 **배열의 단점을 보완**했다.(크기변경 불가, 데이터 추가 및 삭제 오래걸림)
* 배열과 달리 LinkedList는 불연속적으로 존재하는 데이터를 연결했다.
* 불연속적으로 존재하는 데이터를 연결해 놓은 것이기 때문에, 데이터를 추가하거나 삭제할때 그 연결 구조만 바꾸면 된다.
* 데이터의 삭제는 단 한번의 참조변경만으로 가능하다
* 데이터의 추가는 한번의 Node객체 생성과 두번의 참조변경으로 가능하다.

* LinkedList는 배열에 비해 데이터 접근성이 나쁘다
  * doubly linked list와 double circle linked list로 접근성을 향상시켰고, 보통 doubly Linked list를 이용한다.
![](/images/LinkedList.PNG)
#### ArrayList(배열기반) vs LinkedList(연결기반)

| 컬렉션        |  접근시간  |  데이터추가/삭제  | 특징                                 |
|------------|:------:|:----------:|------------------------------------|
| ArrayList  |  빠르다   |    느리다     | 순차적인 추가삭제는 더 빠르다<br/>메모리사용이 비효율적이다 |
| LinkedList |  느리다   |    빠르다     | 데이터가 많을수록 접근성이 떨어진다                |


### 3) Stack, Queue
#### Stack
* LIFO구조, 마지막에 저장된 것을 제일 먼저 꺼내게 된다.
* 저장(psuh), 추출(pop)
##### Stack 메서드
* `empty()` Stack이 비어있는지 알려준다.
* `peak()` 맨 위 객체를 읽어온다. 객체를 꺼내지는 않는다. 비어있으면 예외가 발생한다.
* `pop()` 맨 위 객체를 꺼낸다. 비어있으면 예외가 발생한다.
* `push(Object Item)` 객체를 저장한다.
* `search(Object o)` 주어진 객체를 찾아 그 위치를 반환한다. 못찾으면 -1을 반환하고, 위치는 1부터 시작한다.

##### Stack 활용
* 수식계산, 수식괄호검사, 워드프로세서의 undo/redo, 웹브라우저의 뒤로/앞으로

#### Queue
* FIFO구조, 제일 먼저 저장한 것을 제일 먼저 꺼내게 된다.
* offer(저장), poll(추출)

##### Queue 메서드
예외 발생
* `add(Object o)`객체를 추가한다. 성공하면 true를 반환하고, 공간이 부족하면 예외가 발생한다.
* `remove()`객체를 꺼내서 반환한다. 비어있으면 예외가 발생한다.
* `element()` 삭제없이 요소를 읽어온다. 비어있으면 예외가 발생한다.

예외 발생안함 (**보통 이 메서드들을 많이 씀**)
* `offer()` 객체를 추가한다. 성공하면 true, 실패하면 false를 반환한다
* `poll()` 객체를 꺼내서 반환한다. 비어있으면 null을 반환한다
* `peak()` 객체를 읽어온다.

##### Queue 활용
* 최근사용문서, 인쇄작업 대기목록, 버퍼(buffer)




---
참고

남궁성의 정석코딩 자바의 정석 기초편 https://www.youtube.com/channel/UC1IsspG2U_SYK8tZoRsyvfg
