# BidKit Marketplace

> **금융 IT 제안서를 AI 에이전트 팀이 분업하여 작성하는 Claude Code 플러그인**

100페이지 이상의 기술 제안서를 혼자 쓰고 계신가요? BidKit은 RFP(제안요청서)를 받으면 전략 수립부터 목차 설계, 섹션별 병렬 작성, 교차 검증, 최종 포맷 출력까지 — **5개 전문 AI 에이전트가 협업**하여 탑티어 수준의 기술 제안서를 만들어 줍니다.

---

## 왜 BidKit인가

금융 IT 제안서 작성의 현실적인 문제를 해결합니다:

- **100쪽 이상의 기술 문서** — 아키텍처, 제품 스펙, 이행 계획, 비용 산정, 규제 준수를 한꺼번에 다뤄야 함
- **기술 배경이 제한적인 제안 PM**이 여러 도메인을 조율해야 함
- **섹션 간 수치/용어 불일치**가 빈번하게 발생 (H/W 수량 ≠ 비용표, 제품명 불통일 등)
- **극심한 시간 압박** 속에서 품질을 유지해야 함

### 핵심 설계 아이디어

1. **"제안서 = 확정된 섹션들의 조립"** — 각 섹션은 독립된 SSOT(Single Source of Truth) 파일로 관리되며, 자기 자신의 상태·버전·검증 이력을 가짐
2. **"사용자 = PM, AI = 전문가 팀"** — 사용자는 방향을 잡고 승인하는 프로젝트 매니저, AI가 기술적 판단을 수행
3. **"명령어는 단축키, 자연어가 기본"** — 한국어 자연어 입력으로 동작

---

## 5-에이전트 아키텍처

```
                    ┌─────────────┐
                    │  Overseer   │  전략, 교차검증, 최종승인
                    └──────┬──────┘
           ┌───────┬───────┼───────┬───────┐
           ▼       ▼       ▼       ▼       ▼
        ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐  meta/
        │ BA  │ │ DA  │ │ TA  │ │ SA  │  outline.yaml
        │ Team│ │ Team│ │ Team│ │ Team│  glossary.yaml
        └─────┘ └─────┘ └─────┘ └─────┘
```

각 팀은 **Team Lead + Writer + Researcher + Critic** 4명으로 구성됩니다.

| 에이전트 | 역할 |
|----------|------|
| **Overseer (EA)** | 전사 전략 총괄. 팀 간 용어 통일, 수치 정합성, 전략 정렬 |
| **BA Team** | 비즈니스 분석 — 요구사항, 업무 프로세스, 사용자 인수 테스트 |
| **DA Team** | 데이터 아키텍처 — ERD, 데이터 흐름, 마이그레이션, 보안 |
| **TA Team** | 기술 아키텍처 — 시스템 구성, H/W·S/W, 비용 산정, 이행 계획 |
| **SA Team** | 솔루션 아키텍처 — 제품 스펙, 비교표, 레퍼런스, 라이선스 |

### 작업 흐름

```
① Team Lead: 맥락 파악, 의존성 확인
② Researcher: 자료 수집 (스펙, 레퍼런스, 규제 요건)
③ Writer: 초안 작성
④ Critic: 6차원 검증 (RFP 커버리지, 정확성, 갭, 교차 일관성, 용어, 규제)
   └─ FAIL → Writer 수정 → Critic 재검증 (PASS까지 반복)
⑤ Team Lead: 사용자에게 추천 + 대안 제시 → 승인
⑥ Overseer: 교차 검토 → 최종 확정
```

독립 섹션의 백그라운드 작업은 **팀 간 병렬** 수행. 사용자 대면 인터랙션만 순차적입니다.

---

## 3계층 품질 검증

| 계층 | 수행자 | 검증 내용 |
|------|--------|----------|
| **Critic** | 팀 내 독립 검증자 | RFP 커버리지, 데이터 정확성, 갭 분석, 교차 SSOT 일관성, 용어집 준수, 규제 준수 |
| **Overseer** | 전체 총괄 | 전 SSOT에 걸친 용어·수치·명명·전략 일관성 (8차원) |
| **Diagnose** | 통합 진단 | 전체 SSOT A/B/C/D 등급 + 교차 검증 + 개선 순서 제안 |

