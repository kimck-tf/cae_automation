# SDD — cae-copilot: 버추얼 성능개발(CAE) 통합 지능화·자동화 파일럿

- **버전**: v1.3 (2026-07-05)
- **상태**: 설계 확정 — 구현 착수 전
- **이력**: v1.1 (2026-07-05) — 스펙 리뷰 1차 반영: 전이 충족 의미론 신설(§3.6), 승인 채널 분리(§6.1), 마일스톤 반려 전이 정의, 지표 산출성 정정(§0.2·§12), 구현 페이즈 P-넘버링(§13), tool_class 전수 매핑(§7.1) / v1.2 (2026-07-05) — 스펙 리뷰 2차 반영: 전진 공식 예외 2건(셀프서비스 ③ 면제·override FAIL 무효화), §2.2 승인 흐름 정정, tool_class 참조 단일화(§5→§3.1), pilot 명령 표면 보완(intervene·milestone_requested 기록 주체), 승인 2차 잠금, 지표 단위·해석 주석 / v1.3 (2026-07-05) — 스펙 리뷰 3차 반영: 2차 잠금을 approve 한정으로 정정(훅·빌더의 gate-record 정상 경로 보존), P0 테스트 항목 보강. 최종 승인(4차) 후 advisory 2건 추가 반영: gate-record 호출자 마커 스탬프(감사 실효화)·`!` 마커 상속 실증의 §11.2 병기
- **상위 문서(WHAT의 SSOT)**: `공모전_제안서_통합플랫폼_v6_260705(최종제출).md` — 본 SDD는 그 제안을 실제 구현하기 위한 HOW를 정의한다. 두 문서가 충돌하면 제안서의 "무엇·왜"가 우선하고, 본 SDD의 "어떻게"가 그것을 실현하는 수단이다.
- **시스템 가칭**: `cae-copilot` (리포지토리명, 변경 가능)

---

## 0. 개요

### 0.1 한 줄 정의

하네스 리포지토리를 "헌법"으로 삼는 Claude Code 세션이 MCP/CLI 도구로 CAE 해석 파이프라인(전처리→해석→후처리)을 End-to-End 실행하고, hooks가 작업 매니페스트(SSOT)를 근거로 **게이트 통과·마일스톤 승인 없는 단계 전이를 코드 수준에서 차단**하는 시스템.

### 0.2 목적

- 제안서 Phase 1(트랙① 부품 구조강도 → 트랙② 샤시 부품 피로 nCode 풀 파이프라인)을 **해석팀 실사용 파일럿**으로 구현한다. 공모전 실증 데모는 부산물이며 일정 제약이 아니다.
- 제안서의 핵심 주장 — "모든 산출물은 3중 검증 게이트 통과 필수" — 를 프롬프트 지시가 아닌 **코드(hooks + 결정론 검증기)가 보증**하도록 만든다.
- 실측 지표 5종(무개입 완주율·건당 절감시간·게이트 1회 통과율·동일 에러 재발률·자가수정 반복 횟수)이 **로그 파싱이 아니라 이벤트 원장 기반으로 산출**되도록 상태 모델을 설계한다. 단 산출성은 지표별로 다르다 — 3종(1회 통과율·재발률·자가수정 횟수)은 완전 자동, 무개입 완주율은 원장 + 개입 기록 규약, 건당 절감시간은 원장 + 엔지니어 병행 기록 결합(§12).

### 0.3 범위

**포함 (구현 수준 상세)**
- 하네스(실행 레일 + 검증 기준 + 환류 루프)
- 트랙①·② End-to-End 자동화 파이프라인
- 3중 검증 게이트(문제유형별 스코핑 포함)
- 운영 모드 이원화(마일스톤 승인 / 셀프서비스)
- 지능화 중 트랙 수행에 필요한 부분: 하중이력 분석, 모델·지식 Q&A(세션 컨텍스트 조회)

**제외 (인터페이스 훅만 예약 — §14)**
- 최적화 3종(치수·형상·위상), 여유도 스캔, 설계 협의 패키지 자동 편집의 본격 구현
- 지능화 중 원인진단·파라미터 스터디의 전용 구현(에이전트 일반 능력으로 수행 가능한 수준은 자연 지원)
- 임베딩 기반 RAG(파일+Grep 검색의 재현율 부족이 실측될 때 추가 — D4)

### 0.4 용어

| 용어 | 정의 |
|---|---|
| 하네스(Harness) | AI가 따라야 할 업무표준·절차·판단 기준·검증 장치의 총체. 본 설계에서 실체는 `CLAUDE.md` + `.claude/skills/` + `harness/` 디렉터리 |
| 게이트(Gate) | 산출물이 하네스를 지켰는지 확인하는 검증 단계. G1(표준 준수)·G2(추론 검증)·G3(물리·실적) 3층 |
| 매니페스트(manifest) | 작업 1건의 상태 SSOT — `jobs/<job_id>/manifest.json` |
| 마일스톤(M1/M2/M3) | 엔지니어 승인 지점: M1 모델 확정 · M2 해석조건 확정 · M3 결과 판정 |
| 무개입 완주율 | 반복 실행 단계에서 **승인 외 수동 개입** 없이 표준 절차를 완료한 작업 비율. 마일스톤 승인은 설계된 절차이므로 "개입"에 포함하지 않는다(§12) |
| problem_class | 문제 유형 분류: `closed_form` / `linear_static_complex` / `fatigue_load_history` / `nonlinear_advanced` — G3 판정 모드를 결정(§4.3) |

### 0.5 관련 문서·자산

| 자산 | 위치 | 본 설계에서의 역할 |
|---|---|---|
| 제안서 v6 (SSOT) | 이 폴더 `공모전_제안서_통합플랫폼_v6_260705(최종제출).md` | WHAT — 요구사항 원천 |
| 기술정본 재배치안 v8 | 이 폴더 `핵심구성요소_재배치안.md` | 4대 구성요소 구조·근거 설명(문구는 v6 우선) |
| hm-assistant | `C:\_PYTHON\0_CK_Project\4. Hypermesh assistant` | HyperMesh bridge(TCP JSON-RPC :9876) vendor 원본, 도구 정의·mock 서버·criteria 패턴, HM 2026.1 API 검증분 |
| agent_cae3 | `C:\_PYTHON\0_CK_Project\11. Agent_CAE_improve\agent_cae` | AbaqusWrapper·theory_validator·error_classifier·html_report 이식 원본 (610 테스트 자산) |
| ClaudeApp | `C:\_PYTHON\0_CK_Project\17. ClaudeApp` | H-Chat 게이트웨이 경유 claude-agent-sdk 검증 경로, 운영 GUI 후보, default-DENY 보안 게이팅 참조 |
| nCode 조사 | `_brain/knowledge/ncode-designlife-automation-mcp.md` | dtproc/.dcl·flowproc 배치 경로, FEABench 교훈 |

