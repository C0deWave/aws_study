## 서버리스 웹 호스팅과 Cloud Front를 이용한 웹 가속화 구현

---
### 목표: 아마존 S3를 이용한 서버리스 웹 호스팅을 하는 방법과 Cloud Front를 이용해서 캐싱을 통한 웹 가속화 구성을 만들어 본다.

![서버리스 웹 호스팅1.png](https://github.com/C0deWave/aws_study/blob/master/이미지/서버리스%20웹%20호스팅1.png?raw=true)

---

### 용어 정리

아마존 S3: Amazon Simple Storage Service로써 객체 스토리지 서비스이다. 웹서버 및 파일 서버용 파일을 보관하는 장소로 사용할 수 있다.

Amazon CloudFront는 짧은 대기 시간과 빠른 전송 속도로 전 세계 고객에게 데이터, 비디오, 애플리케이션 및 API를 안전하게 전달하는 고속 콘텐츠 전송 네트워크(CDN) 서비스입니다.

CDN: 사용자와 가까운 곳에 위치한 Cache Server에 해당 Content를 저장(캐싱)하고 Content 요청시에 Cache Server가 응답을 주는 기술

 - 지리,물리적으로 떨어져 있는 사용자에게 컨텐츠를 더 빠르게 제공할 수 있는 기술
 - 느린 응답속도 / 다운로딩 타임 을 극복하기 위한 기술

CDN 캐싱 방식

▶ 1. Static Caching
– Origin Server에 있는 Content를 운영자가 미리 Cache Server에 복사
  미리 복사해 두기 때문에 사용자가 Cache Server에 Content를 요청시 무조건 Cache Server에 있다.
– 대부분의 국내 CDN에서 이 방식을 사용( ex. NCSOFT 게임파일 다운로드 등)


▶ 2. Dynamic Caching
– Origin Server에 있는 Content를 운영자가 미리 Cache Server에 복사하지 않음
– 사용자가 Content를 요청시 해당 Content가 없는 경우 Origin Server로 부터 다운로드 받아 전달한다.
  (Content가 있는 경우는 캐싱된 Content 사용자에게 전달.)
– 각각의 Content는 일정 시간이후 Cache Server에서 삭제될 수도 있다. (계속 가지고 있을 수도 있음)

출처: https://goddaehee.tistory.com/173 [갓대희의 작은공간]

---
* S3 정적 웹 호스팅 구현
1. AWS S3에 버킷을 만든다.
   AWS S3 - 버킷만들기 - 퍼블릭 엑세스 차단 해제
2. 정적 웹 호스팅을 설정해 준다.
   생성한 버킷에 접속 - 속성 - 정적 웹 사이트 호스팅 - 편집 - 활성화 - 인덱스 문서 지정
3. S3 규칙을 만들어 준다.
   권한 - 버킷정책 - 편집 - 정책생성기 - S3버킷 규칙 지정 - Principal: *, action all로 지정, 버킷 ARN 입력, - Add Statment 클릭 - Generate Policy클릭 - 나온 표에서 Resource 부분의 내용 끝에 "/ *"붙이기 - 복사 후 버킷 정책에 붙여넣기 - 저장 
4. 호스팅 테스트를 한다.
   웹 파일을 업로드 한 다음 http://도메인/index.html을 입력해서 접속 테스트

* Cloud Front를 활용한 웹 가속화 구성
5. Cloud Front에 들어간다.
6. 배포를 생성한다.
7. 원본도메인에 S3버킷을 선택후 생성을 누른다.
8. Html내부의 이미지 주소를 CloudFront의 도메인으로 변경 후 접근한다.
9.  캐싱이 되어 더욱 빠른 정적 웹 호스팅이 된다.
10. 크롬을 통해서 확인해 본다.

![서버리스 웹 호스팅2.png](https://github.com/C0deWave/aws_study/blob/master/이미지/서버리스%20웹%20호스팅2.png?raw=true)

---

발전 방안

1. 나 자신을 소개하는 사이트로 발전 시키면 좋을 것 같다.