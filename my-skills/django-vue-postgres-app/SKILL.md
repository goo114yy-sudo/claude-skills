---
name: "django-vue-postgres-app"
description: "Django·Vue.js·PostgreSQL로 사내 웹 서비스나 관리 시스템을 새로 만들거나 기능·화면을 추가할 때 사용. API, DB 설계, 넥센 보라색 기반의 AI스럽지 않은 UI, 작업 기록과 유지보수 문서화 기준을 따른다."
---

# Django + Vue.js + PostgreSQL 웹 서비스 구축

사내 업무용 웹 서비스(신청·승인 시스템, 자산·장비 관리, 운영 대시보드 등)를 만들 때 따르는 표준이다. 목표는 네 가지다.

1. 유지보수하기 쉬운 구조
2. 사람이 만든 것 같은 차분한 업무 도구 UI
3. 관리자와 사용자 모두 설명서 없이 쓸 수 있는 화면
4. **작업할 때마다 기록을 남겨, 처음 보는 사람도 이어서 유지보수할 수 있는 프로젝트**

사용자가 다른 선택(JWT, Nuxt, 특정 UI 라이브러리 등)을 명시하면 그것을 우선한다.

## 0. 기본 스택

| 영역 | 선택 |
|---|---|
| Backend | Django(최신 LTS) + Django REST Framework, drf-spectacular, django-filter, django-environ |
| DB | PostgreSQL 16 이상, psycopg 3 |
| Frontend | Vue 3(Composition API, script setup) + Vite + TypeScript, Vue Router, Pinia, axios |
| Test/Lint | pytest-django, ruff / Vitest, ESLint, Prettier |
| 배포 | Rocky Linux + nginx + gunicorn(systemd), 또는 Docker Compose |

버전은 작업 시점의 안정 버전을 확인해 고정한다(requirements, package-lock).

## 1. 작업 순서 — 코드보다 설계 먼저

0. **시작 전에 `docs/WORKLOG.md`와 최근 `docs/decisions/`를 읽는다.** 이전 작업의 맥락, 남은 TODO, 결정 사항을 확인한 뒤 시작한다.
1. 짧은 스펙을 먼저 쓰고 사용자에게 확인받는다.
   - 역할(예: 일반 사용자 / 업무 관리자 / 시스템 관리자)과 각 역할이 하는 일
   - 역할별 화면 목록
   - 핵심 데이터 모델과 상태 흐름(예: 신청 → 검토 → 승인/반려 → 완료)
   - 외부 연동(AD/LDAP, 메일, 사내 포털 API 등)
2. 데이터 모델(ERD) 설계 → 확인 → `docs/data-model.md`에 기록
3. 프로젝트 스캐폴드
4. 모델·마이그레이션 → API → 권한 → 테스트
5. 프론트 레이아웃·디자인 토큰 → 화면 구현
6. 화면 검수(스크린샷으로 직접 확인)
7. **문서 갱신 + WORKLOG 기록**(10장) — 이 단계 없이는 작업이 끝난 것이 아니다.

작은 기능 추가라면 1~2단계를 짧게 하되 건너뛰지 않는다.

## 2. 프로젝트 구조

