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

안내에 따라 이력서와 JD를 붙여넣으면 됩니다. JD가 없으면 `없음`을 입력하세요.

### 면접 종료 및 리포트 생성

면접 중 언제든:

```
done
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