---

## 1. 설계 전제 (확정된 결정)

사용자 확인으로 확정된 5개 축:

1. **런타임 = Claude Code 하네스.** claude-agent-sdk(운영: H-Chat 게이트웨이) 위에 CLAUDE.md·Skills·Commands·서브에이전트·hooks로 하네스와 게이트를 구현한다. 기존 두 시스템(hm-assistant, agent_cae3)은 통째 통합하지 않고 **도구·라이브러리로 부품화**한다.
2. **목적 = 실무 파일럿 전용.** 해석팀 실사용이 설계 기준. 공모전 일정은 제약이 아니다.
3. **범위 = 코어 우선.** §0.3의 포함/제외를 따른다.
4. **환경 = 이중 프로파일.** 개발(사외 PC, Mock)·운영(사내 PC, 실물)을 `CAE_PROFILE`로 전환(§10).
5. **강제 수준 = 훅 강제 게이트형.** 에이전트는 단계 안에서 자유, 단계 전이는 코드가 통제.

확정된 환경 사실: HyperMesh **2026.1** / Nastran **사용(정식 러너)** / 하중이력 **.req·.rsp 모두** / 사내 표준 보고서 양식 **pptx**.

제약(제안서에서 상속): 전 과정 사내망 구동(운영), 데이터 외부 반출 없음, 에이전트는 사용자의 기존 권한·솔버 라이선스 범위 내 실행, 공식 판정 권한은 해석 엔지니어 유지, 예측형 ML 미사용(생성·추론형 LLM + 수치 엔진만).

---

## 2. 전체 아키텍처

### 2.1 계층도

```
사용자 (해석 엔지니어 / 설계·시험 셀프서비스)
   │  자연어 지시 · Q&A · 마일스톤 승인 — 단일 채팅 창구
   ▼
Claude Code 세션 ←─ 운영: H-Chat 게이트웨이(ClaudeApp 자산) / 개발: 외부 Claude
   │
   ├─ 하네스 표면 (.claude/)
   │    CLAUDE.md(헌법·안전규칙) · Skills(트랙별 표준절차·판단절차)
   │    Commands(/해석시작 /하중분석 /승인 /상태 /환류) · 서브에이전트(gate2-reviewer)
   │
   ├─ Hooks (게이트 강제 계층)          ◄── 판정 근거 = jobs/<id>/manifest.json (SSOT)
   │    PreToolUse: 게이트 PASS·승인 없이 다음 단계 도구 호출 차단
   │    PostToolUse: 산출물 생성 시 G1 자동 실행·기록
   │    Stop: 미결 게이트·미승인 마일스톤 경고 / SessionStart: 활성 job 요약 주입
   │
   ├─ 도구 계층
   │    MCP: hm_mcp — 기존 RPC 브리지(:9876) 재사용 + MCP 어댑터 한 겹
   │    CLI 러너: Abaqus/Nastran/OptiStruct/nCode — 공통 계약(submit/status/collect)
   │    분석기: 하중이력 분석(.req/.rsp) · 게이트 검증기(G1·G3)
   │    빌더: pptx 보고서·협의자료
   │
   └─ 데이터 계층
        harness/ (표준문서·criteria·사례카드·템플릿·wiki)
        jobs/<job_id>/ (manifest.json · events.jsonl · decks/ · results/ · reports/)
```

### 2.2 제안서 요소 → 구현 매핑

| 제안서 요소 | 구현 방식 | 출처 자산 |
|---|---|---|
| 하네스(실행 레일) | CLAUDE.md + Skills + `harness/` | 신규 + 사내 표준문서 |
| 단일 대화 창구 | Claude Code 세션(운영 GUI는 ClaudeApp) | ClaudeApp (게이트웨이 검증 완) |
| HyperMesh 텍스트 라이브 제어 | 기존 브리지 유지 + MCP 어댑터 | hm-assistant `bridge/` |
| Abaqus/Nastran 해석 | CLI 러너 이식 | agent_cae3 `AbaqusWrapper` + 신규 Nastran |
| nCode 피로 풀 파이프라인 | dtproc/.dcl 템플릿 flow 러너(신규) | _brain 조사 완 |
| 게이트1 표준 대조 | 결정론 검증기 + PostToolUse 자동화 | hm-assistant `config/criteria.py` 패턴 |
| 게이트2 추론 검증 | 검증 서브에이전트(작성 컨텍스트 분리) | 신규 |
| 게이트3 물리·실적 | 물리체크 + 실적범위 대조 + 단순문제 이론값 | agent_cae3 `theory_validator`·`error_classifier` |
| 운영모드 이원화 | 인간 전용 `!pilot approve`(§6.1) → manifest 기록 → PreToolUse 차단 | 신규 |
| 자가개선 환류 | events + 반려사유 → `harness/wiki/inbox/` → 하네스 개정 제안(사람 승인) | agent_cae3 사례 패턴 |
| 실측 지표 5종 | events.jsonl 자동 집계 | 신규 |

### 2.3 핵심 설계 결정 (D1~D6)

- **D1. 게이트·전이 강제 = hooks + manifest.** 에이전트는 단계 안에서 자유(자가복구 포함), 관문은 강제. "게이트 통과 필수"가 프롬프트 권고가 아니라 코드 보증이 된다.
- **D2. HyperMesh = 기존 RPC 브리지 보존 + MCP 한 겹.** "내부는 전용 RPC 세션엔진, 외부는 MCP 어댑터" 하이브리드 — 선행 분석(Critical_Review) 결론 그대로.
- **D3. 솔버 = CLI 러너 (MCP 아님).** 판정 기준: **"잡고 있어야 할 라이브 세션이 있으면 MCP, 호출이 자기완결 배치면 CLI. 지능은 도구가 아니라 하네스+에이전트에 둔다."**
  - 인풋덱 수정·로그 분석·에러 해결의 지능은 에이전트 네이티브(Read/Edit/Grep) + 문법 Skill + 플레이북이 수행한다. 열거형 MCP 도구로 덱 조작을 감싸면 hm-assistant가 45개 @tool을 만들어야 했던 문제가 되돌아온다 — Claude Code 런타임 선택의 최대 배당금(범용 파일 편집)을 반납하는 설계이므로 배제.
  - 자유 편집의 리스크(특히 Nastran 고정폭 필드)는 **G1 덱 린터 의무화**(매 편집 자동 실행)로 막는다. 타입 스키마가 아니라 결정론 검증이 안전판.
  - **폴백 2조건**: ① 운영 보안 검토에서 Bash prefix 화이트리스트 불충분 판정 시 같은 러너를 MCP 파사드로 감싼다(러너 코드 무변경). ② 해석 서버 연계 시 러너 백엔드만 교체(로컬 subprocess → 서버 큐 클라이언트).
