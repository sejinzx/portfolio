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

- Spring Security
- Apache Kafka
- Redis
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

## 1. 수강 신청 시스템

> 동시 요청 상황에서도 데이터 정합성을 유지하도록 설계한 수강 신청 서비스

### Tech

Java · Spring Boot · Spring Security · JWT · MySQL · Apache Kafka · Docker · Kubernetes · GitHub Actions · AWS · JMeter

### 주요 내용

- Spring Security + JWT 기반 인증/인가
- Kafka 기반 비동기 수강 신청 처리
- 조건부 UPDATE 기반 정원 제어
- UNIQUE 제약조건 기반 중복 신청 방지
- Docker 및 Kubernetes 기반 실행 환경 구성

### Problem

비관적 락을 적용한 구조에서 여러 요청이 동일한 강의 데이터를 조회·수정하면서
락 경쟁과 데드락이 발생했습니다.

### Solution

기존의 `조회 → 정원 확인 → 신청 인원 증가` 구조를 제거하고,
정원 조건을 포함한 UPDATE 쿼리로 정원 확인과 인원 증가를 하나의 DB 연산으로 처리했습니다.

또한 `(user_seq, class_seq)` UNIQUE 제약조건을 적용해
동시 요청 상황에서도 중복 신청이 발생하지 않도록 구성했습니다.

### Result

- JMeter **1,000건 동시 요청** 검증
- 정원 초과 신청 **0건**
- 중복 신청 **0건**
- 최종 신청 인원과 저장 데이터 수 일치

📄 [상세 보기](./portfolio.pdf)

---

## 2. Talk-kit

> MSA 기반 AI 발표 연습 플랫폼

### Tech

Java · Spring Boot · Spring Cloud Gateway · Eureka · MySQL · Redis · JWT · Docker · Kubernetes · NHN Cloud

### 담당 역할

#### user-service

- 사용자 CRUD API 구현
- Redis TTL 기반 이메일 인증
- 인증번호 저장 및 만료 처리

#### community-service

- 게시글 CRUD API 구현
- 게시글 좋아요 및 신고
- 게시글·프로젝트 파일 통합 조회 API 구현

### Problem

게시글 조회 시 project-service에서 관리하는 프로젝트 파일 정보도 함께 제공해야 했지만,
서비스별 데이터베이스가 분리되어 있어 community-service에서 직접 조회할 수 없었습니다.

### Solution

Feign Client를 적용해 community-service에서 project-service의 파일 조회 API를 호출하고,
게시글 데이터와 파일 데이터를 조합해 하나의 응답으로 제공했습니다.

### Result

- 다른 서비스의 DB를 직접 참조하지 않는 서비스 간 API 연동 구조 구현
- 게시글과 프로젝트 파일 통합 조회 API 구현

📄 [상세 보기](./portfolio.pdf)

---

## 3. Scheduling

> Docker 기반 실행 환경과 Jenkins 자동화를 적용한 일정 및 Todo 관리 웹 애플리케이션

### Tech

Java · Spring Boot · React · MySQL · Docker · Jenkins

### 주요 내용

- Spring Boot / React / MySQL 컨테이너 환경 구성
- Docker Compose 기반 서비스 네트워크 구성
- Backend Multi-stage Docker Build 적용
- Jenkins Pipeline 기반 재빌드·재기동 자동화

### Problem

React와 Spring Boot를 각각 Docker 컨테이너로 분리하면서
React에서 `localhost:8080`으로 백엔드 API에 접근할 수 없는 문제가 발생했습니다.

또한 코드 변경 이후 Docker 이미지를 다시 빌드하고 컨테이너를 재실행하는 작업을
반복적으로 직접 수행해야 했습니다.

### Solution

Spring Boot와 React를 동일한 Docker Network에 구성하고,
API 요청 대상을 `localhost`에서 백엔드 서비스명인 `spring-app:8080`으로 변경해
Docker 내부 DNS 기반으로 통신하도록 구성했습니다.

Backend는 Gradle Builder와 실행 단계를 분리한 Multi-stage Build를 적용했으며,
Jenkins Pipeline에서 저장소 갱신 후 Docker Compose를 이용해
애플리케이션을 재빌드·재기동하도록 자동화했습니다.

📄 [상세 보기](./portfolio.pdf)

---

## 4. Green-Us

> 환경 보호 활동의 참여 현황과 리뷰를 관리하는 플랫폼

### Tech

Kotlin · Spring Boot · MySQL · Android · Firebase

### 담당 역할

#### Android

- 로그인·회원가입 및 계정 찾기 화면
- 마이페이지·프로필·그리닝 화면
- 리뷰·공지사항·포인트 목록 및 Adapter 구현

#### Backend

- 리뷰 조회·수정 및 최신순 정렬 기능 개선
- 사용자 정보 수정 기능 개선
- 참여 완료 활동 및 진척도 조회 로직 개선

### Problem

기존 참여 조회 API가 사용자의 전체 참여 내역을 반환해
진행 중인 활동과 완료된 활동을 구분해서 사용하기 어려웠습니다.

### Solution

`pComplete = 'N'` 조건을 적용해 진행 중인 참여만 조회할 수 있도록 개선하고,
참여 ID를 기준으로 연결된 Greening 정보를 조회하는 기능을 추가했습니다.

📄 [상세 보기](./portfolio.pdf)
