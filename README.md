# Advisor OSC - 대학교 학사정보 RAG 챗봇

<p align="center">
  <strong>대학교 학사 문서 기반 질의응답 AI 챗봇 시스템</strong>
</p>

<p align="center">
  <a href="#-소개">소개</a> •
  <a href="#-주요-기능">주요 기능</a> •
  <a href="#-빠른-시작">빠른 시작</a> •
  <a href="#-설치-가이드">설치 가이드</a> •
  <a href="#-사용법">사용법</a> •
  <a href="#-api-문서">API 문서</a> •
  <a href="#-문제-해결">문제 해결</a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.11+-blue.svg" alt="Python">
  <img src="https://img.shields.io/badge/React-18-61dafb.svg" alt="React">
  <img src="https://img.shields.io/badge/FastAPI-0.100+-009688.svg" alt="FastAPI">
  <img src="https://img.shields.io/badge/Docker-Compose-2496ED.svg" alt="Docker">
  <img src="https://img.shields.io/badge/RAG-HYBRID__V4-green.svg" alt="RAG">
</p>

---

## 소개

**Advisor OSC**는 대학교 학사정보를 위한 RAG(Retrieval-Augmented Generation) 기반 AI 챗봇입니다.

학칙, 교육과정, 학사일정 등의 PDF 문서를 업로드하면 AI가 문서 내용을 학습하여 학생, 교직원, 교수의 질문에 정확한 답변을 제공합니다.

### 특징

- **HYBRID_V4 RAG 파이프라인**: Semantic Chunking + GPT-4o Vision 이미지 캡셔닝
- **단일 테넌트 구조**: 일반 사용자 + 관리자 (2단계 역할)
- **실시간 스트리밍**: SSE 기반 실시간 답변 출력
- **GPU 가속 지원**: NVIDIA GPU를 활용한 빠른 문서 처리

### 대상 사용자

- 대학생: 학사 관련 질문 (휴학, 졸업, 수강신청 등)
- 대학 교직원: 학사 업무 지원
- 교수: 교육과정 및 학사 규정 확인

---

## 주요 기능

| 기능 | 설명 |
|------|------|
| **문서 업로드** | PDF 파일 업로드 시 자동 텍스트 추출 및 벡터 인덱싱 |
| **RAG 질의응답** | 업로드된 학사 문서 기반 정확한 답변 생성 |
| **실시간 스트리밍** | SSE(Server-Sent Events)로 답변이 실시간 표시 |
| **관리자 대시보드** | 사용자 관리, 사용량 통계, 비용 분석 |
| **사용자 인증** | 회원가입, 로그인, JWT 기반 인증 |
| **오류 리포트** | 사용자가 잘못된 답변 신고 가능 |

### 기술 스택

| 구분 | 기술 |
|------|------|
| **Frontend** | React 18, Axios, Recharts |
| **Backend** | FastAPI (Python 3.11), Uvicorn |
| **Database** | PostgreSQL 15, Qdrant (Vector DB), Redis |
| **AI/ML** | OpenAI GPT-4o, text-embedding-3-small/large |
| **Reranker** | BGE Reranker v2-m3 |
| **OCR** | GPT-4o Vision (이미지 캡셔닝) |
| **Deployment** | Docker, Docker Compose |

### 서비스 포트

| 서비스 | 포트 | 설명 |
|--------|------|------|
| Frontend | 10310 | React 웹 UI |
| Backend | 10311 | FastAPI 서버 |
| PostgreSQL | 10312 | 사용자/세션 DB (localhost only) |
| Redis | 10313 | 캐시/Rate Limiting (localhost only) |
| Qdrant | 10314 | 벡터 DB (localhost only) |

---

## 빠른 시작

### 필수 요구사항

| 항목 | 최소 사양 | 권장 사양 |
|------|----------|----------|
| **OS** | Ubuntu 20.04+ / macOS / Windows WSL2 | Ubuntu 22.04 |
| **RAM** | 8GB | 16GB 이상 |
| **저장공간** | 20GB | 50GB SSD |
| **Docker** | Docker 20.10+ | Docker 24+ |
| **GPU** | 선택사항 | NVIDIA GPU (CUDA 12+) |

