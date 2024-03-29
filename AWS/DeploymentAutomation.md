# Deployment Automation
## 배포 자동화?
* 배포 자동화란 한번의 클릭 혹은 명령어 입력을 통해 전체 배포 과정을 자동으로 진행하는 것을 의미한다.
* 배포 자동화로 수동적이고 반복적인 배포과정을 자동화함으로써 시간을 절약할 수 있다.
* 휴먼에러(Human Error)를 방지할 수 있다.
  * 사람이 수동적으로 배포 과정을 진행하는 중에 생길 수 있는 실수를 방지할 수 있다.
  * 만약 전에 했던 배포 과정과 비교하여 특정 과정을 생략하거나 다르게 진행하여 오류가 발생하는 것은 휴먼 에러이다.
  * 배포 자동화를 통해 배포 과정을 일관되게 진행하는 구조로 설계하여 이러한 휴먼 에러를 방지할 수 있다.
## Pipeline
* 파이프라인이란 실제 서비스로의 배포 과정을 연결하는 구조를 의미한다.
* 파이프라인은 전체 배포 과정을 여러 Stage(단계)들로 분리한다.
* 각 단계는 파이프라인 안에서 순차적으로 실행되며, 각 단계마다 주어진 Action(작업)들을 수행한다.
* 파이프라인을 여러 단계로 분리할 때, 대표적으로 쓰이는 Stage가 있다.
  * Source stage : 원격 저장소에 관리되고 있는 소스 코드에 변경 사항이 일어날 경우, 이를 감지하고 다음 단계로 전달하는 작업을 수행한다.
  * Build stage : Source stage에서 전달받은 코드를 컴파일, 빌드, 테스트하여 가공하며 Build stage를 거쳐 생성된 결과물을 다음단계로 전달하는 작업을 수행한다.
  * Deploy stage : Build stage로부터 전달받은 결과물을 실제 서비스에 반영하는 작업을 수행한다.
* 파이프라인의 stage는 상황과 필요에 따라 더 세분화 되거나 간소화 될 수 있다.

## CI/CD
### CI
* **지속적인 통합(Continuous Integration)**을 의미하며, 간단히 요약하면 빌드/테스트 자동화 과정이다.
* CI를 성공적으로 구현할 경우, 애플리케이션에 대한 새로운 코드 변경 사항이 정기적으로 빌드 및 테스트되어 공유 리포지토리에 통합(merge)된다.
* 이로인해 코드 검증에 들어가는 시간이 줄어들며, 개발 편의성이 증가한다.
* 또한 항상 테스트코드를 통과한 코드만이 레포지토리에 올라가기 때문에 좋은 코드 퀄리티를 유지할 수 있다.

### CD
* **지속적인 제공(Continuous Delivery)**의 의미와, **지속적인 배포(Continuous Deployment)**라는 의미가 있다.
* CI에서 Build되고 Test 된 후에 배포 단계에서 Release 할 준비 단계를 거치고 검증을 한 후 공유 리포지토리 등에 Release 하는 것이 지속적인 제공(Continuous Delivery)이다.
* 리포지토리 뿐만 아니라, 사종자가 사용할 수 있는 Production 레벨까지 자동으로 배포하는 것이 지속적인 배포(Continuous Deployment)이다.
* 이로인해 개발자들은 배포보다 개발에 더욱 신경을 쓸 수 있게 된다.
* 클릭 하나만으로도 수작업 없이 빌드, 테스트, 배포까지 자동으로 되는 것이다.


___
참고

codestates 교육