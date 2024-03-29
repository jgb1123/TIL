# CI/CD

## CI/CD?
* 코드 버전 관리를 하는 VCS 시스템(Git, SVN 등)에 PUSH가 되면 자동으로 테스트와 빌드가 수행되어 안정적인 배포파일을 만드는 과정을 CI(Continuous Integration)라고 한다.
* 이 빌드 결과를 자동으로 운영 서버에 무중단 배포까지 진행되는 과정을 CD(Continuous Deployment)라고 한다.
* 일반적으로는 CI만 구축되어 있지는 않고 CD까지 함께 구축된 경우가 대부분이다.

### CI/CD 등장 배경
* 현대 웹 서비스 개발에서는 하나의 프로젝트를 여러 개발자가 함께 개발을 진행한다.
* 각자가 개발한 코드가 합쳐야 할 때마다 많은 일을 해야했고, 실제로 매주 병합일을 정해서 이날은 코드를 합치는 일만 하기도 했다.
* 이런 수작업 때문에 생산성이 좋을 수가 없었고, 개발자들은 지속해서 코드가 통합되는 환경(CI)을 구축하게 되었다.
* 개발자가 각자 원하는 저장소로 푸시가 될 때마다 코드를 병합하고, 테스트 코드와 빌드를 수행하면서 자동으로 코드가 통합되어 더는 수동으로 코드를 통합할 필요가 없어졌으며, 개발자들은 개발에만 집중할 수 있게 되었다.
* CD 역시 마찬가지로 수십 대 수백 대의 서버에 개발자가 수동으로 배포를 해야 하는 상황이 오면서 더는 수동으로 배포할 수가 없어졌다.
* 따라서 이 또한 자동화하게 되었고, 마찬가지로 개발자들이 개발에만 집중할 수 있게 되었다.

### 고려 사항
* CI 도구를 도입했다고 해서 CI를 하고 있는 것은 아니다.
* 마틴 파울러는 CI에 대해 다음과 같은 4가지 규칙을 이야기한다.
  * 모든 소스 코드가 살아있고 누구든 현재의 소스에 접근할 수 있는 단일 지점을 유지할 것
  * 빌드 프로세스를 자동화해서 누구든 소스로부터 시스템을 빌드하는 단일 명령어를 사용할 수 있게 할 것
  * 테스팅을 자동화해서 단일 명령어로 언제든지 시스템에 대한 건전한 테스트 수트를 실행할 수 있게 할 것
  * 누구나 현재 실행 파일을 얻으면 지금까지 가장 완전한 실행 파일을 얻었다는 확인을 하게 할 것
* 지속적으로 통합하기 위해서 무엇보다 중요한 것은, 이 프로젝트가 완전한 상태임을 보장하기 위해 테스트 코드가 구현되어 있어야만 한다.

## CI/CD 툴
### 설치형 CI/CD 툴(Jenkins)
* 설치형 CI/CD 툴은 대표적으로 젠킨스(Jenkins)가 있다.
* 젠킨스는 개인 컴퓨터나 서비스가 실행되는 서버에 직접 다운로드하여 설치하여 사용한다.
* 윈도우, 맥, 리눅스에 모두 설치 가능하며, 젠킨스를 설치 후 실행하면 해당 컴퓨터나 서버의 주소로 크롬 등의 브라우저를 통해 접속할 수 있는 웹사이트가 열린다.
  * 서버의 아이피 주소의 젠킨스 기본포트 8080으로 접속하고, 지정한 아이디와 비밀번호를 입력하면 다양한 CI/CD 작업을 세팅할 수 있는 페이지가 표시되며, 이 웹사이트가 CI/CD를 설정하기 위한 작업 콘솔이다.
* 젠킨스에 새 자동화 작업을 생성하고 이를 Github, BitBucket, GitLab 등 프로젝트를 저장하는 서비스 계정에 연동을 한다.
  * 이 Github 저장소의 특정 Branch에 소스가 Push 될 때마다 젠킨스에서 이 서버로 코드들을 자동으로 젠킨스 전용 폴더로 다운로드 하게 된다.
