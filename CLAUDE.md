# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 이 워크스페이스의 성격

**문서 리포지토리**다 — 자체 git 리포(`kimck-tf/cae_automation`, main)이며 상위 `0. Research/` 리포 안에 중첩돼 있으므로 커밋은 항상 이 폴더에서 한다. 빌드·린트·테스트가 없다. 실행 코드·pytest는 **구현 리포** `C:\_PYTHON\0_CK_Project\cae-copilot`(GitHub `kimck-tf/cae-copilot`)에 있고, 두 리포는 각자 커밋한다.

문서는 두 트랙이다:

1. **cae-copilot 구현 문서 (살아있는 트랙)** — 사내 CAE 자동화 파일럿의 설계 SSOT(SDD)와 단계별 구현계획(P0·P1·P2…). 현재 작업 대부분이 여기 속한다.
2. **공모전 제안·리서치 아카이브 (동결 트랙)** — 사내 AI 공모전 제안서는 v6로 **최종 제출 완료**(2026-07-05). 결과 발표: 센터레벨 7월 중순 → 연구소레벨 8월 초. `docs/reference/` 전략 분석과 `OLD/` 제안서 이력은 참조용이다.

운영 규칙:

- 파일명 끝 6자리는 작성일 `YYMMDD`(`260711` = 2026-07-11). 단 **SDD는 살아있는 문서**라 파일명 날짜(최초 작성일)와 무관하게 **헤더의 `버전`·`이력`이 진실**이다(현재 v1.8).
- `.omc/`·`.serena/`·`.agents/`·`.codex/`·`bash.exe.stackdump`는 툴링 세션 상태로 **의도적 untracked** — 읽거나 수정하거나 커밋하지 않는다. `.gitignore`가 없으므로 `git add -A`를 쓰지 말고 명시 경로로 add 한다.

## 문서 위계와 충돌 해소 규칙

| 층위 | 문서 | 역할 |
|---|---|---|
| WHAT | `공모전_제안서_통합플랫폼_v6_260705(최종제출).md` | 무엇·왜의 SSOT — SDD와 충돌하면 제안서가 우선 |
| HOW | `SDD_cae-copilot_v1_260705.md` (**v1.8**) | 설계 SSOT — 헤더 이력에 v1.1~v1.8 개정 사유 전체 |
| 실행 | `P{n}_구현계획_*.md` | 단계별 계획 — 충돌 시 SDD 우선, **단 계획 서두 "SDD 개정 노트" 등재 항목은 계획이 우선**하며 단계 완료 시 그 노트를 SDD에 반영하고 버전업한다(P0→v1.6, P1→v1.7·v1.8) |
| 원장 | `cae-copilot/.superpowers/sdd/progress.md` | 구현 진행 원장(구현 리포 쪽, git 로컬 제외) — **구현 재개·감사 시 최우선으로 읽는다** |

**단계 현황(2026-07-11):** P0 완료 → P1 완료(cae-copilot main `fd932a6`, 249 tests PASS) → **P2 진행 중**(`P2_구현계획_260711.md` — 솔버 러너 3종·G2 서브에이전트·G3(구조)·pptx 빌더·Mock E2E). 브리지 실 hwx 구현(스텁 33종) + 벤더 하드닝은 **P2.5로 분리** — hm_bridge는 byte-frozen이라 수정 = fork 선언이며 한 이벤트로 묶는다.

## 구현 트랙 작업 규약

- **계획 작성**: superpowers:writing-plans(청크별 plan-document-reviewer 루프) 후 **Codex 적대 리뷰**로 마감한다. 주의 — `/codex:adversarial-review`는 인자 없이 돌리면 작업트리 diff만 보므로, **커밋된 계획을 검토하려면 `--base HEAD~1`(또는 해당 ref)을 명시**해야 한다.
- **계획 실행**: 새 세션에서 cwd를 `cae-copilot`으로 잡고 superpowers:subagent-driven-development로 Task 단위 실행·커밋한다. 계획 문서는 이 리포에서 Read만 한다.
- **`11. Agent_CAE_improve\agent_cae`는 참고자료지 이식 대상이 아니다** — 구세대 LLM 산출물이므로 정답으로 취급하지 않는다. 도메인 지식·엣지케이스만 참고하고 참고 출처(경로·SHA-256)를 신규 파일 헤더에 기록한다(P2 계획 규약).