### 도메인별 Critical 검증 포인트

| 팀 | 주요 검증 항목 |
|----|--------------|
| **BA** | RFP 커버리지 누락, 업무 2단 구조(흐름도+화면) 미준수, 용어 불일치 |
| **DA** | 화면-모델 불일치, 암호화 누락, FK 미정의, 개인정보 파기 규칙 누락 |
| **TA** | H/W 수량 ≠ 비용표, HA 누락, 망분리 위배, 모니터링 임계값 미정의 |
| **SA** | 성능 측정조건 누락, 인증 만료, 비교표 편향, 라이선스-비용 불일치 |

---

## SSOT 상태 라이프사이클

각 섹션은 9단계 상태를 거쳐 최종 확정됩니다:

```
ideation → draft → verifying → verified → tentative → reviewing → confirmed
                      │                                    │
                      └── FAIL → draft                     └── directive → revision → verifying
```

| 상태 | 의미 |
|------|------|
| `ideation` | 방향 탐색 중 |
| `draft` | 초안 작성 완료, 검증 전 |
| `verifying` / `verified` | Critic 검증 중 / 통과 |
| `tentative` | 사용자 승인 완료, Overseer 검토 대기 |
| `confirmed` | 최종 확정 — 출력에 포함 |
| `revision` | Overseer 지시사항 반영 수정 중 |

---

## 사전 요구사항

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI 설치
- Claude Code **플러그인 기능** 활성화 (최신 버전 권장)

### 선택 의존성

| 도구 | 용도 | 없을 때 |
|------|------|--------|
| Git | 버전 관리, 변경 이력 | 경고 표시, 작성은 가능 |
| `bidkit-parser` | PDF/DOCX/PPTX → 마크다운 변환 | Claude가 직접 PDF 읽기 |
| Pandoc | PDF/PPTX 출력 | Markdown 출력만 가능 |
| Node.js | 고급 계약 검증 스크립트 | 기본 검증만 수행 |

> 어떤 의존성도 hard-blocking이 아닙니다. 없으면 안내하고, 멈추지 않습니다.

---

## 설치 방법

Claude Code 터미널에서 아래 명령어를 순서대로 실행하세요:

```bash
# 1. 마켓플레이스 등록
/plugin marketplace add hwaa-lee/bid-marketplace

# 2. BidKit 플러그인 설치
/plugin install bid@bid-marketplace
```

또는 `/plugin` 명령어로 플러그인 매니저 UI를 열어 직접 탐색·설치할 수도 있습니다.

설치가 완료되면 `/bid:setup`으로 환경이 제대로 구성되었는지 확인할 수 있습니다.

### 업데이트

플러그인 매니저(`/plugin`) → Installed 탭 → bid 선택 → "Update now"

### 설치 오류 해결

`Permission denied (publickey)` 에러가 발생하면, 캐시된 실패 데이터를 삭제 후 재설치하세요:

```bash
# 캐시 삭제
rm -rf ~/.claude-256/plugins/marketplaces/hwaa-lee-bid-marketplace

# Claude Code에서 다시 설치
/plugin marketplace add hwaa-lee/bid-marketplace
/plugin install bid@bid-marketplace
```

---

## 사용법

### 슬래시 명령어

| 명령어 | 목적 | 주도 에이전트 |
|--------|------|-------------|
| `/bid:setup` | 환경 점검 + 설치 안내 | — |
| `/bid:design` | 전략 수립 + 목차 생성 | Overseer |
| `/bid:write <section>` | 섹션 작성/수정 (모드 자동 감지) | Team Lead |
| `/bid:diagnose` | 품질 진단 + 교차 검증 | Overseer |
| `/bid:status` | 진행 현황 대시보드 | — |
| `/bid:output` | 최종 출력 (MD/PDF/PPTX/HTML) | — |
| `/bid:notion` | 노션에 제안서 업로드 (팀 리뷰용) | — |

