# 🧰 Claude 스킬 모음집

여러 공개 저장소에서 쓸 만한 Claude 스킬을 골라 분야별로 정리한 개인 모음집입니다.
스킬 하나 = 폴더 하나이고, 폴더 안의 `SKILL.md`가 Claude가 읽는 지침입니다.

## 사용 방법

| 어디서 | 방법 |
|---|---|
| **Claude.ai / Claude 앱** | 원하는 스킬 폴더를 zip으로 묶어 **설정 → 기능(Capabilities) → 스킬**에서 업로드 |
| **Claude Code** | 스킬 폴더를 `~/.claude/skills/` (개인용) 또는 프로젝트의 `.claude/skills/` 에 복사 |
| **내 스킬 만들기** | `my-skills/_template/` 을 복사해서 `my-skills/<스킬이름>/SKILL.md` 작성 |

> 💡 스킬은 이름만 언급하거나 관련 작업을 요청하면 Claude가 알아서 불러옵니다.
> 예: "systematic-debugging 스킬로 이 에러 원인 찾아줘"

## 폴더 구조

```
skills/
├── documents/       📄 문서·업무
├── development/     💻 개발·자동화
├── infra-security/  🛡️ 인프라·보안
└── design/          🎨 디자인·창작
my-skills/           ✍️ 내가 직접 만든 스킬
licenses/            📜 원저작자 라이선스
```

---

## 📄 문서·업무 (`skills/documents`)

| 스킬 | 설명 | 출처 |
|---|---|---|
| [internal-comms](skills/documents/internal-comms) | 주간 보고, 경영진 업데이트, 장애 보고서, 사내 공지·FAQ 등 사내 커뮤니케이션 문서 작성 | Anthropic |

**링크만 (복사 불가 / Claude에 이미 내장)**

