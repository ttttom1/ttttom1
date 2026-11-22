# 안녕하세요, 윤찬호입니다 👋

**데이터로 가치를 만드는 백엔드 엔지니어.**
수집 구조 설계부터 분석·활용까지 전 과정을 함께 보는 시야로, 장애 상황까지 염두에 둔 설계를 지향합니다.

- 🎓 숭실대학교 정보통계보험수리학과 (주) / 컴퓨터공학과 (복수) — 2026.02 졸업유예
- 🏫 KT Cloud x 구름(groom) 백엔드 부트캠프 수료 예정
- 📫 yorion01@naver.com
- 📝 Blog: [Velog](https://velog.io/@ttttom1/posts)
- 🔗 LinkedIn: [윤찬호](https://www.linkedin.com/in/tv-요리온-7580223b1/)
- ✍️ *"데이터 분석의 가치는 해석 이전의 수집 환경·저장 구조·처리 흐름이 함께 뒷받침될 때 만들어진다."*

---

## 🛠 Tech Stack

**Backend** · Java, Spring Boot, Spring Security
**Data/Analytics** · Python, R, SQL
**Database** · PostgreSQL, MySQL, Redis
**Infra** · AWS EC2 / RDS / S3, Nginx, Flyway, Docker
**Methodology** · DDD, SOLID, Cache-Aside, TDD (Mock 기반 단위 테스트)

**Certs** · 정보처리기사 · SQLD · ADsP · OPIc IM2

---

## 📂 Projects

### 🍈 Melonne — 치료사 전용 폐쇄형 커뮤니티 (2026.01 ~ 현재)
> **Backend Lead** · 7인 팀 (PM · 디자이너 · FE · Cloud · BE 리드 5인 중 메인)
> 🔒 *조직 소유 Private Repository — 코드 공개 불가. 아키텍처·성과 상세는 이력서/면접 시 공유 가능.*
>
> 발달장애 아동 치료사 324명 자체 설문으로 현장 문제를 정의하고, 치료 일지 SaaS ↔ 커뮤니티를 연결하는 데이터 레이어까지 직접 설계.

**🔐 인증 / 인가 / 증명**
- Spring Security + JWT 기반 인증 파이프라인 구축, 역할(THERAPIST/USER) 전환 시점마다 **Redis 캐시 무효화**로 "캐시-DB 불일치" 구조적 차단
- 치료사 자격 증명 업로드 → S3 presigned URL · IAM 권한 · `afterCommit` 보상 로직으로 **Orphan 파일 방지**

**🗃 데이터 스키마 · DB 설계**
- 45개 API / 17개 테이블, Flyway V1~V20 마이그레이션으로 스키마를 **코드로 버전 관리**
- 반응 타입을 `EMPATHY / APPRECIATE / HELPFUL`로 세분화해 **추천 알고리즘 신호**로 재활용 가능한 구조 선제 설계

**🔎 검색 · 무한스크롤**
- PostgreSQL 기본 `tsvector`의 한국어 초성·부분 문자열 한계를 추적 → **`pg_trgm` GIN 인덱스 + ILIKE 병렬 조건**으로 관련도 검색 구축
- `(popularityScore, id)` 복합 커서 기반 **인기순 피드 무한스크롤**, 반응·스크랩 토글 시 점수 실시간 갱신

**⚡ 쿼리 플랜 분석 · 성능 튜닝**
- N+1 다중 발견(게시글 작성자 / 스크랩 여부 / 반응 count) → `@EntityGraph`·`JOIN FETCH`·IN절 배치 조회로 전환
- `EXPLAIN ANALYZE` 기반 인덱스 튜닝 + 조회 패턴에 맞춘 **반정규화 설계**

**🛡 Redis 캐시 · 트래픽 방어**
- 로그인 엔드포인트 **DDoS / 브루트포스 방어** (Rate Limiting + Null 캐싱 60s)
- **TTL Jitter** (`1800 + rand(300)s`) 로 캐시 동시 만료 분산 → DB 병목 차단
- JWT 인증 DB 조회 제거로 **DB 부하 구조적 감소**

**🏛 아키텍처 · 도메인 설계**
- `AuthService` God Class 분리 (의존성 8 → 6), `TokenService`로 책임 분리 (SRP)
- `ActivePostFinder` / `ResourceAccessValidator`로 중복 로직 6중→1곳 축소 (DRY+OCP)
- `MyPageFacade` 도입으로 `UserService` 외부 Repository 참조 **0개** (Facade + DDD 경계)
- **Mock 기반 단위 테스트 90개** 안전망 위에서 리팩토링

**🤖 진행 중**
- AI Auto-Comment 파이프라인 (커뮤니티 글 자동 응답 · 초안 생성)

---

### 📊 의료 데이터 분석 — 당뇨 예측 모델링
> K-Chip 코호트(n=16,955) + 국민건강영양조사(2022~2024, 약 2만 명)

- **결측치 35.1% (흡연량)** — MCAR/MAR/MNAR 진단 후 흡연 상태 **그룹별 조건부 대체**로 편향 제거
- **이상치** — 로그 변환 후 QQ-plot **R² 0.33 → 0.95** 정량 검증
- **다중공선성** — VIF / 상관계수(0.87/0.83) 기반 중복 변수 제거
- **클래스 불균형 (당뇨 유병률 5.4%)** — `class_weight` / SMOTE / Threshold 3전략 × 3모델 **9개 조합** 실험
  → SMOTE가 오히려 탐지율 **77% → 34%** 로 악화되는 역효과 실증 후 제외
- **한계 돌파** — 정확도 94.6% = "전원 정상 판정" 함정 식별 → 평가 지표 전환 → 핵심 변수 누락을 공공 데이터로 보완 → **탐지율 65% → 80%**
- **Data Leakage 방지** — `fit/transform` 분리, SMOTE는 train fold에만 적용

---

## 🧠 현재 관심사
- 에이전트 기반 로컬 LLM 파이프라인 · 지식 자동화 ([[P-Reinforce]] 프로젝트)
- AX(AI 전환)를 통한 도메인 특화 서비스 설계
- 장애 상황까지 설계하는 백엔드 — 캐시 정합성 · 트랜잭션 경계 · 관찰 가능성

---

> 💡 프로필의 contribution 그래프에는 organization private repo 커밋도 포함되어 있습니다.
> (*Settings → Profile → Include private contributions on my profile*)

*"가장 먼저 움직이고, 가장 끝까지 책임진다."*
