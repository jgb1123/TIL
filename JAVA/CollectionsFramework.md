# collections framework
## 1. collections framework란?
### 컬렉션(collection)
   * 여러 객체를 모아 놓은 것을 의미한다. 
### 프레임웍(framework)
   * **표준화, 정형화**된 체계적인 프로그래밍 방식이다.
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

## 3. Arrays
* 배열을 다루기 편리한 메서드들을 제공한다.
* `toString()`배열의 출력
* `copyOf()`, `copyOfRange()`배열의 복사
* `fill()`, `setAll()`배열 채우기
* `sort()`배열의 정렬
* `binarySearch()`배열의 검색 (정렬 후 이분검색)
* `deepToString()`다차원 배열의 출력 
* `deepEquals()` 다차원 배열의 비교
* `asList` 배열을 List로 변환 (변환된 List는 읽기전용) 


## 4. List
### 1) ArrayList
* 기존의 Vector를 개선한 것으로 구현원리와 기능적으로 동일하다. (Vector는 동기화 되어있음)
* List인터페이스를 구현하므로 저장순서가 유지되고 중복을 허용한다.
* 데이터의 저장공간으로 배열을 사용한다.
* 저장 공간을 초과한 객체들이 들어오면 자동으로 저장 공간이 늘어난다.
* 배열과 달리 타입의 안정성을 보장해주는 지네릭을 이용할 수 있다.
* 배열과 달리 객체 element만 담을 수 있다. (기본형으로 추가 시 오토 박싱 됨)

#### ArrayList 메서드

* `ArrayList()`  기본 생성자
* `ArrayList(Collection c)`  컬렉션에 저장되어 있는 객체들을 ArrayList로 만든다.
* `ArrayList(int initialCapacity)` 배열의 길이를 지정한다.
* `boolean` `add(Object o)` 객체를 리스트에 추가한다.
* `void` `add(int index, Object element)`  객체를 저장 시 저장위치를 정할 수 있다.
* `boolean` `addAll(Collection)`  컬렉션에 저장되어 있는 객체들을 리스트에 추가한다.
* `boolean` `addAll(int index, Collection c)`  컬렉션을 저장시 저장위치를 정할 수 있다.
* `boolean` `remove(Object o)`  객체를 리스트에서 삭제한다
* `Object` `remove(int index)`  특정 위치에 있는 객체를 삭제한다.
* `boolean` `removeAll(Collection c)`  컬렉션에 있는 객체들을 모두 삭제한다.
* `void` `clear()`  모든 객체를 삭제한다.
* `int` `indexOf(Object o)`  객체가 몇번 째 저장되어 있는지 찾아준다. 못찾으면 -1을 반환한다.
* `int` `lastIndexOf(Object o)`  indexOf와 달리 뒤에서 부터 찾는다.
* `boolean` `contains(Object o)`  이 객체를 포함하고 있는지 여부를 boolean으로 반환한다.
* `Object` `get(int index)`  그 위치에 있는 객체를 읽는다.
* `Object` `set(int index, Object element)`  그 위치에 있는 객체를 변경한다
* `List` `subList(int fromIndex, int toIndex)`  from~to까지 있는 인덱스를 뽑아 새로운 리스트로 만든다 (from부터 to 이전까지)
* `Object[]` `toArray()`  ArrayList의 객체배열을 반환한다.
* `boolean` `isEmpty()`  ArrayList가 비어있는지 여부를 boolean으로 반환한다
* `void` `trimToSize()`  빈 공간을 제거한다.
* `int` `size()`  ArrayList에 저장된 객체의 갯수를 반환한다.

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
* LinkedList는 **배열의 단점을 보완**했다.(크기변경 불가, 데이터 추가 및 삭제 오래걸림과 같은 단점)
* 배열과 달리 LinkedList는 불연속적으로 존재하는 데이터를 연결했다.
  ```java
  class Node{
      Node next;  // 다음 노드
      Object obj; // 데이터
  }
  ```
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

* 데이터의 잦은 변경이 예쌍된다면 LinkedList를 사용하면 된다.
* 데이터가 자주 변하지 않는다면 ArrayList를 사용하는 것이 좋다.