| 스킬 | 설명 | 비고 |
|---|---|---|
| [docx](https://github.com/anthropics/skills/tree/main/skills/docx) · [xlsx](https://github.com/anthropics/skills/tree/main/skills/xlsx) · [pptx](https://github.com/anthropics/skills/tree/main/skills/pptx) · [pdf](https://github.com/anthropics/skills/tree/main/skills/pdf) | Word·Excel·PPT·PDF 생성·편집 | Claude 유료 플랜에 기본 탑재. 라이선스상 복사 금지 |
| [doc-coauthoring](https://github.com/anthropics/skills/tree/main/skills/doc-coauthoring) | 사람과 함께 문서를 단계별로 공동 작성 | Anthropic |
| [meeting-insights-analyzer](https://github.com/ComposioHQ/awesome-claude-skills/tree/master/meeting-insights-analyzer) | 회의록 분석 (발언 비중, 회피 패턴 등) | Composio · 라이선스 미표기라 링크만 |
| [file-organizer](https://github.com/ComposioHQ/awesome-claude-skills/tree/master/file-organizer) | 폴더·파일 자동 정리, 중복 찾기 | Composio · 링크만 |
| [invoice-organizer](https://github.com/ComposioHQ/awesome-claude-skills/tree/master/invoice-organizer) | 영수증·청구서 정리 | Composio · 링크만 |

## 💻 개발·자동화 (`skills/development`)

**스킬 만들기**

| 스킬 | 설명 | 출처 |
|---|---|---|
| [skill-creator](skills/development/skill-creator) | 새 스킬 만들기·개선·성능 테스트 (공식) | Anthropic |
| [writing-skills](skills/development/writing-skills) | 스킬을 테스트 주도 방식으로 작성·검증 | obra/superpowers |

**개발 작업 흐름** — obra/superpowers의 핵심 워크플로 세트 (순서대로 쓰면 좋음)

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

**도구·자동화**

| 스킬 | 설명 | 출처 |
|---|---|---|
| [mcp-builder](skills/development/mcp-builder) | MCP 서버 만들기 (Python FastMCP / TypeScript) | Anthropic |
| [webapp-testing](skills/development/webapp-testing) | Playwright로 로컬 웹앱 테스트·스크린샷 | Anthropic |
| [playwright-skill](skills/development/playwright-skill) | 브라우저 자동화 종합 (로그인, 링크 점검, 반응형 확인 등) | lackeyjb |
| [modern-python](skills/development/modern-python) | 최신 Python 도구 (uv, ruff) 로 프로젝트·스크립트 구성 | Trail of Bits |
| [gh-cli](skills/development/gh-cli) | GitHub 작업을 `gh` CLI로 처리 | Trail of Bits |
| [devcontainer-setup](skills/development/devcontainer-setup) | Claude Code용 격리 개발 컨테이너 구성 | Trail of Bits |

## 🛡️ 인프라·보안 (`skills/infra-security`)

**운영·장애 대응**

| 스킬 | 설명 | 출처 |
|---|---|---|
| [system-health](skills/infra-security/system-health) | 서버 디스크·메모리·CPU·프로세스 빠른 점검 | agenticdevops |
| [log-analysis](skills/infra-security/log-analysis) | 리눅스·윈도우 등 로그 분석 패턴 | agenticdevops |
| [incident-response](skills/infra-security/incident-response) | 장애 발생 시 단계별 진단·대응 절차 | agenticdevops |
| [docker-ops](skills/infra-security/docker-ops) | Docker 컨테이너 운영·디버깅 | agenticdevops |

**보안 점검** — 보안 전문 기업 Trail of Bits 제작

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
| [hashicorp/agent-skills](https://github.com/hashicorp/agent-skills) | Terraform·Packer 공식 스킬 (HashiCorp) |
| [agenticdevops/agentic-ops-skills](https://github.com/agenticdevops/agentic-ops-skills) | K8s·AWS·Terraform·ArgoCD 운영 스킬 |
| [trailofbits/skills](https://github.com/trailofbits/skills) | 보안 감사 스킬 전체 목록 |

## 🎨 디자인·창작 (`skills/design`)

| 스킬 | 설명 | 출처 |
|---|---|---|
| [frontend-design](skills/design/frontend-design) | "AI가 만든 티" 안 나는 개성 있는 웹 UI 디자인 | Anthropic |
| [theme-factory](skills/design/theme-factory) | 슬라이드·문서·웹페이지에 적용할 테마 10종 + 새 테마 생성 | Anthropic |
| [web-artifacts-builder](skills/design/web-artifacts-builder) | React·Tailwind·shadcn/ui로 복잡한 웹 아티팩트 제작 | Anthropic |
| [algorithmic-art](skills/design/algorithmic-art) | p5.js로 제너러티브 아트 만들기 | Anthropic |
| [slack-gif-creator](skills/design/slack-gif-creator) | 슬랙용 움직이는 GIF 만들기 | Anthropic |

---

## ✍️ 내 스킬 (`my-skills`)

직접 만든 스킬은 여기에 둡니다. `_template/SKILL.md`를 복사해서 시작하세요.

| 스킬 | 설명 |
|---|---|
| _(아직 없음)_ | |

## 더 찾아볼 곳

- [anthropics/skills](https://github.com/anthropics/skills) — Anthropic 공식 스킬
- [obra/superpowers](https://github.com/obra/superpowers) — 개발 워크플로 스킬 모음
- [travisvn/awesome-claude-skills](https://github.com/travisvn/awesome-claude-skills) — 커뮤니티 스킬 목록
- [ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) — 업무 자동화 스킬 목록

## 출처와 라이선스

각 스킬은 원저작자의 라이선스를 따르며, 원본 폴더의 `LICENSE` 파일 또는 `licenses/` 폴더에 보관했습니다.
가져온 시점: 2026-11-03

| 출처 | 라이선스 |
|---|---|
| [anthropics/skills](https://github.com/anthropics/skills) | Apache 2.0 (각 폴더의 LICENSE.txt) |
| [obra/superpowers](https://github.com/obra/superpowers) | MIT |
| [trailofbits/skills](https://github.com/trailofbits/skills) | CC BY-SA 4.0 |
| [agenticdevops/agentic-ops-skills](https://github.com/agenticdevops/agentic-ops-skills) | MIT |
| [lackeyjb/playwright-skill](https://github.com/lackeyjb/playwright-skill) | MIT |
