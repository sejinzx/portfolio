# 👋 정세진 | Backend Developer

> 문제를 분석하고 구조를 개선하는 백엔드 개발자

안정적인 데이터 처리와 서비스 운영을 고려하며 백엔드 서비스를 개발합니다.

📄 **Portfolio** : [Portfolio.pdf](./portfolio.pdf)

📧 **Email** : jeongsejin789@gmail.com

💻 **GitHub** : https://github.com/sejinzx

📝 **Blog** : https://vivid-reminiscences.tistory.com/

---

# 🛠 Tech Stack

## Core

- Java
- Spring Boot
- MySQL

## Experienced

- Apache Kafka
- Spring Security
- Docker
- Kubernetes
- GitHub Actions
- AWS
- NHN Cloud
- React

## Collaboration & Tools

- GitHub
- Notion
- Slack

---

# 📌 Projects

## 1. 클래스룸 (Classroom)

> 동시 수강 신청 상황에서도 정원 초과와 중복 신청을 방지하도록 처리 구조를 개선한 웹 서비스

**기간:** 2026.05 ~ 2026.09

**형태:** 개인 프로젝트

**담당:** 백엔드 개발 (프론트엔드는 OpenAI Codex 활용)

**GitHub:** https://github.com/sejinzx/enrollment_system

### Tech

Java · Spring Boot · MySQL · Apache Kafka · Docker · GitHub Actions · AWS · JMeter · Testcontainers

### 주요 구현

- Spring Security + JWT 기반 인증/인가
- Kafka Producer–Consumer 기반 비동기 수강 신청 처리
- 조건부 UPDATE를 통한 동시성 제어
- UNIQUE 제약조건 기반 중복 신청 방지
- Kafka 재시도 및 DLT 기반 장애 처리
- Testcontainers 기반 Kafka·MySQL 통합 테스트
- GitHub Actions 기반 CI/CD 및 AWS EC2 자동 배포

### Problem

비관적 락 적용 후 동시 요청이 증가하면서 동일 강의 데이터에 대한 락 경쟁과 데드락이 발생했습니다.

### Solution

기존의 `조회 → 정원 확인 → 신청 인원 증가` 구조를 제거하고, 정원 확인과 신청 인원 증가를 하나의 조건부 UPDATE 쿼리로 처리했습니다.

또한 `(user_seq, class_seq)` UNIQUE 제약조건을 적용해 동시 요청에 따른 중복 신청을 방지했습니다.

Kafka 기반 Producer–Consumer 구조로 수강 신청 요청과 실제 처리 로직을 분리하고, 시스템 예외 발생 시 재시도 및 DLT를 통해 실패 메시지를 처리하도록 구성했습니다.

### Result

- JMeter **1,000건 동시 요청** 테스트
- 정원 초과 **0건**, 중복 신청 **0건**
- 최종 신청 인원과 실제 신청 데이터 수 일치
- Testcontainers 기반 메시지 처리·재시도·DLT·중복 전달 시나리오 검증

📄 [상세 보기](./portfolio.pdf)

---

## 2. Talk-kit

> MSA 구조로 개발한 AI 기반 발표 연습 플랫폼

**기간:** 2024.06 ~ 2024.10

**인원:** Backend 3 / Frontend 2

**담당:** 유저 서비스, 커뮤니티 서비스

**GitHub:** https://github.com/orgs/Talk-Kit/repositories

### Tech

Java · Spring Boot · Spring Cloud Gateway · Eureka · MySQL · Redis · JWT · Docker · Kubernetes · NHN Cloud

### 담당 역할

**유저 서비스**

- 사용자 CRUD API 구현
- Redis TTL 기반 이메일 인증
- 인증번호 저장 및 만료 처리

**커뮤니티 서비스**

- 게시글 CRUD API 구현
- 게시글 좋아요 및 신고
- 게시글·프로젝트 파일 통합 조회 API 구현

### Problem

게시글 조회 API에서 프로젝트 파일 정보도 함께 제공해야 했지만, 해당 데이터는 별도의 프로젝트 서비스에서 관리하고 있어 커뮤니티 서비스에서 직접 조회할 수 없었습니다.

