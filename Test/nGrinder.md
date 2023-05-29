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


## Agent 다운로드
* nGrinder Admin Web > admin > Download Agent로 들어가서 agent파일을 직접 다운로드하거나 wget으로 다운로드한다.
  * `wget http://localhost:{포트번호}/agent/download/ngrinder-agent-{version}-localhost.tar`
* 압축을 풀고 에이전트 폴더로 이동한다.
  * `tar -xvf ngrinder-agent-{version}-localhost.tar`
  * `cd ngrinder-agent`
* ngrinder-agent 폴더의 __agent.conf 파일에 컨트롤러의 호스트 명과 포트가 포함되어 있다.
  * `cat __agent.conf | grep agent.controller`
  ```
  common.start_mode=agent
  agent.controller_host=localhost
  agent.controller_port=16001
  agent.region=NONE
  ```
* `./run_agent.sh` 혹은 백그라운드 모드의 경우 `./run_agent_bg.sh`로 실행한다.

## 테스트
### 스크립트 작성
* nGrinder Admin Web > Script > +Create > Create a script로 들어간다.
* 스크립트 종류는 Groovy로 하고, 스크립트 이름을 정한다.
* URL to be tested에 테스트할 URL을 적는다.
* 스크립트를 생성 후 Validate를 클릭하고, 이상이 없으면 Save/Close를 클릭한다.

### Performance Test 생성하기
* Perfomance Test > Create Test로 들어가 원하는 테스트 설정을 한다.
* Test이름, Agent수, Vuser per agent, 스크립트 종류, 스크립트 이름, Duration등을 설정한다.
* 설정 후 Save and Start를 클릭한다.

### 테스트 결과
* 테스트 실행시에도 라이브로 테스트가 진행되는 것을 볼 수 있다. 
* 테스트 종료 이후에는 전체 테스트 Summary와 Detailed Report를 통해 더 자세하게 결과를 확인할 수 있다.

