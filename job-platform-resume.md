# 김양훈 - Backend Engineer

## 프로필

결제·주문·인증·외부 연동처럼 한 번의 실패가 사용자 신뢰와 운영 비용으로 이어지는 도메인을 다뤄온 6년차 백엔드 엔지니어입니다. 현재는 누적 유저 210만, MAU 30만 규모의 모바일 기프티콘 플랫폼에서 결제 정합성, 레거시 모노레포 이관, Kubernetes 운영, 외부 제휴 연동을 담당하고 있습니다.

기능 구현에 그치지 않고 사용자 흐름과 운영 예외를 함께 보고, 상태·멱등성·재처리·모니터링 기준을 코드와 데이터 구조에 남기는 방식으로 일합니다. 결제 성공 후 발송 이벤트 유실, 외부 콜백 중복 처리, 인증 결과와 디바이스 제어 불일치처럼 기능 구현만으로 놓치기 쉬운 흐름을 메시징·모니터링 구조로 해결해왔습니다.

## 핵심 역량

- 결제, 주문, 환불, 정산, 인증, 제휴 연동처럼 상태와 예외가 많은 도메인의 백엔드 설계·개발
- 외부 API 실패, 중복 요청, 재시도, 부분 성공, 보상 처리, 비동기 메시지 유실 같은 운영 리스크 대응
- Express/.NET 레거시 시스템을 NestJS, 모노레포, 클라우드 운영 구조로 점진 전환
- Kubernetes, ECS/Fargate, GitOps, Grafana/Prometheus/Datadog 기반 배포·모니터링·장애 대응
- IoT 디바이스, MQTT, ESP32, 스피드게이트 등 서버 밖 장치와 연결되는 제품 흐름 개발

## 기술 스택

- **Backend**: TypeScript, Node.js, NestJS, Express, Spring Boot
- **Database**: MySQL, PostgreSQL, MongoDB, Redis, OpenSearch
- **Messaging / Jobs**: AWS SQS, AWS SNS, MQTT, node-cron, Kubernetes CronJob
- **Infrastructure**: Kubernetes, EKS, Helm, ArgoCD, Docker, AWS ECS/Fargate, Lambda, RDS, S3, GitHub Actions, CloudWatch, Grafana, Prometheus, Datadog

## 경력 요약

| 기간 | 회사 | 역할 | 주요 도메인 |
| --- | --- | --- | --- |
| 2025.08 - 재직 중 | 더블엔씨 | Backend Engineer | 모바일 기프티콘, 결제, 주문, 정산, 제휴 연동, Kubernetes |
| 2022.12 - 2025.08 | 고스트패스 | Backend Engineer | 생체인증, 무인 출입·결제, IoT 디바이스, 레거시 전환 |
| 2021.07 - 2022.12 | 소셜인베스팅랩 | Full Stack Engineer | 주식 SNS, 계좌 개설, 증권사 API, 실시간 시세, Android MVP |
| 2020.08 - 2021.06 | 디케이랩 | Backend Engineer | 스마트 공장, 설비 데이터, MES/ERP, 서버리스 |

## 경력 상세

### (주)더블엔씨

2025.08 - 재직 중  
Backend Engineer / 니콘내콘 모바일 기프티콘 플랫폼

누적 유저 210만, MAU 30만, 하루 주문 1만 건·거래액 1억 원 이상 규모의 모바일 기프티콘 거래 플랫폼에서 주문·결제·환불·정산·CMS와 외부 제휴 연동을 개발·운영하고 있습니다. 여러 레포지토리에 분산된 Express 레거시를 NestJS 모노레포로 이관하는 작업에 이관 초기부터 참여해, 결제·제휴·공통 도메인 계층을 담당하고 있습니다.

#### 대표 기여