### 3) Stack vs Queue
#### Stack
* LIFO구조, 마지막에 저장된 것을 제일 먼저 꺼내게 된다.
* 저장(psuh), 추출(pop)
##### Stack 메서드
* `boolean` `empty()` Stack이 비어있는지 알려준다.
* `Object` `peak()` 맨 위 객체를 읽어온다. 객체를 꺼내지는 않는다. 비어있으면 예외가 발생한다.
* `Object` `pop()` 맨 위 객체를 꺼낸다. 비어있으면 예외가 발생한다.
* `Object` `push(Object Item)` 객체를 저장한다.
* `int` `search(Object o)` 주어진 객체를 찾아 그 위치를 반환한다. 못찾으면 -1을 반환하고, 위치는 1부터 시작한다.

##### Stack 활용
* 수식계산, 수식괄호검사, 워드프로세서의 undo/redo, 웹브라우저의 뒤로/앞으로

#### Queue
* FIFO구조, 제일 먼저 저장한 것을 제일 먼저 꺼내게 된다.
* offer(저장), poll(추출)

##### Queue 메서드
예외 발생
* `boolean` `add(Object o)`객체를 추가한다. 성공하면 true를 반환하고, 공간이 부족하면 예외가 발생한다.
* `Object` `remove()`객체를 꺼내서 반환한다. 비어있으면 예외가 발생한다.
* `Object` `element()` 삭제없이 요소를 읽어온다. 비어있으면 예외가 발생한다.

예외 발생안함 (**보통 이 메서드들을 많이 씀**)
* `boolean` `offer()` 객체를 추가한다. 성공하면 true, 실패하면 false를 반환한다
* `Object` `poll()` 객체를 꺼내서 반환한다. 비어있으면 null을 반환한다
* `Object` `peak()` 객체를 읽어온다.

##### Queue 활용
* 최근사용문서, 인쇄작업 대기목록, 버퍼(buffer)

## 5. Iterator, ListIterator, Enumeration
* 컬렉션에 저장된 데이터를 접근하는데 사용되는 인터페이스
* ListIterator는 Iterator의 접근성을 향상시킨 것이다.(단방향 -> 양방향)
* Enumeration은 Iterator의 구버전이고, 명령어만 조금 다르고 기능은 같다. (아직 남아있을 수 있기 때문에 알아만 두면 된다.)
### Iterator 메서드
* `boolean hasNext()` 읽어 올 요소가 남아있는지 확인한다. 있으면 true, 없으면 false를 반환한다.
* `Object next()` 다음 요소를 읽어온다. (사용하기 전에 hasNext()를 호출해서 읽어올 요소가 있는지 확인하는것이 안전)
* `previous()` **ListIterator**에만 있는 메서드다

### Iterator 활용
* 컬렉션에 저장된 요소들을 읽어오는 방법을 표준화 한 것이다.
* 컬렉션에 iterator()를 호출해서 Iterator를 구현한 객체를 얻어서 사용한다.
* Collection 인터페이스의 자손인 List와 Set 모두 사용 가능하다. (컬렉션 프레임웍이 변경되어도 사용 가능하다.)
* Iterator는 끝까지 가고 나면 다시 못돌아온다.(1회용이다)
* Map에는 Iterator가 없기 때문에 keySet(), entrySet(), values()들로 호출한 후 Iterator를 사용하면 된다.

```java
ArrayList<String> list1 = new ArrayList<String>();
        ...
Iterator<String> iterator = list1.iterator();
while(iterator.hasNext()){
    String str = iterator.next()
}        
```


## 6. Set
### 1) HashSet
* 순서가 없고 중복이 안된다.
* Set 인터페이스를 구현한 대표적인 컬렉션 클래스이다.
* 순서가 없기때문에 정렬이 되지 않고, 정렬을 하려면 list로 변환 후 정렬을 해야한다.
* 순서를 유지하려면 LinkedHashSet 클래스를 사용하면 된다.