- **D4. 하네스 검색 = 파일트리 + Grep 우선.** 임베딩 RAG는 검색 재현율 부족이 실측되면 추가(agent_cae3 RAG 자산 대기).
- **D5. 게이트2 = 별도 서브에이전트.** 작성 컨텍스트와 분리하되, **같은 LLM이므로 "완전 독립"이 아니라 완화 장치**임을 명세·보고 문구 양쪽에 명기한다. 인식론적 독립은 G3의 물리·실적 검증이 담당한다.
- **D6. fail-closed.** 검증기·훅 자체가 실패하면 게이트 대상 도구는 거부된다. 검증 불가 ≠ 통과.

---

## 3. 상태 모델 (SSOT)

### 3.1 단계 모델 — 선언적 트랙 정의

단계·게이트·마일스톤·도구 클래스 관계를 `pilot/tracks/<track>.yaml`로 선언한다. hooks와 pilot CLI가 이 파일을 읽어 판정한다. 절차 개정 = YAML+Skill 수정(하네스 개정 절차를 따름 — §6.3).

```
트랙① track1-structural                  트랙② track2-fatigue (차이만)
──────────────────────────              ──────────────────────────
S0 접수·계획 (problem_class 분류)         S0 + 하중이력 분석(rainflow·지배
S1 전처리-모델 (HM 메시·물성) ─[M1]           하중케이스·주손상 방향)
S2 전처리-조건 (하중·BC·덱) ──[M2]         S2: 단위하중 케이스 세트
S3 해석 실행 (러너·자가복구 루프)           S3 구조해석 → S3b nCode 피로
S4 후처리·판정 (G3) ─────────[M3]         S4: 피로 특화 G3
S5 보고 (pptx 보고서·협의자료 초안)
```

`tracks/track1.yaml` 스케치:

```yaml
track: track1-structural
stages:
  - {id: S0, name: 접수·계획,    exit_gates: [G2],     produces: [plan.md]}
  - {id: S1, name: 전처리-모델,  exit_gates: [G1],     milestone: M1, tool_classes: [hm_mutate]}
  - {id: S2, name: 전처리-조건,  exit_gates: [G1, G2], milestone: M2, tool_classes: [hm_mutate]}
  - {id: S3, name: 해석 실행,    exit_gates: [],       tool_classes: [solver_run]}
  - {id: S4, name: 후처리·판정,  exit_gates: [G2, G3], milestone: M3}
  - {id: S5, name: 보고,        exit_gates: [G1]}
tool_classes:
  hm_query:   ["mcp__hm__hm_get_*"]                          # 조회 — 상시 허용
  hm_mutate:  ["mcp__hm__hm_create_*", "mcp__hm__hm_set_*", "mcp__hm__hm_delete_*",
               "mcp__hm__hm_import_*", "mcp__hm__hm_mesh_*", "mcp__hm__hm_export_*",
               "mcp__hm__execute_tcl_safe"]                   # 변형·부수효과 — 전수 매핑 보증(§7.1)
  solver_run: ["Bash(python -m cae_tools.runners.*)"]
rules:
  - {tool_class: solver_run, requires: {gates_passed: [S1.G1, S2.G1, S2.G2], milestones_approved: [M1, M2]}}
    # gates_passed의 "passed" = §3.6 ② 기준(FAIL/ERROR 아님) · milestones_approved는 milestone 모드에 한함(§3.6 ③)
  - {tool_class: hm_mutate,  requires: {stage_in: [S1, S2]}}
```

### 3.2 manifest.json 스키마

```jsonc
{
  "job_id": "J-260705-001",
  "track": "track1-structural",
  "problem_class": "closed_form | linear_static_complex | fatigue_load_history | nonlinear_advanced",
  "mode": "milestone | self_service",        // 생성 시 고정, 중도 변경 금지
  "requester": {"name": "...", "role": "cae | design | test"},
  "request_text": "원 지시문 verbatim",
  "current_stage": "S2",
  "stages": {
    "S1": {
      "status": "pending | in_progress | completed | paused",
      "started_at": "...", "completed_at": "...",
      "artifacts": ["decks/bracket_v2.inp"],
      "gates": {"G1": {"verdict": "PASS", "attempts": 2, "last_result": "gates/S1_G1_2.json"}},
      "self_corrections": 3
    }
  },
  "milestones": {
    "M1": {"status": "approved | rejected | pending", "approver": "...", "reason": "...", "ts": "..."}
  },
  "created_at": "...", "updated_at": "..."
}
```

- **쓰기는 `pilot` CLI 한 곳으로 집중**: `pilot new` / `pilot advance` / `pilot gate-record` / `pilot approve` / `pilot intervene`. CLI가 전이 조건(tracks yaml)을 검사·거부하는 1차 방어선. hooks는 2차 방어망(심층 방어). `milestone_requested` 이벤트는 `pilot advance`가 마일스톤 대기 상태로 진입하는 시점에 자동 기록한다(별도 명령 없음).
- 활성 job 포인터: `jobs/ACTIVE` (job_id 1줄) — hooks가 참조. **Phase 1 제약: 리포당 동시 활성 job 1건.** 병행이 필요해지면 세션별 `CAE_JOB_ID` 환경변수가 ACTIVE보다 우선한다.

### 3.3 events.jsonl — 이벤트 원장 (append-only)

`{ts, type, payload}` 라인 단위. type: `stage_start | stage_complete | gate_run | gate_pass | gate_fail | self_correction | milestone_requested | approval | rejection | override | human_intervention | tool_error | session_summary`. 재작업으로 재발화되는 `stage_complete`는 `rework: true` 플래그를 갖는다(지표 이중 계수 방지). **지표 5종의 1차 산출 원천**(지표별 산출성 구분은 §12). manifest의 수치는 이 원장의 집계 캐시다.

### 3.4 GateResult 스키마

```jsonc
{
  "gate": "G1", "target": "decks/bracket_v2.inp",
  "verdict": "PASS | FAIL | ADVISORY | ERROR | NO_REFERENCE",
  "checks": [
    {"id": "unit-mm-t-s", "result": "pass",
     "expected": "E=210000 MPa, rho=7.85e-9 t/mm^3",
     "actual": "E=210000, rho=7.85e-9",
     "source": "harness/standards/units.md#단위계"}   // 모든 체크는 하네스 문서 좌표 인용
  ],
  "ts": "...", "duration_s": 4.2
}
```

- `ERROR` = 검증기 자체 실패 → fail-closed(D6). `NO_REFERENCE` = 대조할 실적 데이터 부재 → PASS 아님, 엔지니어 판단 필요 표시.

### 3.5 manifest 무결성

- `jobs/**/manifest.json`·`events.jsonl`의 직접 Edit/Write는 **훅이 차단**(pilot CLI 경유만 허용).
- pilot CLI는 모든 변경을 events에 동시 기록 — manifest와 events의 불일치는 감사 신호.