> `/bid:write`의 `<section>`은 SSOT ID(`sa-hsm-001`), 제목(`HSM 솔루션`), 키워드(`hsm`), 목차 위치(`3.2.1`) 모두 지원합니다.

### 자연어로도 사용 가능

명령어를 외울 필요 없이 자연어로 말해도 됩니다:

| 이렇게 말하면 | 이렇게 동작합니다 |
|-------------|---------------|
| "RFP 받았는데 어디서부터?" | → `/bid:design` |
| "이행계획 어떻게 할지 고민 중이야" | → `/bid:write impl` (탐색 모드) |
| "HSM 모델 변경해야 해" | → `/bid:write hsm` (재편집 모드) |
| "전체적으로 좀 약한 것 같아" | → `/bid:diagnose` |
| "진행 상황 알려줘" | → `/bid:status` |
| "PDF로 출력해줘" | → `/bid:output` |
| "노션에 올려줘" | → `/bid:notion` |

### 일반적인 워크플로

```
1. RFP 문서를 프로젝트 폴더에 넣기 (PDF/DOCX/MD)
2. /bid:design              ← RFP 분석 → 전략 수립 → 목차 생성
3. /bid:write 사업개요        ← BA 팀이 작성
4. /bid:write 데이터모델      ← DA 팀이 작성
5. /bid:write 시스템아키텍처   ← TA 팀이 작성
6. /bid:write HSM솔루션       ← SA 팀이 작성
7. /bid:diagnose             ← 전체 교차 검증 + A~D 등급
8. /bid:output               ← confirmed SSOT 조립 → 최종 출력
```

### Proposal Guide

매 응답 하단에 현재 진행 상태와 다음 할 일이 자동으로 표시됩니다:

```
─────────────────────────────────────────────────
📋 Project: OO은행 차세대 시스템
─────────────────────────────────────────────────
✅ Done    : [v] 사업개요 (BA), [v] HSM솔루션 (SA)
🔄 Current : [~] 시스템아키텍처 (TA) — 초안 작성 중
💡 Next    : /bid:write 데이터모델 — DA 데이터 구조 설계
─────────────────────────────────────────────────
```

---

## 출력 포맷

| 포맷 | 용도 | 비고 |
|------|------|------|
| **Markdown** | 편집 가능한 소스 | 항상 생성 (외부 도구 불필요) |
| **PPTX** | 고객 제출용 | 회사 템플릿 매핑 지원 |
| **PDF** | 공식 제출, 인쇄 | 페이지 번호, 워터마크, 목차 자동 |
| **HTML** | 내부 리뷰 | 하이퍼링크, 검색, 상태 배지 |

출력은 콘텐츠 생성이 아니라 **confirmed SSOT의 조립**입니다. `outline.yaml` 순서대로 수집 → 교차참조 해결 → 용어집 삽입 → 렌더링.

---

## 프로젝트 디렉토리 구조

`/bid:design` 실행 시 다음 구조가 자동 생성됩니다:

```
proposal-project/
├── meta/
│   ├── proposal-meta.yaml     # 고객, 프로젝트명, 타임라인
│   ├── glossary.yaml          # 통합 용어집
│   ├── outline.yaml           # 목차 + SSOT 순서 + 의존성
│   └── rfp-trace-matrix.md    # RFP 요구사항 → SSOT 매핑
├── ssot/
│   ├── ba/*.md                # 비즈니스 분석 섹션들
│   ├── da/*.md                # 데이터 아키텍처 섹션들
│   ├── ta/*.md                # 기술 아키텍처 섹션들
│   └── sa/*.md                # 솔루션 아키텍처 섹션들
├── ideation/                  # 탐색 노트
├── assets/                    # RFP, 다이어그램 등
└── output/                    # 최종 출력물
```

---

## 플랫폼 지원

| 플랫폼 | 엔트리 포인트 | 인터페이스 |
|--------|-------------|---------|
| **Claude Code** | `CLAUDE.md` + `.claude-plugin/plugin.json` | `/bid:` 명령어 또는 자연어 |
| **Codex** | `AGENTS.md` | 자연어 |

---

## 플러그인 삭제

```
/plugin uninstall bid
```

---

## 라이선스

MIT
