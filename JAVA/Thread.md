# Thread

## 프로세스와 쓰레드
### 프로세스
* 실핼 중인 프로그램, 자원(메모리, cpu 등)과 쓰레드로 구성되어있다.
### 쓰레드
* 프로세스 내에서 실제 작업을 수행한다.
* 모든 프로세스는 최소한 하나의 쓰레드를 가지고 있다.
* 싱글 쓰레드 프로세스 = 자원 + 쓰레드
* 멀티 쓰레드 프로세스 = 자원 + 쓰레드 + ... + 쓰레드
* 하나의 새로운 프로세스를 생성하는 것보다 하나의 새로운 쓰레드를 생성하는 것이 더 적은 비용이 든다.

## 멀티쓰레드의 장단점
### 멀티쓰레드의 장점
* 시스템 자원을 보다 효율적으로 사용할 수 있다.
* 사용자에 대한 응답성이 향상된다.
* 작업이 분리되어 코드가 간결해진다.


### 멀티쓰레드의 단점
* 동기화(synchronization)에 주의해야 한다.
* 교착상태(dead-lock)가 발생하지 않도록 주의해야 한다.
* 각 쓰레드가 효율적으로 고르게 실행될 수 있게 해야 한다.
* 이와 같이 프로그래밍 할 때 고려해야 할 사항들이 많다.
* 멀티쓰레드는 context switching 때문에 싱글쓰레드보다 시간이 조금 더 소요된다.

## 쓰레드의 구현과 실행
* Thread클래스를 상속해서 구현하는 방법과, Runnable인터페이스를 구현해서 쓰레드를 구현하는 방법이 있다.
* 자바는 다중상속이 안되기 떄문에 Thread클래스를 상속받으면 다른 클래스를 상속받기 어렵다.
* 이러한 이유로 인터페이스를 이용하는게 더 유연하다.
### 1) Thread클래스를 상속
* Tread 클래스 상속해서 쓰레드를 구현
```java
class MyThread1 extends Thread{
    public void run(){  // Thread클래스의 run()을 오버라이딩
        ...
    }
}
```
* 쓰레드 생성 및 실행
```java
MyThread t1 = new MyThread();
t1.start()
```


### 2) Runnable 인터페이스을 구현
* Runnable 인터페이스 구현해서 쓰레드를 구현
```java
class MyThread2 implements Runnable{
    public void run(){  // Runnable인터페이스의 추상메서드 run()을 구현
        ...
    }
}
```
* 쓰레드 생성 및 실행
```java
Runnable r = new MyThread2();
Thread t2 = new Thread(r);
// Thread t2 = new Thread(new MyThread2());
t2.start();
```

### 3) 쓰레드의 실행
* 쓰레드를 생성한 후에 `start()`를 호출해야 쓰레드 작업을 시작한다.
* `start()`를 했다고 바로 실행되는건 아니다.
* 먼저 `start()`를 했다고 먼저 실행되는 것도 아니다.
* **OS스케쥴러가 실행순서를 결정**한다.
#### `start()`를 호출하는 이유
* `start()`는 새로운 호출스택을 생성하여 그 새로운 호출스택에서 `run()`이 동작하게 되는 것이다.
1. `main`메서드와 같은 호출스택에서 `start()`가 새로운 호출스택을 생성한다.
2. 새로운 호출스택에서 `run()`이 실행되어 서로 독립적으로 작업을 수행할 수 있게 된다.

### 4) main 쓰레드
* main 메서드의 코드를 수행하는 쓰레드이다.
* 쓰레드는 사용자 쓰레드와 데몬 쓰레드(보조 쓰레드) 두 종류가 있다.
* 실행 중인 사용자 쓰레드가 하나도 없을 때 프로그램은 종료된다.
* main쓰레드가 종료되도 다른 스레드가 실행 중이면 프로그램은 종료되지 않는다.