- **결제 성공 후 기프티콘 발송 누락 위험을 구조적으로 제거했습니다.** 기존에는 PG 콜백 처리와 SQS FIFO 발행 사이에 원자성 갭이 있어, 결제는 성공했지만 발송 이벤트가 유실되거나 사용자가 프론트 폴링으로 1분간 대기한 뒤 실패 안내를 받을 수 있었습니다. Transactional Outbox를 설계·구현해 결제 상태 변경과 발행 대상 기록을 하나의 트랜잭션으로 묶고, MySQL binlog CDC를 primary path로 Redis Leader Election·atomic claim·fallback polling·재시도 한도를 적용해 발행 실패를 추적·재처리 가능한 상태로 전환했습니다. 주 5~8건 발생하던 '결제 완료 후 발송 누락' CS 문의를 0건으로 줄였습니다.
- **결제 콜백 멱등성과 주문 상태 전이 안정성을 보강했습니다.** 중복 콜백, PG 창 이탈, 프론트 취소, 자동 취소, consumer 재처리가 겹치는 상황에서도 주문/결제 상태가 중복 변경되지 않도록 멱등 처리 기준을 정의했습니다.
- **여러 레포지토리에 분산된 레거시를 NestJS + Turborepo 모노레포로 이관하고 있습니다.** API·consumer·cron·도메인 모델이 여러 레포에 흩어져 변경 영향도 파악이 어려웠습니다. CMS와 신규 앱 기능부터 이관해 여러 API·consumer·cron 실행 단위와 공통 계층 구조를 정리하며 변경 범위와 배포 단위를 줄이고 있습니다.
- **공통 계층의 도메인 경계를 구조적으로 정리했습니다.** 공통 서비스 간 수평 참조가 늘며 사이드 이펙트 위험이 커지자, common-core/domain/infra/service로 계층을 분리하고 `no-horizontal-reference` 커스텀 ESLint 룰로 공통 계층에 범용 로직만 남도록 강제했습니다.
- **뱅크샐러드 제휴 연동을 설계부터 운영까지 담당했습니다.** 뱅크샐러드 제휴 API 연동 서비스를 직접 개발해 신규 트래픽 유입 경로를 확보하고, 웰컴페이먼츠·네이버 가격비교 채널 연동을 함께 리드했습니다.
- **100개 이상 배포 단위가 운영되는 EKS/GitOps 환경의 운영 가시성을 개선했습니다.** EKS + ArgoCD 기반 환경에서 Helm 배포, 수십 개 CronJob, Ingress, Secret 구성을 점검하고, Grafana·Prometheus·Datadog 기반 관측 환경에서 API·consumer·batch 작업의 장애 징후를 추적했습니다.

#### 기술 스택

TypeScript, NestJS, Express, Turborepo, MySQL, MongoDB, Redis, OpenSearch, AWS SQS, Kubernetes, EKS, Helm, ArgoCD, Docker, Grafana, Prometheus, Datadog

### (주)고스트패스

2022.12 - 2025.08  
Backend Engineer / 탈중앙화 생체인증 및 보안 솔루션

탈중앙화 생체인증 기술을 활용한 무인 출입·근태·결제 시스템을 개발했습니다. 기존 .NET + DB Stored Procedure 중심의 레거시 시스템을 NestJS/RDS/ECS 기반 구조로 전환하고, 모바일 중심 생체 비교 방식의 보안·속도 한계를 개선하는 키오스크 인증 플로우를 제안·설계했습니다.

#### 대표 기여

- **레거시 구조를 전환해 유지보수성과 운영 비용을 개선했습니다.** DB Stored Procedure에 비즈니스 로직이 집중되어 디버깅, 모니터링, 배포 단위 분리가 어려웠던 구조를 NestJS 애플리케이션 계층과 RDS 데이터 계층으로 분리했습니다. ECS/Fargate 기반 운영 구조로 전환해 AWS 비용을 약 40% 절감했습니다.
- **키오스크 인증 플로우를 제안·설계했고 특허로 등록되었습니다.** 모바일 중심 인증 방식은 인증 시점마다 여러 사용자 단말과 서버를 왕복해야 했고, 민감한 생체 데이터의 전달 범위와 응답 안정성 측면에서 한계가 있었습니다. 근접 사용자 탐색과 실제 인증 검증 책임을 나누고, 검증 책임을 키오스크 장치 쪽으로 옮기는 방식으로 재설계했습니다.
- **서버와 실제 장치 제어를 연결했습니다.** 인증 성공 이후 출입 제어가 실제로 동작하도록 ESP32 기반 디바이스 소프트웨어를 개발하고, 스피드게이트와의 통신 연동을 구현했습니다.
- **실시간 장치 통신 구조를 개선했습니다.** Firebase RTDB에 의존하던 장치 상태 동기화 일부를 MQTT 기반 통신 서버로 대체해 키오스크·서버·IoT 디바이스 간 인증 결과와 출입 제어 명령을 전달했습니다.
- **외부 SDK 제약을 서비스 구조에 맞게 분리했습니다.** 본인인증·결제 모듈이 C/Java SDK 중심으로 제공되는 제약을 NestJS 핵심 서버에 섞지 않고, 모노레포 내 Spring Boot 연동 서버로 분리했습니다.
- **CES 2024 혁신상, CES 2025 최고 혁신상 수상 기술의 백엔드 구조와 운영 기반을 담당했습니다.**

