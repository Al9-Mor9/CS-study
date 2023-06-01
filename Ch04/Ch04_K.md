# 4. 데이터베이스
- 데이터베이스(DB, DataBase): 일정한 규칙, 혹은 규약을 통해 구조화되어 저장되는 데이터의 모음
- DBMS(DataBase Management System): 데이터베이스를 제어, 관리하는 통합 시스템
- DB 안에 있는 데이터들은 특정 DBMS마다 정의된 query language를 통해 CRUD 수행 가능
- DB는 실시간 접근과 동시 공유 가능

# 4.1 데이터베이스의 기본
- <img width="117" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/3782635a-9901-4e3d-916b-8e067be898b1">
- DBMS - MySQL, 응용 프로그램 - Node.js, php..

## 📌 4.1.1 엔터티(entity)
- 엔터티: 여러 개의 속성을 지닌 명사
  - ex. 회원-이름, 아이디, 주소, 전화번호 등의 속성을 가짐
- 서비스의 요구사항에 맞춰 속성이 정해짐
- <img width="458" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/d160184d-caae-4b51-a3bf-1d8c8edc995e">

### ✔️ 약한 엔터티와 강한 엔터티
- A가 독립적으로는 존재하지 못하고 B의 존재 여부에 따라 종속적이면 &rarr; A: 약한 엔터티, B: 강한 엔터티

## 📌 4.1.2 릴레이션(relation)
- 릴레이션: DB에서 정보를 구분하여 저장하는 기본 단위
- DB는 릴레이션 하나에 엔터티에 관한 데이터를 담아서 관리
- 관계형 데이터베이스: 테이블
- NoSQL 데이터베이스: 컬렉션

### ✔️ 테이블과 컬렉션
- 관계형 데이터베이스 - MySQL: 레코드-테이블-데이터베이스
- NoSQL 데이터베이스 - MongoDB: 도큐먼트-컬렉션-데이터베이스
- <img width="526" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/3f7275aa-2695-4119-ae65-4b3edd016a4a">

## 📌 4.1.3 속성(attribute)
- 속성: 릴레이션에서 관리하는 구체적이며 고유한 이름을 갖는 정보

## 📌 4.1.4 도메인(domain)
- 도메인: 릴레이션에 포함된 각각의 속성들이 가질 수 있는 값의 집합
- <img width="207" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/b285e70d-2250-4d71-bd00-b715782f9297">

## 📌 4.1.5 필드와 레코드
- <img width="425" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/edd31d27-4c8a-43be-85c2-e2d3e7f55006">

### ✔️ 필드 타입
- 숫자 타입
  - <img width="648" alt="스크린샷 2023-06-01 오후 4 06 01" src="https://github.com/Haaarimmm/TIL/assets/108309396/b3e65ad3-c63b-42f7-aea8-39acc78551bc">
- 날짜 타입
  - `DATE`: 날짜 부분만 존재, 3바이트
  - `DATETIME`: 날짜 및 시간 부분 모두 포함, 8바이트
  - `TIMESTAMP`: 날짜 및 시간 부분 모두 포함, 4바이트
- 문자 타입
  - `CHAR`/`VARCHAR`: 몇 자까지 입력할지 정함
    - `CHAR`: 테이블 생성 시 선언한 길이로 고정됨
    - `VARCHAR`: 가변 길이 문자열, 입력 데이터에 따라 용량을 가변시켜 저장함
    - 지정된 형태에 따라 저장된 `CHAR`의 경우 **검색에 유리**
    - 검색을 별로 하지 않고 유동적인 길이를 가진 데이터는 `VARCHAR`
  - `TEXT`/`BLOB`
    - `TEXT`: 큰 문자열 저장에 쓰임, 게시판 본문 등
    - `BLOB`: 이미지, 동영상 등 큰 데이터 저장에 쓰임
      - 보통은 AWS 등 서버에 파일을 올리고 경로를 VARCHAR로 저장
  - `ENUM`/`SET`: 문자열을 열거한 타입
    - `ENUM`: ENUM 형태 중 단일 선택만 가능, ENUM 리스트에 없는 잘못된 값 삽입 시 빈 문자열이 삽입됨, 메모리 적게 사용 가능
    - `SET`: 여러 개의 데이터 선택 가능, 비트 단위의 연산 가능
    - 둘 다 공간적으로 이점을 볼 수 있지만 애플리케이션의 수정에 따라 ENUM이나 SET에서 정의한 목록을 수정해야 함

## 📌 4.1.6 관계
- 여러 테이블 간의 관계 정의 시 **관계 화살표** 사용
- <img width="523" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/c753e065-dc2e-4489-8596-fd9442201ccd">

### ✔️ 1:1 관계
- <img width="317" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/6f2719c2-5749-4d8e-8bac-934dc9748a25">


### ✔️ 1:N 관계
- 하나도 넣지 않는 0개의 경우, 0도 포함되는 화살표 사용
- <img width="317" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/a69d75c8-d7d9-4730-80a6-25727d2779ba">

### ✔️ M:N 관계
- 테이블 두 개를 1:M, 1:N 관계 테이블 2개로 나눠서 설정
- <img width="318" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/74af566a-4c3f-44d7-968d-4d95d72a1470">

