# harness-input-plan.md
# book-outline.md 입력 계획 — 책 저술 하네스 연동

**작성 기준일**: 2026-11-25 &nbsp;|&nbsp; **기반 패턴**: `ex1/Code_Generation` (c-audit-harness) · `my-first-harness` (commit-message)

---

## 입력 파일 대응

| 기존 하네스 | 책 저술 하네스 |
|---|---|
| `_workspace/req.txt` — 코드 생성 요구사항 | `book-outline.md` — 집필 요구사항 (소절 목록·교육 원칙 P1-P7·나선형 연결 맵) |

`book-outline.md`는 하네스 루트에 그대로 유지한다. `_workspace/`로 복사하지 않는다. 각 에이전트는 `Read` 도구로 직접 참조한다.

---

## 하네스 디렉토리 구조

```
book-harness/
├── CLAUDE.md
├── book-outline.md                    ← 읽기 전용 입력 (수정 금지)
├── _workspace/
│   ├── chapter-target.txt             ← 오케스트라가 집필 대상 소절 번호 기록
│   ├── chapter-draft.md               ← writer-agent 출력
│   └── review-report.md               ← reviewer-agent 출력
└── .claude/
    ├── agents/
    │   ├── chapter-writer-agent.md    ← 소절 초안 작성
    │   └── chapter-reviewer-agent.md  ← 교육 원칙·기술 정확성 검증
    └── skills/book-write/SKILL.md     ← 오케스트라 워크플로
```

---

## 에이전트 데이터 흐름

**입력**: `book-outline.md` + `chapter-target.txt`
(예: `"5.13 커패시터 평활회로 — 리플 전압, 용량 선정"`)

**① chapter-writer-agent**
- `book-outline.md`에서 대상 소절 요약·선수 소절·나선형 연결 항목 읽기
- 5단계 교육 순서(동작 원리→수식→계산 예제→실제 회로→측정·검증) 적용
- 출력: `_workspace/chapter-draft.md`

**② chapter-reviewer-agent**
- `chapter-draft.md` + `book-outline.md` 대조 검증
- 검증 항목: 교육 원칙 P1-P7 준수 / 기초 필수·심화 선택 표시 / 나선형 연결 명시 / 기술 오류
- 판정: PASS | REDO (사유·수정 지시 포함) → `_workspace/review-report.md`

**③ SKILL.md 오케스트라**
- PASS → `chapter-draft.md` 최종 확정
- REDO → 수정 지시를 포함하여 writer-agent 재호출 (최대 2회)

---

## 운용 규칙

- `book-outline.md` 읽기 전용 — 하네스 실행 중 수정 금지
- `chapter-target.txt`는 매 실행 전 오케스트라가 덮어씀 (1소절씩 순차 처리)
- reviewer 검증 기준: `book-outline.md` 내 P1-P7 및 결정 #010 "기초 필수/심화 선택 표시 체계" 명시 참조
- 기술 정확성 검증(수식·단위·조건 명시 여부)은 chapter-reviewer-agent 역할에 포함