* 코딩 작업을 하고, Github에 Push를 하면 젠킨스가 설치된 서버로 전송이 된다.
* 그러면 그 다음에 처리할 행동들을 정의하면 되고, 먼저 Github에서 가져온 코드에 문제가 없는지 테스트 코드에 지정된 테스트를 돌려서 확인하도록 만들면 된다.
  * Gradle을 사용하는 프로젝트라면 받아온 코드에 Gradle 명령어로 테스트를 수행하도록 할 수 있다.
  * 만약 테스트가 실패할 경우, 지정된 메일 주소로 이메일을 보내거나 슬랙 등으로 메시지를 전송하여 개발자에게 바로 알려줄 수 있다.
* 이전 작업들에 문제가 없다면 실행되도록 설정한 다음 작업들에서 프로젝트를 배포용 파일로 빌드한 다음 원하는 폴더로 옮겨서 실행되고 있던 서비스를 중지하고 이 파일로 새 서비스를 실행하는 명령어를 스크립트로 실행되도록 한다.
* 즉, 개발자는 코딩을 한 후 Git에 Push하기만 하면 서버로 옮겨서 테스트하고, 문제가 없을경우 빌드해서 배포하며 테스트 실패 시 보고하는 것도 포함하여 젠킨스가 다 알아서 해주게 된다.
* 젠킨스의 플러그인은 무궁무진하며, 플러그인을 잘 활용하면 AWS에 연동하여 도커 컨테이너로 올려주거나, 다수의 작업을 파이프라인으로 체계화 하거나, 젠킨스 화면도 꾸밀 수 있다.
* 젠킨스는 오픈소스이므로 무료로 사용 가능하다.
* 하지만 설치형이기 때문에, 이를 위한 EC2 인스턴스가 하나 더 필요하다.

### 클라우드형 CI/CD 툴(Travis CI, Circle CI, Team City)
* 젠킨스처럼 설치형으로 쓸 수 있지만, 기본적으로 클라우드로 제공한다.
* 클라우드에서 자동화 작업을 설정할 수 있으며, 사용 방법은 젠킨스와 비슷하다.
  * Github 등의 Git 저장소 계정과 연동하고 Repository 중 CI/CD를 진행할 프로젝트를 선탠하고 자동화로 처리할 작업을 세팅한다.
* Travis로 예를 들면, 프로젝트 최상위 폴더에 .travis.yml이라는 파일을 만들고 Travis에서 지정한 형식대로 어떤 작업들을 진행할지 작성하면 된다. (다른 CI/CD툴도 비슷함)
* 지정한 Branch에 Push가 들어오면 코드를 Github로부터 Travis의 서버로 가져와서 테스트를 한 후 성공이나 실패 여부를 이메일로 보내도록 만든다.
* 이름만 보면 CI만 된다고 생각할 수 있지만, CD도 가능하다.
* 이 클라우드 서비스는 유료이거나 제한적 무료이다. (가볍게는 무료로 이용할 수 있지만 상업용으로 넘어가면 이용료 발생)

### 그 외 CI/CD 툴(Github Actions)
* 설치형과 클라우드형이 아닌 새로운 형태의 CI/CD 서비스가 있다.
* Github이나 BitBucket, Gitlab 등에서 자체적으로 제공하는 기능이다.
  * 그중 많이쓰이는 것은 Github Actions이며, GitLab에는 CI/CD, BitBucket에는 Pipelines가 있다.
* Github에서 자신의 프로젝트가 있는 Repository에 들어가면 상단에 Actions라는 탭이 있다.
* 여기서 각 언어별로, AWS나 Azure 등의 배포처별로 소스코드를 빌드하고 테스트하고 배포 등을 할 수 있는 워크플로우의 다양한 템플릿이 있다.
  * 필요한 것들을 선택하여 동작을 만들어도 되고, 직접 yml파일로 작성해도 되고, 다른 사용자들이 작성하여 마켓플레이스에 올려놓은 워크플로우들을 다운로드하여 사용할 수 있다.
* Github에 push하는 등 지정한 이벤트가 발생하면 Github에서 제공하는 클라우드 컨테이너 공간에서 빌드, 테스트, 보고, 배포 등이 진행된다.
* 보통 이 서비스들은 작업의 총 시간을 기준으로 일정 시간까지는 무료이며, 그 이상은 유로 등의 가격 책정이 된다. (상업용은 돈을 지불해야 함)
* 이러한 Git 저장소 서비스들 이외에 AWS에서 자체적으로 제공하는 CodePipeline 같은 것도 있고, 정적 사이트를 github등에 올리면 바로 자신의 서버로 배포해주는 Netlify나 Vercel같은 배포 서비스도 있다.

