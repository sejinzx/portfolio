
# 👨‍💻 정세진 | Backend Developer

> Java · Spring Boot 기반 백엔드 개발자

Java와 Spring Boot를 활용해 웹 API와 데이터 처리 로직을 개발하고 있습니다.

수강 신청 시스템에서 동시 요청으로 발생한 데드락을 해결하고, Kafka 메시지 처리 과정의 데이터 정합성을 검증했습니다. 팀 프로젝트에서는 서비스 간 API 연동과 기존 기능 개선을 담당했습니다.

### 📬 Contact

- 📄 **Portfolio:** [portfolio.pdf](./portfolio.pdf)
- 📧 **Email:** jeongsejin789@gmail.com
- 💻 **GitHub:** https://github.com/sejinzx
- 📝 **Blog:** https://vivid-reminiscences.tistory.com/

---

## 🛠️ Tech Stack

| 구분 | 기술 |
| --- | --- |
| Backend | Java, Spring Boot, Spring Security |
| Database & Messaging | MySQL, Redis, Apache Kafka |
| Infra & DevOps | Docker, Jenkins, GitHub Actions, AWS EC2, Kubernetes |
| Other Experience | Kotlin, Android, React, Firebase |
| Collaboration | GitHub, Notion, Slack |

---

## 📌 Projects

| 프로젝트 | 주요 내용 | 기간 |
| --- | --- | --- |
| 🎓 클래스룸 | 동시성 제어, 데이터 정합성, Kafka 비동기 처리 | 2026.05 ~ 09 |
| 🎤 Talk-kit | MSA, 서비스 간 API 연동, 인증 처리 | 2024.06 ~ 10 |
| 🌱 Green-Us | 참여 상태별 API 개선, Android 개발 | 2024.03 ~ 11 |
| 📅 Scheduling | 일정 조회, Docker 환경 구성, Jenkins 자동화 | 2026.04 ~ 06 |

---

## 🎓 01. 클래스룸 (Classroom)

> **수강 신청 시스템 | 동시성 제어 및 데이터 정합성 검증**

- **기간:** 2026.05 ~ 2026.09
- **형태:** 개인 프로젝트
- **기술:** Java, Spring Boot, MySQL, Kafka, Docker, GitHub Actions, AWS EC2
- **GitHub:** https://github.com/sejinzx/enrollment_system
- **Web:** http://13.209.75.120:3000

### 📖 프로젝트 개요

강의 조회, 수강 신청 및 취소 기능을 제공하는 웹 서비스입니다.

동시 신청 상황에서 정원 초과와 중복 신청을 방지하고, Kafka 메시지 재전달 상황에서도 신청 데이터가 중복 반영되지 않도록 구현했습니다.

### 🔧 주요 구현

**수강 신청 및 동시성 제어**
- Spring Security와 JWT 기반 인증·인가
- 조건부 UPDATE를 통한 정원 확인 및 신청 인원 증가
- UNIQUE 제약조건을 이용한 동일 사용자 중복 신청 방지
- 트랜잭션을 통한 신청 인원과 신청 데이터 정합성 관리

**Kafka 비동기 처리**
- Producer–Consumer 구조로 신청 요청과 실제 DB 처리 분리
- 정원 초과, 중복 신청 등 비즈니스 예외는 재시도하지 않도록 처리
- 시스템 예외 발생 시 최대 2회 재시도 후 DLT 전달
- 메시지 중복 전달 시 신청 데이터가 한 번만 반영되도록 처리

**테스트 및 배포**
- JUnit 5, Testcontainers 기반 MySQL·Kafka 통합 테스트
- 동시 신청, 중복 요청, 메시지 재전달 및 장애 처리 시나리오 검증
- Docker 기반 실행 환경 구성
- GitHub Actions와 AWS EC2를 이용한 배포 자동화

### 🔍 Trouble Shooting 01. 동시 요청 데드락

**문제**

초기에는 비관적 락을 이용해 강의 정원을 확인한 뒤 신청 인원을 증가시켰습니다.