### Solution

Feign Client를 적용해 커뮤니티 서비스에서 프로젝트 서비스의 파일 조회 API를 호출하도록 구성했습니다.

조회한 게시글 데이터와 프로젝트 파일 정보를 조합해 클라이언트에 하나의 응답으로 제공했습니다.

### Result

- 서비스 간 직접적인 DB 참조 없이 API를 통한 데이터 연동 구현
- 게시글과 프로젝트 파일 통합 조회 API 구현

📄 [상세 보기](./portfolio.pdf)

---

## 3. Scheduling

> Docker 기반 실행 환경과 Jenkins 자동화를 적용한 일정 및 Todo 관리 웹 애플리케이션

**기간:** 2026.04 ~ 2026.06

**형태:** 개인 프로젝트

**GitHub:** https://github.com/sejinzx/scheduling

### Tech

Java · Spring Boot · React · MySQL · Docker · Jenkins

### 주요 구현

- Spring Boot / React / MySQL Docker 컨테이너 환경 구성
- Docker Compose 기반 서비스 의존성 및 네트워크 구성
- Backend Multi-stage Docker Build 적용
- Jenkins Pipeline 기반 애플리케이션 재빌드·재기동 자동화

### Problem

React와 Spring Boot를 각각 Docker 컨테이너로 분리하면서 React에서 `localhost:8080`으로 백엔드 API에 접근할 수 없는 문제가 발생했습니다.

또한 코드 변경 이후 Docker 이미지를 재빌드하고 컨테이너를 재실행하는 작업을 반복적으로 수행해야 했습니다.

### Solution

Spring Boot, React, MySQL을 동일한 Docker Network에 구성하고 API 요청 대상을 `localhost`에서 백엔드 서비스명인 `spring-app:8080`으로 변경해 컨테이너 간 통신 문제를 해결했습니다.

Backend에는 Gradle Builder 단계와 실행 단계를 분리한 Multi-stage Build를 적용했습니다.

Jenkins Pipeline을 구성해 저장소 갱신 후 Docker Compose를 이용한 이미지 재빌드 및 컨테이너 재기동 과정을 자동화했습니다.

### Result

- Docker 내부 DNS 기반 컨테이너 간 통신 구조 구성
- Multi-stage Build를 통한 빌드 및 실행 환경 분리
- Jenkins Pipeline 기반 빌드·실행 과정 자동화

📄 [상세 보기](./portfolio.pdf)

---

## 4. Green-Us

> 환경 보호 활동의 참여 현황과 리뷰를 공유하는 플랫폼

**기간:** 2024.03 ~ 2024.11

**인원:** Frontend 2 / Backend 2

**GitHub:** https://github.com/green-us-2024/green-us

### Tech

Kotlin · Spring Boot · MySQL · Android · Firebase

### 담당 역할

**Android 개발**

- 로그인·회원가입 및 계정 찾기 화면
- 마이페이지·프로필·그리닝 화면
- 리뷰·공지사항·포인트 목록 및 Adapter
- 프로필 이미지 처리 및 화면 전환 개선

**백엔드 개발**

- 리뷰 조회·수정 및 최신순 정렬 기능 개선
- 사용자 정보 수정 기능 개선
- 참여 완료 활동 조회 및 진척도 로직 개선

### Problem

기존 참여 조회 API가 사용자의 전체 참여 내역을 반환해 진행 중인 활동과 완료된 활동을 구분하기 어려웠습니다.

### Solution

`pComplete = 'N'` 조건을 적용해 진행 중인 참여만 조회할 수 있도록 개선했습니다.

Repository–Service–Controller 계층에 상태별 조회 기능을 추가하고, 참여 ID를 기준으로 연결된 Greening 정보를 조회하도록 기능을 보완했습니다.

### Result

- 진행 상태에 따른 참여 데이터 조회 기능 분리
- 참여 ID 기반 Greening 정보 조회 기능 개선

📄 [상세 보기](./portfolio.pdf)