## 쓰레드의 I/O블락킹(blocking)
* I/O블락킹은 입력, 출력 시 작업을 중단하는 것을 말한다.
* 멀티 쓰레드를 이용하면 한 쓰레드에서 I/O블락킹 등으로 지연되는 동안 다른 쓰레드가 작업을 할 수 있으므로, 자원을 더 효율적으로 쓸 수 있다.

## 쓰레드의 우선순위
* 작업 중요도에 따라 쓰레드의 우선순위를 다르게 하여 특정 쓰레드가 더 많은 작업시간을 갖게 할 수 있다.
* 쓰레드의 우선순위는 1 ~ 10까지 정할 수 있다. (JVM에서 정해 놓음)
* 쓰레드 우선순위를 지정해 주지 않으면 우선순위는 5가 된다.
* `setPriority(int newPriority)` 쓰레드의 우선순위를 지정한 값으로 변경한다.
* `int getPriority()` 쓰레드의 우선순위를 반환한다.
* 작업의 중요도에 따라 우선순위를 설정했더라도 희망사항일 뿐이고, OS스케쥴러가 알아서 실행한다.


## 쓰레드 그룹
* 서로 관련된 쓰레드를 그룹으로 묶어서 다루기 위한 것이다.
* 모든 쓰레드는 반드시 하나의 쓰레드 그룹에 포함되어 있어야 한다.
* 쓰레드 그룹을 지정하지 않고 생성한 쓰레드는 main쓰레드 그룹에 속한다.
* 자신을 생성한 쓰레드(부모 쓰레드)의 그룹과 우선순위를 상속받는다.
* `getThreadGroup()` 쓰레드 자신이 속한 쓰레드 그룹을 반환한다. 

### 쓰레드 그룹의 메서드 
* `ThreadGroup(String name)` 지정된 이름의 새로운 쓰레드 그룹을 생성한다.
* `ThreadGroup(ThreadGroup parent, String name)` 지정된 쓰레드 그룹에 포함되는 새로운 쓰레드 그룹을 생성한다.
* `activeCount()` 쓰레드 그룹에 포합된 활성상태에 있는 쓰레드의 수를 반환한다.
* `activeGroupCount()` 쓰레드 그룹에 포합된 활성상태에 있는 쓰레드 그룹의 수를 반환한다.
* `checkAccess()` 현재 실행중인 쓰레드가 쓰레드 그룹을 변경할 권한이 있는지 체크한다.
* `destroy()` 쓰레드 그룹과 하위쓰레드 그룹까지 모두 삭제한다. (비어있어야 삭제 가능)
* `enumerate(Thread[] list, boolean recurse)` 쓰레드 그룹에 속한 쓰레드의 목록을 지정된 배열에 담고 그 개수를 반환한다.
* `enumerate(ThreadGroup[] list, blooean recurse)` 쓰레드 그룹에 속한 쓰레드 그룹의 목록을 지정된 배열에 담고 그 개수를 반환한다.
    * recurse의 값으로 true를 주면 하위쓰레드 그룹에 속한 하위쓰레드 그룹의 쓰레드와 쓰레드 그룹까지 배열에 담는다.
* `getMaxPriority()` 쓰레드 그룹의 최대우선순위를 반환한다.
* `getName()` 쓰레드 그룹의 이름을 상환한다.
* `getParent()`쓰레드 그룹의 상위 쓰레드 그룹을 반환한다.
* `interrupt()`쓰레드 그룹에 속한 모든 쓰레드를 interrupt한다.
* `isDaemon()` 쓰레드 그룹이 데몬 쓰레드 그룹인지 확인한다.
* `isDestroyed` 쓰레드 그룹이 삭제되었는지 확인한다.
* `list()` 쓰레드 그룹에 속한 쓰레드와 하위 쓰레드 그룹에 대한 정보를 출력한다.
* `parentOf(ThreadGroup g)` 지정된 쓰레드 그룹의 상위 쓰레드 그룹인지 확인한다.
* `setDaemon(boolean on)` 쓰레드 그룹을 데몬 쓰레드 그룹으로 설정/해제 한다.
* `setMaxPriority(int pri)` 쓰레드 그룹의 최대 우선순위를 설정한다.
 