#### HashSet 메서드
* `HashSet()` 기본 생성자
* `HashSet(Collection c)` 컬렉션에 저장되어 있는 객체들을 HashSet으로 만든다.
* `HashSet(int initialCapacity)` 초기 용량을 정해준다
* `HashSet(int initialCpapacity, float loadFactor)`loadFactor로 언제 용량을 늘려줄지 정한다. 
* `boolean` `add(Object o)` 객체를 추가한다
* `boolean` `addAll(Collection c)` 컬렉션에 저장되어있는 객체들을 추가한다. (합집합을 구할 때 사용한다)
* `boolean` `remove(Object o)` 객체를 삭제한다
* `boolean` `removeAll(Collection c)` 컬렉션에 있는 객체들을 모두 삭제한다. (차집합을 구할 때 사용한다)
* `boolean` `retainAll(Collection c)` 컬렉션에 있는거만 남기고 삭제한다. (교집합을 구할 때 사용한다)
* `void` `clear()` 모든 객체를 삭제한다
* `boolean` `contains(Object o)` 이 객체를 포함하고 있는지 여부를 boolean으로 반환한다.
* `boolean` `containsAll(Collection c)` 컬렉션에있는 객체들을 모두 포함하고 있는지 여부를 boolean으로 반환한다.
* `Iterator` `iterator()` 컬렉션에있는 요소를 읽어준다.
* `boolean` `isEmpty()` 비어있는지 여부를 boolean으로 반환한다.
* `int` `size()` 저장된 객체의 개수를 확인한다.
* `Object[]` `toArray()` 저장되어있는 객체들을 객체배열로 반환한다.

#### Hashset 활용
* HashSet은 객체를 저장하기전에 기존에 같은 객체가 있는지 확인한다.
* 같은 객체가 없으면 저장하고 있으면 저장하지 않는다.
* `add(Object o)`는 저장할 객체의 equlas()와 hashCode()를 호출한다.
* equals()와 hashCode()를 오버라이딩 하지 않으면 비교작업이 제대로 작동하지 않는다.
* 아래 예시와 같이 equlas()와 hashCod()를 오버라이딩 한다.
  ```java
  public int hashCode(){
    return Objects.hash(name, age);
  }
  
  public boolean equals(Object obj){
    if(!(obj instanceof Person)) return false;
    Person p = (Person)obj; //obg에는 name과 age라는 멤버가 없기 때문에 형변환을 해준다.
    return this.name.equals(p.name) && this.age==p.age;
  }
  ```
### 2) TreeSet
* **범위 탐색과 정렬**에 유리한 컬렉션 클래스이다.
* 이진 탐색 트리(binary search tree)로 구현되어있다.
* 이진트리는 모든 노드가 최대 2개의 하위 노드를 갖는다.
* 각 노드들은 나무형태로 연결되어있다. (LinkedList의 변형)
  ```java
    class TreeNode{
        TreeNode left;  //왼쪽 자식노드
        Object element; //저장할 객체
        Treenode right; //오른쪽 자식노드
    }
  ```
* 부모보다 작은 값은 왼쪽, 큰 값은 오른쪽에 저장한다. (이진 탐색 트리)
* 데이터가 많아질 수록 추가, 삭제에 시간이 더 오래 걸린다. (비교 횟수가 증가하기 때문)
* HashSet보다 데이터 추가, 삭제에 시간이 더 오래 걸린다.


#### TreeSet 메서드
* `add()`,`size()`,`remove()`,`isEmpty()`,`iterator()` 기존 collection들과 동일
* `TreeSet()` 기본 생성자
* `TreeSet(Collection c)` 컬렉션을 저장하는 TreeSet을 생성한다.
* `TreeSet(Comparator comp)` 주어진 정렬기준으로 정렬하는 TreeSet을 생성한다. 
  * **원래 비교기준이 필수**로 있어야 하고, 없으면 저장하는 객체의 comparable을 이용한다.
* `Object` `first()`정렬된 순서에서 첫 번째 객체를 반환한다.
* `Object` `last()`정렬된 순서에서 마지막 객체를 반환한다.
* `Object` `ceiling(Object o)` 객체와 같거나, 큰 값을 가진 가까운 객체를 반환한다.(이상 중 가까운)
* `Object` `floor(Object o)` 객체와 같거나, 작은 값을 가진 가까운 객체를 반환한다. (이하 중 가까운)
* `Object` `higher(Object o)` 객체보다 큰 값을 가진 가까운 객체를 반환한다. (초과 중 가까운)
* `Object` `lower(Object o )`객체보다 작은 값을 가진 가까운 객체를 반환한다. (미만 중 가까운)
* `SortedSet` `subSet(Object fromElement, Object toElement)` from이상 to미만의 결과를 반환한다. 
* `SortedSet` `HeadSet(Object toElement)` 객체보다 작은 값의 객체들을 반환한다.(미만 모두)
* `SortedSet` `tailSet(Object fromElement)`객체보다 큰 값의 객체들을 반환한다.(초과 모두)

