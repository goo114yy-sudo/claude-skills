# 🧰 Claude 스킬 모음집

GitHub에서 많이 쓰이는 Claude 스킬을 골라 분야별로 정리한 개인 모음집입니다.
스킬 하나 = 폴더 하나이고, 폴더 안의 `SKILL.md`가 Claude가 읽는 지침입니다.
⭐ 표시는 원본 저장소의 GitHub 스타 수입니다 (2026-11-03 기준, 인기도 참고용).

## 사용 방법

| 어디서 | 방법 |
|---|---|
| **Claude.ai / Claude 앱** | 원하는 스킬 폴더를 zip으로 묶어 **설정 → 기능(Capabilities) → 스킬**에서 업로드 |
| **Claude Code** | 스킬 폴더를 `~/.claude/skills/` (개인용) 또는 프로젝트의 `.claude/skills/` 에 복사 |
| **내 스킬 만들기** | `my-skills/_template/` 을 복사해서 `my-skills/<스킬이름>/SKILL.md` 작성 |

> 💡 스킬은 이름만 언급하거나 관련 작업을 요청하면 Claude가 알아서 불러옵니다.
> 예: "systematic-debugging 스킬로 이 에러 원인 찾아줘"

## 처음이라면 이것부터 (추천 TOP 10)

| 스킬 | 왜 좋은가 |
|---|---|
| [diagram-design](skills/documents/diagram-design) | 네트워크·시스템 구성도, 플로차트를 깔끔한 그림으로 |
| [humanizer](skills/documents/humanizer) | 보고서·메일의 "AI가 쓴 티"를 없애줌 |
| [internal-comms](skills/documents/internal-comms) | 주간보고·장애보고·공지 양식 |
| [skill-creator](skills/development/skill-creator) | 내 업무용 스킬 만들 때 필수 |
| [brainstorming](skills/development/brainstorming) | 무언가 새로 만들기 전 요구사항 정리 |
| [systematic-debugging](skills/development/systematic-debugging) | 에러 원인을 추측 말고 근거로 추적 |
| [spec-driven-development](skills/development/spec-driven-development) | 스크립트·툴 만들기 전에 명세부터 |
| [security-and-hardening](skills/development/security-and-hardening) | 코드·설정 보안 점검 체크리스트 |
| [caveman](skills/development/caveman) | 답변을 짧고 핵심만 (토큰 절약) |
| [frontend-design](skills/design/frontend-design) | 사내 웹페이지·대시보드 디자인 |

## 폴더 구조

```
skills/
├── documents/       📄 문서·업무
├── development/     💻 개발·자동화
├── infra-security/  🛡️ 인프라·보안
├── design/          🎨 디자인·창작
└── references/      📋 개발 스킬들이 함께 쓰는 체크리스트
my-skills/           ✍️ 내가 직접 만든 스킬
licenses/            📜 원저작자 라이선스
```

---

## 📄 문서·업무 (`skills/documents`)

| 스킬 | 설명 | 출처 |
|---|---|---|
| [diagram-design](skills/documents/diagram-design) | 아키텍처도, IT 현황도, 플로차트, 시퀀스, ER 다이어그램 등을 HTML로 깔끔하게 그림 (서버·DB·클라우드 아이콘 포함) | cathrynlavery ⭐64k |
| [humanizer](skills/documents/humanizer) | AI 티 나는 문장(과장, 상투어, 억지 3단 나열 등)을 사람이 쓴 글처럼 다듬기 | blader ⭐64k |
| [internal-comms](skills/documents/internal-comms) | 주간 보고, 경영진 업데이트, 장애 보고서, 사내 공지·FAQ 등 사내 커뮤니케이션 문서 작성 | Anthropic ⭐187k |

**링크만 (복사 불가 / Claude에 이미 내장)**