## 📌 4.1.7 키
- 테이블 자체의 인덱스를 위해 설정된 장치
- 슈퍼키: 유일성 존재
  - 유일성: 중복되는 값X
- 후보키: 유일성 + 최소성
  - 최소성: 필드를 조합하지 않고 최소 필드만 써서 키를 형성 가능
- 후보키 중 기본키로 선택되지 못한 키는 대체키가 됨
- <img width="292" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/8253b9dd-927b-4614-80eb-00b70d0f85b4">


### ✔️ 기본키(Primary Key, PK)
- 유일성과 최소성 만족
- 테이블의 데이터 중 고유하게 존재하는 속성
- 복합키를 기본키로 설정할 수 있지만 최소성 만족하지 않음
- 자연키 / 인조키 중 골라서 설정

### 🔅 자연키 / 인조키
1. 자연키
   - 중복된 값들을 제외하며 중복되지 않는 것을 '자연스레' 뽑다가 나오는 키
   - 언젠가는 변하는 속성을 가짐
2. 인조키
   - 인위적으로 생성한 키
   - 변하지 않음
   - ex) 인위적으로 유저 아이디 부여 시 고유 식별자가 생겨남

### ✔️ 외래키(Foreign Key, FK)
- 다른 테이블의 기본키를 그대로 참조하는 값
- 외래키는 중복되어도 괜찮음

### ✔️ 후보키(Candidate Key)
- 기본키가 될 수 있는 후보들
- 유일성과 최소성 동시에 만족

### ✔️ 대체키(Alternate Key)
- 후보키가 두 개 이상일 경우 하나를 기본키, 남은 후보키들은 대체키가 됨

### ✔️ 슈퍼키(Super Key)
- 각 레코드를 유일하게 식별할 수 있는 유일성을 갖춘 키


# 4.2 ERD와 정규화 과정
- ERD(Entity Relationship Diagram)
  - DB 구축 시 가장 기초적인 뼈대 역할
  - 릴레이션 간의 관계들을 정의

## 📌 4.2.1. ERD의 중요성
- ERD는 시스템의 요구사항을 기반으로 작성됨
- DB는 ERD를 기반으로 구축
- 구축 이후에도 디버깅 또는 비즈니스 프로세스 재설계 필요 시 설계도 역할 담당
- But, ERD는 비정형 데이터를 충분히 표현할 수 없다는 단점 존재

## 📌 4.2.2 정규화 과정
- 릴레이션을 여러 개로 분리하는 과정
  - 릴레이션 간의 잘못된 종속 관계로 인해 발생한 DB 이상현상을 해결하거나
  - 저장공간을 효율적으로 사용하기 위해
- 정규화 과정: 정규형 원칙을 기반으로 정규형을 만들어가는 과정
  - 정규화된 정도는 정규형(NF, Normal Form)으로 표현
  - 기본 정규형: 제1정규형, 제2정규형, 제3정규형, 보이스/코드 정규형
  - 고급 정규형: 제4정규형, 제5정규형

### ✔️ 정규형 원칙
- 자료의 중복성&darr;
- 독립적인 관계는 별개의 릴레이션으로 표현해야 함

### ✔️ 제1정규형
- <img width="453" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/61abc2b4-f6c9-4689-b48c-2e07550342d3">
- 릴레이션의 **모든 도메인이 더 이상 분해될 수 없는 원자값(atomic value)**만으로 구성되어야 함
- 릴레이션의 속성 값 중 한 개의 기본키에 대해 두 개 이상의 값을 가지는 반복 집합X
- 반복 집합은 제거

### ✔️ 제2정규형
- <img width="524" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/5bd730f9-8c45-449a-9d89-fb164242e906">
- 제1정규형이고 **부분 함수의 종속성을 제거**한 형태
  - 부분 함수의 종속성 제거: 기본키가 아닌 모든 속성이 기본키에 완전 함수 종속적인 것 

### ✔️ 제3정규형
- <img width="378" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/96f2d1d5-0561-4ceb-8a16-76083b2d3615">
- 제2정규형이고 기본키가 아닌 모든 속성이 **이행적 함수 종속(transitive FD)을 만족하지 않는 상태**
  - 이행적 함수 종속: A&rarr;B와 B&rarr;C가 존재 시 A&rarr;C가 성립, 이 때 집합 C가 집합 A에 이행적으로 함수 종속이 되었다고 말함
  - <img width="222" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/01e5d2e5-6d25-45ea-b3ea-3af38cf99fbc">


### ✔️ 보이스/코드 정규형(BCNF)
- 제3정규형이고 결정자가 후보키가 아닌 함수 종속 관계를 제거하여 릴레이션의 함수 종속 관계에서 모든 결정자가 후보키인 상태
  - X&rarr;Y 일 때 X는 결정자, Y는 종속자
- 결정자이지만 후보키가 아닌 속성은 분리
- <img width="404" alt="image" src="https://github.com/Haaarimmm/TIL/assets/108309396/7c3570a7-4020-4c28-90ae-68e801c9e58a">