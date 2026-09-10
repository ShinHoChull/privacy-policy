한눈에 보는 시스템 아키텍처 (Architecture Diagram)

flowchart TB
    subgraph Client["사용자 인터페이스"]
        User(["개발자 (슬랙/모바일)"])
        Slack["Slack 채널 (#dev-lotto / #dev-android)"]
    end

    subgraph AgentSystem["Slack-Gemini AI Agent (Python OOP)"]
        direction TB
        Main["main.py (Slack Bolt SocketMode)"]
        SessionMgr["AgentSessionManager\n(채널별 프로젝트 컨텍스트 분리)"]
        Gemini["Google Gemini 3.6 Flash\n(Tool/Function Calling)"]
        PM["ProjectManager\n(안전장치 & Git 작업 통제)"]
        
        Main --> SessionMgr
        SessionMgr --> Gemini
        Gemini <-->|"Tool 호출 / 결과 반환"| PM
    end

    subgraph LocalMachine["맥북 로컬 환경"]
        FS[("로컬 프로젝트 소스코드")]
        GitCLI["Git / GitHub CLI (gh)"]
    end

    subgraph Remote["GitHub 원격 저장소"]
        OriginMain[("origin / main")]
        OriginBranch[("origin / yc-MMDD-HHMM-task")]
        PR["GitHub Pull Request (#PR)"]
    end

    User -->|"1. 지시/분석 요청"| Slack
    Slack -->|"이벤트 수신"| Main
    PM -->|"파일 읽기/쓰기"| FS
    PM -->|"브랜치 생성/커밋/푸시"| GitCLI
    GitCLI -->|"2. 안전 브랜치 푸시"| OriginBranch
    GitCLI -->|"3. PR 자동 생성 (Merge 제외)"| PR
    PR -->|"4. 슬랙으로 PR 링크 회신"| Slack
    User -.->|"5. 인간 최종 코드 리뷰 & Squash Merge"| OriginMain
