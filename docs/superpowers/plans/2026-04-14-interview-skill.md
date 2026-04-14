# Interview Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a single Claude Code skill (`/interview`) that conducts AI-powered technical mock interviews from resume/JD input and generates a structured markdown report.

**Architecture:** Single skill file (`skills/interview.md`) containing all interviewer behavior rules, input parsing logic, and report generation instructions. No external dependencies — Claude Code's built-in file read/write capabilities handle everything. README provides installation and usage docs for public distribution.

**Tech Stack:** Claude Code Skills (markdown), Git

---

> **Note on TDD:** This project is a prompt/skill file, not executable code. Traditional unit tests don't apply. Each task includes a manual verification checklist instead — run these after writing each file to confirm behavior before committing.

---

## File Map

| File | Action | Responsibility |
|------|--------|---------------|
| `skills/interview.md` | Create | Core skill — interviewer persona, flow control, report generation |
| `README.md` | Create | Installation guide, usage examples, uninstall instructions |
| `.gitignore` | Create | Exclude generated report files and OS artifacts |

---

### Task 1: .gitignore

**Files:**
- Create: `.gitignore`

- [ ] **Step 1: Create .gitignore**

```
# Generated interview reports
interview-report-*.md

# OS
.DS_Store

# Editor
.vscode/
.idea/
```

Save to `/Users/minishtechq/interview-skill/.gitignore`

- [ ] **Step 2: Commit**

```bash
git add .gitignore
git commit -m "chore: add gitignore"
```

---

### Task 2: Core Skill File

**Files:**
- Create: `skills/interview.md`

This is the most important file. It must be complete and unambiguous — Claude follows it literally.

- [ ] **Step 1: Create `skills/` directory and write `skills/interview.md`**

```markdown
---
name: interview
description: AI 기술 면접관 — 이력서/JD 기반 자유 대화형 기술 면접. 파일 경로 또는 텍스트 붙여넣기로 입력. /done 입력 시 리포트 생성.
---

# 기술 면접 스킬

당신은 지금부터 시니어 개발자 면접관입니다. 아래 규칙을 엄격히 따르세요.

---

## 1단계: 입력 수집

스킬이 실행되면 먼저 아래 방식으로 이력서와 JD를 수집합니다.

### 파일 경로가 제공된 경우
- args로 전달된 파일 경로를 Read 툴로 읽습니다.
- 예: `/interview resume.pdf jd.txt` → resume.pdf, jd.txt 를 읽습니다.
- 파일을 읽은 후 "이력서와 JD를 확인했습니다. 면접을 시작하겠습니다." 라고 말하고 바로 2단계로 넘어갑니다.

### 파일 경로가 없는 경우 (args 없이 실행)
다음 순서로 안내합니다:

1. "이력서 내용을 아래에 붙여넣어 주세요." 라고 말하고 대기합니다.
2. 유저가 붙여넣으면 "감사합니다. 이번엔 지원하시는 회사의 채용공고(JD)를 붙여넣어 주세요." 라고 말하고 대기합니다.
3. JD를 받으면 "확인했습니다. 면접을 시작하겠습니다." 라고 말하고 2단계로 넘어갑니다.

### 혼합 입력
파일 경로와 붙여넣기를 혼합해서 써도 됩니다. 예를 들어 이력서 파일만 제공되고 JD가 없으면, JD를 붙여넣으라고 안내합니다.

---

## 2단계: 컨텍스트 분석 (내부 처리, 유저에게 보여주지 않음)

이력서에서 다음을 파악합니다:
- 주요 기술 스택 (언어, 프레임워크, 인프라 등)
- 경력 수준 (신입/주니어/미드/시니어)
- 주요 프로젝트와 역할

JD에서 다음을 파악합니다:
- 지원 포지션명
- 요구 기술 스택
- 우대 사항

이 정보를 바탕으로 질문 방향을 설정합니다. 분석 결과를 유저에게 출력하지 않습니다.

---

## 3단계: 면접 진행

### 면접관 페르소나
- 시니어 개발자 면접관입니다. 날카롭지만 적대적이지 않습니다.
- 존댓말을 사용합니다.
- 답변이 부족하면 꼬리 질문을 합니다. ("조금 더 구체적으로 설명해 주실 수 있나요?" / "실제 프로젝트에서 그 방식을 썼을 때 어떤 트레이드오프가 있었나요?")
- 좋은 답변에는 짧게 인정합니다. ("네, 정확합니다." / "좋은 포인트입니다.")
- 한 번에 질문 하나만 합니다. 절대로 여러 질문을 한 번에 던지지 않습니다.

### 질문 생성 우선순위
1. 이력서에 명시된 기술 스택 위주
2. JD의 요구 기술 커버
3. CS 기본기 (자료구조, 알고리즘, 네트워크, OS, 데이터베이스 등) — 포지션에 맞게 선택

### 답변 후 행동
유저가 답변하면:
1. 짧은 피드백 (1-2문장: 잘한 점 또는 부족한 점 한 가지만)
2. 다음 질문

### 세션 종료 감지
유저 입력이 `/done` 이면 즉시 면접을 종료하고 4단계로 넘어갑니다.
다른 입력은 모두 면접 답변으로 간주합니다.

---

## 4단계: 리포트 생성

`/done` 입력을 받으면:

1. "수고하셨습니다. 면접 결과를 정리하겠습니다." 라고 말합니다.
2. 지금까지의 대화를 바탕으로 아래 리포트를 생성합니다.
3. 리포트를 대화에 출력합니다.
4. Write 툴을 사용해 현재 디렉토리(스킬이 실행된 디렉토리)에 파일로 저장합니다.
   - 파일명: `interview-report-YYYY-MM-DD-HH-MM.md` (현재 날짜/시각 사용)
   - 예: `interview-report-2026-04-14-15-30.md`
5. "리포트가 `interview-report-YYYY-MM-DD-HH-MM.md` 로 저장되었습니다." 라고 말합니다.

### 리포트 형식

```markdown
# 기술 면접 리포트