동일 강의에 동시 요청이 집중되면서 락 경합과 데드락이 발생했습니다.

**해결**

정원 확인과 신청 인원 증가를 하나의 조건부 UPDATE로 통합했습니다.

```sql
UPDATE classes
SET class_curr_apps = class_curr_apps + 1
WHERE class_seq = ?
  AND class_deleted = false
  AND class_state = 'OPEN'
  AND class_curr_apps < class_max_cap;
```

UPDATE 영향 행 수를 기준으로 정원 확보 여부를 판단하고, UNIQUE 제약조건과 트랜잭션을 적용해 중복 신청을 방지했습니다.

**검증 결과**

정원 100명인 강의에 200건의 동시 신청을 요청했습니다.

| 항목 | 결과 |
| --- | --- |
| 신청 성공 | 100건 |
| 정원 초과 | 100건 |
| 최종 신청 인원 | 100명 |
| 실제 신청 데이터 | 100건 |

정원 초과 없이 최종 신청 인원과 신청 데이터 수가 일치했습니다.

### 🔍 Trouble Shooting 02. Kafka 메시지 중복 및 장애 처리

**문제**

Kafka Consumer의 장애나 메시지 재처리 과정에서 동일한 신청 메시지가 중복 전달될 수 있습니다.

또한 정원 초과처럼 재시도해도 결과가 달라지지 않는 예외와 일시적인 시스템 장애를 구분할 필요가 있었습니다.

**해결**

- 비즈니스 예외: 재시도 없이 처리 종료
- 시스템 예외: 최대 2회 재시도
- 재시도 최종 실패: DLT 전달
- 중복 메시지: UNIQUE 제약조건과 트랜잭션으로 중복 반영 방지

**통합 테스트 결과**

JUnit 5와 Testcontainers를 이용해 실제 MySQL·Kafka 컨테이너 환경에서 검증했습니다.

| 테스트 | 결과 |
| --- | --- |
| 동일 사용자 동시 신청 20회 | 신청 데이터 1건 저장 |
| 동일 Kafka 메시지 2회 전달 | 신청 데이터 1건 반영 |
| 일시적 시스템 장애 | Retry 후 정상 처리 |
| 재시도 최종 실패 | DLT 전달 확인 |
| 신청 인원 정합성 | 신청 인원과 실제 신청 건수 일치 |

---

## 🎤 02. Talk-kit

> **AI 기반 발표 연습 플랫폼 | 서비스 간 API 연동 및 인증 처리**

- **기간:** 2024.06 ~ 2024.10
- **형태:** 팀 프로젝트 (Backend 3명, Frontend 2명)
- **담당:** 유저 서비스, 커뮤니티 서비스, 최종 문서 작성
- **기술:** Java, Spring Boot, Spring Cloud Gateway, Eureka, MySQL, Redis, JWT, Docker, Kubernetes, NHN Cloud
- **GitHub:** https://github.com/orgs/Talk-Kit/repositories

### 📖 프로젝트 개요

AI 대본 생성, 음성 녹음, 프로젝트 관리 및 커뮤니티 피드백 기능을 제공하는 플랫폼입니다.

Spring Cloud Gateway와 Eureka를 활용한 MSA 환경에서 유저·커뮤니티 서비스를 개발했습니다. 팀 구성 변경 이후에는 담당 서비스의 잔여 기능과 서비스 간 연동 오류를 보완했습니다.

### 🔧 담당 구현

**유저 서비스**
- 사용자 정보 CRUD API
- Redis TTL 기반 이메일 인증번호 저장 및 30분 만료 처리
- 사용자 관련 잔여 기능 구현

**커뮤니티 서비스**
- 게시글 CRUD API
- 게시글 좋아요 및 신고 기능
- 게시글과 프로젝트 파일 정보 통합 조회
- Feign Client를 이용한 서비스 간 API 호출
- RequestInterceptor를 통한 JWT 인증 헤더 전달