```
project/
├── backend/
│   ├── config/              # settings/{base,dev,prod}.py, urls.py, wsgi.py
│   ├── apps/
│   │   ├── accounts/        # 커스텀 User, 역할, 인증
│   │   ├── core/            # TimeStampedModel, 권한, 예외 처리, 페이지네이션, AuditLog
│   │   └── <도메인>/        # models, serializers, views, services, filters, tests
│   ├── manage.py
│   └── requirements/        # base.txt, dev.txt, prod.txt
├── frontend/
│   ├── src/
│   │   ├── api/             # axios 인스턴스 + 도메인별 API 함수
│   │   ├── layouts/         # UserLayout.vue, AdminLayout.vue
│   │   ├── views/user/      # 사용자 화면
│   │   ├── views/admin/     # 관리자 화면
│   │   ├── components/      # Button, DataTable, FormField, EmptyState 등 공통 UI
│   │   ├── composables/     # useList, useForm, usePermission
│   │   ├── stores/          # Pinia (auth, ui)
│   │   ├── router/
│   │   └── styles/          # tokens.css, base.css
│   └── vite.config.ts
├── deploy/                  # nginx.conf, gunicorn.service, 백업 스크립트
├── docs/                    # 유지보수 문서 (10장 참고)
│   ├── WORKLOG.md
│   ├── architecture.md
│   ├── data-model.md
│   ├── operations.md
│   ├── troubleshooting.md
│   └── decisions/
├── Makefile                 # 자주 쓰는 명령 모음
├── CHANGELOG.md
├── .env.example
└── README.md
```

## 3. 백엔드 (Django + DRF)

- **커스텀 User 모델**(`AUTH_USER_MODEL = "accounts.User"`)을 첫 migrate 전에 만든다. 나중에 바꾸기 매우 어렵다.
- 설정은 base/dev/prod로 분리하고 비밀값은 `.env`(django-environ)에 둔다. SECRET_KEY, DB 비밀번호를 코드에 넣지 않는다.
- `LANGUAGE_CODE = "ko-kr"`, `TIME_ZONE = "Asia/Seoul"`, `USE_TZ = True`.
- 업무 모델은 `core.TimeStampedModel`(created_at, updated_at, created_by, updated_by)을 상속한다.
- 비즈니스 로직은 `services.py`에 둔다. View와 Serializer는 얇게 유지한다. 상태 변경은 서비스 함수 + `transaction.atomic`.
- API는 `/api/v1/` 아래 ViewSet + Router. 목록은 페이지네이션, 검색·필터·정렬을 기본 제공한다.
- 상태 전이는 전용 action으로 만든다(`POST /api/v1/requests/{id}/approve/`). PATCH로 status를 직접 바꾸게 하지 않는다.
- 에러 응답 형식을 커스텀 exception handler로 통일한다.
  `{"code": "not_authenticated", "message": "사용자에게 보여줄 한국어 문장", "fields": {"title": ["필수 항목입니다."]}}`
  code에는 DRF 예외의 `default_code`를 그대로 쓴다.
- 권한은 역할 기반 Permission 클래스로 만든다. 프론트에서 메뉴를 숨겨도 **백엔드에서 반드시 다시 검사**한다. 객체 단위 권한(본인 신청만 조회)은 테스트로 보장한다.
- 인증: 같은 도메인에서 서빙하는 사내 시스템은 **세션 + CSRF**를 기본으로 한다. SPA가 csrftoken 쿠키를 받도록 `GET /api/v1/auth/csrf/`(ensure_csrf_cookie)를 둔다. 모바일 앱·외부 연동이 생기면 그때 JWT를 추가한다. AD 연동이 필요하면 django-auth-ldap.
- 참고: DRF SessionAuthentication은 미인증 요청에 401이 아니라 403을 반환한다. 프론트는 상태코드가 아니라 `code === "not_authenticated"`로 로그인 만료를 판단한다.
- 권한 변경, 승인, 삭제 등 중요한 변경은 AuditLog(누가, 언제, 무엇을, 이전값/이후값)에 기록한다.
- Django admin은 `/django-admin/`에 두고 시스템 관리자용 비상 도구로만 쓴다. 업무 관리자 화면은 Vue로 따로 만든다.
- 헬스체크 `GET /api/health/`(DB 연결 확인 포함)를 모니터링 연동용으로 둔다.
- OpenAPI 문서는 drf-spectacular `/api/docs/`. 운영에서는 관리자만 접근하게 한다.

## 4. PostgreSQL