## Travis CI
* Travis CI는 깃허브에서 제공하는 무료 CI 서비스이다.

### Travis CI 연동
#### Travis 웹서비스 설정
* Travis CI 공식 홈페이지에서 깃허브 계정으로 로그인 한 뒤 계정명->settings로 들어간다.
* 설정 페이지 아래쪽에 깃허브 저장소 검색창이 있꼬, 여기서 저장소 이름을 입력해서 찾은 다음 오른쪽의 상태바를 활성화시킨다.
* 활성화한 저장소를 클릭하면 저장서 빌드 히스토리 페이지로 이동하게 된다.
* 상세한 설정은 프로젝트의 yml파일로 진행한다.
#### 프로젝트 설정
* Travis CI의 상세한 설정은 프로젝트에 존재하는 .travis.yml 파일로 할 수 있다.
* 프로젝트의 build.gradle과 같은 위치에 .travis.yml 파일을 생성한 후 아래와 같은 코드를 추가한다.
  ```yaml
  language: java
  jdk:
    - openjdk8
  branches:
    only:
      - master
  # Travis CI 서버의 Home
  cache:
    directories:
      - '$HOME/.m2/repository'
      - '$HOME/.gradle'
  script: "./gradlew clean build"
  
  # CI 실행 완료 시 메일로 알람
  notifications:
    email:
      recipients:
        - 알림을 받을 메일
  ```
* 그 다음 master 브랜치에 커밋과 푸시를 하고, Travis CI 저장소 페이지를 확인해본다.
  * 빌드가 성공한 것이 확인되면 .travis.yml파일에 등록한 이메일을 확인해본다.

### Travis CI와 AWS S3 연동하기
#### S3?
* S3는 AWS에서 제공하는 일종의 파일 서버이다.
* 이미지 파일을 비롯한 정적 파일들을 관리하거나 배포 파일들을 관리하는 등의 기능을 지원한다.
  * 보통 이미지 업로드를 구현한다면 S3를 이용하여 구현하는 경우가 많다.
* 실제 배포는 AWS CodeDeploy라는 서비스를 이용하지만, S3연동이 필요한 이유는 Jar 파일을 전달하기 위해서이다.
  * CodeDeploy는 저장 기능이 없으므로, Travis CI가 빌드한 결과물을 받아서 CodeDeploy가 가져갈 수 있도록 보관할 수 있는 공간이 필요한데, 보통 이럴 때 AWS S3를 이용한다.
  * CodeDeploy가 빌드도 하고 배포도 할 수 있지만, CodeDeploy에서 모든 것을 하게 될 땐 항상 빌드를 하게 되므로 확장성이 많이 떨어진다. (빌드와 배포를 분리하는게 좋음)

#### AWS Key 발급
* 일반적으로 AWS 서비스에 외부 서비스가 접근할 수 없기 때문에, 접근 가능한 권한을 가진 Key를 생성해서 사용해야 한다.
* AWS에서는 이러한 인증과 관련된 기능을 제공하는 서비스인 IAM(Identity and Access Management)이 있다.
* IAM은 AWS에서 제공하는 서비스의 접근 방식과 권한을 관리한다.
* IAM을 통해 Travis CI가 AWS의 S3와 CodeDeploy에 접근할 수 있도록 해야 한다.
* AWS 웹 콘솔에서 IAM으로 이동하고, 사용자 -> 사용자 추가 버튼을 클릭한다.
* 생성할 사용자의 이름과 액세스 유형을 선택하는데, 액세스 유형은 프로그래밍 방식 액세스이다.
* 권한 설정 방식은 3개 중 기존 정책 직접 연결을 선택한다.
* s3full로 검색하여 체크하고 다음 권한으로 CodeDeployFull을 검색하여 체크한다.
  * 실무에서는 권한도 S3와 CodeDeploy를 분리해서 관리하기도 한다.
* 태그는 Name 값을 지정하는데 본인이 인지 기능한 정도의 이름으로 만든다.
* 마지막으로 본인이 생성한 권한 설정 항목을 확인하고, 생성이 완료되면 액세스 키와 비밀 액세스 키가 생성된다. (Travis CI에서 사용될 키)