**협업**
- 팀 구성 변경 이후 미완성 백엔드 기능 보완
- 서비스 간 연동 오류 수정
- 프로젝트 최종 문서 작성

### 🔍 Trouble Shooting. 서비스 간 데이터 연동

**문제**

커뮤니티 게시글을 조회할 때 프로젝트 파일 정보도 함께 제공해야 했습니다.

파일 정보는 별도의 프로젝트 서비스에서 관리하고 있었으며, 커뮤니티 서비스에서 해당 테이블을 직접 참조하면 서비스 간 DB 의존성이 발생하는 구조였습니다.

**해결**

Feign Client를 적용해 프로젝트 서비스의 파일 조회 API를 호출하도록 변경했습니다.

인증이 필요한 요청에는 RequestInterceptor를 적용해 JWT 인증 헤더를 전달했습니다.

**처리 흐름**

```text
클라이언트
    │
    ▼
커뮤니티 서비스
    ├── 게시글 데이터 조회
    │
    └── Feign Client
          │ JWT 인증 헤더 전달
          ▼
      프로젝트 서비스
          │
          └── 파일 정보 반환
    │
    ▼
게시글 + 파일 정보 통합 응답
```

**결과**

- 프로젝트 서비스의 DB를 직접 조회하지 않고 파일 정보 연동
- JWT 인증 정보를 포함한 서비스 간 요청 처리
- 게시글과 첨부파일을 함께 반환하는 API 구현

---

## 🌱 03. Green-Us

> **환경 보호 활동 플랫폼 | 기존 API 개선 및 Android 기능 개발**

- **기간:** 2024.03 ~ 2024.11
- **형태:** 팀 프로젝트 (Backend 2명, Frontend 2명)
- **담당:** Android 주요 화면 개발 및 백엔드 API 개선
- **기술:** Kotlin, Spring Boot, MySQL, Android, Firebase
- **GitHub:** https://github.com/green-us-2024/green-us

### 📖 프로젝트 개요

환경 보호 활동의 참여 현황과 리뷰를 관리하는 플랫폼입니다.

팀원 공백으로 Android 개발을 담당했으며, 이후 Spring Boot 백엔드 기능 개선에도 참여했습니다.

### 🔧 담당 구현

**Android 개발**
- 로그인, 회원가입 및 계정 찾기 화면
- 마이페이지, 프로필, 환경 보호 활동 화면
- 리뷰, 공지사항, 포인트 목록 및 Adapter
- 프로필 이미지 처리 및 화면 전환 개선

**백엔드 개발**
- 리뷰 조회 및 최신순 정렬 기능 수정
- 사용자 정보 수정 기능 개선
- 참여 완료 활동 조회 및 진척도 로직 개선
- 참여 상태별 조회 API 구현

### 🔍 Trouble Shooting. 참여 상태별 조회 API 개선

**문제**

기존 참여 조회 API는 사용자의 전체 참여 내역을 반환했습니다.

진행 중인 활동만 필요한 화면에서도 완료된 활동이 함께 조회되어 데이터를 구분하기 어려웠습니다.

**해결**

참여 완료 여부를 나타내는 `pComplete` 값을 이용해 조회 조건을 추가했습니다.

```sql
WHERE pComplete = 'N'
```

Repository–Service–Controller 계층에 상태별 조회 기능을 추가하고, 참여 ID를 기준으로 연결된 Greening 정보를 조회하도록 로직을 보완했습니다.

**결과**

- 진행 중인 활동과 완료된 활동의 조회 기능 분리
- 참여 상태에 맞는 데이터 조회
- 참여 ID와 연결된 활동 정보 조회 기능 개선

---

## 📅 04. Scheduling

> **일정 및 Todo 관리 웹 애플리케이션 | Docker · Jenkins 자동화**

- **기간:** 2026.04 ~ 2026.06
- **형태:** 개인 프로젝트
- **기술:** Java, Spring Boot, React, MySQL, Docker, Jenkins
- **GitHub:** https://github.com/sejinzx/scheduling

