# collections framework
## collections framework란?
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

## 핵심 인터페이스
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


## ArrayList
* 기존의 Vector를 개선한 것으로 구현원리와 기능적으로 동일하다.
* List인터페이스를 구현하므로 저장순서가 유지되고 중복을 허용한다.
* 데이터의 저장공간으로 배열을 사용한다.
### ArrayList 메서드
* ArrayList() : 기본 생성자
* ArrayList(Collection c) : 컬렉션에 저장되어있는 객체들을 ArrayList로 만든다.
* ArrayList(int initialCapacity) : 배열의 길이를 지정한다.
* add(Object o) : 객체를 리스트에 추가한다.
* add(int index, Object element) : 객체를 저장 시 저장위치를 정할 수 있다.
* addAll(Collection) : 컬렉션에 저장되어있는 객체들을 리스트에 추가한다.
* addAll(int index, Collection c) : 컬렉션을 저장시 저장위치를 정할 수 있다.
* remove(Object o) : 객체를 리스트에서 삭제한다
* remove(int index) : 특정 위치에있는 객체를 삭제한다.
* removeAll(Collection c) : 컬렉션에있는 객체들을 모두 삭제한다.
* clear() : 모든객체를 삭제한다.
* indexOf(Object o) : 객체가 몇번 째 저장되어있는지 찾아준다. 못찾으면 -1을 반환한다.
* lastIndexOf(Object o) : indexOf와 달리 뒤에서 부터 찾는다.
* contains(Object o) : 객체가 존재하는지를 여부를 boolean으로 반환한다.
* get(int index) : 그 위치에 있는 객체를 읽는다.
* set(int index, Object element) : 그 위치에있는 객체를 변경한다
* subList(int fromIndex, int toIndex) : from~to까지 있는 인덱스를 뽑아 새로운 리스트로 만든다 (from부터 to 이전까지)
* toArray() : ArrayList의 객체배열을 반환한다.
* isEmpty() : ArrayList가 비어있는지 여부를 boolean으로 반환한다
* trimToSize() : 빈공간을 제거한다.
* size() : ArrayList에 저장된 객체의 갯수를 반환한다.

### ArrayList에 저장된 객체의 삭제 과정
1. 삭제할 데이터 아래의 데이터를 한칸씩 위로 복사해서 삭제할 데이터를 덮어쓴다.
2. 데이터가 한칸씩 이동했기 떄문에 마지막 데이터는 null로 변경한다
3. 데이터가 삭제되어 데이터의 개수가 줄었으므로 size의 값을 감소시킨다.
* 되도록이면 1번이 일어나지 않도록 하는게 좋다.
* 마지막 객체부터 삭제하는게 좋다.

---
참고

남궁성의 정석코딩 자바의 정석 기초편 https://www.youtube.com/channel/UC1IsspG2U_SYK8tZoRsyvfg
