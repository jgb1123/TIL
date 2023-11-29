# RDS
## RDS?
* 웹 서비스의 백엔드를 다룬다고 했을 때, 애플리케이션 코드를 작성하는 것 만큼 중요한 것이 데이터베이스를 다루는 일이다.
* 규모 있는 회사에서는 데이터베이스를 전문적으로 처리하는 DBA라는 직군이 있기 때문에, 상대적으로 개발자가 데이터베이스를 전문적으로 다룰 일이 적다.
* 하지만 이런 상황은 대용량/대량의 데이터를 다루기 때문에 전문성이 필요한 것이고, 백엔드 개발자가 데이터베이스를 몰라도 된다는 의미는 아니다.
* AWS에서는 관리형 서비스인 RDS를 제공한다.
* RDS는 AWS에서 지원하는 클라우드 기반 관계형 데이터베이스로, 하드웨어 프로비저닝, 데이터베이스 설정, 패치 및 백업과 같이 잦은 운영 작업을 자동화하여 개발자가 개발에 집중할 수 있게 지원하는 서비스이다.
* 조정 가능한 용량을 지원하여 예상치 못한 양의 데이터가 쌓여도 비용만 추가로 내면 정상적으로 서비스가 가능하다는 장점도 있다.

## RDS 인스턴스 생성하기
### DBMS 선택
* RDS 대시보드에서 데이터베이스 생성 버튼을 누른다.
* RDS 생성 과정이 진행되는데, 본인이 사용하고 싶은(혹은 가장 잘 사용하는)데이터베이스를 고르면 된다.
* 프리티어의 경우, MariaDB를 추천한다. (가격, Amazon Aurora 교체 용이성)
* 상용 데이터베이스인 오라클, MSSQL이 오픈소스인 MySQL, MariaDB, PostgreSQL보다는 동일한 사양 대비 가격이 더 높다.
* Amazon Aurora 교체 용이성때문으로, Amazon Aurora는 RDS MySQL 대비 5배, RDS PostgreSQL보다 3배의 성능을 제공하며, AWS에서 직접 엔지니어링 하고있다.
    * 일단은 MariaDB로 시작하고, 차후 서비스 규모가 커지면 Aurora로 교체하면 된다.

### 사용 사례 선택
* 만약 프리티어로 이용하고자 하면 프리티어로 선택하면 된다.

### 상세 설정
* 스토리지 유형은 범용(SSD), 할당된 스토리지는 20GiB, DB 인스턴스 이름과 사용자 정보를 등록하면 된다.
* 사용자 정보로 실제 데이터베이스 접근하게 되니 메모해 놓는게 좋다.

### 네트워크 및 보안
* VPC는 Default VPC(vpc-c61de2ad), 서브넷 그룹은 default로 설정한다.
* 퍼블릭 액세스는 예로 변경한다. (이후 보안 그룹에서 지정된 IP만 접근하도록 막을 예정)

### 데이터베이스 옵션
* 포트는 3306, DB 파라미터 그룹은 default.mariadb10.2, 옵션 그룹은 default:mariadb-10-2로 설정한다. (버전은 알맞게, 파라미터 그룹의 변경은 이후에 진행)
* 완료 버튼을 누르면 생성과정이 진행되며, 세부 정보 보기를 통해 상세 정보를 확인할 수 있다.

## RDS 운영환경에 맞는 파라미터 설정하기
* RDS를 처음 생성하면 몇 가지 설정을 필수로 해야 한다.
  * 타임존
  * Character Set
  * Max Connection
### 파라미터 그룹 생성
* 카테고리에서 파라미터 그룹 탭을 통해 이동하면 된다.
* 파라미터 그룹을 생성하는데, DB엔진을 선택하는 항목에서, 방금 생성한 MariaDB와 같은 버전을 맞춰 생성한다.
* 생성이 완료되면 파라미터 그룹 목록 창에 새로 생성된 그룹을 볼 수 있으며, 해당 파라미트 그룹을 클릭하여 파라미터 편집 버튼을 눌러 편집 모드로 전환한다.

### 타임존
* time_zone을 검색하여 Asia/Seoul을 선택한다.

### Character Set
* Character Set은 항목이 많으며, 아래 8개 항목 중 character 항목들은 모두 utf8mb4로, collation 항목들은 utf8mb4_general_ci로 변경해야 한다.
  * character_set_client
  * character_set_connection
  * character_set_database
  * character_set_filesystem
  * character_set_results
  * character_set_server
  * collation_connection
  * collation_server
