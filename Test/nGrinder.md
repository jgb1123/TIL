# nGrinder
## JMeter와의 차이
* JMeter는 단일 컴퓨터에서 수행되는 반면, nGrinder는 컨트롤러 및 에이전트로 구성된 분산 아키텍처로 수행된다.
* nGrinder는 jython 또는 Groovy와 같은 스크립트 언어를 사용하여 스크립트를 작성한다.
* 클라우드 환경에서도 실행 가능하다.

## nGrinder Architecture
* nGrinder는 Controller, Agent, Target 서버로 나누어져 있다.
### Controller
* 테스트 스크립트를 작성하고 agent에 부하 발생 명령을 해 테스트를 시작할 수 있도록 하는 서버이다.
* 부하 테스트를 위한 웹 기반이 GUI를 제공한다.
* 사용자가 스크립트 생성 또는 변경할 수 있도록 한다.
* 테스트를 실시하고 테스트 결과를 모니터링 할 수 있다.

### Agent
* Controller의 명령을 받아 실제 부하를 발생시키는 서버이다.
* 프로세스와 쓰레드 수를 조정하며 가상 사용자를 생성한다.
* controller에서 실행한 테스트 스크립트에 따라 동작하며, target 서버에 부하를 발생시킨다.

### Target
* 부하테스트의 대상이 될 서버이다.
* nGrinder-Monitor를 설치하면 현재 서버의 상태를 Controller에서 모니터링 할 수 있다.

### 서버 구축
* Controller, Agent, Target 서버는 각각 구축하는 것이 좋다.
* 세가지 요소들을 하나의 서버로 구동한다면 서버가 온전히 성능테스트만을 위해 자원을 사용할 수 없게 된다.
* 따라서 정확한 수치를 산출해내기 어렵기 때문이다. 

## 설치 및 접속
* [nGrinder 설치 페이지](https://github.com/naver/ngrinder/releases)
* 위 페이지에서 직접 다운로드 하거나, wget으로 다운로드 한다.
  * `wget https://github.com/naver/ngrinder/releases/download/ngrinder-3.5.8-20221230/ngrinder-controller-3.5.8.war`
* war파일이 있는 위치에서 파일을 실행하는데, port번호를 사용자가 직접 설정할 수 있다.
  * `java -jar ngrinder-controller-{version}.war --port={포트번호}`
* 초기 계정의 ID, PW는 admin, admin이므로 해당 ID와 PW로 접속한다.
  * nGrinder Admin Web > admin > User Management에서 admin이외의 불필요한 계정은 모두 삭제한다.
  * admin클릭 후 Change Password를 통해 안전한 비밀번호로 변경한다.