### 3.6 전이 충족 의미론 (pilot advance·훅의 판정 규칙)

단계 전이의 충족 조건 — 이 규칙이 상태기계의 전부다:

> **전진 가능 = ① 해당 단계의 모든 exit_gates 실행 완료 ∧ ② 각 verdict ∉ {FAIL, ERROR} (엔지니어 override로 무효화된 FAIL 제외 — §4.4) ∧ ③ 단계에 마일스톤이 지정돼 있으면 status=approved (milestone 모드에 한함 — self_service job은 ③ 면제, §6.2)**

- **PASS**: 충족.
- **ADVISORY / NO_REFERENCE**: 전이를 막지 않는다 — 단, 승인 요약 카드에 필수 표기되어 마일스톤 승인의 판단 대상이 된다. 마일스톤 대기가 생략되는 셀프서비스 job에서는 산출물 상태로 기록되고 '사전검토용' 워터마크가 그 역할을 대신한다(§6.2).
- **FAIL**: 수정 후 재실행(attempts 증가). **ERROR**: fail-closed — 전진 불가, 사람 호출.
- **마일스톤 반려 전이**: `rejected` 기록 시 해당 마일스톤의 소속 단계는 `in_progress`로 복귀(재작업). 재작업 후 게이트는 재실행되며 attempts는 누적된다. 마일스톤은 재요청(`milestone_requested`) 시 `pending`으로 복귀 → 재승인. 반려 사유는 §6.1 환류 경로를 따른다.

---

## 4. 3중 검증 게이트

### 4.1 G1 — 표준 준수 (결정론 Python, 상시·저비용)

- **트리거**: PostToolUse 자동 — `decks/**`·`reports/**` Edit/Write, HM 덱 export 후. 수동 호출도 가능.
- **검사 대상별 체크**:
  - 덱 린트: 단위계(mm-t-s: E=210000 MPa, ρ=7.85e-9 t/mm³), 필수 카드/키워드, **Nastran 고정폭 필드 정렬**, 재질–프로퍼티–섹션 연결 무결성, 사내 표준(명명 규칙·출력 요청 세트)
  - 메시 품질: S1 출구에서 hm_mcp 품질 리포트를 `harness/criteria/mesh_quality.yaml` 기준과 대조
  - 보고서 린트: 필수 섹션 존재, **출처 인용 존재(무출처 차단)**, 수치–단위 표기. 트리거 2경로 — 원고(md, LLM 작성 섹션)는 PostToolUse 자동, 최종 pptx는 빌더가 내장 린트 후 gate-record(§7.5)
- **기준의 이중 표현**: 사람용 `harness/standards/*.md` ↔ 기계용 `harness/criteria/*.yaml` 쌍. 모든 체크의 `source` 필드가 문서 절을 인용해 동기화를 강제.

### 4.2 G2 — 추론 검증 (LLM 서브에이전트, 중간 비용)

- **트리거**: 추론 산출물의 단계 출구 — S0 계획, S2 조건 정의, S4 판정 서술.
- **메커니즘**: `gate2-reviewer` 서브에이전트가 **작성 과정의 컨텍스트 없이** 산출물 + 관련 하네스 발췌 + 단계별 체크리스트만 받아 검토. 반환: `{concerns[], verdict}`.
- **검사 범위**: 가정 타당성, 요청–조건 정합(하중 방향·크기·구속이 요청과 맞는가), 논리 비약, 표준 해석절차와의 정합.
- **기록 진본성**: G2 verdict는 메인 에이전트 손을 거치지 않는다 — SubagentStop 훅이 gate2-reviewer 완료 시점에 결과를 파싱해 직접 `pilot gate-record`를 호출한다. 에이전트의 `gate-record` 직접 호출은 차단(§5).
- FAIL → 수정 전 전진 불가. **한계 명기(D5)**: 같은 LLM 기반이므로 완화 장치이며, 독립 검증은 G3가 담당.

### 4.3 G3 — 물리·실적 (결정론+데이터, 최종 관문)

**트리거**: S4. **problem_class별 판정 모드** — "만능 검증기" 오버클레임을 구조적으로 차단:

| problem_class | 자동 체크 | 판정 모드 |
|---|---|---|
| closed_form | 이론값 직접 대조 (theory_validator 이식) | **자동 PASS/FAIL** + 엔지니어 승인 |
| linear_static_complex | 평형 잔차(반력합=하중합)·단위 일관성·오더 체크·응력분포 패턴 + 유사 부품 **실적 범위 대조** | ADVISORY — 자동 판정 없음, M3 승인 필수 |
| fatigue_load_history | 지배 하중케이스 정합(하중이력 분석 ↔ 피로 기여도)·수명 오더·손상 위치 타당성 + 수명 실적 범위 | ADVISORY — M3 승인 필수 |
| nonlinear_advanced | 참고 신호만(자동 판정 배제) | ADVISORY — M3 승인 필수 |

- 실적 범위 DB = `harness/cases/` 사례 카드의 정량 필드(부품군·하중유형·응답 범위·출처 보고서·verified 플래그). **콜드스타트를 정직하게 계획**: Phase 1 파일럿 부품군 한정 수동 구축, 부재 시 `NO_REFERENCE`.

### 4.4 공통 규칙

- fail-closed(D6): verdict `ERROR`는 통과가 아니다.
- **override 절차**: 게이트 FAIL을 엔지니어가 검토 후 무효화할 수 있다 — 반드시 `pilot approve --override <gate> --reason`(인간 전용 채널, §6.1)으로 기록되고, `override` 이벤트는 **"개입"으로 계수**된다(지표 왜곡 방지, §12). 게이트 과잉 차단(false positive)의 안전밸브.
- 게이트 서술 규칙: 대외 문구는 "차단을 보증한다"가 아니라 **"리스크를 겹겹이 거르고, 자동판정 밖은 엔지니어 승인이 최종 방어선"** — 제안서 정직성 규약 상속.

---

## 5. Hooks 명세

