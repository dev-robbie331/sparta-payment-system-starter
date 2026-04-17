# Sparta Payment System

**팀스파르타 내일배움캠프 Spring 트랙 : 커머스 기반 결제 시스템 Starter 프로젝트**

이 프로젝트는 이커머스 결제 시스템 구축 강의의 **초기 셋업(Starter) 프로젝트**입니다.
수강생은 이 프로젝트를 fork & clone한 뒤, 강의를 따라가며 상품 · 회원 · 장바구니 · 주문 · 결제 · 환불 도메인을 직접 구현합니다.

> Spring Boot + JPA + MySQL + Thymeleaf UI가 미리 구성되어 있으며, **백엔드 API를 구현하면 화면이 동작**하는 구조입니다.

---

## 기술 스택

| 구분 | 기술 |
|------|------|
| Framework | Spring Boot 4.0.5 |
| Language | Java 17 |
| ORM | Spring Data JPA (Hibernate) |
| Database | MySQL 8 (Docker) |
| Template Engine | Thymeleaf |
| Security | Spring Security (학습용 전체 허용) |
| Build Tool | Gradle |
| Container | Docker Compose |

---

## 프로젝트 구조

```
sparta-payment-system/
├── build.gradle                  # 프로젝트 빌드 설정
├── settings.gradle               # 프로젝트 이름 설정
├── docker-compose.yml            # MySQL 컨테이너 설정
├── gradlew / gradlew.bat         # Gradle Wrapper
└── src/main/
    ├── java/com/sparta/paymentsystem/
    │   ├── SpartaPaymentSystemApplication.java   # 메인 애플리케이션
    │   ├── global/
    │   │   ├── config/
    │   │   │   ├── JpaAuditingConfig.java        # JPA Auditing 설정
    │   │   │   └── SecurityConfig.java           # Spring Security 설정
    │   │   └── entity/
    │   │       └── BaseTimeEntity.java           # 공통 시간 엔티티
    │   └── web/
    │       └── PageController.java               # 화면(View) 컨트롤러
    └── resources/
        ├── application.yaml          # 애플리케이션 설정
        ├── static/
        │   ├── css/style.css         # 공통 스타일시트
        │   ├── favicon.png           # 파비콘
        │   └── images/               # 상품 이미지 (9종)
        └── templates/                # Thymeleaf 화면 템플릿
            ├── index.html            # 메인(상품 목록) 페이지
            ├── layout/default.html   # 공통 레이아웃
            ├── auth/                 # 로그인 · 회원가입
            ├── cart/                 # 장바구니
            ├── order/                # 주문서 · 주문 목록 · 주문 상세 · 주문 완료
            ├── product/              # 상품 상세
            └── error/                # 에러 페이지
```

---

## 패키지 및 제공 코드 설명

### `SpartaPaymentSystemApplication.java`

Spring Boot 메인 클래스입니다. `@SpringBootApplication` 어노테이션으로 컴포넌트 스캔, 자동 설정, 설정 클래스 등록을 한 번에 처리합니다.

### `global/config/JpaAuditingConfig.java`

`@EnableJpaAuditing`을 활성화하는 설정 클래스입니다. 이 설정 덕분에 엔티티의 `@CreatedDate`, `@LastModifiedDate` 필드가 자동으로 채워집니다.

### `global/config/SecurityConfig.java`

Spring Security 설정 클래스입니다. **CSRF 비활성화 + 전체 요청 허용** 상태로 최초 구성되어 있습니다.

### `global/entity/BaseTimeEntity.java`

모든 엔티티가 상속받는 **공통 시간 추적 엔티티**입니다. `@MappedSuperclass`로 선언되어 테이블을 생성하지 않고, 상속받는 엔티티에 아래 두 컬럼을 자동 추가합니다.

| 필드 | 설명 |
|------|------|
| `createdAt` | 엔티티 생성 시각 (최초 1회 자동 기록, 수정 불가) |
| `updatedAt` | 엔티티 수정 시각 (변경 시마다 자동 갱신) |

### `web/PageController.java`

Thymeleaf 화면을 반환하는 **View 컨트롤러**입니다. 모든 데이터는 JavaScript가 백엔드 API를 호출하여 가져오는 구조이며, 이 컨트롤러는 HTML 템플릿만 반환합니다.

| URL | 반환 화면 |
|-----|----------|
| `GET /` | 메인 페이지 (상품 목록) |
| `GET /products/{id}` | 상품 상세 |
| `GET /login` | 로그인 |
| `GET /signup` | 회원가입 |
| `GET /cart` | 장바구니 |
| `GET /orders` | 주문 목록 |
| `GET /orders/{orderId}` | 주문 상세 |
| `GET /checkout` | 주문서 (결제 전) |
| `GET /orders/complete` | 주문 완료 |

### `application.yaml`

| 설정 항목 | 값 | 설명 |
|-----------|-----|------|
| `datasource.url` | `jdbc:mysql://localhost:3306/payment_db` | Docker MySQL 연결 |
| `jpa.ddl-auto` | `create` | 앱 시작 시 테이블 자동 생성 |
| `jpa.show-sql` | `true` | 실행되는 SQL을 콘솔에 출력 |
| `jpa.open-in-view` | `false` | OSIV 비활성화 (트랜잭션 밖 지연 로딩 방지) |
| `sql.init.mode` | `always` | `data.sql` 자동 실행 (초기 데이터 삽입용) |
| `thymeleaf.cache` | `false` | 개발 중 템플릿 변경 즉시 반영 |
| `server.port` | `8080` | 서버 포트 |
| `jwt.secret` | Base64 인코딩 키 | JWT 토큰 서명용 시크릿 |
| `jwt.expiration` | `3600000` (1시간) | JWT 만료 시간 |

### `docker-compose.yml`

MySQL 8 컨테이너를 실행하는 Docker Compose 파일입니다.

| 항목 | 값 |
|------|-----|
| 컨테이너 이름 | `sparta-payment-mysql` |
| Root 비밀번호 | `root1234` |
| 데이터베이스 | `payment_db` |
| 포트 | `3306:3306` |
| 캐릭터셋 | `utf8mb4` |

### `templates/` (Thymeleaf 화면)

강의 중 구현할 백엔드 API가 완성되면 동작하는 **프론트엔드 화면 템플릿**이 미리 제공됩니다. 수강생은 프론트엔드 코드를 수정할 필요 없이, 백엔드 API 구현에만 집중하면 됩니다.

### `static/images/`

상품 목록에 표시될 **샘플 상품 이미지 9종**이 포함되어 있습니다. (티셔츠, 니트, 코트, 데님, 치노, 조거, 스니커즈, 모자, 가방)

---

## 시작하기

### 1. 프로젝트 클론

```bash
git clone <repository-url>
cd sparta-payment-system-starter
```

### 2. MySQL 실행 (Docker)

```bash
docker-compose up -d
```

### 3. 애플리케이션 실행

```bash
./gradlew bootRun
```

### 4. 브라우저 접속

```
http://localhost:8080
```
