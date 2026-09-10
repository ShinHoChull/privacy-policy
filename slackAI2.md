```mermaid
sequenceDiagram
    autonumber
    actor Dev as 개발자 (Slack)
    participant Agent as Slack Agent (Python)
    participant Gemini as Gemini 3.6 Flash
    participant PM as ProjectManager
    participant Git as Local Git / GitHub CLI
    participant GH as GitHub Remote

    Dev->>Agent: "로또 필터 기능 추가하고 PR 올려줘"
    Agent->>Gemini: 컨텍스트 및 요청 전달

    rect rgb(240, 248, 255)
        Note over Gemini,PM: [수정 전 안전 분기 단계]
        Gemini->>PM: start_feature_branch("lotto-filter")
        PM->>Git: git checkout main && git pull
        PM->>Git: git checkout -b yc-0910-1530-lotto-filter
        PM-->>Gemini: 브랜치 생성 완료 보고
    end

    rect rgb(255, 250, 240)
        Note over Gemini,PM: [코드 수정 및 일지 기록]
        Gemini->>PM: write_code_file(...)
        PM->>PM: 소스 반영 및 AI_CHANGELOG.md 자동 기록
        PM-->>Gemini: 파일 수정 완료 보고
    end

    rect rgb(245, 255, 245)
        Note over PM,GH: [안전 검사 및 원격 반영]
        Gemini->>PM: git_commit_and_push("feat: 로또 필터 추가")
        PM->>PM: 보안 파일(.env, build 등) 커밋 포함 여부 검사
        PM->>Git: git add . && git commit
        PM->>GH: git push origin yc-0910-1530-lotto-filter
        PM->>GH: gh pr create (--base main)
        GH-->>PM: 생성된 PR URL 반환
    end

    PM-->>Agent: 최종 작업 완료 정보
    Agent->>Dev: 🚀 "작업 완료! PR 링크: https://github.com/.../pull/15"
    Note over Dev,GH: 개발자가 브라우저에서 직접 Diff 확인 후 최종 Merge 승인