| 스킬 | 설명 | 비고 |
|---|---|---|
| [docx](https://github.com/anthropics/skills/tree/main/skills/docx) · [xlsx](https://github.com/anthropics/skills/tree/main/skills/xlsx) · [pptx](https://github.com/anthropics/skills/tree/main/skills/pptx) · [pdf](https://github.com/anthropics/skills/tree/main/skills/pdf) | Word·Excel·PPT·PDF 생성·편집 | Claude 유료 플랜에 기본 탑재. 라이선스상 복사 금지 |
| [doc-coauthoring](https://github.com/anthropics/skills/tree/main/skills/doc-coauthoring) | 사람과 함께 문서를 단계별로 공동 작성 | Anthropic |
| [last30days](https://github.com/mvanhorn/last30days-skill) | 최근 30일 Reddit·X·웹에서 특정 주제 동향 리서치 | ⭐66k · 용량이 커서(18MB) 링크만 |
| [obsidian-skills](https://github.com/kepano/obsidian-skills) | Obsidian 메모앱 노트·캔버스 다루기 | ⭐51k · Obsidian 사용 시 |
| [meeting-insights-analyzer](https://github.com/ComposioHQ/awesome-claude-skills/tree/master/meeting-insights-analyzer) | 회의록 분석 (발언 비중, 회피 패턴 등) | Composio ⭐79k · 라이선스 미표기라 링크만 |
| [file-organizer](https://github.com/ComposioHQ/awesome-claude-skills/tree/master/file-organizer) | 폴더·파일 자동 정리, 중복 찾기 | Composio · 링크만 |
| [invoice-organizer](https://github.com/ComposioHQ/awesome-claude-skills/tree/master/invoice-organizer) | 영수증·청구서 정리 | Composio · 링크만 |

## 💻 개발·자동화 (`skills/development`)

**스킬·Claude 설정 관리**

| 스킬 | 설명 | 출처 |
|---|---|---|
| [skill-creator](skills/development/skill-creator) | 새 스킬 만들기·개선·성능 테스트 (공식) | Anthropic ⭐187k |
| [writing-skills](skills/development/writing-skills) | 스킬을 테스트 주도 방식으로 작성·검증 | obra/superpowers ⭐311k |
| [claude-md-improver](skills/development/claude-md-improver) | 프로젝트의 CLAUDE.md(Claude 작업 지침서) 점검·개선 | Anthropic 공식 플러그인 ⭐46k |
| [claude-automation-recommender](skills/development/claude-automation-recommender) | 코드베이스를 보고 쓸 만한 훅·스킬·MCP 자동화 추천 | Anthropic 공식 플러그인 |
| [caveman](skills/development/caveman) | 군더더기 없이 짧게 답하는 모드 (기술 내용은 유지, 토큰 절약) | JuliusBrussee ⭐117k |

**개발 작업 흐름 ①** — obra/superpowers ⭐311k (GitHub에서 가장 인기 있는 스킬 세트)

| 스킬 | 설명 |
|---|---|
| [brainstorming](skills/development/brainstorming) | 무언가 새로 만들기 전에 질문을 통해 요구사항·설계를 다듬기 |
| [writing-plans](skills/development/writing-plans) | 요구사항을 단계별 구현 계획으로 작성 |
| [executing-plans](skills/development/executing-plans) | 작성한 계획을 순서대로 실행 |
| [test-driven-development](skills/development/test-driven-development) | 테스트 먼저 작성 → 구현 (TDD) |
| [systematic-debugging](skills/development/systematic-debugging) | 버그·에러를 추측 대신 근본 원인부터 체계적으로 추적 |
| [verification-before-completion](skills/development/verification-before-completion) | "완료"라고 말하기 전에 실제 실행 결과로 검증 |
| [requesting-code-review](skills/development/requesting-code-review) | 작업 후 코드 리뷰 요청 |
| [receiving-code-review](skills/development/receiving-code-review) | 받은 리뷰를 무작정 따르지 않고 검증 후 반영 |

**개발 작업 흐름 ②** — addyosmani/agent-skills ⭐113k (구글 엔지니어 Addy Osmani 제작)

| 스킬 | 설명 |
|---|---|
| [interview-me](skills/development/interview-me) | 한 번에 하나씩 질문해서 내가 진짜 원하는 걸 끌어내기 |
| [spec-driven-development](skills/development/spec-driven-development) | 코드 짜기 전에 명세서부터 작성 |
| [planning-and-task-breakdown](skills/development/planning-and-task-breakdown) | 작업을 순서 있는 작은 단위로 쪼개기 |
| [code-review-and-quality](skills/development/code-review-and-quality) | 여러 관점(정확성·보안·성능·가독성)의 코드 리뷰 |
| [debugging-and-error-recovery](skills/development/debugging-and-error-recovery) | 빌드 실패·테스트 실패 시 근본 원인 디버깅 |
| [security-and-hardening](skills/development/security-and-hardening) | 입력 검증, 인증, 비밀값 관리 등 보안 강화 |
| [observability-and-instrumentation](skills/development/observability-and-instrumentation) | 로그·메트릭·트레이싱 넣어서 운영 중 문제 파악 |
| [git-workflow-and-versioning](skills/development/git-workflow-and-versioning) | 커밋·브랜치·충돌 해결 등 Git 작업 규칙 |
| [ci-cd-and-automation](skills/development/ci-cd-and-automation) | 빌드·배포 파이프라인 자동화 |
| [documentation-and-adrs](skills/development/documentation-and-adrs) | 설계 결정 기록(ADR)과 문서화 |
| [context-engineering](skills/development/context-engineering) | Claude 작업 품질이 떨어질 때 컨텍스트 정리 |

> ⚠️ 이 세트 일부는 `skills/references/` 의 체크리스트를 참조합니다. zip으로 따로 올릴 때는 해당 체크리스트도 같이 넣어주세요.

**도구·자동화**

| 스킬 | 설명 | 출처 |
|---|---|---|
| [playground](skills/development/playground) | 설정·데이터·코드 구조를 직접 만져볼 수 있는 HTML 플레이그라운드 생성 | Anthropic 공식 플러그인 |
| [mcp-builder](skills/development/mcp-builder) | MCP 서버 만들기 (Python FastMCP / TypeScript) | Anthropic |
| [webapp-testing](skills/development/webapp-testing) | Playwright로 로컬 웹앱 테스트·스크린샷 | Anthropic |
| [playwright-skill](skills/development/playwright-skill) | 브라우저 자동화 종합 (로그인, 링크 점검, 반응형 확인 등) | lackeyjb ⭐3k |
| [modern-python](skills/development/modern-python) | 최신 Python 도구 (uv, ruff) 로 프로젝트·스크립트 구성 | Trail of Bits ⭐8k |
| [gh-cli](skills/development/gh-cli) | GitHub 작업을 `gh` CLI로 처리 | Trail of Bits |
| [devcontainer-setup](skills/development/devcontainer-setup) | Claude Code용 격리 개발 컨테이너 구성 | Trail of Bits |

**링크만**

| 저장소 | 내용 | 비고 |
|---|---|---|
| [andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills) | LLM 코딩 실수를 줄이는 행동 지침 (카파시 원칙) | ⭐229k · 라이선스 미표기라 링크만 |
| [Understand-Anything](https://github.com/Egonex-AI/Understand-Anything) | 코드베이스를 지식 그래프로 분석 | ⭐90k · 별도 설치형 도구 |

## 🛡️ 인프라·보안 (`skills/infra-security`)

**운영·장애 대응**

| 스킬 | 설명 | 출처 |
|---|---|---|
| [system-health](skills/infra-security/system-health) | 서버 디스크·메모리·CPU·프로세스 빠른 점검 | agenticdevops ⚠️ |
| [log-analysis](skills/infra-security/log-analysis) | 리눅스·윈도우 등 로그 분석 패턴 | agenticdevops ⚠️ |
| [incident-response](skills/infra-security/incident-response) | 장애 발생 시 단계별 진단·대응 절차 | agenticdevops ⚠️ |
| [docker-ops](skills/infra-security/docker-ops) | Docker 컨테이너 운영·디버깅 | agenticdevops ⚠️ |

> ⚠️ agenticdevops 스킬은 내용은 무난하지만 사용자가 거의 없는 저장소라 검증이 덜 됐습니다. 쓰기 전에 내용을 한 번 확인하세요.

**보안 점검** — 보안 전문 기업 Trail of Bits 제작 ⭐8k

| 스킬 | 설명 |
|---|---|
| [semgrep](skills/infra-security/semgrep) | Semgrep으로 코드 정적 분석 (취약점 스캔) |
| [codeql](skills/infra-security/codeql) | CodeQL로 심층 정적 분석 |
| [sarif-parsing](skills/infra-security/sarif-parsing) | 스캐너 결과(SARIF 파일) 해석·정리 |
| [differential-review](skills/infra-security/differential-review) | 코드 변경분(PR·커밋)의 보안 리뷰 |
| [sharp-edges](skills/infra-security/sharp-edges) | 실수하기 쉬운 API·위험한 기본 설정 찾기 |
| [supply-chain-risk-auditor](skills/infra-security/supply-chain-risk-auditor) | 의존성 패키지 공급망 위험 감사 |

**링크만 (참고용)**

| 저장소 | 내용 |
|---|---|
| [claude-security](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/claude-security) | Anthropic 공식 코드 보안 스캔 플러그인 (독점 라이선스라 복사 불가, Claude Code에서 설치) |
| [hashicorp/agent-skills](https://github.com/hashicorp/agent-skills) | Terraform·Packer 공식 스킬 (HashiCorp) |
| [agenticdevops/agentic-ops-skills](https://github.com/agenticdevops/agentic-ops-skills) | K8s·AWS·Terraform·ArgoCD 운영 스킬 |
| [trailofbits/skills](https://github.com/trailofbits/skills) | 보안 감사 스킬 전체 목록 |

## 🎨 디자인·창작 (`skills/design`)

| 스킬 | 설명 | 출처 |
|---|---|---|
| [ui-ux-pro-max](skills/design/ui-ux-pro-max) | 웹·모바일 UI/UX 디자인 지식 데이터베이스 (스타일·색상·폰트 조합 추천) | nextlevelbuilder ⭐144k |
| [taste-skill](skills/design/taste-skill) | 코딩 에이전트에 "디자인 감각" 부여 (아트 디렉션, 레이아웃, 질감) | Leonxlnx ⭐109k |
| [frontend-design](skills/design/frontend-design) | "AI가 만든 티" 안 나는 개성 있는 웹 UI 디자인 | Anthropic |
| [theme-factory](skills/design/theme-factory) | 슬라이드·문서·웹페이지에 적용할 테마 10종 + 새 테마 생성 | Anthropic |
| [web-artifacts-builder](skills/design/web-artifacts-builder) | React·Tailwind·shadcn/ui로 복잡한 웹 아티팩트 제작 | Anthropic |
| [algorithmic-art](skills/design/algorithmic-art) | p5.js로 제너러티브 아트 만들기 | Anthropic |
| [slack-gif-creator](skills/design/slack-gif-creator) | 슬랙용 움직이는 GIF 만들기 | Anthropic |

**링크만**

| 저장소 | 내용 | 비고 |
|---|---|---|
| [impeccable](https://github.com/pbakaus/impeccable) | 디자인 용어·명령으로 UI 품질 높이기 | ⭐101k · 용량이 커서(97MB) 링크만 |

---

## ✍️ 내 스킬 (`my-skills`)

직접 만든 스킬은 여기에 둡니다. `_template/SKILL.md`를 복사해서 시작하세요.

| 스킬 | 설명 |
|---|---|
| [django-vue-postgres-app](my-skills/django-vue-postgres-app) | Django·Vue.js·PostgreSQL 사내 웹 서비스 구축 표준 (DB·API 설계, 넥센 보라색 UI 토큰, WORKLOG·유지보수 문서화 규칙) |
| [spec-driven-development](my-skills/spec-driven-development) | GitHub Spec Kit 방식: 원칙 → 명세(무엇·왜) → 계획(어떻게) → 작업 분해 → 구현 → 명세와 대조 |
| [web-design-judgment](my-skills/web-design-judgment) | 웹 UI가 "AI가 만든 티" 안 나게 하는 금지 규칙 + Emil Kowalski식 애니메이션 원칙 + 출시 전 체크리스트 |

> 참고: `skills/development/spec-driven-development`(addyosmani)와 이름이 같지만 내용이 다른 별개 스킬입니다. 내 스킬 쪽이 GitHub Spec Kit 8단계 방식입니다.

## 더 찾아볼 곳

- [anthropics/skills](https://github.com/anthropics/skills) — Anthropic 공식 스킬
- [anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official) — Anthropic 공식 플러그인 모음
- [obra/superpowers](https://github.com/obra/superpowers) — 개발 워크플로 스킬 모음
- [travisvn/awesome-claude-skills](https://github.com/travisvn/awesome-claude-skills) — 커뮤니티 스킬 목록
- [ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) — 업무 자동화 스킬 목록

## 출처와 라이선스

각 스킬은 원저작자의 라이선스를 따르며, 스킬 폴더 안의 `LICENSE` 파일 또는 `licenses/` 폴더에 보관했습니다. 스킬 내용은 원본 그대로이며 수정하지 않았습니다.
가져온 시점: 2026-11-03

| 출처 | 라이선스 |
|---|---|
| [anthropics/skills](https://github.com/anthropics/skills) | Apache 2.0 (각 폴더의 LICENSE.txt) |
| [anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official) | Apache 2.0 (각 폴더의 LICENSE) |
| [obra/superpowers](https://github.com/obra/superpowers) | MIT |
| [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) | MIT |
| [trailofbits/skills](https://github.com/trailofbits/skills) | CC BY-SA 4.0 |
| [cathrynlavery/diagram-design](https://github.com/cathrynlavery/diagram-design) | MIT |
| [blader/humanizer](https://github.com/blader/humanizer) | MIT |
| [nextlevelbuilder/ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) | MIT |
| [Leonxlnx/taste-skill](https://github.com/Leonxlnx/taste-skill) | MIT |
| [JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman) | Apache 2.0 |
| [agenticdevops/agentic-ops-skills](https://github.com/agenticdevops/agentic-ops-skills) | MIT |
| [lackeyjb/playwright-skill](https://github.com/lackeyjb/playwright-skill) | MIT |
