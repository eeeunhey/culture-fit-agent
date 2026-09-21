# 🤝 Culture Fit Agent (컬처핏 평가 에이전트)

기업의 채용공고(JD) 및 조직문화 데이터와 지원자의 이력서/자기소개서 데이터를 다각도로 분석하여, 직무 적합도 및 컬처핏(조직문화 부합도)을 측정하고 추천 리포트를 생성하는 LangGraph 기반 멀티 에이전트 시스템입니다.

---

## 📁 프로젝트 구조 및 협업 영역

본 프로젝트는 **기업(Company)** 파트와 **지원자(Applicant)** 파트가 각자의 영역에서 독립적으로 프로토타입을 개발하고, 검증된 로직을 LangGraph 파이프라인으로 통합하는 협업 구조를 갖추고 있습니다.

```text
culture-fit-agent/
│
├── notebooks/                     # [실험 공간] 팀원별 독립 작업 영역 (Git 충돌 방지)
│   ├── company/                   # 기업 담당자 작업 영역
│   │   └── 01_company_test.ipynb  # JD 분석, 인재상/조직문화 추출 실험 및 패키지 검증
│   │
│   └── applicant/                 # 지원자 담당자 작업 영역
│       └── 01_applicant_test.ipynb# 이력서 분석, 성향/역량 분석 실험
│
├── src/                           # [공통 모듈] 검증된 로직을 통합하는 공간
│   ├── app.py                     # LangGraph 메인 엔트리포인트 (langgraph.json 참조)
│   ├── nodes.py                   # 실험을 마친 Node 함수들을 이관하는 곳
│   ├── routers.py                 # 조건부 라우팅 함수 정의
│   └── state.py                   # 공통으로 공유하는 State 데이터 규격
│
├── langgraph.json                 # LangGraph CLI 설정 파일
├── pyproject.toml                 # 의존성 및 패키지 설정
├── RUN_GUIDE.md                   # 상세 실행 명령어 가이드
└── README.md                      # 프로젝트 소개 및 협업 규칙 (본 문서)
```

---

## ⚡ 빠른 시작 (Quick Start)

본 프로젝트는 초고속 패키지 매니저인 [`uv`](https://docs.astral.sh/uv/)를 사용합니다.

### 1. 저장소 클론
```bash
git clone https://github.com/eeeunhey/culture-fit-agent.git
cd culture-fit-agent
```

### 2. 가상환경 및 의존성 자동 동기화
`uv`가 설치되어 있다면 OS와 무관하게 아래 한 줄로 파이썬 런타임과 전체 패키지가 자동 설치됩니다:
```bash
uv sync
```

### 3. 환경 변수(.env) 설정
```bash
# Git Bash 또는 macOS/Linux 환경
cp .env.sample .env

# Windows PowerShell 환경
Copy-Item .env.sample .env
```
생성된 `.env` 파일에 본인의 `OPENAI_API_KEY`를 입력합니다.

### 4. 개발 환경 가동

#### A) 주피터 노트북 실행
- VS Code에서 `notebooks/company/` 또는 `notebooks/applicant/` 내의 `.ipynb` 파일을 엽니다.
- 우측 상단 커널 선택기에서 **`.venv (Python 3.14.2)`**를 선택하고 셀을 실행합니다.

#### B) LangGraph 로컬 API 서버 실행
```bash
# Git Bash 사용자
PYTHONUTF8=1 uv run langgraph dev

# Windows PowerShell 사용자
$env:PYTHONUTF8="1"; uv run langgraph dev

# macOS / Linux 사용자
uv run langgraph dev
```

---

## 🛡️ 팀원 & AI 공통 협업 원칙 (Collaboration & AI Guidelines)

팀원이 직접 코딩하거나, Cursor / GitHub Copilot / Claude Code 등 **AI 코딩 어시스턴트를 활용할 때** 공통으로 준수해야 할 필수 원칙입니다:

1. **도메인 작업 영역 준수 (Domain Isolation)**
   - 기업 분석 관련 작업은 오직 `notebooks/company/` 내에서만 수행합니다.
   - 지원자 분석 관련 작업은 오직 `notebooks/applicant/` 내에서만 수행합니다.
   - AI에게 작업을 요청할 때도 상대방 폴더의 파일을 수정하지 않도록 주의합니다.
2. **공통 상태(State) 호환성 유지**
   - `src/state.py`는 두 파트가 데이터를 주고받는 공통 규약입니다.
   - 기존 State의 키 이름을 임의로 변경하거나 삭제하지 마십시오.
3. **보안 및 개인정보 보호**
   - API 키(OpenAI, LangSmith, Tavily 등)를 코드나 노트북에 직접 하드코딩하지 마십시오. 항상 `.env`에서 로드합니다.
   - 실제 지원자의 개인정보가 담긴 이력서는 커밋하지 않습니다.
4. **임의 샘플 파일 생성 금지**
   - 불필요한 더미/샘플 파일을 임의로 프로젝트에 생성하지 않습니다.
5. **Windows UTF-8 인코딩 보장**
   - 파이썬 파일 오픈 코드 작성 시 항상 `open(..., encoding="utf-8")`을 명시합니다.