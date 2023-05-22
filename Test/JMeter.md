# JMeter
## JMeter?
* Apache에서 만든 자바로 만들어진 웹 어플리케이션 부하 테스트 오픈소스이다.
### 부하 테스트?
* 해당 하드웨어 및 네트워크 환경에서 얼마나 많은 사용자가 동시에 사용할 수 있는지 테스트하는 것이다.

### 장점
* Apache에서 만든 오래된 툴로, 유명하고 자료가 많다.
* 다양한 프로토콜을 지원한다.
* GUI, email, DB, SSL 등 지원하는 기능과 플로그인이 많다.
* 시나리오 기반 테스트가 가능하다.

### 단점
* 결과는 리스너로 만들어 보는데, 모니터링이 불편하다.
  * 많은 리스너가 존재할 경우 설정을 일일이 해줘야 해서 귀찮다.
* 스레드 기반이라 성능 제약이 있다.

## 설치
* [Apache Jmeter 다운로드 페이지](https://jmeter.apache.org/download_jmeter.cgi)에서 설치를 한다.
* 압축을 푼 폴더 아래 bin폴더로 가서 jmeter실행하면 GUI가 나타난다.

## JMeter 용어
### Thread Group
* Thread Group은 모든 테스트 플랜 작성의 시작 지점으로, Thread Group 아래에 모든 컨트롤러와 샘플러가 위치한다.
* Thread Group에 실행하는 쓰레드 수, 램프업 시간, 테스트 수행 시간을 지정한다.
* 하나의 테스트 플랜에는 여러개의 Thread Group을 지정할 수 있다.

### Sampler
* JMeter가 대상 시스템에 요청해야 하는 정보를 설정한다.
* HTTP 프로토콜을 이용해 테스트한다면 HTTP RequestSampler를 추가하고, URL정보와 파라미터 값들을 설장한다.
* 각 샘플러는 해당 프로토콜에 맞는 속성값들을 정의하게 되어있고, 테스트 시 해당 프로토콜에 대한 이해 없이는 설정이 어렵다.

## Logical Controllers
* JMeter가 언제 서버에 요청을 전달할지를 결정한다.
* HTTP URL이 2개이고, 해당 요청의 순서가 존재한다면 HTTP Request Sampler를 2개 등록하고 Logical Controller 2개로 상관관계를 정의할 수 있다.

### Listener
* JMeter를 통해 테스트한 결과 정보나 상태 정보를 표시한다.
* 일반적으로는 Graph Result를 이용해 진행되는 추이를 그래프로 확인한다.
* 수집된 정보는 XML, CSV로도 저장이 가능하다.
