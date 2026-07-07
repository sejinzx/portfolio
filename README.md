# 👋 정세진 | Backend Developer

> 문제를 분석하고 구조를 개선하는 백엔드 개발자

기능 구현을 넘어 문제를 분석하고, 상황에 맞는 구조를 설계하며 서비스를 개선하는 백엔드 개발자입니다.

📄 **Portfolio** : [Portfolio.pdf](./portfolio.pdf)

📧 **Email** : jeongsejin789@gmail.com

💻 **GitHub** : https://github.com/sejinzx

📝 **Blog** : https://vivid-reminiscences.tistory.com/

---

# 🛠 Tech Stack

## Core

- Java
- Spring Boot
- Spring Security
- MySQL

## Experienced

- Redis
- Apache Kafka
- Docker
- Kubernetes
- GitHub Actions
- NHN Cloud
- React

---

# 📌 Projects

## 1. 수강 신청 시스템

> Kafka 기반 비동기 처리 구조를 적용한 수강 신청 서비스

### Tech

Java · Spring Boot · Spring Security · JWT · MySQL · Redis · Apache Kafka · Docker · Kubernetes · GitHub Actions

### 주요 내용

- JWT 기반 인증/인가 구현
- Redis TTL 기반 인증 데이터 관리
- Kafka Producer/Consumer 기반 비동기 처리
- Docker 및 Kubernetes 기반 실행 환경 구성
- GitHub Actions 기반 빌드·배포 자동화

### Problem

비관적 락을 적용했지만 요청 증가 시 데이터베이스 락 경합이 발생할 수 있음을 확인했습니다.

### Solution

Kafka를 도입하여 Producer가 요청 이벤트를 발행하고 Consumer가 이를 처리하는 비동기 구조를 구현했습니다.

### Result

- 최대 **1,000건** 동시 요청 테스트
- 정원 초과 신청 **0건**
- 중복 신청 **0건**
- 데이터 정합성 유지

📄 [상세 보기](./portfolio.pdf)

---

## 2. Talk-kit

> MSA 기반 AI 발표 연습 플랫폼

### Tech

Java · Spring Boot · Spring Cloud Gateway · Eureka · MySQL · Redis · Docker · Kubernetes

### 담당 역할

#### user-service

- 사용자 CRUD API 구현
- Redis TTL 기반 이메일 인증 구현

#### community-service

- 게시글 CRUD API 구현
- 좋아요 및 신고 기능 구현
- 게시글·프로젝트 파일 통합 조회 API 구현

### Problem

게시글 조회 시 project-service의 파일 정보를 함께 제공해야 했지만 서비스별 데이터베이스가 분리되어 있었습니다.

### Solution

Feign Client를 활용하여 project-service API를 호출하고 하나의 응답으로 조합했습니다.

### Result

- 서비스 간 결합도 유지
- 게시글·파일 통합 조회 API 구현
- 화면 요구사항에 맞는 조회 구조 구현

📄 [상세 보기](./portfolio.pdf)

---

## 3. Scheduling

> 일정 및 투두 관리 웹 애플리케이션

### Tech

React · Java · Spring Boot · Spring Data JPA · MySQL · Docker

### 주요 내용

- 일정 및 투두 CRUD API 구현
- 날짜·월 단위 조회 API 설계
- DTO 기반 응답 구조 설계

### Problem

월간 캘린더 화면을 구성하기 위해 날짜별 API를 반복 호출해야 했습니다.

### Solution

월 단위 조회 API를 설계하고 화면에 맞는 응답 구조로 개선했습니다.

### Result

- API 호출 **31회 → 1회**
- 프론트엔드 데이터 처리 로직 단순화
- 캘린더 화면에 최적화된 조회 구조 구현

📄 [상세 보기](./portfolio.pdf)
