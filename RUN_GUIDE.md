# 🚀 Culture Fit Agent 실행 및 협업 가이드

본 가이드는 **기업(Company)** 분석 파트와 **지원자(Applicant)** 분석 파트가 각각 독립적으로 실험하고, LangGraph 기반의 서비스로 통합하여 가동할 수 있는 표준 실행 절차를 제공합니다.

---

## 📁 디렉터리 구조 개요

```text
culture-fit-agent/
│
├── notebooks/                     # [실험 공간] 팀원별 독립 작업 공간
│   ├── company/                   # 기업 담당자 작업 영역
│   │   ├── 01_company_test.ipynb  # JD 분석, 인재상/조직문화 추출 실험
│   │   └── sample_jd.txt          # 기업 테스트용 샘플 채용공고
│   │
│   └── applicant/                 # 지원자 담당자 작업 영역
│       ├── 01_applicant_test.ipynb# 이력서 분석, 성향/역량 분석 실험
│       └── sample_resume.txt      # 지원자 테스트용 샘플 이력서
│
├── src/                           # [공통 모듈] 검증된 로직을 통합하는 공간
│   ├── app.py                     # LangGraph 메인 진입점 (langgraph.json 참조)
│   ├── nodes.py                   # 실험을 마친 Node 함수들을 이관하는 곳
│   ├── routers.py                 # 조건부 라우팅 함수 정의
│   └── state.py                   # 공통으로 공유하는 State 데이터 규격
│
├── langgraph.json                 # LangGraph CLI 설정 파일
├── pyproject.toml                 # 의존성 및 패키지 설정
└── RUN_GUIDE.md                   # 본 실행 가이드
```

---

## 1. 초기 환경 구축 명령어

본 프로젝트는 초고속 패키지 매니저인 `uv`를 사용합니다.

### 1.1 저장소 복제 및 가상환경 동기화
터미널(PowerShell 또는 Bash)에서 아래 명령어를 실행합니다.

```powershell
# 1. 패키지 의존성 전체 설치 (가상환경 자동 생성 및 최신화)
uv sync

# 2. 가상환경 활성화 (Windows PowerShell 기준)
.venv\Scripts\Activate.ps1

# (참고: macOS/Linux 환경인 경우)
# source .venv/bin/activate
```

### 1.2 환경 변수(.env) 설정
루트 디렉터리의 `.env.sample`을 복사하여 `.env`를 생성하고 필요한 API 키를 입력합니다.

```powershell
# .env 파일 생성
Copy-Item .env.sample .env
```
`.env` 파일에 발급받은 OpenAI API 키 등을 입력합니다:
```env
OPENAI_API_KEY="your-api-key-here"
```

---

## 2. 주피터 노트북 실행 및 테스트 방법

### 2.1 VS Code / Cursor 에디터에서 바로 실행할 때 (권장)
1. 에디터에서 `notebooks/company/01_company_test.ipynb` 또는 `notebooks/applicant/01_applicant_test.ipynb`를 엽니다.
2. 노트북 우측 상단의 **[Select Kernel (커널 선택)]** 버튼을 클릭합니다.
3. **[Python Environments...]** 선택 후 프로젝트 루트의 가상환경(`.venv`: Python 3.11+)을 선택합니다.
4. 셀을 순서대로 실행(`Shift + Enter`)하여 테스트를 진행합니다.

> 💡 **참고**: 각 노트북 상단에 `sys.path.append(str(Path("../../").resolve()))`가 포함되어 있어, 어느 폴더에 있든 `src` 모듈을 바로 import할 수 있습니다.

### 2.2 터미널에서 JupyterLab 또는 Notebook 서버 띄우기
웹 브라우저 인터페이스에서 노트북을 띄우고 싶다면 다음 명령어를 사용합니다.

```powershell
# 가상환경 내에서 주피터 실행
uv run jupyter lab
# 또는
uv run jupyter notebook
```

---

## 3. LangGraph 로컬 서버 가동 명령어

노트북에서 실험한 로직을 `src/nodes.py`와 `src/app.py`에 반영한 뒤, LangGraph Studio 및 로컬 API 서버를 가동하여 워크플로우를 시각적으로 확인합니다.

```powershell
# Windows 환경에서 한글 및 이모지 출력 인코딩 오류(cp949) 방지 (선택 사항)
$env:PYTHONIOENCODING="utf-8"

# LangGraph 로컬 개발 서버 실행 (핫 리로딩 지원)
uv run langgraph dev
```

서버가 구동되면 콘솔에 출력되는 URL(기본 `http://localhost:2024`) 또는 LangGraph Studio Web UI에 접속하여 에이전트 그래프의 동작을 실시간 디버깅할 수 있습니다.

---

## 4. 팀 협업 및 Git 충돌 방지 3원칙

1. **작업 폴더 격리**:
   - 기업 파트 작업자는 `notebooks/company/`만 수정합니다.
   - 지원자 파트 작업자는 `notebooks/applicant/`만 수정합니다.
   - 각자 독립된 폴더의 파일을 수정하므로 Git 브랜치 병합 시 충돌이 발생하지 않습니다.

2. **노트북 커밋 전 "출력 비우기" (Clear Outputs)**:
   - 셀을 실행하면 그래프나 대용량 로그가 `.ipynb` 파일 내부에 바이너리/텍스트로 누적되어 불필요한 Git diff를 유발합니다.
   - 커밋하기 전에 반드시 **[Clear All Outputs (모든 출력 지우기)]**를 누르고 저장 후 커밋하세요.

3. **`src/state.py` 사전 조율**:
   - 공통으로 사용하는 `src/state.py`의 키 규격(예: `company_culture`, `applicant_profile`)을 변경할 때는 사전에 팀원과 먼저 소통 후 수정합니다.