```python
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": env("DB_NAME"),
        "USER": env("DB_USER"),
        "PASSWORD": env("DB_PASSWORD"),
        "HOST": env("DB_HOST", default="localhost"),
        "PORT": env("DB_PORT", default="5432"),
        "CONN_MAX_AGE": 60,
        "CONN_HEALTH_CHECKS": True,
    }
}
```

- 앱 전용 DB 사용자를 만들고(superuser 금지) 해당 DB 권한만 준다.
- 무결성은 DB에서도 보장한다: `UniqueConstraint`, `CheckConstraint`, 의도에 맞는 `on_delete`(업무 데이터는 PROTECT가 기본, CASCADE는 신중히).
- 자주 필터·정렬하는 컬럼에 `Meta.indexes`를 둔다. 상태 + 날짜 같은 복합 인덱스를 고려한다.
- 상태값은 `TextChoices`. 사용자가 관리해야 하는 코드값은 별도 모델로 만든다.
- 모델과 필드에 `verbose_name`(한국어)과 필요한 경우 `help_text`를 단다. DB 자체가 문서 역할을 하게 한다.
- JSONField는 구조가 정말 가변적일 때만 쓴다.
- N+1 방지: 목록 API는 `select_related` / `prefetch_related`를 쓰고, 쿼리 수 테스트(`assertNumQueries`)로 확인한다.
- 삭제가 업무상 위험하면 soft delete(`is_active` 또는 `deleted_at`).
- 마이그레이션 파일은 생성 후 내용을 확인한다. 운영 대용량 테이블 변경은 별도 계획을 세운다.
- 백업: `pg_dump -Fc` 일일 cron + 보관 기간. **복구 절차를 `docs/operations.md`에 적고 실제로 한 번 복구해 본다.**

## 5. 프론트엔드 (Vue 3)

- `script setup lang="ts"` + Composition API만 쓴다. Options API를 섞지 않는다.
- API 호출은 `src/api/`에서만 한다. 컴포넌트에서 axios를 직접 부르지 않는다.

```ts
// src/api/client.ts
import axios from "axios";

export const api = axios.create({
  baseURL: "/api/v1",
  xsrfCookieName: "csrftoken",
  xsrfHeaderName: "X-CSRFToken",
});

api.interceptors.response.use(
  (res) => res,
  (err) => {
    // 세션 인증은 미인증 시 403을 주므로 상태코드 대신 code로 판단한다
    if (err.response?.data?.code === "not_authenticated") {
      window.location.assign("/login");
    }
    return Promise.reject(err);
  },
);
```

- 개발 중에는 Vite proxy로 `/api`를 `http://localhost:8000`에 연결해 운영과 같은 same-origin 구조를 유지한다(CORS 설정 불필요).
- 라우트 meta에 `roles`를 두고 router guard에서 검사한다. 권한 없는 메뉴는 아예 노출하지 않는다.
- 목록 화면은 공통 `useList` composable을 쓴다. 페이지·검색·필터·정렬 상태를 **URL query와 동기화**해 새로고침과 링크 공유 시에도 유지되게 한다.
- 모든 데이터 화면은 네 가지 상태를 구현한다: 로딩 / 비어 있음 / 오류 / 정상.
- 폼: 백엔드 `fields` 오류를 각 필드 아래 인라인으로 표시, 제출 중 버튼 비활성화, 저장하지 않은 변경이 있으면 이탈 시 확인.
- `v-html`은 쓰지 않는다. 꼭 필요하면 sanitize 후 제한적으로.
- 날짜는 `YYYY-MM-DD HH:mm`, 숫자는 천 단위 콤마. 공통 formatter를 쓴다.

## 6. GUI 원칙 — AI가 만든 것 같지 않게

목표는 화려한 랜딩 페이지가 아니라 **매일 쓰는 잘 만든 업무 도구**다. 정보 밀도가 적당하고 차분한 화면(GitHub, Linear, Jira 관리 화면 같은 느낌)을 지향한다.