## 데몬 쓰레드(daemon thread)
* 일반 쓰레드(non-daemon thread)의 작업을 돕는 보조적인 역할을 수행한다.
* 일반 쓰레드가 모두 종료되면 자동적으로 종료된다.
* 가비지 컬렉터, 자동저장, 화면 자동갱신 등에 사용된다.
* 무한루프와 조건문을 이용해서 실행 후 대기하다가 특정 조건이 만족되면 작업을 수행하고 다시 대기하도록 작성한다.
  * 일반 쓰레드가 종료되면 데몬 쓰레드도 종료되기 때문에 무한루프를 사용해도 된다.
  * 무한루프안에서 계속 실행되면 안되니, sleep 타임을 준다.
* `isDaemon()` 쓰레드가 데몬쓰레드인지 확인 후 boolean값으로 반환한다.(데몬이면 true)
* `setDaemon(boolean on)` 데몬 쓰레드 또는 사용자 쓰레드로 변경한다.(true면 데몬으로)
  * 반드시 start()를 호출하기 전에 실행되어야 한다.
  * 그렇지 않으면 IllegalThreadStateException이 발생한다.

## 쓰레드의 상태
|  순서   | 상태  | 설명                                                                                  |
|:-----:|----------------------------|-------------------------------------------------------------------------------------|
|   1   | NEW | 쓰레드가 생성되고 아직 start()가 호출되지 않은 상태이다.                                                 |
|   2   | RUNNABLE | 실행 중 또는 실행 가능한 상태이다.                                                                |
|   3   | BLOCKED  | 동기화블럭에 의해서 일시정지된 상태이다.(lock이 풀릴 때까지 기다리는 상태)                                        |
|   4   | WAITING.<br/>TIMED_WAITING | 쓰레드의 작업이 종료되지는 않았지만, 실행가능하지 않은 일시정지 상태이다.<br/>TIMED_WAITING은 일시정지 시간이 지정된 경우를 의미한다. |
|   5   | TERMINATED                 | 쓰레드의 작업이 종료된 상태이다.                                                                  |

## 쓰레드의 실행제어 메서드
* 쓰레드의 실행을 제어할 수 있는 메서드가 제공된다.
* 이러한 메서드들을 활용하여 보다 효율적인 프로그램을 작성할 수 있다.
### 1) sleep()
* **현재** 쓰레드를 지정된 시간동안 멈추게 한다.
* static 메서드이기 때문에 현재 쓰레드에 대해서만 사용 가능하다. (특정 쓰레드 지정 불가)
  * th1.sleep(2000) 이런식으로 사용 금지 (에러는 안나지만 오해할 수 있다)
* `sleep(long millis)` ms단위이다.
* `sleep(long millis, int nanos)` ns까지 조절할 수 있다. (잘안씀)
* 예외처리를 해야 한다.(InterruptedException이 발생하면 깨어난다.)
```java
try{
    Thread.sleep(1000, 500000);
} catch(InterruptedException e) {}
```
* 메서드로 만들어서 사용하면 편하다.
```java
void delay(long millis){
    try{
        Thread.sleep(millis);
    }catch(InterruptedException e){}
} 
```
### 2) interrupt()
* 대기상태(WAITING)인 쓰레드를 실행대기 상태(RUNNABLE)로 만든다.
* `interrupt()` 쓰레드의 interrupted 상태를 false에서 true로 변경한다
* `isInterrupted()` 쓰레드의 interrupted상태를 반환한다.
* `interrupted()` 현재 쓰레드의 interrupted상태를 반환하고, **false로 초기화**한다.
* interrupt()를 이용하면 응답성을 높일 수 있다.

