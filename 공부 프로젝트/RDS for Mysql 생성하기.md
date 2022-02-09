## RDS for MySQL 생성하기 

### 목표: 완전 관리형 MySQL 데이터베이스를 구축하고 Linux 기반의 가상서버에서 MySQL 클라이언트로 연결합니다.

### 사용 서비스: AWS RDS

---

### 실습 진행

![](https://raw.githubusercontent.com/C0deWave/aws_study/127ecde37cfc22fae7914f1a6f706f3a2cf3e01a/이미지/RDS%20for%20Mysql%20구성1.png)

1. 검색창에 RDS를 눌러서 Amazon RDS로 이동 - 좌측의 데이터 베이스 클릭 - 생성 클릭
2. Mysql 선택 - 템플릿:Dev/test 선택 - 계정의 암호 입력 - DB인스턴스 클래스: t2.micro 지정 - 가용성및 내구성 체크 안함(할 경우 스탠바이 인스턴스 생성) - 연결에서 VPC 지정 - VPC 보안 그룹은 기본으로 - 가용 영역 2a 지정 - 비용 확인 후 생성 클릭
3. 생성후 해당 인스턴스를 눌러 연결&보안의 엔드포인트 확인(DNS 역할을 한다.)
4. EC2에 들어가서 데이터베이스 보안그룹 생성하기 EC2 - 보안그룹 클릭 - 검색창에서 VPC ID를 이용해서 필터링 - 보안그룹 생성 클릭 - 인바운드 규칙에 Mysql 적용
5. RDS 페이지로 이동 - 인스턴스 선택 - 편집 - 보안그룹 지정 - 수정완료
6. Bastion에 접속해서 RDS에 접속이 되는지 확인하기 - mysql -u admin -p -h [RDS endpoint]
7. Bastion에 이전에 User Data를 이용해 PhpMyAdmin이 설치되어 있으므로 해당 설정 변경
8. cd /var/www/html/phpmyadmin 으로 이동
9. sudo vi config.inc.~~~ 파일을 열어서 localhost를 변경
10. Bastion 보안 규칙 변경 - html추가 - Bastion IP/phpMyAdmin 을 입력해서 접속
11. 로그인을 해서 데이터베이스에 접속해보기

--- 

## 실습 결과

![](https://raw.githubusercontent.com/C0deWave/aws_study/127ecde37cfc22fae7914f1a6f706f3a2cf3e01a/이미지/RDS%20for%20Mysql%20구성2.png)