#### Travis CI에 키 등록
* Travis CI의 설정 화면으로 이동한다.
* 설정 항목 중 Environment Variables에서, AWS_ACCESS_KEY와 AWS_SECRET_KET를 변수로 해서 IAM 사용자에게 발급받은 키 값들을 등록한다.
* 여기에 등록된 값들은 이제 .travis.yml에서 $AWS_ACCESS_KEY, $AWS_SECRET_KEY라는 이름으로 사용할 수 있다.

#### S3 버킷 생성
* Travis CI에서 생성된 Build 파일을 저장하도록 구성하며, S3에 저장된 Build 파일은 이후 AWS의 CodeDeploy에서 배포할 파일로 가져가도록 구성한다.
* AWS에서 S3로 이동하여 버킷을 생성하는데, 버킷 명은 배포할 Zip파일이 모여있는 장소임을 의미하도록 짓는게 좋다.
* 버전관리는 별 다른 설정 할 것 없이 넘어가도 된다.
* 버킷의 보안과 권한 설정 부분에서 퍼블릭 액세스는 모두 차단을 한다.
  * 실제 서비스에서 할 때는 Jar파일이 퍼블릭일 경우 누구나 내려받을 수 있어 코드나 설정값, 주요 키값들이 모두 탈취될수 있다.
  * 또한 퍼블릭이 아니더라도 IAM 사용자로 발급받은 키를 사용하니 접근이 가능하다.

#### .travis.yml 추가
* Travis CI에서 빌드하여 만든 Jar 파일을 S3에 올릴 수 있도록 .travis.yml에 코드를 추가한다.
```yaml
...
before_deploy:
  - zip -r freelec-springboot2-webservice *
  - mkdir -p deploy
  - mv freelec-springboot2-webservice.zip deploy/freelec-springboot2-webservice.zip

deploy:
  - provider: s3
    access_key_id: $AWS_ACCESS_KEY # Travis repo settings에 설정된 값
    secret_access_key: $AWS_SECRET_KEY # Travis repo settings에 설정된 값
    bucket: freelec-springboot-build # S3 버킷
    region: ap-northeast-2
    skip_cleanup: true
    acl: private # zip 파일 접근을 private으로
    local_dir: deploy # before_deploy에서 생성한 디렉토리
    wait-until-deployed: true
...
```
* 설정이 다 되고 깃허브로 PUSH하면, Travis CI에서 자동으로 빌드가 진행되는 것을 확인하고 모든 빌드가 성공하는지 확인한다.
* S3버킷을 가보면 업로드가 성공한 것을 확인할 수 있다.

### Travis CI와 AWS S3, CodeDeploy 연동하기
#### EC2에 IAM 추가하기
* IAM에서 역할 -> 역할 만들기를 클릭한다.
  * EC2에서 사용할 것 이기 때문에 사용자가 아닌 역할로 처리한다.
* 서비스 선택에서는 AWS 서비스 -> EC2를 선택한다.
* 정책에선 검색하여 AmazonEC2RoleforAWSCodeDeploy를 선택한다.
* 태그는 본인이 원하는 이름으로 지으면 된다.
* 역할의 이름을 등록하고, 나머지 등록 정보를 확인한다.
* EC2 인스턴스 목록으로 이동 후 본인의 인스턴스를 오른쪽 버튼으로 인스턴스 설정->IAM 역할 연결/바꾸기를 클릭한다.
* 방금 생성한 역할을 선택하고, 선택이 완료되면 해당 EC2 인스턴스를 재부팅한다.
  * 재부팅을 해야만 역할이 정상적으로 적용된다.

#### CodeDeploy 에이전트 설치
* EC에 접속한다.
* `aws s3 cp s3://aws-codedeploy-ap-northeast-2/latest/install . --region ap -northeast-2`
* `chmod +x ./install && sudo ./install auto` install 파일에 실행 권한이 없으니 실행권한을 추가하고 install파일로 설치를 진행한다. (만약 설치가 안되면 ruby를 설치하면 됨)
* `sudo service codedeploy-agent status` Agent가 정상적으로 실행되고 있는지 상태 검사
* `The AWS CodeDeploy agent is running as PID xxx`라고 출력되면 정상이다.