### 3) suspend(), resume(), stop()
* `suspend()` 쓰레드의 실행을 일시정지 시킨다.
* `resume()` suspend()에 의해 일시 정지된 쓰레드를 실행대기상태로 만든다.
* `stop()` 쓰레드를 즉시 종료시킨다.
* 하지만 이 메서드들은 dead-lock(교착상태)을 일으킬수 있어 deprecated 되었다. (사용권장X)
* 아래와 같이 직접작성하여 구현하면 된다.
```java
class Test implements Runnable{
    volatile boolean suspended = false; // volatile은 쉽게 바뀌는 변수라는 의미
    volatile boolean suspended = false; // CPU가 메모리 내의 복사본을 사용안하고 원본을 사용함
    
    public void run(){
        while(!stopped) {       // stop이 false가 되야 완전 정지
            if(!suspended){     // suspended가 true가 되어도 while문은 계속 동작
                /*쓰레드가 수행할 내용*/
            }
        }
    }
    public void suspend() {suspended = true;}
    public void resume() {suspended = false;}
    public void stop() {stopped = true;}
} 
```

### 4) join()
* 지정된 시간동안 특정 쓰레드가 작업하는 것을 기다린다.
* `join()` 작업이 모두 끝날 때까지 기다린다.
* `join(long millis)` ms단위 동안 기다린다.
* `join(long millis, int nanos)` ns까지 조절할 수 있다.
* sleep()처럼 예외처리를 해야한다.(InterruptedException이 발생하면 작업을 재개한다.)
 
### 5) yield()
* `yield()` 남은 시간을 다음 쓰레드에게 양보하고, 현재 쓰레드는 실행대기한다.
* yiled()을 이용하면 효율을 높일 수 있다. (일시 정지된 시간동안 다른 쓰레드에게 양보)
* 하지만 yiled()는 OS스케쥴러에게 알려만 줄 뿐 OS스케쥴러가 정한다. (절대적인 사용은 아니다.)

## 쓰레드의 동기화
* 멀티 쓰레드 프로세스에서는 다른 쓰레드의 작업에 영향을 미칠 수 있다.
* 진행중인 작업이 다른 쓰레드에게 간섭받지 않게 하려면 **동기화**가 필요하다.
* 쓰레드에서의 동기화는 한 쓰레드가 진행중인 작업을 다른 쓰레드가 **간섭하지 못하게 막는 것을 의미**한다.
* 동기화를 하려면 간섭받지 않아야 하는 문장들을 **임계영역**으로 설정한다.
* 임계영역은 lock을 얻은 단 하나의 쓰레드만 출입 가능하다. (자바에서는 객체 1개에 락 1개)
* `synchronized`로 임계영역을 설정할 수 있다.
  * 메서드 전체를 임계영역으로 지정
    ```java
    public synchronized void 메서드(){
        ...
    }
    ```
  * 특정한 영역을 임계영역으로 지정 (임계 영역은 가능하면 영역을 좁게 하는게 좋다.)
    ```java
    synchronized(객체의 참조변수){
        ...
    }
    ```

### wait()과 notify()
* 동기화의 효율을 높이기 위해 wait()와 notify()를 사용한다.
* Object클래스에 정의되어 있으며, 동기화 블록 내에서만 사용할 수 있다.
* `wait()` 객체의 lock을 풀고 쓰레드를 해당 객체의 waiting pool에 넣는다.
* `notify()` waiting pool에서 대기중인 쓰레드 중의 하나를 깨운다.
* `notifyAll()` waiting pool에서 대기중인 모든 쓰레드를 깨운다.
* lock을 오래 쥐는 일이 없어지므로 효율성이 증가한다.
* wait()와 notify()의 단점은 호출되는 대상이 불분명하다.
  * 이러한 부분을 해소하기 위해 Lock & condition이라는 게 있다.






___
참고

남궁성의 정석코딩 자바의 정석 기초편 https://www.youtube.com/channel/UC1IsspG2U_SYK8tZoRsyvfg