**Read triggers:**

- 구현 작업 재개 → `cae-copilot/.superpowers/sdd/progress.md` + 최신 `P{n}_구현계획` 서두("사용자 확정 결정"·"SDD 개정 노트")부터 읽는다.
- 스펙·설계 질문 → SDD 해당 절(태스크·리뷰 모두 § 번호 인용이 관례).
- 전략적 배경·"왜 이 설계인가" → `C:\_PYTHON\0_CK_Project\0. Research\101. TEMP_HM_automation\CLAUDE.md`를 먼저 읽는다(130건 벤치마킹, 동어반복 검증 함정, 세 시스템 관계, 라이센스 실태).
- HyperMesh 도구 실물(hwx API·TCP/JSON-RPC bridge) → `C:\_PYTHON\0_CK_Project\4. Hypermesh assistant`(= `hm-assistant`, vendor 기준 커밋 `03d1533`).

## 리서치·공모전 아카이브

**`docs/reference/`** — 전략 원본 PDF 3종(기술 브리핑 / `Hyper-Pilot_Strategic_CAE_LLM_Benchmarking` / `Hyper-Pilot_Strategic_Blueprint`)과 메타 분석 3종: `GPT_Summary_3pdf_260628.md`(객관 종합), `GPT_Critical_Review_3pdf_260628.md`(외부 출처로 반증·보정), `CAE-LLM_종합전략_최종보고서_260628.md`. 빨리 결론만 필요하면 Summary §10 → Critical_Review §최종판단.

**`공모전_AI로 일하는 방식/`** — `공지1.txt`(공식 공지 1차 출처: 심사 4축·**사내 AI 도구만 허용**·예측형 AI 제외·서비스화 배포본 제외), `예시케이스1·2.txt`(**타팀·경쟁자의 실제 제안** — 벤치마킹·차별화 대상이지 우리 주장이 아니다. 층위 혼동 금지), `과제취합.csv`.

**제안 이력** — `OLD/`(제안서 v1~v5·작성 프롬프트·초기 pptx), `최종제출/`(제출 슬라이드 이미지 10장), 루트의 `이미지*.png`·`핵심구성요소_재배치안.md`(작성 과정 산출물).

**도메인 개념 요지** (상세 근거는 101 워크스페이스):

- **Hyper-Pilot = `hm-assistant`** — 자연어 → LangGraph → TCP/JSON-RPC → HyperMesh `hwx` 실시간 제어 + 이론값 검증 가드레일. cae-copilot은 이 계보를 사내 규정(Claude Code 네이티브 스택, LangGraph 미도입)에 맞게 재설계한 구현이다.
- **RPC vs MCP 대립의 결론은 수렴** — MCP도 내부는 JSON-RPC 2.0이라 프로토콜은 차별점이 아니고, 진짜 해자는 *상용 전처리 워크플로 완결성 + 검증 게이트 품질*이다(Critical_Review 판정). cae-copilot의 "내부 RPC bridge + 외부 MCP 어댑터(thin relay)" 구조가 그 결론의 구현이다.
- **이론값 가드레일은 문제유형별 레이어로 한정** — 만능 검증기 서사 금지. SDD G3의 problem_class 분기(closed_form/reference/none)가 이 원칙의 코드화다.

## 정직성·포지셔닝 규칙 (모든 문서 작업에 적용)

- "세계 최초"·"범용 자동화"·"만능 검증"·"RPC/MCP가 유일한 해자" 같은 광범위 주장 금지 — 새 주장에는 충돌 선례와 방어선을 함께 적는다.
- 층위를 섞지 않는다: 원본(PDF) 주장 / 분석(MD) 판정 / 제안서 주장 / SDD 설계는 서로 다른 진술 층위다 — 인용할 때 어느 층위인지 밝힌다.
- 외부 인용은 1차 출처에서 확인한 것만 쓴다(추측 인용 금지). `Critical_Review`의 URL 각주가 그 기준이다.