### 📖 프로젝트 개요

React와 Spring Boot 기반의 일정 및 Todo 관리 웹 애플리케이션입니다.

일정 조회 및 우선순위 처리 기능을 구현하고, Docker로 실행 환경을 구성했습니다. Jenkins Pipeline을 적용해 코드 변경 이후의 빌드 및 컨테이너 실행 과정을 자동화했습니다.

### 🔧 주요 구현

**일정 관리**
- 일정 및 Todo 관리 기능
- JPQL을 활용한 월별 일정 조회
- 마감 임박 일정 조회 및 우선순위 처리
- Todo 일정 제외 조건과 마감일 기준 분류 로직 적용

**실행 환경**
- Spring Boot, React, MySQL Docker 컨테이너 분리
- Docker Compose 기반 서비스 의존성 및 네트워크 구성
- Backend Multi-stage Docker Build
- Jenkins Pipeline 기반 빌드 및 실행 자동화

### 🔍 Trouble Shooting 01. Docker 컨테이너 간 API 통신

**문제**

React와 Spring Boot를 각각 Docker 컨테이너로 분리한 이후 프론트엔드에서 백엔드 API에 접근하지 못했습니다.

기존 React 프록시 대상은 `localhost:8080`으로 설정되어 있었습니다.

**원인**

Docker 컨테이너 내부의 `localhost`는 해당 컨테이너 자신을 가리킵니다.

React 컨테이너에서는 `localhost:8080`으로 백엔드 컨테이너에 접근할 수 없었습니다.

**해결**

Spring Boot, React, MySQL을 동일한 Docker Network에 구성하고 React 프록시 대상을 다음과 같이 변경했습니다.

```text
기존: localhost:8080
변경: spring-app:8080
```

Docker 내부 DNS를 통해 서비스명으로 백엔드 컨테이너에 접근하도록 수정했습니다.

**결과**

분리된 컨테이너 간 API 통신 문제를 해결했습니다.

### 🔍 Trouble Shooting 02. Docker 빌드 구조 개선

**문제**

초기에는 빌드된 JAR 파일이 존재해야 Docker 이미지를 생성할 수 있었습니다.

**해결**

Multi-stage Build를 적용해 Gradle 빌드와 애플리케이션 실행 단계를 분리했습니다.

```text
Gradle Builder
    │
    ├── 소스 코드 빌드
    └── JAR 파일 생성
            │
            ▼
Runtime Image
    │
    └── JAR 파일 실행
```

Docker 이미지 생성 과정에서 애플리케이션을 빌드하고, 최종 실행 단계에는 생성된 JAR 파일만 전달하도록 구성했습니다.

**결과**

별도의 사전 JAR 빌드 없이 Docker 이미지 생성 과정에서 빌드할 수 있도록 변경했습니다.

### 🔍 Trouble Shooting 03. Jenkins 빌드·실행 자동화

**문제**

코드 변경 후 GitHub 저장소 갱신, 이미지 재빌드, 컨테이너 재실행 작업을 수동으로 반복해야 했습니다.

**해결**

Jenkins Pipeline에서 GitHub main 브랜치의 코드를 가져온 뒤 Docker Compose 명령을 실행하도록 구성했습니다.

```bash
docker compose down
docker compose up --build -d
```

Jenkins 전용 이미지를 구성하고 Docker 실행 환경과 연결해 Pipeline에서 Docker 명령을 실행할 수 있도록 했습니다.

**결과**

- 저장소 갱신 및 Docker 이미지 재빌드 자동화
- 컨테이너 종료 및 재실행 자동화
- 반복적인 수동 빌드·실행 작업 감소

---

## 📄 Portfolio

프로젝트별 아키텍처, 구현 내용 및 테스트 결과는 포트폴리오에서 확인하실 수 있습니다.

**📘 [전체 포트폴리오 PDF 보기](./portfolio.pdf)**