#### tree traversal(트리 순회)
* 이진트리의 모든 노드를 한번씩 읽는 것을 트리 순회라고 한다.
* 전위, 중위, 후위, 레벨 순회 등이 있으며, **중위 순회하면 오름차순으로 정렬된다.**
* 이로 인해 TreeSet의 특징인 정렬 및 범위검색에 유리해진다.

전위 순회(Preorder) : Root -> Left -> Right

중위 순회(Inorder) : Left -> Root -> Right

후위 순회(Postorder) : Left -> Right -> Root 

레벨 순회(level order) : 맨 위 root층 부터 순서대로

## 7. Comparator, Comparable
* 객체 정렬에 필요한 메서드를 정의한 인터페이스
* Comparator **기본 정렬기준 외**에 다른 기준으로 정렬하고자 할때 사용 `compare(Object o1, Object o2)`
* Comparable **기본 정렬기준**을 구현하는데 사용 `comparTo(Object o)`
* `compare(Object o1, Object o2)`o1, o2 두 객체를 비교
* `CompareTo(Object o)` 주어진 객체 o를 자신(this)과 비교
* 같으면 0, 왼쪽이 크면 양수, 오른쪽이 크면 음수를 반환
* 정렬 기준을 반대로 하려면 정렬기준에 -1을 곱해준다.
* 예를 들면 sort()의 경우 `sort(Object[] a)`로 사용하려면 객체 안에 정렬기준이 있어야 한다.
* 만약 객체 안에 정렬 기준이 없으면 `sort(Object[] a, Comparator c)`와 같이 사용해야 한다.
```java
class Student implements Comparable<Student>{
    ...
    public int compareTo(Student s){
        return this.age - s.age;
    }
} 
```
```java
class Student implements Comparator<Student>{
    ...
    public int compare(Student s1, Student s2){
        return s1.age - s2.age;
    }
    
}
```
* 위 예시를 정렬기준으로 사용 시 return값이 양수일 경우(왼쪽이 클 경우), 두 원소의 위치를 교환한다.
  * 큰수가 오른쪽으로 가며 오름차순이 된다.



## 8. Map
* Map 인터페이스를 구현한다.
* 데이터를 키와 값의 쌍으로 저장한다
* 순서가 없고, 키는 중복이 안되고 값은 중복 된다.

### 1) HashMap
* Map인터페이스를 구현한 대표적인 컬렉션 클래스이다.
* 순서를 유지하려면 LinkedHashMap 클래스를 사용하면 된다.
* hashing기법으로 데이터를 저장하며, 데이터가 많아도 검색이 빠르다.
> **hashing**은 해시 함수을 이용해서 hash table에 데이터를 저장하고 읽어오는 것이다.
> 
> **hash table**은 배열과 linkedList가 조합된 형태이다. 배열의 접근성의 장점과, LinkedList의 변경에 유리하다는 장점을 섞은 것이다.
#### Hash table에 저장된 데이터를 가져오는 과정
1. 키로 해시 함수를 호출해서 hash code를 얻는다.
2. Hash code에 대응하는 LinkedList를 배열에서 찾는다
3. LinkedList에서 키와 일치하는 데이터를 찾는다.
* 해시 함수은 같은 키에 대해 항상 같은 hash code를 반환해야 한다. (다른 키여도 같은 값의 hash code를 반환할 수 있다.)