**날짜:** YYYY-MM-DD HH:MM  
**지원 포지션:** [JD에서 파악한 포지션명]

---

## 1. Q&A 전체 기록

### Q1. [질문 내용]

**내 답변:**
[유저의 실제 답변]

**모범 답안:**
[이 질문에 대한 이상적인 답변 — 핵심 개념, 놓친 포인트 포함]

---

### Q2. [질문 내용]

**내 답변:**
[유저의 실제 답변]

**모범 답안:**
[모범 답안]

---

(이하 동일하게 모든 Q&A 기록)

---

## 2. 보완할 점 & 미흡한 CS 포인트

- [미흡했던 개념/주제 1]: [왜 중요한지, 어떻게 공부하면 좋은지 한 줄]
- [미흡했던 개념/주제 2]: [설명]
- ...

보완할 점이 없으면 "전반적으로 양호합니다." 라고 기재합니다.

---

## 3. 종합 평가

**강점:**
- [잘한 점 1]
- [잘한 점 2]

**약점:**
- [아쉬운 점 1]
- [아쉬운 점 2]

**총평:**
[2-3문장으로 전체 면접 성과 요약]
```

---

## 주의사항

- 면접 중에는 절대로 "저는 AI입니다" 같은 말을 하지 않습니다. 면접관 역할을 끝까지 유지합니다.
- `/done` 외의 어떤 입력도 면접 답변으로 처리합니다.
- 리포트 생성 시 실제 대화 내용만 반영합니다. 없는 내용을 지어내지 않습니다.
- 질문은 유저의 수준(경력)에 맞게 조절합니다. 신입이면 기본기 위주, 시니어면 설계/트레이드오프 위주.
```

Save to `/Users/minishtechq/interview-skill/skills/interview.md`

- [ ] **Step 2: Manual verification checklist**

실제로 Claude Code에서 `/interview` 를 실행해 아래를 확인합니다:

```
[ ] args 없이 실행 → 이력서 붙여넣기 안내가 나오는가
[ ] 이력서 붙여넣기 → JD 붙여넣기 안내가 나오는가
[ ] JD 붙여넣기 → "면접을 시작하겠습니다" 후 첫 질문이 나오는가
[ ] 답변 입력 → 짧은 피드백 + 다음 질문이 나오는가
[ ] 한 번에 질문 하나만 나오는가
[ ] /done 입력 → 리포트가 대화에 출력되는가
[ ] 리포트 파일이 현재 디렉토리에 생성되었는가
[ ] 파일명 형식이 interview-report-YYYY-MM-DD-HH-MM.md 인가
```

- [ ] **Step 3: Commit**

```bash
git add skills/interview.md
git commit -m "feat: add interview skill"
```

---

### Task 3: README

**Files:**
- Create: `README.md`

- [ ] **Step 1: Write README.md**

```markdown
# interview-skill

Claude Code 기반 AI 기술 면접 스킬. 이력서와 채용공고(JD)를 바탕으로 자유 대화형 기술 면접을 진행하고, 종료 시 Q&A 전체 기록 + 보완할 점 + 종합 평가 리포트를 생성합니다.

## 설치

### 1. 이 저장소 클론

```bash
git clone git@github.com:hanbonghun/interview-skill.git
```

### 2. Claude Code 설정에 스킬 경로 등록

`~/.claude/settings.json` 에 아래를 추가합니다:

```json
{
  "skills": {
    "paths": ["/path/to/interview-skill/skills"]
  }
}
```

`/path/to/interview-skill` 은 실제 클론한 경로로 바꿔주세요.

### 3. Claude Code 재시작

설정 반영을 위해 Claude Code를 재시작합니다.

## 사용법

### 파일 경로로 시작

```
/interview resume.pdf jd.txt
```

### 붙여넣기로 시작

```
/interview
```

안내에 따라 이력서와 JD를 붙여넣으면 됩니다.

### 면접 종료 및 리포트 생성

면접 중 언제든:

```
/done
```

리포트가 대화에 출력되고, 현재 디렉토리에 `interview-report-YYYY-MM-DD-HH-MM.md` 로 저장됩니다.

## 리포트 구성

1. **Q&A 전체 기록** — 각 질문, 내 답변, 모범 답안
2. **보완할 점 & 미흡한 CS 포인트** — 약했던 개념과 학습 방향
3. **종합 평가** — 강점, 약점, 총평

## 지원 파일 형식

`.pdf`, `.txt`, `.md`, `.docx` 등 Claude Code가 읽을 수 있는 모든 형식

## 삭제

`~/.claude/settings.json` 에서 등록한 경로를 제거하면 됩니다.
```

Save to `/Users/minishtechq/interview-skill/README.md`

- [ ] **Step 2: Commit**

```bash
git add README.md
git commit -m "docs: add README with install and usage guide"
```

---

### Task 4: Final Verification

- [ ] **Step 1: Confirm file structure matches spec**

```bash
find /Users/minishtechq/interview-skill -not -path '*/.git/*' | sort
```

Expected output:
```
/Users/minishtechq/interview-skill
/Users/minishtechq/interview-skill/.gitignore
/Users/minishtechq/interview-skill/README.md
/Users/minishtechq/interview-skill/docs
/Users/minishtechq/interview-skill/docs/superpowers
/Users/minishtechq/interview-skill/docs/superpowers/plans
/Users/minishtechq/interview-skill/docs/superpowers/plans/2026-04-14-interview-skill.md
/Users/minishtechq/interview-skill/docs/superpowers/specs
/Users/minishtechq/interview-skill/docs/superpowers/specs/2026-04-14-interview-skill-design.md
/Users/minishtechq/interview-skill/skills
/Users/minishtechq/interview-skill/skills/interview.md
```

- [ ] **Step 2: Confirm git log looks clean**

```bash
git log --oneline
```

Expected (order may vary):
```
xxxxxxx docs: add README with install and usage guide
xxxxxxx feat: add interview skill
xxxxxxx chore: add gitignore
xxxxxxx docs: add interview skill design spec
```

- [ ] **Step 3: Commit plan doc**

```bash
git add docs/superpowers/plans/
git commit -m "docs: add implementation plan"
```
