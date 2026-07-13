# SDD — cae-copilot: 버추얼 성능개발(CAE) 통합 지능화·자동화 파일럿

- **버전**: v1.9 (2026-07-14)
- **상태**: P2 트랙① 완주 구현 완료 반영 — dev 프로파일 완성(솔버 러너 3종·G2 서브에이전트·G3(구조)·pptx 빌더·Mock E2E S0→S5) + P2.5 브리지 실기 페이즈 신설. 개정노트 11건(§13·15·10.2·7.2·7.5·4.3·4.1·3.6·5·2.2·0.5)
- **이력**: v1.1 (2026-07-05) — 스펙 리뷰 1차 반영: 전이 충족 의미론 신설(§3.6), 승인 채널 분리(§6.1), 마일스톤 반려 전이 정의, 지표 산출성 정정(§0.2·§12), 구현 페이즈 P-넘버링(§13), tool_class 전수 매핑(§7.1) / v1.2 (2026-07-05) — 스펙 리뷰 2차 반영: 전진 공식 예외 2건(셀프서비스 ③ 면제·override FAIL 무효화), §2.2 승인 흐름 정정, tool_class 참조 단일화(§5→§3.1), pilot 명령 표면 보완(intervene·milestone_requested 기록 주체), 승인 2차 잠금, 지표 단위·해석 주석 / v1.3 (2026-07-05) — 스펙 리뷰 3차 반영: 2차 잠금을 approve 한정으로 정정(훅·빌더의 gate-record 정상 경로 보존), P0 테스트 항목 보강. 최종 승인(4차) 후 advisory 2건 추가 반영: gate-record 호출자 마커 스탬프(감사 실효화)·`!` 마커 상속 실증의 §11.2 병기 / v1.4 (2026-07-05) — GUI 전략 반영(사용자 검토 결정): 프론트엔드 2-헤드 원칙(D7), 승인 채널 일반화(§6.1), 승인 카드 파일화, P-GUI 페이즈 신설(§13), SDK/CLI 하네스 동등성 스모크(§15); 확인 패스 advisory 5건 반영(D7 데이터소스에 GateResult·harness 문서 추가, verdict의 manifest 직접 렌더링, 카드 seq 버전링, 동등성 스모크에 승인 채널 행동 포함, 표기 정렬 2건) / v1.5 (2026-07-05) — Codex 적대적 리뷰 재검토 반영: 게이트 기록 계산형 전환(gate-record 폐지 → gate-run, target_sha TOCTOU 대조), 집행 기계류 에이전트 쓰기 차단 신설(D8), jobs/ACTIVE 폐지·CAE_JOB_ID 단일화·쓰기 명령 --job 필수, events 선기록·원자적 manifest·pilot rebuild(§3.5), 셀프서비스 보강 2건(워터마크 판정 등급·P5 조건부 개방); 확인 패스 4건 반영(게이트 인스턴스 귀속 규칙·G2 보호 경로 입력 검증·증거 파일 쓰기 차단·SSOT 용어 정리) + advisory 4건(환류 반영 주체 인간 명시·CAE_JOB_ID 설정 주체·D8 적용 경계·SessionStart 표기); 최종 확인 1건 반영(§10.2 allowlist 네거티브형 전환 — S0 plan.md 교착 해소, 차단 SSOT를 §5로 단일화) + 표기 정렬 3건(§12 인스턴스 언어·§3.4 requires 병기·§9 runtime/ 트리 반영) / v1.6 (2026-07-07) — P0 구현·수동실증 완료 반영(Task 16.3): 개정 노트 8건 — job_created·stage_paused 이벤트 추가·gate_pass/gate_fail 발화 폐지(§3.3), gates 슬롯 게이트×대상 2계층(§3.2·§3.6, Codex F3), 원장 쓰기 job별 파일락 직렬화·개행 커밋 정의(§3.5, Codex F1·F2), override target_sha 바인딩(§4.4, Codex F4), D8 개발 이스케이프 `CAE_COPILOT_DEV_UNLOCK` 명문화(§10.2), paused 해제 = `intervene --resume`·상한 윈도 재정의(§5), paused 단계 편집 하드 차단(§5, F#1), Bash 쓰기 경계·Windows 훅 문법 명문화(§5·§10.2, 최종 리뷰 I1a); 수동실증 findings 3건 — Windows `${CLAUDE_PROJECT_DIR}` 필수(§5), `!`·CLAUDECODE 상속 확정 → 승인채널 = 별도 터미널(§6.1), Stop 훅 턴별 발화 누적 미결 등재(§15) / v1.7 (2026-07-10) — P1 구현 완료 반영: 개정 노트 8건 — 덱 export 프로파일 실물 2종 `fmt: "OS" | "Abaqus"`·Nastran .bdf export는 P2에서 러너와 함께 검토(§7.1), 메시 품질 체크 P2 이연·S1 출구 G1은 export된 덱 린트로 성립(§4.1), 이식 규모 명세화 = 43 이식 + 2 노출 추가(`hm_get_materials`·`hm_get_bridge_status`) = 45 노출·솔버 실행 2종 제외(§7.1), export-후 G1 트리거 = PostToolUse가 `mcp__hm__hm_export_*` 매칭·성공 응답만 검증·`success≠true`→`export-failed` ERROR로 stale 덱 PASS 차단·대상 `deck_path` 우선(§4.1·5, Codex F2), session_summary dedup 해소(§15), G1 미지원 대상 = `unsupported-target`·미지원 카드 형식 = `unsupported-card-format` = ERROR fail-closed(§4.1, Codex F3), 에러 정규화 3패밀리 `[hm_mcp:connection|timeout|rpc:<Type>]` 고정(§7.1), RP 동적 계산 체인 노출명 `hm_get_bounding_box`→`hm_create_nodeset_by_box`(§7.1) / v1.8 (2026-07-11) — Codex 적대적 리뷰(P1 브랜치 `--base origin/main`, NO-SHIP 8건) 트리아지 반영: 실결함 2건 수정 — ① gate-run G1/G3·G2 payload 대상의 job_dir 격리 강제(§4.4 — job 밖 정상 덱·`../` traversal로 stage 기록 위조 벡터 차단, #5) ② G1 데이터-완비성(§4.1 — 필수 카드·키워드가 존재해도 데이터 행 없는 헤더-only 빈 덱 fail-closed, criteria `data_bearing`, #4); 백로그 등재 — 벤더 브리지(hm_bridge) 하드닝 P2(§10.2·§15: loopback 기본화·TCP dispatch allowlist(`execute_tcl_raw` 제외)·`execute_tcl_safe` 인자 검증·request-id dedup/타임아웃 계약 일치, #1·#2·#3)·`note_self_correction` job-lock 원자화 P2(#8)·승인 환경변수 경계 = 기존 P3 보안 검토 항목 재확인(#7); 조치 불요 — 브리지 stub NotImplementedError = 선언된 Phase 경계로 MANUAL_P1 실기 게이트가 커버(#6, MANUAL_P1에 수행 범위 A·B·E·F 명시) / v1.9 (2026-07-14): P2 트랙① 완주 구현 확정·보정 반영(개정노트 11건) — §13 페이즈 표 P2.5 신설(브리지 실 hwx·벤더 하드닝·MANUAL_P1 C·D·메시품질 G1, 노트 1)·벤더 하드닝/메시품질 배치 P2→P2.5(§15·§10.2·§4.1, 노트 2)·§7.2 솔버 러너 실측 보정(OptiStruct "로컬 라이센스 보유" 스테일 정정=미보유·3러너 확정·dev 실솔브 스모크=Abaqus LE 2024·Nastran/OptiStruct dev mock-only, 노트 3)·G3 verdict enum·비차단 의미론 §4.4 SSOT 상호참조 보강(§3.6·§4.3 — 추가 아님, 노트 4)·Nastran .op2 파싱 없이 extracted_files 전달만(§7.2, 노트 5)·pptx dev 기본 레이아웃 자체생성·사내 템플릿 P3(§7.5, 노트 6)·agent_cae '이식'→'참고 재구현' 용어 정정(§0.5·2.2·4.3·7.2·7.5, 노트 7)·reactions=부호 3성분 병진 벡터 [Fx,Fy,Fz]·모멘트 P3(§7.2, 노트 8)·error 택소노미 5 family×12 code+unknown·signature=family:code(§7.2, 노트 9)·§5 '러너 종료' 훅 = 실패 collect 자가복구 계수 이행(result.json sha 중복방지·note_self_correction result_sha·_job_lock 원자화, 노트 10)·analysis_spec.json = closed_form theory 입력(job 루트 비보호, 실 생산자 P3, §4.3, 노트 11)
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
| 매니페스트(manifest) | 작업 1건의 상태 조회 뷰(훅·전이 판정용) — `jobs/<job_id>/manifest.json`. truth의 원천은 events 원장이고 manifest는 재생성 캐시(§3.5) |
| 마일스톤(M1/M2/M3) | 엔지니어 승인 지점: M1 모델 확정 · M2 해석조건 확정 · M3 결과 판정 |
| 무개입 완주율 | 반복 실행 단계에서 **승인 외 수동 개입** 없이 표준 절차를 완료한 작업 비율. 마일스톤 승인은 설계된 절차이므로 "개입"에 포함하지 않는다(§12) |
| problem_class | 문제 유형 분류: `closed_form` / `linear_static_complex` / `fatigue_load_history` / `nonlinear_advanced` — G3 판정 모드를 결정(§4.3) |

### 0.5 관련 문서·자산

| 자산 | 위치 | 본 설계에서의 역할 |
|---|---|---|
| 제안서 v6 (SSOT) | 이 폴더 `공모전_제안서_통합플랫폼_v6_260705(최종제출).md` | WHAT — 요구사항 원천 |
| 기술정본 재배치안 v8 | 이 폴더 `핵심구성요소_재배치안.md` | 4대 구성요소 구조·근거 설명(문구는 v6 우선) |
| hm-assistant | `C:\_PYTHON\0_CK_Project\4. Hypermesh assistant` | HyperMesh bridge(TCP JSON-RPC :9876) vendor 원본, 도구 정의·mock 서버·criteria 패턴, HM 2026.1 API 검증분 |
| agent_cae3 | `C:\_PYTHON\0_CK_Project\11. Agent_CAE_improve\agent_cae` | AbaqusWrapper·theory_validator·error_classifier·html_report 참고 재구현 원본 (610 테스트 자산) |
| ClaudeApp | `C:\_PYTHON\0_CK_Project\17. ClaudeApp` | H-Chat 게이트웨이 경유 claude-agent-sdk 검증 경로, GUI 헤드(P-GUI)의 기반 자산(D7), default-DENY 보안 게이팅 참조 |
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
   │   ※ 프론트엔드 2-헤드(D7): CLI(개발·파워유저) / GUI(엔지니어, P-GUI)
   │      — 같은 세션 런타임·같은 하네스 위의 두 머리
   │
   ├─ 하네스 표면 (.claude/)
   │    CLAUDE.md(헌법·안전규칙) · Skills(트랙별 표준절차·판단절차)
   │    Commands(/해석시작 /하중분석 /승인 /상태 /환류) · 서브에이전트(gate2-reviewer)
   │
   ├─ Hooks (게이트 강제 계층)          ◄── 판정 근거 = manifest (조회 뷰 — truth 원천은 events, §3.5)
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
| Abaqus/Nastran 해석 | CLI 러너 참고 재구현 | agent_cae3 `AbaqusWrapper` + 신규 Nastran |
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
- **D7. 프론트엔드 2-헤드.** 하네스 리포(hooks·게이트·pilot·MCP·Skills)가 제품 본체이고, **CLI**(Claude Code — 개발·파워유저)와 **GUI**(claude-agent-sdk 기반, ClaudeApp 계열 — 엔지니어)는 같은 세션 런타임 위의 두 프론트엔드다. GUI는 `setting_sources=["project"]`로 동일 하네스를 로딩하며, 헤드가 하네스 의미론을 바꾸는 것을 금지한다. 상태 렌더링(진행 보드·승인 패널·게이트 뷰어)은 외부화된 산출물만 읽는다 — manifest·events·카드 + GateResult 파일·링크된 harness 문서(읽기 전용). 헤드 사적 상태 금지 — 훅 강제를 위해 외부화한 상태(SSOT)를 GUI 데이터소스로 재사용.
- **D8. 집행 기계류는 에이전트에게 읽기 전용.** 검증기(`gates/`)·기준·표준·사례(`harness/` — `wiki/inbox` 제외)·전이 규칙(`pilot/`)·훅·설정(`.claude/`·`CLAUDE.md`)은 에이전트가 수정할 수 없다(§5·§10.2). 게이트를 통과하는 유일한 방법은 산출물을 고치는 것이고, 기준·검증기의 개정은 환류 제안(§6.3) → 인간 승인 경로만 존재한다 — "실패하는 테스트 대신 테스트를 고친다"는 드리프트 경로의 구조적 봉쇄.

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
      "gates": {"G1": {"decks/bracket_v2.inp": {"verdict": "PASS", "attempts": 2, "last_result": "gates/S1_G1_2.json"}}},  // 게이트×대상 2계층 — 대상마다 독립 슬롯(Codex F3)
      "self_corrections": 3
    }
  },
  "milestones": {
    "M1": {"status": "approved | rejected | pending", "approver": "...", "reason": "...", "ts": "..."}
  },
  "created_at": "...", "updated_at": "..."
}
```

- **쓰기는 `pilot` CLI 한 곳으로 집중**: `pilot new` / `pilot advance` / `pilot gate-run` / `pilot approve` / `pilot intervene` / `pilot rebuild`. CLI가 전이 조건(tracks yaml)을 검사·거부하는 1차 방어선. hooks는 2차 방어망(심층 방어). `milestone_requested` 이벤트는 `pilot advance`가 마일스톤 대기 상태로 진입하는 시점에 자동 기록한다(별도 명령 없음).
- **쓰기 명령은 `--job <job_id>` 필수** — 대상 job을 항상 명시하고 pilot이 manifest의 job_id와 대조한다. 활성 포인터 파일(`jobs/ACTIVE`)은 **두지 않는다** — 포인터 전환에 의한 오귀속 시나리오를 원천 제거. 세션 컨텍스트(훅의 규칙 평가 대상 job)는 `CAE_JOB_ID` 환경변수로 단일화. **설정 주체는 세션 기동자**(사용자 셸·GUI 런처) — 훅은 자식 프로세스라 부모 세션의 환경변수를 설정할 수 없다. SessionStart 훅은 검증·안내만 담당: 미설정이면 미완료 job 목록을 안내하고, 게이트 대상 도구는 fail-closed로 차단된다. **Phase 1 제약: 세션당 활성 job 1건.**

### 3.3 events.jsonl — 이벤트 원장 (append-only)

`{seq, ts, type, payload}` 라인 단위(seq = 단조 증가 일련번호 — 재생성 결정성 확보). type: `job_created | stage_start | stage_complete | gate_run | self_correction | milestone_requested | stage_paused | approval | rejection | override | human_intervention | tool_error | session_summary`. **`job_created`**(seq=1) = job 메타(job_id·track·mode·problem_class·requester·request_text)를 원장에 심어 `pilot rebuild`가 manifest를 0에서 재생성할 수 있게 하는 기점(§3.5). **`stage_paused`** = 자가복구 상한 도달 시 단계 paused 전이를 원장에 남겨 rebuild가 paused를 복원(§5, `human_intervention` 자동 유형과 함께 기록). **`gate_pass`·`gate_fail`은 발화하지 않는다** — 판정은 `gate_run` payload의 `verdict`가 단일 원천이며, 같은 사실을 두 타입으로 이중 기록하면 rebuild·지표(§12) 불일치만 생긴다. 재작업으로 재발화되는 `stage_complete`는 `rework: true` 플래그를 갖는다(지표 이중 계수 방지). **지표 5종의 1차 산출 원천**(지표별 산출성 구분은 §12). manifest의 수치는 이 원장의 집계 캐시다.

### 3.4 GateResult 스키마

```jsonc
{
  "gate": "G1", "target": "decks/bracket_v2.inp", "target_sha": "sha256:…",  // 검증 시점의 대상 파일 해시
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
- **TOCTOU 방지**: `pilot advance`는 전이 판정 시 GateResult의 `target_sha`를 대상 파일의 현재 해시와 대조한다(훅의 requires 검사도 동일 기준 — §3.6) — 게이트 기록 후 산출물이 재수정됐으면 해당 게이트는 미충족(재실행 필요)으로 간주한다.

### 3.5 상태 무결성·원자성

- `jobs/**/manifest.json`·`events.jsonl`의 직접 Edit/Write는 **훅이 차단**(pilot CLI 경유만 허용).
- **쓰기 순서 고정**: pilot의 모든 상태 변경은 ① events 선기록(append) → ② manifest 갱신(temp 파일 작성 후 atomic rename) 순서를 따른다. **events가 유일한 truth, manifest는 재생성 캐시** — 불일치 시 `pilot rebuild`가 events에서 manifest를 재생성한다.
- **쓰기 직렬화(Codex F2)**: 원장·GateResult 쓰기는 **job별 OS 파일락**으로 직렬화한다 — 인간 승인 채널(별도 프로세스 `pilot approve`)과 에이전트 세션 훅(자동 G1)이 같은 job에 동시 기록하는 것이 정상 운영 흐름이므로, seq 단조·attempt 유일을 락으로 보장한다. OS 락은 프로세스 종료 시 자동 해제라 stale lock이 없다.
- **원장 커밋 정의(Codex F1)**: "커밋 = 개행으로 종결된 라인"을 읽기·쓰기가 공유한다 — 읽기는 개행 없는 마지막 라인을 (완전한 JSON이라도) 미커밋으로 스킵하고, append는 파일 끝이 개행이 아니면 마지막 개행까지 truncate 후 기록한다. 그래서 "rebuild가 반영한 이벤트를 다음 append가 삭제"하는 되돌림이 구조적으로 불가능하다. 커밋된 라인의 파싱 실패는 위치 무관 fail-closed(손상 원장 위 append 자체 거부 — 복구는 수동).
- **복구·멱등 규칙**: rebuild는 찢긴 꼬리 라인(torn tail)을 관용(무시)하고, 상태 전이는 멱등 적용한다(예: 이미 approved인 마일스톤에 대한 중복 approval 이벤트는 상태 무변경). 크래시 복구 시나리오는 P0 테스트 대상(§11).

### 3.6 전이 충족 의미론 (pilot advance·훅의 판정 규칙)

단계 전이의 충족 조건 — 이 규칙이 상태기계의 전부다:

> **전진 가능 = ① 해당 단계의 모든 exit_gates 실행 완료 ∧ ② 각 verdict ∉ {FAIL, ERROR} (엔지니어 override로 무효화된 FAIL 제외 — §4.4) ∧ ③ 단계에 마일스톤이 지정돼 있으면 status=approved (milestone 모드에 한함 — self_service job은 ③ 면제, §6.2)**

- **PASS**: 충족 — 단 GateResult의 `target_sha`가 대상 파일의 현재 해시와 일치할 때만 유효(§3.4 TOCTOU 방지), 불일치 시 재실행 필요.
- **ADVISORY / NO_REFERENCE**: 전이를 막지 않는다 — 단, 승인 요약 카드에 필수 표기되어 마일스톤 승인의 판단 대상이 된다. 마일스톤 대기가 생략되는 셀프서비스 job에서는 산출물 상태로 기록되고 '사전검토용' 워터마크가 그 역할을 대신한다(§6.2). verdict enum과 이 비차단 전이 의미론의 SSOT는 §4.4(공통 규칙)다.
- **FAIL**: 수정 후 재실행(attempts 증가). **ERROR**: fail-closed — 전진 불가, 사람 호출.
- **기록의 인스턴스 귀속(게이트×대상 2계층 — Codex F3)**: gate-run 기록은 단계가 아니라 **게이트×대상(target) 인스턴스** 기준으로 귀속된다(§3.2 `gates[게이트][target]`) — 같은 대상의 재실행은 그 (게이트,대상) 슬롯을 최신 GateResult로 갱신하고(attempts 누적), **다른 대상은 독립 슬롯**이라 서로 덮지 않는다. 게이트 충족 판정 = **관찰된 모든 대상 인스턴스가 ∉{FAIL, ERROR} ∧ 최소 1개 존재**(각 현행 target_sha 일치 기준, §3.4). 게이트당 1슬롯이면 다른 target의 PASS가 실제 산출물의 FAIL을 덮어 전이를 우회하므로 2계층으로 막는다. 이 귀속이 없으면 S3 자가복구(덱 재편집)가 S2.G1을 영구 미충족으로 만드는 교착이 생긴다. §12의 최초 실행/재실행 구분은 이벤트 순서(seq)로 산출한다.
- **마일스톤 반려 전이**: `rejected` 기록 시 해당 마일스톤의 소속 단계는 `in_progress`로 복귀(재작업). 재작업 후 게이트는 재실행되며 attempts는 누적된다. 마일스톤은 재요청(`milestone_requested`) 시 `pending`으로 복귀 → 재승인. 반려 사유는 §6.1 환류 경로를 따른다.

---

## 4. 3중 검증 게이트

### 4.1 G1 — 표준 준수 (결정론 Python, 상시·저비용)

- **트리거**: PostToolUse 자동 — `decks/**`·`reports/**` Edit/Write, HM 덱 export 후(matcher가 `mcp__hm__hm_export_*` 완료를 매칭 — **성공 응답만 검증 경로**: `success≠true`·불명 응답은 대상 파일 잔존 시 `export-failed` ERROR로 stale 덱 PASS를 차단하고, 린트 대상은 응답 `deck_path` 우선·입력 `path` 폴백 — §5·Codex F2). 수동 호출도 가능.
- **검사 대상별 체크**:
  - 덱 린트: 단위계(mm-t-s: E=210000 MPa, ρ=7.85e-9 t/mm³), 필수 카드/키워드(**데이터-완비성 포함** — 필수 카드·키워드가 존재해도 데이터 행이 없는 헤더-only 빈 덱은 FAIL, criteria `data_bearing` 목록, Codex 적대 리뷰 2026-07-11 #4), **Nastran 고정폭 필드 정렬**, 재질–프로퍼티–섹션 연결 무결성, 사내 표준(명명 규칙·출력 요청 세트)
  - 메시 품질: **P2.5 이연**(hm-assistant에 품질 리포트 도구 부재 — 사용자 결정 2026-07-07; 품질 리포트가 브리지 실 hwx 구현에 커플되므로 배치 P2→P2.5, §13). P1의 S1 출구 G1은 "export된 덱의 린트"로 성립하며, 메시 품질 대조(S1 출구에서 hm_mcp 품질 리포트를 `harness/criteria/mesh_quality.yaml` 기준과 대조)는 P2.5에서 증보
  - 보고서 린트: 필수 섹션 존재, **출처 인용 존재(무출처 차단)**, 수치–단위 표기. 트리거 2경로 — 원고(md, LLM 작성 섹션)는 PostToolUse 자동, 최종 pptx는 빌더가 `pilot gate-run` 호출(§7.5)
- **미지원 대상·형식 = ERROR(fail-closed D6)**: G1 라우터가 `.fem`/`.inp` 밖 확장자(예: `reports/*.md` — 보고서 린트는 P2)를 만나면 verdict **ERROR**(check `unsupported-target`). 같은 원칙으로 `.fem` 내부의 미지원 카드 형식(large-field `*`·연속 `+`)도 **ERROR**(check `unsupported-card-format`) — 조용한 무시는 false PASS를 낳는다(Codex 적대 리뷰 2026-07-07 F3). P2에서 보고서·pptx 린터 등록·실기 export 샘플 기반 파서 확장으로 해소.
- **기준의 이중 표현**: 사람용 `harness/standards/*.md` ↔ 기계용 `harness/criteria/*.yaml` 쌍. 모든 체크의 `source` 필드가 문서 절을 인용해 동기화를 강제.

### 4.2 G2 — 추론 검증 (LLM 서브에이전트, 중간 비용)

- **트리거**: 추론 산출물의 단계 출구 — S0 계획, S2 조건 정의, S4 판정 서술.
- **메커니즘**: `gate2-reviewer` 서브에이전트가 **작성 과정의 컨텍스트 없이** 산출물 + 관련 하네스 발췌 + 단계별 체크리스트만 받아 검토. 반환: `{concerns[], verdict}`.
- **검사 범위**: 가정 타당성, 요청–조건 정합(하중 방향·크기·구속이 요청과 맞는가), 논리 비약, 표준 해석절차와의 정합.
- **기록 진본성**: G2 verdict는 메인 에이전트 손을 거치지 않는다 — SubagentStop 훅이 gate2-reviewer 완료 시점에 결과를 파싱해 **D8 보호 경로 `pilot/runtime/g2/`**(훅 프로세스만 기록 가능 — 에이전트 도구 쓰기 차단 경로)에 저장하고 `pilot gate-run G2 --from-subagent <그 파일> --job <id>`를 호출한다. **pilot은 `--from-subagent` 입력이 이 보호 경로 안에 있을 때만 수용**한다(에이전트가 위조 결과 파일을 주입하는 벡터 차단). G2는 계산 불가능한 주장형 게이트라 이 경로만 유효(§4.4) — 에이전트의 G2 기록 직접 호출은 차단(§5).
- FAIL → 수정 전 전진 불가. **한계 명기(D5)**: 같은 LLM 기반이므로 완화 장치이며, 독립 검증은 G3가 담당.

### 4.3 G3 — 물리·실적 (결정론+데이터, 최종 관문)

**트리거**: S4. **problem_class별 판정 모드** — "만능 검증기" 오버클레임을 구조적으로 차단:

| problem_class | 자동 체크 | 판정 모드 |
|---|---|---|
| closed_form | 이론값 직접 대조 (theory_validator 참고 재구현) | **자동 PASS/FAIL** + 엔지니어 승인 |
| linear_static_complex | 평형 잔차(반력합=하중합)·단위 일관성·오더 체크·응력분포 패턴 + 유사 부품 **실적 범위 대조** | ADVISORY — 자동 판정 없음, M3 승인 필수 |
| fatigue_load_history | 지배 하중케이스 정합(하중이력 분석 ↔ 피로 기여도)·수명 오더·손상 위치 타당성 + 수명 실적 범위 | ADVISORY — M3 승인 필수 |
| nonlinear_advanced | 참고 신호만(자동 판정 배제) | ADVISORY — M3 승인 필수 |

- 실적 범위 DB = `harness/cases/` 사례 카드의 정량 필드(부품군·하중유형·응답 범위·출처 보고서·verified 플래그). **콜드스타트를 정직하게 계획**: Phase 1 파일럿 부품군 한정 수동 구축, 부재 시 `NO_REFERENCE`.
- **verdict 의미론(상호참조)**: 위 판정 모드 표는 problem_class별 스코프만 규정한다 — verdict enum·ADVISORY/NO_REFERENCE 비차단 전이의 SSOT는 §4.4(공통 규칙)·§3.6이다. 자동 PASS/FAIL은 `closed_form` 한정이고, 그 밖은 M3 승인이 최종 방어선(§4.4).
- **`closed_form` 이론 입력 = `analysis_spec.json`**: theory 검증기 입력(structure_type·geometry·material·load)은 job 루트의 **비보호 아티팩트** `analysis_spec.json`에서 읽는다. P2(dev)는 합성 픽스처로 공급하고, 실 생산자(deck→spec 추출)는 P3로 이연한다(사용자 결정 2026-07-12).

### 4.4 공통 규칙

- fail-closed(D6): verdict `ERROR`는 통과가 아니다.
- **기록은 계산이지 주장이 아니다**: G1·G3의 GateResult는 `pilot gate-run <게이트> <대상> --job <id>`가 검증기를 pilot 프로세스 안에서 직접 실행해 산출한다 — verdict를 인자로 받는 기록 명령이 존재하지 않으므로 위조 대상 자체가 없다. 누가 호출하든(훅·빌더·에이전트) 결과가 동일하므로 gate-run은 호출 차단이 필요 없다. G2만 예외(LLM 판정 = 본질적 주장) — SubagentStop 훅 전용 기록 경로(§4.2), 에이전트 직접 기록은 차단(§5).
- **게이트 대상 격리**: gate-run G1/G3 대상과 G2 payload `target`은 **`resolve()` 후 job_dir 안이어야** 수용된다 — job 밖의 정상 덱을 대상으로 지정하거나 `../` traversal로 현재 단계의 게이트 기록을 위조하는 벡터를 차단한다(Codex 적대 리뷰 2026-07-11 #5). 자동 G1(PostToolUse)은 이미 `decks/`·`reports/` 하위로 격리돼 있다.
- **override 절차**: 게이트 FAIL을 엔지니어가 검토 후 무효화할 수 있다 — 반드시 `pilot approve --override <gate> --reason`(인간 전용 채널, §6.1)으로 기록되고, `override` 이벤트는 **"개입"으로 계수**된다(지표 왜곡 방지, §12). **override는 기록 시점의 대상 파일 해시(target_sha)에 바인딩**된다(Codex F4) — 현재 파일 해시와 일치할 때만 유효하고, 산출물이 재수정되면 소멸해 재승인을 요구한다("오탐 예외 처리"가 영구 백도어가 되는 것을 차단). 게이트 과잉 차단(false positive)의 안전밸브.
- 게이트 서술 규칙: 대외 문구는 "차단을 보증한다"가 아니라 **"리스크를 겹겹이 거르고, 자동판정 밖은 엔지니어 승인이 최종 방어선"** — 제안서 정직성 규약 상속.

---

## 5. Hooks 명세

| Hook | 트리거 | 규칙 |
|---|---|---|
| PreToolUse | `solver_run` 클래스 호출 | tracks yaml의 requires 검사: 선행 게이트 충족(§3.6 ② 기준) + (milestone 모드) M1·M2 승인. 미충족 → **차단**, 거부 사유를 구조화해 에이전트에 반환(승인 요청 유도) |
| PreToolUse | nCode 러너 호출 | S3 완료 + 하중이력 분석 산출물 존재 |
| PreToolUse | `hm_mutate` 클래스 (패턴 목록은 §3.1 tool_classes가 SSOT) | 활성 job + stage ∈ {S1, S2}. **조회(`hm_query`)는 상시 허용**(Q&A·세션 브리지) |
| PreToolUse | 활성 job의 gated 산출물(`decks/**`·`reports/**`) Edit/Write — 그 단계가 `paused`일 때 | **차단(F#1)** — 자가복구 상한으로 paused된 단계는 편집·변형(`hm_mutate`)·솔버 세 쓰기 경로를 일관되게 막는다("멈추고 사람 호출"을 코드가 강제 — CLAUDE.md 지시문 의존 제거). 해제는 `pilot intervene --resume`(§6.1) |
| PreToolUse | `jobs/**/manifest.json`·`events.jsonl` 직접 Edit/Write | 차단 (pilot CLI 경유만) |
| PreToolUse | 집행 기계류 Edit/Write — `gates/**`·`harness/**`(`wiki/inbox` 제외)·`pilot/**`·`.claude/**`·`CLAUDE.md` | **차단(D8)** — "기준·검증기를 고쳐 게이트를 통과"하는 드리프트 경로 봉쇄. 개정은 `wiki/inbox/` 제안 → 인간 승인(§6.3) |
| PreToolUse | 증거·계기판 Edit/Write — `jobs/**/results/**`(result.json·extracted·`load_analysis.json`)·`jobs/**/gates/**`(GateResult 파일) | **차단** — G3가 읽는 입력·게이트 증빙은 러너·분석기·pilot(서브프로세스 파일 IO)만 기록. "결과를 고쳐 G3를 통과"하는 오염 벡터 봉쇄 — 동어반복 검증 함정의 파일시스템 재진입 차단(D5의 인식론적 독립 보전) |
| PreToolUse | 에이전트의 `pilot approve`(승인·반려·override)·G2 기록 호출 | **무조건 차단** — 승인은 인간 전용 채널(§6.1), G2 기록은 SubagentStop 훅 전용(§4.2). ※ gate-run(G1·G3)은 계산형이라 차단 불요(§4.4) |
| PreToolUse | 보고서 빌더 `--official` | M3 승인 필수. 미승인·셀프서비스 → '사전검토용' 워터마크 강제 |
| PostToolUse | `decks/**`·`reports/**` Edit/Write, `mcp__hm__hm_export_*` 완료 | **`pilot gate-run G1` 자동 호출**(계산형 §4.4) → 기록, FAIL 시 체크리스트 반환(수정 유도). export 경로는 **성공 응답만 검증**(`success≠true`·불명 → 대상 파일 잔존 시 `export-failed` ERROR로 stale 덱 PASS 차단), 린트 대상 = 응답 `deck_path` 우선(입력 `path` 폴백) — Codex F2 |
| PostToolUse | 러너 종료 | result.json 파싱·기록. **실패 collect**(result.json에 `error` 존재)는 `error.signature`로 §5 자가복구 사다리에 계수 — 동일 result.json 중복 계수 방지(내용 sha 대조). `note_self_correction`에 optional `result_sha`, scan~pause 임계구역은 `_job_lock`으로 원자화(Task 1·8) |
| SubagentStop | gate2-reviewer 완료 | 결과 파싱 → G2 기록(`gate-run G2 --from-subagent <결과파일>`) — 메인 에이전트 미경유(§4.2) |
| Stop | 에이전트 턴 종료(주의: "세션 완전 종료"가 아님 — §15) | 미결 게이트·미승인 마일스톤 요약 **경고(차단 아님)** + `session_summary` 이벤트 기록 |
| SessionStart | 세션 시작 | `CAE_JOB_ID` 검증(미설정 시 미완료 job 안내 — §3.2) + 해당 job의 현재 단계·미결 항목을 컨텍스트에 주입(중단 후 재개 인체공학) |

- **도구→단계 매핑은 도구 클래스 단위**(3.1 tool_classes)로 거칠게 유지 — 견고성 우선. 세밀한 절차 준수는 Skill(레일)이, 관문은 hooks(강제)가 맡는 역할 분리.
- **fail-closed 래퍼**: 훅 스크립트 예외 시 게이트 대상 도구 클래스는 거부 + 오류 보고.
- **자가복구 상한**: 단계당 self_correction 5회(설정값) 또는 동일 에러 시그니처 3회 연속 → 단계 `paused`, 사람 호출(무한 루프 방지 — agent_cae3 교훈). **해제 = `pilot intervene --resume`**(인간 전용 — approve와 동일한 2차 잠금·훅 차단): `human_intervention` 기록 후 해당 단계에 `stage_start`를 재발화해 재개하며, 상한 집계 윈도는 **그 단계의 최신 `stage_start` 이후**로 정의한다(재개 직후 첫 수정에서 즉시 재-paused되는 교착 방지). manifest `self_corrections` 표시값은 단계 누계 유지(지표용).
- **훅 쓰기 차단의 경계(정직성 — 최종 리뷰 I1a)**: 위 Edit/Write 차단(장부·증거 불변·기계류 D8·paused 편집)은 **Edit/Write 도구 경로만** 검사한다. Bash 리다이렉션(`echo > …`·`sed -i`)·`python -c "open(...,'w')"`로 장부·증거·기계류를 쓰는 것은 훅이 못 막는 잔여 벡터이고, permission allowlist 미등재(사람 확인 프롬프트)가 백스톱이다 — 시도 자체가 규약 위반(CLAUDE.md). Bash 쓰기의 코드 차단 강화는 P3(§10.2).
- **Windows 훅 실행(구현 실증 — Task 16.2)**: `settings.json` 훅 커맨드 경로는 `${CLAUDE_PROJECT_DIR}`(POSIX 폼)로 쓴다 — Claude Code(Windows)가 기본 훅 셸로 Git Bash를 쓰므로 `%CLAUDE_PROJECT_DIR%`(cmd/PS 폼)는 미확장돼 전 훅이 hook error로 죽는다(집행 전체 무력). P0 수동 실증에서 발견·수정(6a0f91b).

---

## 6. 운영 모드 (Human-in-the-Loop)

### 6.1 마일스톤 승인 모드 (기본)

- M1/M2/M3 도달 시 에이전트가 **승인 요약 카드** 생성(Skill 정의 양식): 변경사항·게이트 결과·리스크·다음 단계. 카드는 **채팅 출력과 동시에 `jobs/<job_id>/cards/<Mx>_<seq>.md` 파일로도 저장**한다(반려→재요청 시 seq 증가, 덮어쓰기 금지 — 감사 추적 보존, GateResult JSON 파일화와 같은 원칙). 단 카드는 에이전트 작성 서술이므로 **승인 패널의 게이트 verdict·ADVISORY/NO_REFERENCE 플래그는 카드 텍스트가 아니라 manifest에서 직접 렌더링**한다(카드는 서술 보조 — 서술 왜곡 채널 차단). 이때 `milestone_requested` 이벤트 기록(기록 주체 = `pilot advance`, §3.2 — 승인 대기시간 측정 기점).
- **승인은 인간 전용 채널**: 정의 = **에이전트 세션 밖 프로세스에서 실행되는 `pilot approve`만 유효** → pilot CLI가 manifest·events 기록. 헤드별 구현 — **CLI**: 엔지니어가 **Claude Code 밖 별도 터미널**에서 `pilot approve M1 --reason "..."` 실행(입력창의 `!` 직접 실행은 세션 마커를 상속해 2차 잠금에 오차단됨 — 아래 실증에서 확정) / **GUI(P-GUI)**: 승인 패널 버튼이 세션 밖 프로세스로 `pilot approve` 실행(세션 마커 상속 문제 원천 부재). **에이전트의 `pilot approve` 호출은 훅이 무조건 차단**(§5) — 훅 입장에서 "사람이 시킨 승인"과 "에이전트 자발 승인"을 구분할 수 없으므로 채널 자체를 분리한다. `/승인` command는 대리 실행이 아니라 승인 카드 재표시 + 실행할 명령 안내다.
- **Phase 1 신뢰 경계**: 승인의 진본성은 "같은 PC, 같은 사용자 계정"(단일 사용자 파일럿)에 의존한다. 다중 사용자 운영으로 확장 시 승인자 인증 채널이 필요하다(§15).
- **2차 잠금(심층 방어)**: 훅의 명령 패턴 매칭에는 우회면(변형 표기·간접 호출)이 있으므로, pilot CLI 자체도 에이전트 세션 환경 마커를 감지하면 **approve를 거부**한다. ※ **G1·G3에 한해** 계산형 전환(§4.4)으로 잠금·호출자 스탬프가 불필요하다 — 누가 호출해도 결과가 같다. G2(주장형)는 보호 경로 입력 검증으로 방어한다(§4.2). **P0 수동 실증(Task 16.2) 결과**: `!` 직접 실행은 세션 마커 `CLAUDECODE=1`을 **상속한다** → pilot 2차 잠금이 사람이 친 `!pilot approve`까지 오차단한다. 따라서 CLI 헤드의 기본 승인 채널 = **Claude Code 밖에서 연 별도 터미널**(CLAUDECODE 없는 일반 셸)로 확정. (GUI 헤드는 패널이 세션 밖 프로세스로 실행하므로 무관 — 위 헤드별 구현.)
- **반려 사유는 `harness/wiki/inbox/`로 자동 적재** — 환류 루프의 1차 입력원.

### 6.2 자동 진행 모드 (셀프서비스)

- job 생성 시 `mode=self_service` 고정(중도 전환 금지 — 전환하려면 새 job).
- 마일스톤 **대기만 생략**, 게이트(G1·G2·G3)는 전부 상시 작동. G3의 "M3 승인 필수"(§4.3)는 셀프서비스에서 '사전검토용' 워터마크 + 공식 판정 차단으로 **대체**된다 — 승인이 사라지는 게 아니라 공식화 시점(엔지니어 검토)으로 이연되는 것.
- 산출물 전체 '사전검토용' 워터마크 — **게이트 판정 등급(ADVISORY/NO_REFERENCE 포함)을 워터마크에 병기**해 판정 상태가 산출물 표면에 드러나게 한다. 공식 판정·`--official` 보고서 차단.
- **Phase 1 개방 범위 제한**: 트랙①의 `closed_form`·`linear_static_complex`만. 피로·비선형은 마일스톤 모드 강제. 게이트 강건성이 실측으로 확인된 범위만 개방한다는 제안서 원칙의 실행형 — **P5 개방 시점에 `linear_static_complex` 포함 여부는 P3 실측치(게이트 1회 통과율·ADVISORY 판정 품질)를 조건으로 재확인한다. 시간 순서가 아니라 데이터가 개방을 결정한다.**

### 6.3 환류 루프 (자가개선)

- 입력원: 승인·반려 사유, self_correction 기록(에러 시그니처→해결책), 완료 해석의 노하우, Q&A 로그(지식 갭 신호 — 수집은 프롬프트 규약: 세션 중 반복·미답 질문을 에이전트가 `wiki/inbox/`에 적재. 강제 메커니즘 아님을 명기).
- `/환류` command(또는 주기 배치): `wiki/inbox/` + events를 정리해 **사례 카드·플레이북·표준 개정 제안 초안** 생성 → **사람 승인 후 인간(비에이전트 프로세스)이 `harness/`에 반영**한다 — D8에 따라 에이전트는 승인 이후에도 적용 주체가 될 수 없다(하네스 개정도 게이트를 거친다).
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
- hm_mcp 역할: 도구 스키마 노출, RPC 중계, 재연결·타임아웃, 에러 정규화(실패 메시지 3패밀리 고정 — `[hm_mcp:connection|timeout|rpc:<Type>] <원인·다음 행동>`). **로직 없음.**
- **명명 규칙 = 훅 게이팅의 지탱점**: `hm_get_*`(조회) / `hm_create_*`·`hm_set_*`·`hm_delete_*`·`hm_import_*`·`hm_mesh_*`·`hm_export_*`(변형·부수효과). `execute_tcl_safe`는 hm_mutate 클래스에 명시 등재. **전수 매핑 보증**: hm_mcp 서버는 기동 시 등록 도구 전체가 조회/변형 클래스 중 하나에 매칭되는지 자체 검사하고, 미분류 도구가 있으면 기동을 거부한다(fail-closed).
- 도구 카테고리(hm-assistant **43 이식 + 2 노출 추가 = 45 노출**): 조회(모델트리·컴포넌트·바운딩박스·선택 엔티티), CAD 임포트(2026.1 API 검증분), 메시(tetmesh volume-only 기본), 물성·프로퍼티, 하중·BC(node-based·박스 선택), 덱 export(실물 2종 — `fmt: "OS" | "Abaqus"`; Nastran .bdf export는 P2에서 러너와 함께 추가 검토 — §7.2 .op2 경로 전제), `execute_tcl_safe`(화이트리스트만). 제외 2종 = 솔버 실행(`submit_analysis`·`check_job_status`) — `cae_tools.runners`(P2) 소관(MCP 경로로 두면 `solver_run` 게이팅 우회). 추가 2종 = `hm_get_materials`(bridge RPC 기존·@tool 미노출을 S2 재료 확인 조회로 노출)·`hm_get_bridge_status`(신규 — 연결 진단, 위 에러 정규화의 가시화).
- **hm-assistant 5대 원칙 전면 계승** — 집행 위치 3중(MCP 인자 검증 + G1 + Skill 지침):
  mm 단위 강제 / node-based BC / volume-only tet / RP 동적 계산(`hm_get_bounding_box`→`hm_create_nodeset_by_box` 체인) / TCL raw 노출 금지.
- `hm_get_*` 조회 도구가 곧 **세션 컨텍스트 브리지**: "이 부품 응력이 왜 높지?" 류 지시어 질문에 모델트리·선택 엔티티·최신 결과를 조회해 즉답. 대형 FE 모델은 컨텍스트에 담지 않고 질의 도구로 조회(제안서 2.6).

### 7.2 솔버 러너 — 공통 CLI 계약

```
python -m cae_tools.runners.<solver> submit  --deck <path> --job-dir <dir>   → job_ticket.json (즉시)
python -m cae_tools.runners.<solver> status  --job-dir <dir>                 → running|completed|failed
python -m cae_tools.runners.<solver> collect --job-dir <dir>                 → result.json + extracted/*.csv
```

- **submit은 detach 실행** — Claude 세션 종료와 무관하게 해석 지속, 재접속 후 collect(수시간 잡 대응). `job_ticket.json`에 `job_id`를 기록하고 collect가 manifest의 job_id와 대조한다(오귀속 방지 — §3.2).
- `result.json` 공통 스키마:

```jsonc
{
  "solver": "abaqus | nastran | optistruct | ncode",
  "state": "completed | failed", "exit_code": 0, "wall_time_s": 1234,
  "error": {"family": "...", "code": "...", "signature": "...", "log_excerpt": "..."},  // 실패 시
  "results": {
    "max_stress": {"value": 312.5, "unit": "MPa", "location": "..."},
    "max_displacement": {"value": 1.82, "unit": "mm", "location": "..."},
    "reactions": {"sum": [Fx,Fy,Fz], "applied": [Fx,Fy,Fz]},  // 부호 3성분 병진 벡터(스칼라 크기 아님; 모멘트 P3 이연). G3 평형 잔차 |sum+applied|/|applied|의 필수 입력 — 부호규약 sum≈-applied@평형(절대 물리부호 실검증 P3)
    "extracted_files": ["extracted/stress_field.csv"]
  }
}
```

- 에러 분류: agent_cae3 `error_classifier` **참고 재구현** — 택소노미는 도메인 재검토로 **5 family(license/input/convergence/resource/runtime) × 12 code + unknown**으로 확정(참고 4 family × 21 sub-code에서 llm/physics 드롭·license 신설). signature = 소문자 `family:code` 콜론 토큰(예 `convergence:divergence`), fail-closed sink은 리터럴 `unknown`(code=None). 이 시그니처가 재발률 지표(§12)·error-playbooks(§8, 시그니처를 키로 sync)·환류의 공통 키.
- 솔버별:
  - **Abaqus**: AbaqusWrapper 참고 재구현(입력 스크립트 동적 생성 → `abaqus cae noGUI` → odbAccess 추출). 관련 테스트 동반 재구현.
  - **Nastran**: 정식 구현 — .bdf 실행. **수치는 `.f06` 텍스트에서 추출**하고, **`.op2`는 파싱하지 않고 `extracted_files`로 전달만** 한다(P4 nCode의 FE 입력 — pyNastran 의존 미도입). .op2가 nCode의 FE 입력이므로 트랙②의 기본 구조해석 경로.
  - **OptiStruct**: 동일 계약. **P2 포함 = 3러너 확정**(실구현 환경은 Abaqus·Nastran·OptiStruct 3솔버 모두 대응 필요 — 사용자 확인 2026-07-11). `.out` 텍스트 요약 + `.h3d`/`.op2` 산출 파일 전달까지(심층 추출 P3). 로컬 라이센스는 **미보유**(v1.8 "로컬 라이센스 보유"는 스테일 — 정정).
  - **nCode**: §7.3.
- **dev 로컬 실솔브 스모크 = Abaqus Learning Edition 2024**: 노드 1,000 한도·병렬 불가·상용 DB(.cae/.odb) 비호환·교육용 무상 약관(사내 개발 검증 사용의 약관 적합성은 사용자 판단). 소형 스모크 덱은 한도 내라 파이프라인 무영향. **Nastran·OptiStruct는 dev에서 mock-only**(실검증은 P3 사내 환경).

### 7.3 nCode 러너 — 템플릿 flow 치환

- **패턴**: GUI에서 대표 피로 flow(`.flo`) 1개 제작 → 러너가 FE 결과(.op2)·하중이력(.req/.rsp)·재료 SN 경로와 파라미터만 치환 → `dtproc <dcl>` / `flowproc <flo>` 배치 실행 → 수명·손상 CSV 추출. (공식 외부 Python API 부재 → subprocess 래핑이 표준 경로)
- 단일 머신 실행(MPI 분산 불필요).
- ⚠️ **미결 #1**: 정확한 CLI 인자 문법은 공개 웹에 없음 → 로컬 설치본 매뉴얼 'Batch Processing' 섹션에서 확정(P4 착수 조건). 템플릿 flow 방식이라 문법 의존면 자체가 좁다.

### 7.4 하중이력 분석기

- `cae_tools/analysis/load_history.py`: 입력 `.req`·`.rsp` **모두** → rainflow 카운팅, 채널별 손상 추정(Miner), Damage Rose(주손상 방향), **지배 하중케이스 랭킹** → `jobs/<id>/results/load_analysis.json` + 시각화 PNG (에이전트 쓰기 차단 경로 — §5 증거 보호). (주하중방향 시각화 PoC 이식)
- 출력이 트랙② S0 산출물이자 **G3-피로 "지배 하중케이스 정합" 체크의 기준값** — 분석과 검증이 같은 데이터를 공유.

### 7.5 보고 빌더 (pptx)

- `report build --job <id> [--official]`: **python-pptx 기반 레이아웃** + LLM 작성 섹션(요약·근거·트레이드오프) + **게이트 증빙 자동 첨부**(manifest에서 게이트 결과·승인 이력). **dev 프로파일: 사내 양식 파일 부재 → 빌더가 기본 레이아웃을 자체 생성**하고, 사내 표준 양식 템플릿 치환은 P3(운영 이관) 항목.
- 시각화: agent_cae3 `html_report` 렌더러 참고 재구현(헤드리스 matplotlib — 컨투어·이력 차트)→ PNG를 pptx에 삽입.
- 빌더는 pptx 생성 직후 `pilot gate-run G1 <pptx> --job <id>`를 호출한다 — 린트는 pilot 안의 검증기가 수행(계산형 §4.4). pptx는 에이전트 Edit 경로 밖(바이너리, 빌더 생성물)이므로 PostToolUse 대신 빌더가 트리거를 겸한다.
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
├─ pilot/                     # job 관리 CLI · tracks/*.yaml · metrics 집계 · runtime/(훅 전용 전달 경로 — D8 보호, §4.2)
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
├─ jobs/                      # <job_id>/ (manifest·events·cards·decks·results·reports) — 세션 컨텍스트는 CAE_JOB_ID(§3.2)
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
- `settings.json` allowlist: hm_mcp 도구 / `python -m cae_tools.*`·`python -m gates.*`·`python -m pilot` Bash prefix / Read는 리포+jobs 범위. **Edit/Write 허용은 네거티브형으로 정의한다(차단 의도의 SSOT는 §5)**: `jobs/**`에서 manifest·events·`results/**`·`gates/**`를 **제외한 전부**(decks·reports 원고·cards·plan 등 에이전트 산출물) + `harness/wiki/inbox/**`. 그 외 리포 전체 — 집행 기계류(`gates/`·`harness/` 나머지·`pilot/`·`.claude/`·`CLAUDE.md`) — 는 쓰기 차단(D8·§5). 러너·분석기·pilot은 서브프로세스 파일 IO로 기록하므로 무영향. 운영 프로파일에서 웹 접근 deny. **deny가 allow에 우선**: `pilot approve`·G2 기록의 에이전트 호출 차단(§5)은 allowlist 안에서도 유효하다. 단 훅의 쓰기 차단은 **Edit/Write 도구 경로만** 검사하므로 Bash 리다이렉션·`python -c` 파일쓰기는 훅 밖 잔여 벡터다 — allowlist 미등재(사람 확인)가 백스톱이고 코드 차단 강화는 P3(§5).
- manifest 무결성(§3.5): 장부 조작에 의한 게이트 우회 경로 봉쇄.
- 에이전트는 사용자 계정 권한·라이선스 범위 내 실행. 공식 판정 권한은 엔지니어 유지(M3 + `--official` 훅).
- **D8의 적용 경계**: 위 차단은 **운영 에이전트 세션**(해석 업무 수행)용 설정이다. cae-copilot 자체를 개발하는 세션(P0~P5에서 gates/·pilot/·hooks 구현)은 기동자가 **`CAE_COPILOT_DEV_UNLOCK=1`**을 설정해 훅의 **D8 기계류 차단만** 해제한다(장부·증거·approve·G2 차단은 개발 중에도 유지 — fail-closed 기본). 이 구분이 없으면 P0 첫날 자기 리포를 수정할 수 없다. `tests/**`도 운영 세션에서는 쓰기 불가(의도된 제한).
- **벤더 브리지(hm_bridge) 신뢰 경계(P2.5 하드닝/P3 배치, Codex 적대 리뷰 2026-07-11)**: byte-frozen 상류 fork인 TCP 브리지는 0.0.0.0 무인증 바인딩·`execute_tcl_raw` dispatch 등록·arg 주입 가능한 `execute_tcl_safe`를 그대로 안고 있다. Phase 1 신뢰 경계는 **엔지니어 로컬 HM 콘솔 + localhost relay**(§6.1 단일 사용자 계정)라 즉시 위험은 낮으나, 공유망 배치 전 fork 하드닝 필수 — loopback 기본화·dispatch allowlist(raw 제외)·typed 인자 검증. 이 하드닝은 브리지 실 hwx 구현과 **동일 fork 이벤트(P2.5)**로 묶는다(§13·§15 백로그). MCP 계층은 이미 `execute_tcl_raw`를 노출하지 않으나 TCP dispatch는 별도 차단이 없다.
- GUI 헤드(ClaudeApp 계열) 채택 시 default-DENY 정책과 본 리포 allowlist의 정합 확인(P-GUI 착수 체크 항목).

---

## 11. 검증 전략 — 이 시스템이 옳음을 어떻게 아는가

1. **단위 — FAIL 검출 우선 원칙.** 게이트 검증기 테스트는 **의도적 오류 주입 코퍼스**(단위 틀린 덱, 필드 밀린 .bdf, 반력 불일치 결과, 실적 범위 밖 수명 등)를 "잡아내는지"로 작성한다. PASS 케이스만 테스트하는 자기만족을 금지한다 — 검증기의 존재 증명은 "맞혔다"가 아니라 **"틀린 것을 잡는다"**(동어반복 함정의 테스트 규약화).
2. **통합 — Mock E2E + 우회 시도 테스트.** 트랙① 합성 케이스 완주에 더해 **적대적 시나리오가 1급 테스트**: 승인 없이 솔버 호출→차단 / 게이트 FAIL 상태로 전진→차단 / manifest 직접 수정→거부 / **에이전트가 `pilot approve` 호출→차단 / 에이전트의 G2 기록 직접 호출→차단 / 검증기·기준·훅·tracks 등 기계류 Edit→차단(D8) / result.json·load_analysis 등 증거 파일 Edit→차단(§5) / 게이트 기록 후 산출물 재수정→advance가 해시 불일치로 거부(TOCTOU, §3.4) / 크래시 복구: events 선기록 후 중단→`pilot rebuild`로 manifest 재생성(§3.5)** / 자가복구 상한 초과→중단 / `!` 직접 실행의 세션 마커 상속 여부 실증(§6.1). 보조: criteria yaml의 `source` 인용이 standards 문서에 실존하는지 검사하는 스테일 참조 테스트.
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

**게이트 1회 통과율의 집계 단위**: 분모 = 단계-출구 게이트 인스턴스(단계×게이트)의 최초 실행. 동일 게이트 인스턴스의 최초 이후 재실행(seq 기준, §3.6 — 예: 자가복구 중 덱 재편집마다 도는 G1)은 분모에서 제외하고 `self_correction` 신호로 계수한다 — 두 지표의 역할 분리(관문 품질 vs 수정 횟수). 초안 반복이 많은 단계에서는 자연히 낮게 나오는 특성이 있으므로 파일럿 보고 시 해석 주석을 병기한다.

**절감시간의 단위 구분**: `milestone_requested→approval` 간격은 벽시계 리드타임이라 야간·부재 대기를 포함한다. 맨아워 절감(제안서 산식의 단위)은 엔지니어 병행 기록이 기준이고, 원장 간격은 리드타임 보조 지표로만 쓴다(단위 혼합 시 왜곡·음수 가능).

---

## 13. 구현 순서 (구현 페이즈 P0~P5 + P-GUI)

승인 마일스톤 M1~M3(§0.4)과 **구분되는 별도 이름공간**으로 P-넘버링을 쓴다. 상세 계획은 별도 문서로, **P 단위로 분할 작성**한다(단일 plan으로 다루지 않는다).

```
P0 골격       리포 구조 + pilot CLI/manifest + hooks + tracks.yaml + CLAUDE.md v1
              → "훅 차단이 작동하는 빈 파이프라인" (우회 시도 테스트 이때부터)
P1 전처리     hm_mcp + bridge vendor(2026.1) + G1 덱 린트 + S1·S2 Skill
P2 트랙① 완주 Abaqus·Nastran·OptiStruct 러너 + G2 서브에이전트 + G3(구조) + pptx 빌더
              → Mock E2E 통과 = dev 프로파일 완성
P2.5 브리지실기 hm_bridge 실 hwx 구현(스텁 33종) + 벤더 하드닝(#1·#2·#3, fork 선언·
              CHECKSUMS 개정) + MANUAL_P1 C·D + 메시 품질 G1        (P2 후, P3 전제)
P3 운영 이관  사내 설치 + 실 하네스 문서 + 파일럿 실측 개시          (P2 후)
P4 트랙②     load_history(.req/.rsp) + nCode 러너 + G3-피로          (P2 후, P3와 병행 가능)
P-GUI 헤드    claude-agent-sdk GUI(ClaudeApp 계열): 하네스 project 로딩(D7) +
              승인 패널·진행 보드·게이트 결과 뷰어(표준 문서 링크)·양식형
              진입점·마일스톤 알림                                    (P2 후 병행, P5의 전제조건)
P5 성숙       환류 자동화 + 지표 집계 + 셀프서비스 모드 개방(§6.2 제한 범위,
              비전문가 대상 개방은 P-GUI 완료 전제)
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
| SDK/CLI 하네스 동등성 (GUI 헤드) | P-GUI | ClaudeApp은 격리 목적으로 `setting_sources=[]`로 하네스 로딩을 껐음 — GUI 헤드는 project 로딩을 켜야 하며, hooks 발화·Skills 로딩·승인 채널 행동(패널 approve 통과 + 세션 내 에이전트 approve 차단)의 SDK/CLI 동등성을 P-GUI 착수 스모크(§11.2 우회 스위트 재실행 포함)로 실증 |
| Stop 훅 session_summary 중복 누적 (실증 발견 — Task 16.2) | 원장 팽창 | Claude Code Stop 훅은 "세션 종료"가 아니라 **매 에이전트 턴 종료**마다 발화 → 상태 불변이어도 동일 `session_summary`가 append-only 원장에 누적. **P1 해소(dedup 채택)** — Stop 훅이 직전 `session_summary`와 payload가 동일하면 기록 생략(정보 무손실). `harness/wiki/inbox/stop-hook-summary-accumulation.md` 제안 반영 |
| 벤더 브리지(hm_bridge) 보안 자세 (Codex 적대 리뷰 2026-07-11 #1·#2·#3) | 모델 변조·중복 실행 | byte-frozen 상류 fork라 P1 미개변. **P2.5 하드닝 대상(실 구현과 동일 fork 이벤트)**: ① `execute_tcl_safe`가 `tcl_args`를 raw 연결 → `; ` 주입으로 화이트리스트 무력화(#1) → 명령별 typed 검증·이스케이프 ② TCP dispatch가 0.0.0.0 무인증 바인딩 + `execute_tcl_raw` 등록(MCP는 숨김·`VENDORED_FROM.md` "노출금지" 명시하나 TCP는 노출, #2) → loopback 기본화·dispatch allowlist ③ 클라이언트 60초 vs 브리지 300초 타임아웃 불일치·미취소로 변형 RPC 재시도 시 중복 실행(#3, 현재 stub이라 잠재—실 hwx 구현 시 발현) → request-id dedup·타임아웃 계약 일치. **실 구현은 MANUAL_P1 실기 게이트 이후** |
| 자가복구 상한 검사 비원자성 (Codex 적대 리뷰 2026-07-11 #8) | 상한 초과 1건 | `store.note_self_correction`이 paused 선검사→append→재검사를 job-lock 밖에서 수행 → 동일 job 동시 훅이 둘 다 선검사 통과 시 상한 +1 기록 가능. 1차 통제는 F#1 하드컷·단일세션 훅 직렬화라 창 희박(medium). **P2**: scan~pause 전체를 `_job_lock` 임계구역으로 |

---

## 16. 정직성·포지셔닝 규칙 (설계 수준 상속)

워크스페이스 작업 규약을 시스템 서술·보고 문구에 설계 수준으로 박는다:

1. 게이트는 **"차단 보증"이 아니라 "리스크를 겹겹이 거르는 완화 장치"**로 서술한다. 자동판정 밖의 최종 방어선은 엔지니어 승인이다.
2. G2(LLM 자기검토·교차검증)는 **같은 LLM 기반의 완화 장치**임을 명기한다. 독립 검증은 G3(물리·실적)가 담당한다.
3. G3는 **문제유형별 스코핑**을 항상 동반한다 — "이론값 검증"은 closed_form에만 성립하는 표현이다.
4. **실측치와 목표치를 구분 표기**한다(무개입 완주율 80% = Phase 1 목표).
5. 무개입 완주율은 사람의 최종 책임을 배제한다는 의미가 아니다 — 공식 판정·설계 반영은 엔지니어 승인 후 진행.