#### HashMap 메서드
* `HashMap()` 기본 생성자
* `HashMap(int initialCapacity)` 초기 용량을 정한다.(hash table의 배열의 용량)
* `HashMap(int initialCapacity, float loadFactor)` loadFactor로 언제 용량을 늘려줄 지 정한다.
* `HashMap(Map m)` 다른 맵을 해쉬맵으로 바꾼다.
* `Object` `put(Object key, Object value)` 데이터를 저장한다.
* `void` `putAll(Map m)` 맵에 있는 데이터들을 저장한다.
* `Object` `remove(Object key)`키를 삭제한다.
* `Object` `replace(Object key, Object value)`기존의 키를 새로운 값으로 저장한다.
* `boolean` `replace(Object key, ObjectoldValue, Object newValue)`기존의 키를 새로운 값으로 저장한다.
* `Set` `entrySet()`키와 값을 쌍으로 갖는 Set을 얻는다.
* `Set` `keySet()`키들만 갖는 Set을 얻는다.
* `Collection` `values()`값들만 갖는 collection을 얻는다.
* `Object` `get(Object key)` 키에 해당하는 값을 반환한다.
* `Object` `getOrDefault(Object key, Object defaultValue)` 키에 해당하는 값이 없으면 지정한 값(defaultValue)을 가져온다.
* `boolean` `containsKey(Object key)` 키가 포함되어 있는지 여부를 boolean값으로 반환한다.
* `boolean` `containsValue(Object value)` 값이 포함되어 있는지 여부를 boolean값으로 반환한다.
* `int` `size()` 저장된 개수를 확인한다.
* `boolean` `isEmpty()` 비어있는지 여부를 boolean으로 반환한다.
* `void` `clear()` 모두 삭제한다.
* `clone()` 복제한다.

### 2) TreeMap
* 범위검색과 정렬에 유리한 컬렉션 클래스이다.
* HashMap보다 데이터 추가, 삭제에 시간이 더 오래 걸린다. 
* Key, Value로 저장한다는 차이만 빼면 TreeSet과 거의 동일하다.

## 9. Collections 클래스
* 컬렉션을 위한 메서드들을 제공한다.
### 1) 컬렉션 채우기, 복사, 정렬, 검색
* fill(), copy(), sort(), binarySearch() 등

### 2) 컬렉션의 동기화
* synchronized___()
* Collection, List, Set, Map, SortedSet, SortedMap
```java
static Collection synchronizedCollection(Collection c)
static List synchronizedList(List list)
static Set synchronizedSet(Set s)
static Map synchronizedMap(Map m)
static SortedSet synchronizedSortedSet(SortedSet s)
static SortedMap synchronizedSortedMap(SortedMap m)
```
### 3) readOnly 컬렉션 만들기
* unmodifiable___()
* Collection, List, Set, Map, NavigableSet, SortedSet, NavigableMap, SortedMap
```java
static Collection unmodifiableCollection(Collection c)
static List unmodifiableList(List list)
static Set unmodifiableSet(Set s)
static Map unmodifiableMap(Map m)
static NavigableSet unmodifiableNavigableSet(NavigableSet s)
static SortedSet unmodifiableSortedSet(SortedSet s)
static NavigableMap unmodifiableNavigableMap(NavigableMap m)
static SortedMap unmodifiableSortedMap(SortedMap m)
```
### 4) singleton 컬렉션 만들기 (객체 1개만 저장)
* singleton___()
* List, Set, Map
```java
static List singletonList(Object o)
static Set singleton(Object o)		// 주의
static Map singletonMap(Object key, Object value)
```


### 5) 한 종류의 객체만 저장하는 컬렉션 만들기
* checked___()
* Collection, List, Set, Map, Queue, NavigableSet, SortedSet, NavigableMap, SortedMap
* **JDK1.5부턴 지네릭스를 이용**한다.
```java
static Collection checkedCollection(Collection c, Class type)
static List checkedList(List list, Class type)
static Set checkedSet(Set s, Class type)
static Map checkedMap(Map m, Class keyType, Class valueType)
static Queue checkedQueue(Queue queue, Class type)
static NavigableSet checkedNavigableSet(NavigableSet s, Class type)
static SortedSet checkedSortedSet(SortedSet s, Class type)
static NavigableMap checkedNavigableMap(NavigableMap m, Class keyType, Class valueType)
static SortedMap checkedSortedMap(SortedMap m, Class keyType, Class valueType)
```


---
참고

남궁성의 정석코딩 자바의 정석 기초편 https://www.youtube.com/channel/UC1IsspG2U_SYK8tZoRsyvfg