### 브랜드 색 — 넥센타이어 보라색

- 주색은 넥센타이어 CI의 **보라색**이다. 아래 토큰의 값은 임시값이며, **회사 공식 CI 가이드의 HEX 값을 받으면 그 값으로 교체**하고 hover·연한 배경 단계도 같은 색상에서 다시 만든다.
- 보라색은 **단색으로, 필요한 곳에만** 쓴다: 주요 버튼, 현재 선택된 메뉴, 링크, 포커스 표시, 선택된 행의 연한 배경. 넓은 면적(헤더 전체, 사이드바 전체, 배경)을 보라색으로 칠하지 않는다.
- 보라색 그라디언트는 쓰지 않는다. 보라·파랑 그라디언트는 AI 생성 UI의 대표적인 티라서, 브랜드가 보라색이어도 단색 사용이 원칙이다.
- 본문 글자색은 회색 계열, 보라색 위 글자는 흰색. 명도 대비 4.5:1 이상을 지킨다.

### 하지 말 것 (AI 생성 UI의 전형적인 티)

- 그라디언트 배경, 글래스모피즘, 네온·발광 효과
- 모든 요소를 큰 그림자 + 큰 라운드(16px 이상) 카드로 감싸기
- 의미 없는 히어로 섹션, "Welcome back 👋" 같은 인사말, 이모지 아이콘
- 근거 없는 KPI 카드 4개 + 장식용 차트를 나열한 대시보드
- 모든 텍스트 가운데 정렬, 과하게 큰 제목
- 모든 버튼·메뉴에 아이콘, hover마다 튀는 애니메이션
- "Lorem ipsum", "John Doe" 같은 샘플 문구, 영어·한국어가 섞인 라벨
- Tailwind 기본 색(indigo-500, violet-500 등)이나 UI 라이브러리 기본 테마를 그대로 쓰기

### 할 것

- **색**: 넥센 보라색 1개 + 중립 회색 스케일. 빨강·주황·초록은 오류·경고·완료 같은 상태를 뜻할 때만 쓴다.
- **표면**: 그림자 대신 1px 테두리와 배경 명도 차이로 영역을 나눈다. 라운드는 4~6px.
- **글꼴**: Pretendard(사내망·웹필터 환경 대비 웹폰트 파일 self-host). 본문 14px, 표 13~14px, 줄간격 1.5. 숫자 열은 `font-variant-numeric: tabular-nums`.
- **간격**: 4px 배수(4, 8, 12, 16, 24, 32)만 쓴다.
- **레이아웃**: 좌측 사이드바(메뉴) + 상단 바(현재 위치, 사용자) + 본문. 왼쪽 정렬. 표 화면은 넓게, 폼 화면은 본문 폭 640~720px.
- **문구**: 짧고 일관된 한국어 존댓말. 버튼은 동사형("저장", "승인", "반려"). 오류 메시지는 무엇이 문제이고 어떻게 하면 되는지 쓴다.
- 실제 업무 용어와 실제와 비슷한 데이터로 화면을 검수한다.

### 디자인 토큰 (styles/tokens.css)

```css
:root {
  /* 브랜드: 넥센타이어 보라색 — 공식 CI HEX를 받으면 아래 3개 값을 교체 */
  --color-primary: #5b2c83;          /* 주요 버튼, 선택 메뉴, 링크 */
  --color-primary-hover: #4a2370;    /* 주요 버튼 hover */
  --color-primary-subtle: #f3eef8;   /* 선택된 행·메뉴의 연한 배경 */

  --gray-0: #ffffff;
  --gray-50: #f7f8fa;
  --gray-100: #eef0f3;
  --gray-200: #dfe3e8;
  --gray-400: #9aa3ae;
  --gray-600: #5b6573;
  --gray-800: #2b323b;
  --gray-900: #1a1f26;

  --color-danger: #c62828;
  --color-warning: #b26a00;
  --color-success: #2e7d32;

  --border: 1px solid var(--gray-200);
  --radius: 6px;
  --font: "Pretendard", system-ui, sans-serif;
  --text-sm: 13px;
  --text-base: 14px;
  --text-lg: 16px;
  --text-xl: 20px;
}
```