| Hook | 트리거 | 규칙 |
|---|---|---|
| PreToolUse | `solver_run` 클래스 호출 | tracks yaml의 requires 검사: 선행 게이트 충족(§3.6 ② 기준) + (milestone 모드) M1·M2 승인. 미충족 → **차단**, 거부 사유를 구조화해 에이전트에 반환(승인 요청 유도) |
| PreToolUse | nCode 러너 호출 | S3 완료 + 하중이력 분석 산출물 존재 |
| PreToolUse | `hm_mutate` 클래스 (패턴 목록은 §3.1 tool_classes가 SSOT) | 활성 job + stage ∈ {S1, S2}. **조회(`hm_query`)는 상시 허용**(Q&A·세션 브리지) |
| PreToolUse | `jobs/**/manifest.json`·`events.jsonl` 직접 Edit/Write | 차단 (pilot CLI 경유만) |
| PreToolUse | 에이전트의 `pilot approve`(승인·반려·override)·`pilot gate-record` 호출 | **무조건 차단** — 승인은 인간 전용 채널(§6.1), 게이트 기록은 훅·검증기 전용 경로 |
| PreToolUse | 보고서 빌더 `--official` | M3 승인 필수. 미승인·셀프서비스 → '사전검토용' 워터마크 강제 |
| PostToolUse | `decks/**`·`reports/**` Edit/Write, HM export | **G1 자동 실행** → manifest 기록, FAIL 시 체크리스트 반환(수정 유도) |
| PostToolUse | 러너 종료 | result.json 파싱·기록, 에러 시 error_classifier 시그니처 로깅 |
| SubagentStop | gate2-reviewer 완료 | 결과 파싱 → `pilot gate-record`(G2) 직접 기록 — 메인 에이전트 미경유(§4.2) |
| Stop | 세션 종료 | 미결 게이트·미승인 마일스톤 요약 **경고(차단 아님)** + `session_summary` 이벤트 기록 |
| SessionStart | 세션 시작 | 활성 job의 현재 단계·미결 항목을 컨텍스트에 주입(중단 후 재개 인체공학) |

- **도구→단계 매핑은 도구 클래스 단위**(3.1 tool_classes)로 거칠게 유지 — 견고성 우선. 세밀한 절차 준수는 Skill(레일)이, 관문은 hooks(강제)가 맡는 역할 분리.
- **fail-closed 래퍼**: 훅 스크립트 예외 시 게이트 대상 도구 클래스는 거부 + 오류 보고.
- **자가복구 상한**: 단계당 self_correction 5회(설정값) 또는 동일 에러 시그니처 3회 연속 → 단계 `paused`, 사람 호출(무한 루프 방지 — agent_cae3 교훈).

---

## 6. 운영 모드 (Human-in-the-Loop)

### 6.1 마일스톤 승인 모드 (기본)

- M1/M2/M3 도달 시 에이전트가 **승인 요약 카드** 생성(Skill 정의 양식): 변경사항·게이트 결과·리스크·다음 단계 — 이때 `milestone_requested` 이벤트 기록(기록 주체 = `pilot advance`, §3.2 — 승인 대기시간 측정 기점).
- **승인은 인간 전용 채널**: 엔지니어가 Claude Code 입력창에서 `!pilot approve M1 --reason "..."`(`!` = 사용자 직접 실행) 또는 별도 터미널로 직접 실행 → pilot CLI가 manifest·events 기록. **에이전트의 `pilot approve` 호출은 훅이 무조건 차단**(§5) — 훅 입장에서 "사람이 시킨 승인"과 "에이전트 자발 승인"을 구분할 수 없으므로 채널 자체를 분리한다. `/승인` command는 대리 실행이 아니라 승인 카드 재표시 + 실행할 명령 안내다.
- **Phase 1 신뢰 경계**: 승인의 진본성은 "같은 PC, 같은 사용자 계정"(단일 사용자 파일럿)에 의존한다. 다중 사용자 운영으로 확장 시 승인자 인증 채널이 필요하다(§15).
- **2차 잠금(심층 방어) — approve 한정**: 훅의 명령 패턴 매칭에는 우회면(변형 표기·간접 호출)이 있으므로, pilot CLI 자체도 에이전트 세션 환경 마커를 감지하면 **approve를 거부**한다. **gate-record는 잠금 대상에서 제외** — 정당한 호출자(PostToolUse/SubagentStop 훅, pptx 빌더)가 모두 세션 프로세스 트리 안에서 실행되어 마커를 상속하므로, 마커 잠금을 걸면 정상 경로가 fail-closed로 정지한다. gate-record 보호는 1차 PreToolUse deny(§5) + manifest–events 대조 감사(§3.5)로 유지한다. 감사 실효화: pilot gate-record는 세션 마커를 감지해도 거부하지 않되 **이벤트 payload에 호출자 마커를 스탬프**한다 — 정상 경로 무영향, 난독화 우회 호출의 사후 감사 신호 확보. `!` 직접 실행이 세션 환경 마커를 상속하지 않는지 **P0 우회 테스트로 실증**하고, 상속한다면(사람 채널까지 오차단) 별도 터미널을 기본 승인 채널로 확정한다.
- **반려 사유는 `harness/wiki/inbox/`로 자동 적재** — 환류 루프의 1차 입력원.

### 6.2 자동 진행 모드 (셀프서비스)

- job 생성 시 `mode=self_service` 고정(중도 전환 금지 — 전환하려면 새 job).
- 마일스톤 **대기만 생략**, 게이트(G1·G2·G3)는 전부 상시 작동. G3의 "M3 승인 필수"(§4.3)는 셀프서비스에서 '사전검토용' 워터마크 + 공식 판정 차단으로 **대체**된다 — 승인이 사라지는 게 아니라 공식화 시점(엔지니어 검토)으로 이연되는 것.
- 산출물 전체 '사전검토용' 워터마크, 공식 판정·`--official` 보고서 차단.
- **Phase 1 개방 범위 제한**: 트랙①의 `closed_form`·`linear_static_complex`만. 피로·비선형은 마일스톤 모드 강제. 게이트 강건성이 실측으로 확인된 범위만 개방한다는 제안서 원칙의 실행형.

### 6.3 환류 루프 (자가개선)

- 입력원: 승인·반려 사유, self_correction 기록(에러 시그니처→해결책), 완료 해석의 노하우, Q&A 로그(지식 갭 신호 — 수집은 프롬프트 규약: 세션 중 반복·미답 질문을 에이전트가 `wiki/inbox/`에 적재. 강제 메커니즘 아님을 명기).
- `/환류` command(또는 주기 배치): `wiki/inbox/` + events를 정리해 **사례 카드·플레이북·표준 개정 제안 초안** 생성 → **사람 승인 후** `harness/` 반영(하네스 개정도 게이트를 거친다).
- 강건화 계측: 게이트 1회 통과율↑ · 동일 에러 재발률↓ · 자가수정 횟수↓ (§12와 동일 원장).

---

## 7. 도구 계층

### 7.1 hm_mcp — HyperMesh MCP 서버

```
Claude Code ←(MCP stdio)→ hm_mcp (신규, thin 릴레이)
                             ↓ TCP JSON-RPC :9876 — 기존 프로토콜 무변경
                          hm_bridge/ (HyperMesh 2026.1 내장 Python — hm-assistant vendor)
```