#### 기술 스택

TypeScript, NestJS, Spring Boot, Java, ASP.NET, PostgreSQL, MongoDB, Redis, MQTT, Firebase, ESP32, AWS ECS/Fargate, GitHub Actions, CodeDeploy, React

### (주)소셜인베스팅랩

2021.07 - 2022.12  
Full Stack Engineer / SNS 기반 주식 매매 플랫폼

SNS 기반 투자 플랫폼의 초기 제품 검증과 실서비스 운영 구간에서 백엔드, Android MVP, 증권사 API 연동, 계좌 개설 이벤트를 담당했습니다. 사용자의 돈과 계좌 상태가 연결되는 기능은 증권사별 응답 차이와 보상 처리 지연을 백엔드에서 흡수하는 방향으로 개발했습니다.

#### 대표 기여

- **증권사 API 연동의 상태값과 예외 흐름을 백엔드에서 표준화했습니다.** 신한, NH, KB, 삼성증권 API를 연동하며 증권사별 응답 형식과 상태값 차이를 흡수해 계좌 개설·매매 플로우를 일관되게 제공했습니다.
- **주식 계좌 개설 이벤트를 큐 기반 후속 처리로 설계했습니다.** 외부 증권사 확인, 보상 지급, 사용자 알림이 한 요청에 묶이면 지연·실패·중복 지급 위험이 커진다고 보고, 참여 조건 검증과 계좌 상태 확인, 보상 지급 요청을 SQS 기반 후속 처리로 분리했습니다.
- **실시간 주식 데이터 전달 구조 개선에 기여했습니다.** Socket.IO + Redis 기반 실시간 시세 전달 구조에서 종목별 구독 처리와 Redis 기반 데이터 공유 개선 작업을 수행해 반응 속도 30% 개선, 서버 부하 40% 이상 절감에 기여했습니다.
- **Android MVP를 1인 개발해 출시 병목을 줄였습니다.** 초기 앱 MVP를 직접 개발해 출시 일정을 약 1개월 단축했습니다.
- **서비스는 누적 다운로드 45만 건, 계좌 개설 4만 건을 달성했습니다.**

#### 기술 스택

Node.js, TypeScript, Socket.IO, Redis, MySQL, Android(Java), AWS Lambda, EventBridge, ECS/Fargate, SQS, AWS SNS, Slack API

### (주)디케이랩

2020.08 - 2021.06  
Backend Engineer / 스마트 공장 제조공정 시각화 솔루션

스마트 공장 제조공정 시각화 솔루션에서 설비 상태, 생산 이력, 협력사·ERP·MES 연동 API를 개발했습니다. 제조 현장에서 전화·메신저로 확인하던 설비 상태와 생산 이력을 대시보드와 외부 시스템에 반영하는 구조를 구현했습니다.

#### 대표 기여

- **설비 상태를 실시간으로 반영하는 구조를 구현했습니다.** 폴링 방식의 지연을 줄이기 위해 WebSocket 기반 실시간 설비 상태 전달 구조를 구현했습니다.
- **공장별로 다른 설비·공정 구조를 수용할 수 있게 설계했습니다.** DynamoDB/MongoDB 기반 비정형 공정 이력 저장 구조와 바코드 기반 생산 이력 추적 흐름을 설계했습니다.
- **MES·ERP 연동 API의 데이터 포맷 차이를 흡수했습니다.** 외부 시스템별 데이터 포맷 차이를 백엔드에서 정리하고, 공정 상태와 생산 이력을 일관된 형태로 조회할 수 있도록 API를 구성했습니다.
- **Lambda cold start 병목을 개선했습니다.** EventBridge warm-up 트리거를 도입해 초기 응답 시간을 600ms에서 100ms로 개선했습니다.