- 색은 반드시 토큰으로만 쓴다. 컴포넌트 안에 HEX 값을 직접 쓰지 않는다(브랜드 색이 바뀌어도 토큰 한 곳만 고치면 되게).
- 컴포넌트: 기본은 토큰 기반 자체 컴포넌트(Button, Input, Select, DataTable, Modal, Badge, EmptyState 등 소수)로 만든다. DatePicker나 복잡한 테이블처럼 직접 만들기 비싼 것만 라이브러리(PrimeVue 등)를 쓰고, **테마를 토큰에 맞게 재정의**한다.

## 7. 직관적인 화면 — 역할별 기준

### 사용자 화면

- 첫 화면은 "내가 할 일과 내 요청 상태"다. 통계보다 다음 행동이 먼저 보이게 한다.
- 핵심 작업(새 신청 등)은 첫 화면에서 1클릭, 완료까지 3단계 이내.
- 진행 상태는 Badge + 단계 표시(신청 → 검토 → 완료)로 지금 어디인지 보여준다.
- 빈 상태에는 설명 한 줄 + 다음 행동 버튼을 둔다("아직 신청 내역이 없습니다." + [새 신청]).
- 입력 항목은 꼭 필요한 것만 받는다. 이름·부서처럼 알 수 있는 값은 미리 채운다.

### 관리자 화면

- **목록 → 상세 → 편집** 패턴으로 통일한다. 목록 상단에 검색·필터 바를 고정하고 결과 건수를 보여준다.
- 표: 정렬 가능한 컬럼, 행 클릭 시 상세, 체크박스 일괄 처리(일괄 승인 등), 엑셀(CSV) 다운로드.
- 처리 대기 건수는 메뉴 옆 숫자 배지로 보여준다.
- 위험 작업(삭제, 권한 변경)은 대상을 명시한 확인 모달을 띄운다. 되돌릴 수 없는 작업은 대상 이름을 입력해야 실행되게 한다.
- 상세 화면에 변경 이력(AuditLog) 탭을 둔다.
- 사용자 관리, 역할 부여, 코드 관리 화면을 기본으로 제공한다.

### 공통

- 성공은 짧은 토스트, 실패는 원인이 있는 위치(필드·영역)에 표시한다. 토스트를 남발하지 않는다.
- 페이지 제목이나 브레드크럼으로 현재 위치를 항상 보여준다.
- 키보드: Enter로 검색, Esc로 모달 닫기, 폼 탭 순서가 자연스럽게.
- 1280px 기준으로 설계하고, 사용자 화면은 모바일 폭에서도 깨지지 않게 한다.

## 8. 보안

- 운영: `DEBUG = False`, `ALLOWED_HOSTS`, `CSRF_TRUSTED_ORIGINS`, HTTPS 쿠키·`SECURE_*` 설정, `python manage.py check --deploy` 통과.
- 로그인 시도 제한(django-axes 등), 세션 만료 시간 설정.
- 파일 업로드: 확장자·크기 제한, 저장 경로는 웹 루트 밖, 다운로드는 권한 확인 후 서빙.
- 개인정보(전화번호, 사번 등)는 목록에서 마스킹하고, 필요하면 조회 이력을 남긴다.
- 원시 SQL은 피하고, 꼭 써야 하면 파라미터 바인딩.

## 9. 배포 (Rocky Linux 기준)

```
브라우저 → nginx (HTTPS, Vue 빌드 정적파일, /api·/django-admin 프록시)
         → gunicorn (systemd, unix socket) → Django → PostgreSQL
```