- bridge는 이 리포로 **vendor 복사**(fork-copy, 출처 명기) — 파일럿을 단일 배포물로. hm-assistant는 독립 유지.
- hm_mcp 역할: 도구 스키마 노출, RPC 중계, 재연결·타임아웃, 에러 정규화. **로직 없음.**
- **명명 규칙 = 훅 게이팅의 지탱점**: `hm_get_*`(조회) / `hm_create_*`·`hm_set_*`·`hm_delete_*`·`hm_import_*`·`hm_mesh_*`·`hm_export_*`(변형·부수효과). `execute_tcl_safe`는 hm_mutate 클래스에 명시 등재. **전수 매핑 보증**: hm_mcp 서버는 기동 시 등록 도구 전체가 조회/변형 클래스 중 하나에 매칭되는지 자체 검사하고, 미분류 도구가 있으면 기동을 거부한다(fail-closed).
- 도구 카테고리(hm-assistant 45 @tool 이식): 조회(모델트리·컴포넌트·품질리포트·바운딩박스·선택 엔티티), CAD 임포트(2026.1 API 검증분), 메시(tetmesh volume-only 기본), 물성·프로퍼티, 하중·BC(node-based·박스 선택), 덱 export(Abaqus/OptiStruct/Nastran 프로파일), `execute_tcl_safe`(화이트리스트만).
- **hm-assistant 5대 원칙 전면 계승** — 집행 위치 3중(MCP 인자 검증 + G1 + Skill 지침):
  mm 단위 강제 / node-based BC / volume-only tet / RP 동적 계산(`get_bounding_box`→`select_nodes_by_box` 체인) / TCL raw 노출 금지.
- `hm_get_*` 조회 도구가 곧 **세션 컨텍스트 브리지**: "이 부품 응력이 왜 높지?" 류 지시어 질문에 모델트리·선택 엔티티·최신 결과를 조회해 즉답. 대형 FE 모델은 컨텍스트에 담지 않고 질의 도구로 조회(제안서 2.6).

### 7.2 솔버 러너 — 공통 CLI 계약

```
python -m cae_tools.runners.<solver> submit  --deck <path> --job-dir <dir>   → job_ticket.json (즉시)
python -m cae_tools.runners.<solver> status  --job-dir <dir>                 → running|completed|failed
python -m cae_tools.runners.<solver> collect --job-dir <dir>                 → result.json + extracted/*.csv
```

- **submit은 detach 실행** — Claude 세션 종료와 무관하게 해석 지속, 재접속 후 collect(수시간 잡 대응).
- `result.json` 공통 스키마:

```jsonc
{
  "solver": "abaqus | nastran | optistruct | ncode",
  "state": "completed | failed", "exit_code": 0, "wall_time_s": 1234,
  "error": {"family": "...", "code": "...", "signature": "...", "log_excerpt": "..."},  // 실패 시
  "results": {
    "max_stress": {"value": 312.5, "unit": "MPa", "location": "..."},
    "max_displacement": {"value": 1.82, "unit": "mm", "location": "..."},
    "reactions": {"sum": [...], "applied": [...]},     // G3 평형 잔차용 — 필수 추출 항목
    "extracted_files": ["extracted/stress_field.csv"]
  }
}
```

- 에러 분류: agent_cae3 `error_classifier`(4 family × 21 sub-code) 이식 — 시그니처가 재발률 지표·플레이북·환류의 공통 키.
- 솔버별:
  - **Abaqus**: AbaqusWrapper 이식(입력 스크립트 동적 생성 → `abaqus cae noGUI` → odbAccess 추출). 관련 테스트 동반 이식.
  - **Nastran**: 정식 구현 — .bdf 실행, `.f06`(텍스트 로그·에러)·`.op2`(결과) 추출. .op2가 nCode의 FE 입력이므로 트랙②의 기본 구조해석 경로.
  - **OptiStruct**: 동일 계약, 로컬 라이센스 보유. 단 P2 구현 범위는 Abaqus·Nastran 우선 — OptiStruct는 계약만 공유하는 후순위(제안서 명시 솔버가 아니므로 포함 여부는 계획 단계에서 판단).
  - **nCode**: §7.3.

### 7.3 nCode 러너 — 템플릿 flow 치환

- **패턴**: GUI에서 대표 피로 flow(`.flo`) 1개 제작 → 러너가 FE 결과(.op2)·하중이력(.req/.rsp)·재료 SN 경로와 파라미터만 치환 → `dtproc <dcl>` / `flowproc <flo>` 배치 실행 → 수명·손상 CSV 추출. (공식 외부 Python API 부재 → subprocess 래핑이 표준 경로)
- 단일 머신 실행(MPI 분산 불필요).
- ⚠️ **미결 #1**: 정확한 CLI 인자 문법은 공개 웹에 없음 → 로컬 설치본 매뉴얼 'Batch Processing' 섹션에서 확정(P4 착수 조건). 템플릿 flow 방식이라 문법 의존면 자체가 좁다.

### 7.4 하중이력 분석기

- `cae_tools/analysis/load_history.py`: 입력 `.req`·`.rsp` **모두** → rainflow 카운팅, 채널별 손상 추정(Miner), Damage Rose(주손상 방향), **지배 하중케이스 랭킹** → `load_analysis.json` + 시각화 PNG. (주하중방향 시각화 PoC 이식)
- 출력이 트랙② S0 산출물이자 **G3-피로 "지배 하중케이스 정합" 체크의 기준값** — 분석과 검증이 같은 데이터를 공유.

### 7.5 보고 빌더 (pptx)

- `report build --job <id> [--official]`: **python-pptx 기반 사내 표준 양식 템플릿 치환** + LLM 작성 섹션(요약·근거·트레이드오프) + **게이트 증빙 자동 첨부**(manifest에서 게이트 결과·승인 이력).
- 시각화: agent_cae3 `html_report` 렌더러 이식(헤드리스 matplotlib — 컨투어·이력 차트)→ PNG를 pptx에 삽입.
- 빌더는 pptx 생성 직후 **내장 보고서 린트**(§4.1)를 실행하고 결과를 gate-record로 기록한다 — pptx는 에이전트 Edit 경로 밖(바이너리, 빌더 생성물)이므로 PostToolUse 대신 빌더가 트리거를 겸한다.
- `--official`은 M3 승인 필수(훅). 미승인·셀프서비스는 '사전검토용' 워터마크.
- 협의자료 초안: Skill이 대책 요약·근거·트레이드오프·증빙 구조로 작성 → G1 보고서 린트(필수 섹션·출처 인용) 통과 필수.

---

## 8. 트랙 파이프라인 — 단계 × 도구 × 스킬