* utf8과 utf8mb4의 차이는 이모지 저장 가능 여부이다.
  * 보편적으로는 utf8mb4를 많이 사용한다.

### Max Connection
* RDS의 Max Connection은 인스턴스 사양에 따라 자동으로 정해진다.
* 프리티어 사양으로는 약 60개의 커넥션만 가능하기 때문에 더 큰값으로 지정해놓고,RDS 사양을 높이게 된다면 다시 지정하면 된다.

### 파라미터 그룹 데이터베이스에 연결
* 옵션 항목에서 DB 파라미터 그룹은 default로 설정했었는데, DB 파라미터 그룹을 방금 생성한 신규 파라미터 그룹으로 변경하면 된다.
* 즉시 적용을 할 수도 있고, 예약시간을 걸어두어 새벽 시간대에 진행할 수도 있다.
* 만약 파라미터 그룹이 제대로 반영되지 않았을 경우에는 데이터베이스를 재부팅하면 된다.

## 로컬 PC에서 RDS 접속
* 로컬 PC에서 RDS로 접근하기 위해서 RDS의 보안 그룹에 본인 PC의 IP를 추가해야 한다.
* RDS 세부정보 페이지에서 보안그룹으로 들어가서, EC2에 사용된 보안 그룹의 그룹 ID를 복사한다.
* 해당 그룹 ID와 로컬 PC의 IP를 RDS 보안그룹의 인바운드로 추가한다.
  * 인바운드 규칙 유형에서 MYSQ/Aurora를 선택하면 자동으로 3306포트가 선택된다.
  * 현재 내 PC의 IP를 등록하고, EC2의 보안 그룹도 추가한다.
    * EC2와 RDS간에 접근이 가능해지며, EC2의 경우 2대 이상이 될 수도 있는데 매번 IP를 등록할 수 없으니 보편적으로 이렇게 그룹간에 연동을 진행한다.

### 인텔리제이 Database 플러그인
* 이 플러그인은 인텔리제이 공식 플러그인은 아니고, 유료 버전을 사용하면 강력한 기능의 데이터베이스 기능을 사용할 수 있따.
* 인텔리제이에서 database 플러그인을 검색 후 설치한다.
* 인텔리제이 재 시작 후 Action 검색(Ctrl + Shift + a)으로 Database Browser를 실행한다.
* 생성한 RDS의 접속 정보를 차례로 등록한다.
* Test Connection을 클릭해 연결 테스트를 해보고, Connection Successful이 나오면 최종 적용을 한다.
* Open SQL Console -> New SQL Console을 통해 콘솔창을 연다.
* `use AWS RDS의 웹 콘솔에서 지정한 데이터베이스명` SQL을 실행한다.
* Execute Statement버튼을 클릭했을때, SQL statement executed successfully 메시지가 떴으면 쿼리가 정상적으로 실행된 것이다.
* `show variables like 'c%';` 현재의 character_set, collation 설정 확인
  * character_set_database, collation_connection 2가지 항목은 latin1로 되어있을 수 있다.
  * 이 2개의 항목은 MariaDB에서만 RDS 파라미터 그룹으로는 변경이 안되어 직접 변경해야 한다.
    * `ALTER DATABASE 데이터베이스명`
    * `CHARACTER SET = 'utf8mb4`
    * `CLLATE = 'utf8mb4_general_ci';`
* `select @@time_zone, now();` 타임존 확인
* 테스트용 테이블을 생성해서 한글 데이터도 잘 등록되는지 확인(웬만하면 테이블은 모든 설정이 끝난 후에 생성 하는게 좋음) 

### EC2에서 RDS 접근 확인
* `sudo yum install mysql` MySQL 접근 테스트를 위해 MySQL CLI 설치
* `mysql -u 계정 -p -h Host주소` 로컬에서 접근하듯이 계정, 비밀번호, 호스트 주소를 사용해 RDS에 접속
* 패스워드를 입력하라는 메시지가 나오면 패스워드까지 입력하면 되며, EC2에서 RDS로 접속되는 것을 확인할 수 있다.
* `show databases;` 데이터베이스 목록 확인

___
참고

이동욱, 스프링 부트와 AWS로 혼자 구현하는 웹 서비스