#### CodeDeploy를 위한 권한 설정
* CodeDeploy에서 EC2에 접근하려면 역시 권한이 필요하며, AWS의 서비스이니 IAM 역할을 생성한다.
* 서비스는 AWS 서비스 -> CodeDeploy를 선택한다.
* CodeDeploy는 권한이 하나뿐이라 선택 없이 넘어가면 된다.
* 태그는 본인이 원하는 이름으로 지으면 된다.
* CodeDeploy를 위한 역할 이름과 선택 항목들을 확인하고 생성 완료를 한다.

#### CodeDeploy 생성
* CodeDeploy는 AWS의 배포 삼형 중 하나이다.
  * Code Commit : 깃허브와 같은 코드 저장소의 역할을 하지만, 대부분 깃허브를 사용한다.
  * Code Build : Travis CI와 마찬가지로 빌드용 서비스이며, 멀티 모듈을 배포해야 하는 경우 사용 해볼만 하지만 규모가 있는 서비스에서는 대부분 젠킨스/팀시티 등을 이용한다.
  * CodeDeploy : AWS의 배포 서비스로 CodeDeploy는 대체재가 없다.
* CodeDeploy 서비스로 이동해 애플리케이션 생성을 클릭한다.
* 생성할 CodeDeploy의 이름과 컴퓨팅 플랫폼을 선택하는데, 컴퓨팅 플랫폼은 EC2/온프레미스를 선택하면 된다.
* 생성이 완료되면 배포 그룹을 생성하라는 메시지를 볼 수 있고, 화면 중앙의 배포 그룹 생성을 클릭한다.
* 배포 그룹 이름과 서비스 역할을 등록하는데, 서비스 역할은 좀 전에 생성한 CodeDeploy용 IAM 역할을 선택한다.
* 배포 유형에서는 현재 위치를 선택하고, 만약 배포할 서비스가 2대 이상이라면 블루/그린을 선택하면 된다.
* 환경 구성에서는 Amazon EC2 인스턴스에 체크한다.
* 마지막으로 배포 구성은 CodeDeployDefault.AllAtOnce를 선택하고 로드 밸런싱은 체크를 해제한다.
  * 배포 구성의 경우, 지금은 1대 서버로 실습중이기 때문에 전체 배포하는 옵션으로 선택했다.

#### Travis CI, S3, CodeDeploy 연동
* `mkdir ~/app/step2 && mkdir ~/app/step2/zip`S3에서 넘겨줄 zip 파일을 저장할 디렉토리를 만든다.
* Travis CI의 Build가 끝나면 S3에 zip파일이 전송되고, 이 zip 파일은 /home/ec2-user/app/step2/zip로 복사되어 압축이 풀어질 것이다.
* AWS CodeDeploy의 설정은 appspec.yml로 진행한다.
```yaml
version: 0.0  # CodeDeploy의 버전으로, 프로젝트 버전이 아니므로 0.0외에 다른 버전을 사용하면 오류가 발생
os: linux
files:
  - source: / # CodeDeploy에서 전달해 준 파일 중 destination으로 이동시킬 대상을 지정하는데, 루트경로(/)를 지정하면 전체 파일을 의미
    destination: /home/ec2-user/app/step2/zip/  # source에서 지정된 파일을 받을 위치로, 이후 Jar를 실행하는 등은 destination에서 옮긴 파일들로 진행 됨
    overwrite: yes  # 기존에 파일들이 있으면 덮어쓸지를 결정
```

* travis.yml에도 CodeDeploy 내용을 추가한다.
```yaml
deploy:
  ...
  
  -provider: codedeploy
   access_key_id: $AWS_ACCESS_KEY # travis repo settings에 설정된 값
   secret_access_key: $AWS_SECRET_KEY # travis repo settings에 설정된 값
   
   bucket: freelec-springboot-build # S3 버킷
   key: freelec-springboot2-webservice.zip # 빌드 파일을 압축해서 전달
   
   bundle_type: zip # 압축 확장자
   application: freelec-springboot2-webservice  # 웹 콘솔에서 등록한 CodeDeploy 애플리케이션
   
   deployment_group: freelec-springboot2-webservice-group # 웹 콘솔에서 등록한 CodeDeploy 배포 그룹
   
   region: ap-northeast-2
   wait-until-deployed: true
```
* 그리고 나서 커밋하고 깃허브에 푸쉬하면 Travis CI가 자동으로 시작된다.
* `cd /home/ec2-user/app/step2/zip` 해당 폴더에서
* `ll` 파일들이 잘 왔는지 확인
* 파일이 잘 도착했다면 Travis CI와 S3, CodeDeploy가 연동이 완료 된 것이다.