| 단계 | 트랙① 구조강도 | 트랙② 샤시 피로 (차이만) |
|---|---|---|
| S0 | Skill `track1-structural`: 요청 해석·problem_class 분류·해석 계획 → G2 | + `load_history` 실행, 지배 하중케이스 선정 → G2 |
| S1 | hm_mcp: 기존 FE모델 수정(주경로) / CAD 임포트(2026.1) → 덱 export → G1 → **M1** | 동일 |
| S2 | hm_mcp: 하중·BC(node-based) → 덱 export → G1·G2 → **M2** | 단위하중 케이스 세트 정의 |
| S3 | runner(abaqus/nastran/optistruct) submit→status→collect + **자가복구 루프**(.msg/.f06/.dat Read → `error-playbooks` Skill → 덱 수정(Edit) → G1 자동 → 재실행) | + S3b: runner.ncode (flow 치환) |
| S4 | collect 결과 → 판정 서술 → **G2**(판정 추론 검증)·**G3**(problem_class별) → 승인 카드 → **M3** | G3-피로(정합·오더·실적범위) |
| S5 | `report build`(pptx) + 협의자료 Skill → G1 보고서 린트 | 동일 |

덱 수정·로그 해석·에러 해결의 주체는 **에이전트 네이티브 능력 + Skill**이다(D3). 러너는 실행·라이센스·바이너리 추출 등 에이전트가 물리적으로 할 수 없는 것만 담당한다.

---

## 9. 리포지토리 구조 (모노리포)

```
cae-copilot/
├─ CLAUDE.md                  # 헌법: 역할·5대 원칙·게이트 의무·금지사항·용어
├─ .claude/
│  ├─ skills/                 # track1-structural/ track2-fatigue/ load-analysis/
│  │                          # error-playbooks/ report-writing/ approval-cards/
│  │                          # optimization/ (자리만 예약 — §14)
│  ├─ commands/               # 해석시작 하중분석 승인 상태 환류
│  ├─ agents/                 # gate2-reviewer.md
│  ├─ hooks/                  # pre_tool_use.py post_tool_use.py stop.py session_start.py
│  └─ settings.json           # 권한 allowlist + 훅 등록
├─ pilot/                     # job 관리 CLI · tracks/*.yaml · metrics 집계
├─ gates/                     # g1_lint/ (deck·mesh·report) · g3_physics/ (theory·평형·실적대조)
├─ cae_tools/
│  ├─ hm_mcp/                 # MCP 서버 (bridge 클라이언트)
│  ├─ hm_bridge/              # vendor: hm-assistant bridge (HM 2026.1)
│  ├─ runners/                # abaqus.py nastran.py optistruct.py ncode.py
│  ├─ analysis/               # load_history.py (.req/.rsp)
│  └─ report/                 # pptx builder + matplotlib 렌더러
├─ harness/
│  ├─ standards/              # 사내 표준 (운영: 실문서 / 개발: 샘플)
│  ├─ criteria/               # 기계가독 기준 yaml (standards와 쌍)
│  ├─ cases/                  # 사례 카드 = 실적 범위 DB
│  ├─ templates/              # 덱 템플릿 · nCode flow 템플릿 · pptx 양식
│  └─ wiki/                   # inbox/ (환류 수집) → 정리된 카드
├─ jobs/                      # ACTIVE 포인터 + <job_id>/ (manifest·events·decks·results·reports)
└─ tests/                     # 게이트 FAIL 검출 · 훅 우회 시도 · Mock E2E
```

---

## 10. 환경 프로파일·보안

### 10.1 이중 프로파일 (`CAE_PROFILE=dev|ops`)

| | 개발 (사외 PC) | 운영 (사내 PC) |
|---|---|---|
| LLM | 외부 Claude | H-Chat 게이트웨이(`ANTHROPIC_BASE_URL`, ClaudeApp 검증 경로) |
| HyperMesh | Mock HM 서버(hm-assistant 자산 이식·확장) | HyperMesh 2026.1 + bridge |
| 솔버 | Mock 러너(canned result.json) | Abaqus·Nastran·OptiStruct·nCode 실물 |
| 하네스 문서 | 샘플·비식별 | 사내 표준·보고서(기존 권한 체계 내) |
| jobs | 합성 케이스 | 실 부품 케이스 |

- **훅·게이트 로직은 프로파일 무관 동일** — dev에서 검증한 강제 체계가 ops에서 그대로 작동.
- Mock 규칙: 파이프라인·훅·게이트 로직 검증용. 물리 의미를 갖는 mock은 트랙① 대표 케이스 1종(검증값 정합 역산 — hm-assistant 응답모델 패턴)만.
- hooks·MCP·서브에이전트는 전부 **클라이언트(Claude Code 프로세스) 측 기능** — 게이트웨이는 LLM 엔드포인트만 바꾼다. 운영 확인 대상은 기능 유무가 아니라 게이트웨이의 rate/토큰 한도(§15).

### 10.2 보안·권한

- 운영 전 과정 사내망, 데이터 외부 반출 없음(제안서 2.7).
- `settings.json` allowlist: hm_mcp 도구 / `python -m cae_tools.*`·`python -m gates.*`·`python -m pilot` Bash prefix / Read·Edit는 리포+jobs 범위. 운영 프로파일에서 웹 접근 deny. **deny가 allow에 우선**: `pilot approve`·`pilot gate-record`의 에이전트 호출 차단(§5)은 allowlist 안에서도 유효하다.
- manifest 무결성(§3.5): 장부 조작에 의한 게이트 우회 경로 봉쇄.
- 에이전트는 사용자 계정 권한·라이선스 범위 내 실행. 공식 판정 권한은 엔지니어 유지(M3 + `--official` 훅).
- 운영 GUI로 ClaudeApp 채택 시 default-DENY 정책과 본 리포 allowlist의 정합 확인(P3 체크 항목).

---

## 11. 검증 전략 — 이 시스템이 옳음을 어떻게 아는가

1. **단위 — FAIL 검출 우선 원칙.** 게이트 검증기 테스트는 **의도적 오류 주입 코퍼스**(단위 틀린 덱, 필드 밀린 .bdf, 반력 불일치 결과, 실적 범위 밖 수명 등)를 "잡아내는지"로 작성한다. PASS 케이스만 테스트하는 자기만족을 금지한다 — 검증기의 존재 증명은 "맞혔다"가 아니라 **"틀린 것을 잡는다"**(동어반복 함정의 테스트 규약화).
2. **통합 — Mock E2E + 우회 시도 테스트.** 트랙① 합성 케이스 완주에 더해 **적대적 시나리오가 1급 테스트**: 승인 없이 솔버 호출→차단 / 게이트 FAIL 상태로 전진→차단 / manifest 직접 수정→거부 / **에이전트가 `pilot approve` 호출→차단 / 에이전트가 `gate-record` 직접 호출→차단 / 훅·빌더 경유 gate-record는 통과(정상 경로 보존, §6.1 2차 잠금의 approve 한정 검증)** / 자가복구 상한 초과→중단 / `!` 직접 실행의 세션 마커 상속 여부 실증(§6.1). 보조: criteria yaml의 `source` 인용이 standards 문서에 실존하는지 검사하는 스테일 참조 테스트.
3. **실측 — 운영 파일럿.** 실 부품 케이스로 지표 5종 수집 + 엔지니어 병행 기록. **무개입 완주율 80%는 목표치이며 실측으로 확정**한다(실측치/목표치 구분 유지 — 제안서 층위 그대로).