#### 기술 스택

Node.js, TypeScript, Python, AWS Lambda, API Gateway, WebSocket, DynamoDB, MongoDB, S3, CloudWatch

## 주요 프로젝트

### AI 개발 워크플로우 자동화

2026

Codex, Claude Code, Cursor를 코드 생성 도구로만 쓰지 않고, 레거시 분석 기준과 리뷰 품질, 테스트 시나리오, 문서화 비용을 줄이는 개발 산출물로 연결했습니다.

- 다수 레포지토리의 서비스 역할, DB 스키마, 배포 방식, 외부 연동 정보를 문서화 vault로 정리했습니다.
- 문서화 vault를 LLM/AI Agent가 레포지토리 맥락을 잃지 않고 참조할 수 있는 컨텍스트 기반으로 활용했습니다.
- PR 리뷰 시 도메인 정합성, 사이드 이펙트, 테스트 누락, 장애 가능성을 점검하는 리뷰 체크리스트를 설계했습니다.
- 결제·환불·정산처럼 예외가 많은 로직에 대해 실패, 재시도, 중복 요청, 보상 처리 테스트 시나리오 초안을 도출했습니다.
- Express.js 레거시 기능을 NestJS 모노레포로 이관할 때 라우트, 의존성, DB 접근, cron/consumer 흐름을 요약하고 작업 단위를 나누는 분석 방식에 적용했습니다.

### ClawShow - AI 소셜 플랫폼

2026

LLM 기반 AI 에이전트들이 참여하는 소셜 플랫폼을 풀스택으로 설계·개발한 개인 프로젝트입니다. 외부 에이전트 호출, 세션 상태, 실시간 진행 상황을 하나의 사용자 흐름으로 묶는 데 집중했습니다.

- NestJS(Fastify), TypeORM, PostgreSQL 기반 백엔드 설계 및 구현
- 외부 LLM 에이전트 연동에서 세션 관리와 응답 상태 추적을 구현하고, 진행 상태를 Socket.IO로 전달해 비동기 처리 흐름을 사용자에게 노출
- Next.js, React 19 기반 프론트엔드를 포함해 전 구간 단독 개발

### 미팍(Mepark) - 종합 주차 플랫폼

2022.03 - 2022.12  
https://valetkorea.com

중소 주차장 대상 PMS와 발렛파킹 전용 보험을 결합한 모빌리티 플랫폼의 백엔드 전반을 담당했습니다.

- AWS 서버 구축, 아키텍처·DB 설계, API 개발
- 주차장 키오스크 출차 결제 연동과 발렛 보험료 결제 흐름 설계
- DB손해보험 발렛파킹 전용 보험 연동
- 주차장 매출·입출차 통계 대시보드 API 응답 시간 10초에서 1초로 개선
- 모바일 앱 간편결제, 주차예약, 월주차 신청 백엔드 API 개발

## 오픈소스 및 개인 도구

- **ESP32_Firebase_RestApi_Stream**: 기존 ESP32 Firebase 라이브러리의 타임아웃 이슈를 해결하기 위해 Firebase REST API 기반 Auth, Get, Put, Stream 기능을 직접 구현했습니다.  
  https://github.com/hoonapps/ESP32_Firebase_RestApi_Stream
- **kr-validators**: 한국 서비스에서 자주 필요한 이메일, 전화번호, 주민등록번호, 사업자등록번호 유효성 검사를 제공하는 npm 패키지를 배포했습니다.  
  https://www.npmjs.com/package/kr-validators
- **개인 트레이딩 자동화 실험**: OpenAI API, 거래소 WebSocket, 기술적 분석, Telegram 알림, 일간 리포트를 연결한 개인 자동화 프로젝트를 구현했습니다.

## 교육

### 팀노바

2019.09 - 2020.03  
부트캠프

### 한국공학대학교

2013 - 2020  
메카트로닉스공학과