### 배포 자동화 구성
* 실제 Jar를 배포하여 실행을 해야한다.

#### deploy.sh 파일 추가
* step2 환경에서 실행될 deploy.sh 파일을 생성한다.
```shell
#!/bin/bash

REPOSITORY=/home/ec2-user/app/step2
PROJECT_NAME=freelec-springboot2-webservice

echo "> Build 파일 복사"

cp $REPOSITORY/zip/*.jar $REPOSITORY/

echo "> 현재 구동중인 애플리케이션 pid 확인"

CURRENT_PID=$(pgrep -fl freelec-springboot2-webservice | grep jar | awk '{print $1}') # 

echo "현재 구동중인 어플리케이션 pid: $CURRENT_PID"

if [ -z "$CURRENT_PID" ]; then
    echo "> 현재 구동중인 애플리케이션이 없으므로 종료하지 않습니다."
else
    echo "> kill -15 $CURRENT_PID"
    kill -15 $CURRENT_PID
    sleep 5
fi

echo "> 새 어플리케이션 배포"

JAR_NAME=$(ls -tr $REPOSITORY/*.jar | tail -n 1)

echo "> JAR Name: $JAR_NAME"

echo "> $JAR_NAME 에 실행권한 추가"

chmod +x $JAR_NAME

echo "> $JAR_NAME 실행"

nohup java -jar \
    -Dspring.config.location=classpath:/application.properties,classpath:/application-real.properties,/home/ec2-user/app/application-oauth.properties,/home/ec2-user/app/application-real-db.properties \
    -Dspring.profiles.active=real \
    $JAR_NAME > $REPOSITORY/nohup.out 2>&1 &
```

#### .travis.yml 파일 수정
* 현재는 설정으로는 모든 파일을 zip파일로 만드는데, 실제로 필요한 파일들은 Jar, appspec.yml, 배포를 위한 스크립트들이다.
* 이 외 나머지는 배포에 필요하지 않으니 포함하지 않도록 수정한다.
```yaml
before_deploy:
  - mkdir -p before-deploy # zip에 포함시킬 파일들을 담을 디렉토리 생성, Travis CI는 S3로 특정 파일만 업로드가 안됨(디렉토리 단위로만 업로드 가능)
  - cp scripts/*.sh before-deploy/
  - cp appspec.yml before-deploy/
  - cp build/libs/*.jar before-deploy/
  - cd before-deploy && zip -r before-deploy * # before-deploy로 이동후 전체 압축
  - cd ../ && mkdir -p deploy # 상위 디렉토리로 이동후 deploy 디렉토리 생성
  - mv before-deploy/before-deploy.zip deploy/freelec-springboot2-webservice.zip # deploy로 zip파일 이동
```

#### appspec.yml 파일 수정
* 아래 코드를 추가한다.
```yaml
permissions:
  - object: /
    pattern: "**"
    owner: ec2-user
    group: ec2-user

hooks:
  ApplicationStart:
    - location: deploy.sh
      timeout: 60
      runas: ec2-user
```
* 여기까지 설정 후 깃허브로 커밋 및 푸쉬를 하고, Travis CI에서 성공 메시지를 확인하면 CodeDeploy에서의 배포가 성공한 것이다.

### CodeDeploy 로그 확인
* AWS가 지원하는 서비스에서는 오류가 발생했을 때 로그 찾는 방법을 모르면 오류를 해결하기 힘들다.
* CodeDeploy에 관한 대부분 내용은 /opt/codedeploy-agent/deployment-root에 있다.
* 해당 디렉토리로 이동한 뒤 목록(`ll`)을 확인해보면 deployment-logs라는 디렉토리가 있고, 해당 디렉토리 안에 codedeploy-agent-deployments.log파일이 로그 파일이다.



___
참고

이동욱, 스프링 부트와 AWS로 혼자 구현하는 웹 서비스

https://www.youtube.com/watch?v=UbI0Q_9epDM