### Step 1: Docker 설치 확인

```bash
# Docker 버전 확인
docker --version
# 출력 예: Docker version 24.0.7

# Docker Compose 확인
docker compose version
# 출력 예: Docker Compose version v2.21.0
```

> Docker가 없다면 [Docker 설치 가이드](#docker-설치)를 참고하세요.

### Step 2: 프로젝트 다운로드

```bash
# GitHub에서 프로젝트 다운로드
git clone https://github.com/Hallym-AIED-OSC/AI-Advisor.git
cd AI-Advisor
```

### Step 3: 환경 변수 설정

```bash
# 환경 변수 템플릿 복사
cp .env.example .env
cp backend/.env.example backend/.env.docker
```

**`.env` 파일 편집** (docker-compose용):

```env
# 서버 IP 또는 도메인 (외부 접속용)
SERVER_HOST=localhost

# PostgreSQL 비밀번호 (변경 권장)
POSTGRES_PASSWORD=your_secure_password
```

**`backend/.env.docker` 파일 편집**:

```bash
nano backend/.env.docker
```

필수 항목:

```env
# 필수: OpenAI API Key
OPENAI_API_KEY=sk-여기에-실제-API-키-입력

# 필수: JWT 비밀키 (임의의 긴 문자열)
JWT_SECRET_KEY=your-secret-key-change-this-in-production

# 선택: Google Gemini API Key
GOOGLE_API_KEY=your-google-api-key
```

### Step 4: 서비스 실행

```bash
# 서비스 시작 (첫 실행 시 빌드로 5-10분 소요)
docker compose up -d

# 실행 상태 확인
docker compose ps
```

정상 실행 시:
```
NAME                   STATUS          PORTS
advisor-osc-backend    Up (healthy)    0.0.0.0:10311->8000/tcp
advisor-osc-frontend   Up (healthy)    0.0.0.0:10310->80/tcp
advisor-osc-postgres   Up (healthy)    127.0.0.1:10312->5432/tcp
advisor-osc-redis      Up (healthy)    127.0.0.1:10313->6379/tcp
advisor-osc-qdrant     Up              127.0.0.1:10314->6333/tcp
```

### Step 5: 접속 확인

| 서비스 | URL | 설명 |
|--------|-----|------|
| **채팅 화면** | http://localhost:10310 | 메인 웹 UI |
| **관리자 로그인** | http://localhost:10310/admin-login | 관리자 대시보드 |
| **API 문서** | http://localhost:10311/docs | Swagger API 문서 |
| **API 헬스체크** | http://localhost:10311/health | 서버 상태 확인 |

**초기 관리자 계정:**
- 사용자명: `admin`
- 비밀번호: `admin1234`

> 운영 환경에서는 반드시 관리자 비밀번호를 변경하세요!

---

## 설치 가이드

### Docker 설치

#### Ubuntu/Debian

```bash
# Docker 설치
curl -fsSL https://get.docker.com | sh

# 현재 사용자를 docker 그룹에 추가
sudo usermod -aG docker $USER

# 변경사항 적용 (재로그인 또는)
newgrp docker

# 설치 확인
docker --version
docker compose version
```

#### macOS

1. [Docker Desktop for Mac](https://www.docker.com/products/docker-desktop/) 다운로드
2. 설치 후 Docker Desktop 실행
3. 터미널에서 확인: `docker --version`

#### Windows

1. PowerShell (관리자)에서 WSL2 활성화:
   ```powershell
   wsl --install
   ```
2. [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop/) 다운로드 및 설치
3. Docker Desktop → Settings → WSL Integration 활성화

### GPU 설정 (선택사항)

GPU가 있으면 문서 처리 속도가 향상됩니다.

```bash
# NVIDIA Container Toolkit 설치
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
  sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
sudo systemctl restart docker

# GPU 작동 확인
docker run --rm --gpus all nvidia/cuda:12.0-base nvidia-smi
```

### CPU 모드 실행 (GPU 없는 경우)

`docker-compose.yml`에서 GPU 관련 설정 주석 처리:

```yaml
backend:
  # runtime: nvidia  # 주석 처리
  # deploy:          # 아래 블록 전체 주석 처리
  #   resources:
  #     reservations:
  #       devices:
  #         - driver: nvidia
  #           device_ids: ['0']
  #           capabilities: [gpu]
```

`backend/.env.docker`에서 CPU 모드 설정:

```env
BGE_DEVICE=cpu
BGE_USE_FP16=false
```

### OpenAI API 키 발급

1. [OpenAI Platform](https://platform.openai.com/) 접속
2. 회원가입 또는 로그인
3. 우측 상단 프로필 → "View API keys"
4. "Create new secret key" 클릭
5. 생성된 키를 `backend/.env.docker`의 `OPENAI_API_KEY`에 입력

---

## 사용법

### 일반 사용자

#### 1. 회원가입/로그인

1. http://localhost:10310 접속
2. "회원가입" 클릭 → 정보 입력 → 가입
3. 로그인하여 채팅 화면 진입

#### 2. 질문하기

채팅창에 학사 관련 질문 입력:

```
예시 질문들:
- "휴학 신청은 어떻게 하나요?"
- "졸업 요건이 뭐예요?"
- "수강신청 기간이 언제예요?"
- "장학금 신청 방법 알려줘"
```

### 관리자

#### 관리자 로그인

1. http://localhost:10310/admin-login 접속
2. 관리자 계정으로 로그인

#### 대시보드 기능

| 메뉴 | 기능 |
|------|------|
| **대시보드** | 전체 통계, 일별/월별 사용량 |
| **사용자 관리** | 사용자 목록, 활성화/비활성화 |
| **문서 관리** | 업로드된 문서 목록, 삭제, 재인덱싱 |
| **비용 분석** | API 사용량, 예상 비용 |

#### 문서 업로드 (관리자)

관리자 대시보드에서 학사 관련 PDF 문서 업로드:
- 학칙
- 교육과정
- 학사일정
- 장학금 안내 등

---

## API 문서

### 엔드포인트 개요

| API 그룹 | Prefix | 설명 |
|----------|--------|------|
| Auth | `/api/auth` | 인증 (회원가입, 로그인) |
| RAG Chat | `/api/chat` | 기본 RAG 채팅 |
| HYBRID_V4 | `/api/v4` | HYBRID_V4 RAG 채팅 (권장) |
| Admin | `/api/admin` | 관리자 기능 |
| Data Management | `/api/admin/data` | 문서 관리 |
| Error Reports | `/api/errors` | 오류 신고 |

### 인증 API

#### 회원가입

```bash
curl -X POST http://localhost:10311/api/auth/signup \
  -H "Content-Type: application/json" \
  -d '{
    "username": "testuser",
    "password": "password123",
    "email": "test@example.com"
  }'
```

#### 로그인

```bash
curl -X POST http://localhost:10311/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "testuser",
    "password": "password123"
  }'
```

응답:
```json
{
  "access_token": "eyJhbGc...",
  "token_type": "bearer",
  "user": {
    "id": 1,
    "username": "testuser",
    "role": "user"
  }
}
```

### HYBRID_V4 RAG 채팅 API (권장)

#### 채팅 (스트리밍)

```bash
curl -X POST http://localhost:10311/api/v4/chat/send \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "휴학 신청 방법 알려줘",
    "session_id": null
  }'
```

응답 (SSE 스트리밍):
```
data: {"content": "휴학"}
data: {"content": " 신청은"}
data: {"content": " 다음과"}
...
data: {"done": true}
```

#### 질의 (비스트리밍)

```bash
curl -X POST http://localhost:10311/api/v4/chat/query \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "question": "졸업 요건이 뭐야?",
    "top_k": 5
  }'
```

### 관리자 API

#### 대시보드 통계

```bash
curl -X GET http://localhost:10311/api/admin/dashboard \
  -H "Authorization: Bearer ADMIN_TOKEN"
```

#### 사용자 목록

```bash
curl -X GET http://localhost:10311/api/admin/users \
  -H "Authorization: Bearer ADMIN_TOKEN"
```

> 전체 API 문서: http://localhost:10311/docs

---

## 문제 해결

### 자주 발생하는 문제

#### 1. 컨테이너가 시작되지 않음

```bash
# 상태 확인
docker compose ps

# 로그 확인
docker compose logs backend
docker compose logs postgres

# 전체 재시작
docker compose down && docker compose up -d
```

#### 2. "Cannot connect to database" 오류

```bash
# PostgreSQL 컨테이너 확인
docker compose logs postgres

# 데이터베이스 연결 테스트
docker compose exec postgres psql -U advisor -d advisor_osc_db -c "\dt"
```

#### 3. 포트 충돌

```bash
# 사용 중인 포트 확인
sudo lsof -i :10310
sudo lsof -i :10311

# docker-compose.yml에서 포트 변경
ports:
  - "8080:80"  # 10310 대신 8080
```

#### 4. GPU 인식 안됨

```bash
# NVIDIA 드라이버 확인
nvidia-smi

# Docker GPU 테스트
docker run --rm --gpus all nvidia/cuda:12.0-base nvidia-smi
```

#### 5. API 키 오류

```bash
# 환경 변수 확인
docker compose exec backend env | grep API_KEY

# .env.docker 파일 확인
cat backend/.env.docker | grep API_KEY
```

### 로그 확인

```bash
# 전체 로그
docker compose logs -f

# 특정 서비스 로그
docker compose logs -f backend

# 최근 100줄
docker compose logs --tail=100 backend
```

### 데이터 초기화

```bash
# 컨테이너 및 볼륨 삭제 (데이터 초기화)
docker compose down -v

# 다시 시작
docker compose up -d
```

---

## 프로젝트 구조

```
advisor_osc/
├── backend/                    # FastAPI 백엔드
│   ├── routers/               # API 라우터
│   │   ├── auth.py            # 인증 (회원가입/로그인)
│   │   ├── rag_chat.py        # 기본 RAG 채팅
│   │   ├── hybrid_v4_chat.py  # HYBRID_V4 RAG 채팅
│   │   ├── admin.py           # 관리자 API
│   │   ├── data_management.py # 문서 관리
│   │   └── error_reports.py   # 오류 신고
│   ├── services/              # 비즈니스 로직
│   │   └── hybrid_v4_service.py  # HYBRID_V4 서비스
│   ├── rag/                   # RAG 파이프라인
│   │   ├── parsers/           # 문서 파서 (GPT-4o Vision)
│   │   └── retriever/         # 벡터 검색
│   ├── database/              # 데이터베이스 모델
│   ├── providers/             # LLM/임베딩 프로바이더
│   ├── config.py              # 설정 관리
│   ├── server.py              # FastAPI 메인
│   └── .env.example           # 환경 변수 템플릿
├── frontend/                  # React 프론트엔드
│   ├── src/
│   │   ├── components/        # React 컴포넌트
│   │   ├── api/               # API 클라이언트
│   │   └── styles/            # CSS 스타일
│   └── public/
├── docker-compose.yml         # Docker 서비스 정의
├── .env.example               # 환경 변수 템플릿
└── README.md                  # 이 문서
```

---

## 라이선스

이 프로젝트는 MIT 라이선스로 배포됩니다.

## 기여하기

버그 리포트, 기능 제안, Pull Request를 환영합니다!

1. Fork
2. Feature 브랜치 생성 (`git checkout -b feature/NewFeature`)
3. 커밋 (`git commit -m 'Add NewFeature'`)
4. Push (`git push origin feature/NewFeature`)
5. Pull Request 생성

## 문의

- **Issues**: [GitHub Issues](https://github.com/Hallym-AIED-OSC/AI-Advisor/issues)
- **Discussions**: [GitHub Discussions](https://github.com/Hallym-AIED-OSC/AI-Advisor/discussions)

---

<p align="center">
  Made by <a href="https://github.com/Hallym-AIED-OSC">Hallym AI Education OSC</a>
</p>