- `npm run build` 결과물을 nginx가 서빙한다. SPA 라우팅을 위해 `try_files $uri $uri/ /index.html;`.
- Django static은 `collectstatic` 후 nginx가 서빙한다.
- gunicorn workers는 (CPU 코어 × 2) + 1에서 시작하고, systemd로 자동 재시작한다.
- 외부에는 80/443만 열고, 5432는 앱 서버에서만 접근하게 한다.
- Docker Compose를 원하면 같은 구조(nginx, backend, db)로 compose 파일을 만든다.
- `/api/health/`를 모니터링(Zabbix 등)에 등록한다.
- 배포 절차, 로그 위치, logrotate 설정은 모두 `docs/operations.md`에 적는다.

## 10. 작업 기록과 유지보수 — 누구나 이어받을 수 있게

원칙: **이 프로젝트를 처음 보는 담당자가 README와 docs만 보고 30분 안에 로컬에서 실행하고, 1시간 안에 어디를 고쳐야 할지 알 수 있어야 한다.** 코드를 바꾸면 문서도 같은 작업 안에서 바꾼다.

### 10-1. 작업할 때마다 WORKLOG 기록 (필수)

작업(기능 추가, 수정, 장애 조치, 설정 변경)을 마칠 때마다 `docs/WORKLOG.md` **맨 위에** 항목을 추가한다. 최신 기록이 위에 온다.

```markdown
## 2026-10-14 — 신청 승인/반려 API 추가

- 작업자: 박동진 (Claude와 작업)
- 요청/배경: 관리자가 신청 건을 승인·반려할 수 있어야 함
- 변경 내용:
  - backend/apps/requests/services.py — approve(), reject() 추가
  - backend/apps/requests/views.py — approve/reject action 추가
  - frontend/src/views/admin/RequestDetail.vue — 승인·반려 버튼과 사유 입력 모달
- DB 변경: requests_request.reject_reason 컬럼 추가 (마이그레이션 0004)
- 확인 방법: make test / 관리자 계정으로 신청 상세 → 승인 클릭 → 상태 '승인' 확인
- 확인하지 못한 것: 메일 알림은 SMTP 정보가 없어 미테스트
- 남은 TODO: 반려 시 신청자 메일 알림
- 관련 문서: docs/decisions/0003-상태전이-action-방식.md
```

- 작은 수정이라도 기록한다. 몇 줄이면 충분하다.
- "무엇을"보다 **"왜"**를 꼭 남긴다. 코드만 봐서는 알 수 없는 것이 이유다.
- 장애 조치는 증상, 원인, 조치, 재발 방지를 함께 적고 `docs/troubleshooting.md`에도 요약을 옮긴다.

### 10-2. 문서 구성

| 문서 | 내용 | 언제 갱신 |
|---|---|---|
| `README.md` | 이 시스템이 무엇인지, 로컬 실행 방법(복사·붙여넣기로 되는 명령어), 문서 안내 | 실행 방법이 바뀔 때 |
| `docs/WORKLOG.md` | 작업 기록(10-1) | **매 작업** |
| `docs/architecture.md` | 전체 구성도, 앱별 역할, 외부 연동, 인증·권한 구조 | 구조가 바뀔 때 |
| `docs/data-model.md` | 주요 테이블, 관계(ERD), 상태 흐름, 코드값 의미 | 모델이 바뀔 때 |
| `docs/operations.md` | 서버 정보, 배포 절차, 재시작, 로그 위치, 백업·복구, 계정·인증서 갱신 주기 | 운영 방식이 바뀔 때 |
| `docs/troubleshooting.md` | 겪었던 문제와 해결 방법(증상 → 원인 → 조치) | 문제를 해결했을 때 |
| `docs/decisions/NNNN-제목.md` | 중요한 결정과 이유(ADR) | 새 라이브러리·구조·방식을 정할 때 |
| `CHANGELOG.md` | 사용자에게 보이는 변경을 버전·날짜별로 | 운영 배포할 때 |