---

## 12. 지표 정의 (events.jsonl 산식)

| 지표 | 산식 | 산출성 |
|---|---|---|
| 무개입 완주율 | `human_intervention`(및 `override`) 이벤트 0건으로 완주한 job / 전체 완주 job | 원장 + 개입 기록 규약(하단) |
| 건당 절감시간 | 기준선(8~16h, 대표 12h) − (승인 대기: `milestone_requested`→`approval` 간격 + 개입 소요) | 원장 + 엔지니어 병행 기록 |
| 게이트 1회 통과율 | 최초 실행이 PASS인 단계-출구 게이트 인스턴스 / 전체 인스턴스 | 완전 자동 |
| 동일 에러 재발률 | 동일 error 시그니처의 job 간 재발 비율 | 완전 자동 |
| 자가수정 반복 횟수 | `self_correction` 이벤트 수 / job | 완전 자동 |

**"개입"의 정의(고정)**: 마일스톤 승인·반려는 설계된 절차이므로 개입이 아니다. 개입 = 승인 외 수동 수정·수동 재실행·게이트 override·자가복구 상한 초과로 인한 사람 호출. 이 정의를 고정해 지표 조작 여지를 제거한다.

**`human_intervention`의 기록 경로(3중)**: ① 자동 — 자가복구 상한 초과 paused, 게이트 override(훅·pilot이 기록). ② 사람 직접 — `pilot intervene --reason`(수동 수정·수동 재실행 시 엔지니어가 기록). ③ 보조 — 세션 중 사람의 수동 개입을 에이전트가 인지하면 기록 의무(Skill 규율). **완전 자동 감지가 아니며**, 파일럿 중 ②의 준수가 지표 신뢰도의 전제다.

**게이트 1회 통과율의 집계 단위**: 분모 = 단계-출구 게이트 인스턴스(단계×게이트)의 최초 실행. 단계 내 자동 재실행(예: 자가복구 중 덱 재편집마다 도는 G1)은 분모에서 제외하고 `self_correction` 신호로 계수한다 — 두 지표의 역할 분리(관문 품질 vs 수정 횟수). 초안 반복이 많은 단계에서는 자연히 낮게 나오는 특성이 있으므로 파일럿 보고 시 해석 주석을 병기한다.

**절감시간의 단위 구분**: `milestone_requested→approval` 간격은 벽시계 리드타임이라 야간·부재 대기를 포함한다. 맨아워 절감(제안서 산식의 단위)은 엔지니어 병행 기록이 기준이고, 원장 간격은 리드타임 보조 지표로만 쓴다(단위 혼합 시 왜곡·음수 가능).

---

## 13. 구현 순서 (구현 페이즈 P0~P5)

승인 마일스톤 M1~M3(§0.4)과 **구분되는 별도 이름공간**으로 P-넘버링을 쓴다. 상세 계획은 별도 문서로, **P 단위로 분할 작성**한다(단일 plan으로 다루지 않는다).

```
P0 골격       리포 구조 + pilot CLI/manifest + hooks + tracks.yaml + CLAUDE.md v1
              → "훅 차단이 작동하는 빈 파이프라인" (우회 시도 테스트 이때부터)
P1 전처리     hm_mcp + bridge vendor(2026.1) + G1 덱 린트 + S1·S2 Skill
P2 트랙① 완주 Abaqus·Nastran 러너 + G2 서브에이전트 + G3(구조) + pptx 빌더
              → Mock E2E 통과 = dev 프로파일 완성
P3 운영 이관  사내 설치 + 실 하네스 문서 + 파일럿 실측 개시          (P2 후)
P4 트랙②     load_history(.req/.rsp) + nCode 러너 + G3-피로          (P2 후, P3와 병행 가능)
P5 성숙       환류 자동화 + 지표 집계 + 셀프서비스 모드 개방(§6.2 제한 범위)
```

---

## 14. 확장 인터페이스 (코어 밖 — 훅만 예약)

- G3 결과에 `margin` 필드(목표 대비 여유) — 여유도 스캔의 미래 입력.
- manifest에 `improvement_request` 예약 슬롯 — 최적화 루프 연결점.
- 러너의 파라미터화 실행(같은 덱 + 파라미터 세트) — 파라스터디·치수 최적화의 실행 기반.
- `.claude/skills/optimization/` 자리 예약(내용 없음).

---

## 15. 리스크·미결

| 항목 | 영향 | 대응 |
|---|---|---|
| nCode CLI 정문법 미확정 (**유일 잔여 미결**) | 트랙② | P4 착수 조건으로 로컬 매뉴얼 'Batch Processing' 확인. 템플릿 flow 방식이라 문법 의존면이 좁음 |
| H-Chat 게이트웨이 rate/토큰 한도 | 운영 비용·속도 | P3 초기 스모크 측정, G2 호출 빈도 조정 여지를 설계에 유지 |
| 실적 범위 DB 콜드스타트 | G3 판정력 | 파일럿 부품군 한정 수동 구축, `NO_REFERENCE` 정직 표시 |
| 게이트 과잉 차단(false positive) | 완주율·UX | override 절차(§4.4) — 기록 필수, '개입'으로 계수 |
| HM 2026.1 hwx API 변동 | hm_mcp | hm-assistant Phase 2 API matrix·검증분 활용 |
| 사내 반입·설치 제약 | P3 | 반입 절차 사전 확인. bridge는 표준 라이브러리+hwx만(기존 규칙), 나머지는 통상 pip |
| 다중 사용자 승인 진본성 | 확산(Phase 2+) | Phase 1 신뢰 경계 = 단일 사용자 계정(§6.1). 확산 시 승인자 인증 채널 설계 |

---

## 16. 정직성·포지셔닝 규칙 (설계 수준 상속)

워크스페이스 작업 규약을 시스템 서술·보고 문구에 설계 수준으로 박는다:

1. 게이트는 **"차단 보증"이 아니라 "리스크를 겹겹이 거르는 완화 장치"**로 서술한다. 자동판정 밖의 최종 방어선은 엔지니어 승인이다.
2. G2(LLM 자기검토·교차검증)는 **같은 LLM 기반의 완화 장치**임을 명기한다. 독립 검증은 G3(물리·실적)가 담당한다.
3. G3는 **문제유형별 스코핑**을 항상 동반한다 — "이론값 검증"은 closed_form에만 성립하는 표현이다.
4. **실측치와 목표치를 구분 표기**한다(무개입 완주율 80% = Phase 1 목표).
5. 무개입 완주율은 사람의 최종 책임을 배제한다는 의미가 아니다 — 공식 판정·설계 반영은 엔지니어 승인 후 진행.