- 결정 기록(ADR)은 짧게: 배경 / 선택지 / 결정 / 이유 / 영향. "왜 JWT가 아니라 세션인가" 같은 질문에 답할 수 있게.
- 문서에는 서버 주소, 계정 **이름**, 비밀값의 **보관 위치**까지만 적는다. 비밀번호·키 자체는 절대 적지 않는다.

### 10-3. 쉽게 읽히는 코드

- **똑똑한 코드보다 뻔한 코드.** 같은 일은 프로젝트 전체에서 한 가지 방식으로만 한다(목록은 항상 useList, 상태 변경은 항상 services).
- 새 패턴이나 라이브러리를 들일 때는 기존 방식으로 안 되는 이유를 ADR로 남긴다.
- 이름은 영어, 업무 규칙 주석은 한국어로 **"왜"**를 쓴다. 예: `# 반려 후 7일이 지나면 재신청 불가 (보안팀 요청, 2026-10)`
- 숫자·문자열 상수는 이름 붙인 상수나 settings로 뺀다(매직 넘버 금지).
- 파일이 300줄, 함수가 50줄을 넘으면 나눌지 검토한다.
- 서비스 함수에는 무엇을 하고 어떤 예외를 내는지 docstring을 단다.
- 죽은 코드, 주석 처리한 코드는 남기지 않는다. 기록은 Git과 WORKLOG가 한다.

### 10-4. 명령어는 Makefile로 통일

새 담당자가 명령어를 외우지 않아도 되게 한다. README에 이 표를 그대로 넣는다.

```makefile
setup:    ## 처음 한 번: 패키지 설치, .env 복사, DB 생성
dev:      ## 백엔드 + 프론트 개발 서버 실행
test:     ## 백엔드·프론트 테스트 전체 실행
lint:     ## ruff, eslint, prettier 검사
migrate:  ## 마이그레이션 생성 확인 후 적용
build:    ## 프론트 운영 빌드 + collectstatic
backup:   ## DB 백업 (pg_dump)
```

### 10-5. Git

- 커밋은 작게, 한 커밋에 한 가지 목적.
- 커밋 메시지는 한국어로 `[구분] 요약` 형식. 구분: 기능 / 수정 / 문서 / 리팩터링 / 설정 / 배포.
  예: `[기능] 신청 승인·반려 API 추가`
- 운영 배포 시 태그(`v1.2.0`)를 달고 CHANGELOG를 갱신한다.

## 11. 완료 전 체크리스트

- [ ] `python manage.py makemigrations --check` 통과, 마이그레이션 내용 확인
- [ ] 백엔드 테스트: 모델 제약, 권한(타인 데이터 접근 차단), 상태 전이, 주요 API
- [ ] 프론트: 타입 체크, 주요 composable 테스트
- [ ] ruff / ESLint / Prettier 통과
- [ ] 모든 화면의 로딩·빈 상태·오류 상태 확인
- [ ] Playwright 등으로 주요 화면 스크린샷을 찍어 6장의 "하지 말 것" 목록과 대조, 보라색이 필요한 곳에만 쓰였는지 확인
- [ ] 관리자·사용자 계정 각각으로 로그인해 메뉴와 권한 확인
- [ ] **`docs/WORKLOG.md`에 이번 작업 기록 추가**
- [ ] 바뀐 내용에 해당하는 문서 갱신(README, architecture, data-model, operations, troubleshooting, ADR)
- [ ] `.env.example`에 새 설정값 반영

## 12. 결과 보고

작업이 끝나면 사용자에게 짧게 정리한다.

1. 만들거나 바꾼 것(화면, API, 모델)
2. 로컬 실행·확인 방법
3. 갱신한 문서 목록(WORKLOG 포함)
4. 사용자가 결정해야 할 것, 남은 TODO
5. 확인한 것과 확인하지 못한 것(테스트, 화면 검수 결과)