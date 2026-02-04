# AI Advisor - RAG 기반 AI 챗봇

<p align="center">
  <strong>문서 기반 질의응답이 가능한 오픈소스 AI 챗봇 시스템</strong>
</p>

<p align="center">
  <a href="#-소개">소개</a> •
  <a href="#-주요-기능">주요 기능</a> •
  <a href="#-빠른-시작-5분">빠른 시작</a> •
  <a href="#-상세-설치-가이드">상세 설치</a> •
  <a href="#-사용법">사용법</a> •
  <a href="#-api-문서">API 문서</a> •
  <a href="#-문제-해결">문제 해결</a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.11+-blue.svg" alt="Python">
  <img src="https://img.shields.io/badge/React-18-61dafb.svg" alt="React">
  <img src="https://img.shields.io/badge/FastAPI-0.100+-009688.svg" alt="FastAPI">
  <img src="https://img.shields.io/badge/Docker-Compose-2496ED.svg" alt="Docker">
</p>

---

## 📖 소개

AI Advisor는 **RAG(Retrieval-Augmented Generation)** 기술을 활용한 문서 기반 AI 챗봇입니다.
PDF 문서를 업로드하면 AI가 문서 내용을 학습하고, 사용자의 질문에 정확한 답변을 제공합니다.

### 이런 분들에게 추천합니다

- 📚 대량의 PDF 문서에서 정보를 빠르게 찾고 싶은 분
- 🏢 사내 문서 기반 Q&A 시스템이 필요한 기업
- 🎓 학습 자료를 기반으로 AI 튜터를 만들고 싶은 교육기관
- 💻 RAG 기술을 배우고 싶은 개발자

---

## ✨ 주요 기능

| 기능 | 설명 |
|------|------|
| 📄 **문서 업로드** | PDF 파일을 업로드하면 자동으로 텍스트 추출 및 인덱싱 |
| 🤖 **RAG 질의응답** | 업로드된 문서를 기반으로 정확한 답변 생성 |
| 💬 **실시간 스트리밍** | 답변이 실시간으로 한 글자씩 표시 |
| 📊 **관리자 대시보드** | 사용자 관리, 사용량 통계, 비용 분석 |
| 🔐 **사용자 인증** | 회원가입, 로그인, JWT 기반 인증 |
| 🚀 **GPU 가속** | NVIDIA GPU를 활용한 빠른 처리 (선택사항) |

### 기술 스택

| 구분 | 기술 |
|------|------|
| **Frontend** | React 18, Material-UI, Axios |
| **Backend** | FastAPI (Python 3.11), Uvicorn |
| **Database** | PostgreSQL 15, Qdrant (Vector DB), Redis |
| **AI/ML** | OpenAI GPT-4o, BGE Reranker v2-m3 |
| **OCR** | GPT-4o Vision (73.3% 정확도) |
| **Deployment** | Docker, Docker Compose, Nginx |

---

## 🚀 빠른 시작 (5분)

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
# Docker 설치 확인
docker --version
# 출력 예: Docker version 24.0.7

# Docker Compose 확인
docker compose version
# 출력 예: Docker Compose version v2.21.0
```

> **Docker가 없다면?** [Docker 설치 가이드](#docker-설치-ubuntudebian)를 참고하세요.

### Step 2: 프로젝트 다운로드

```bash
# GitHub에서 프로젝트 다운로드
git clone https://github.com/Hallym-AIED-OSC/AI-Advisor.git

# 프로젝트 폴더로 이동
cd AI-Advisor
```

### Step 3: 환경 변수 설정

```bash
# 환경 변수 템플릿 복사
cp .env.example .env
cp backend/.env.example backend/.env.docker
```

**backend/.env.docker 파일을 열어서 OpenAI API 키 입력:**

```bash
# 파일 편집 (nano, vim, 또는 원하는 에디터 사용)
nano backend/.env.docker
```

아래 부분을 찾아서 실제 API 키로 변경:

```env
# ✅ 필수: OpenAI API Key
OPENAI_API_KEY=sk-여기에-실제-API-키-입력

# ✅ 필수: JWT 비밀키 (아무 문자열이나 입력, 보안을 위해 길게)
JWT_SECRET_KEY=my-super-secret-key-change-this-in-production-12345
```

> **OpenAI API 키가 없다면?** [OpenAI API 키 발급 방법](#openai-api-키-발급-방법)을 참고하세요.

### Step 4: 서비스 실행

```bash
# 서비스 시작 (첫 실행 시 이미지 빌드로 5-10분 소요)
docker compose up -d

# 실행 상태 확인
docker compose ps
```

정상 실행 시 출력:
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
| **채팅 화면** | http://localhost:10310 | 메인 웹 인터페이스 |
| **관리자** | http://localhost:10310/admin-login | 관리자 대시보드 |
| **API 문서** | http://localhost:10311/docs | Swagger API 문서 |

**초기 관리자 계정:**
- 사용자명: `admin`
- 비밀번호: `admin1234`

> ⚠️ **보안 주의**: 운영 환경에서는 반드시 관리자 비밀번호를 변경하세요!

---

## 📋 상세 설치 가이드

### Docker 설치 (Ubuntu/Debian)

```bash
# 1. Docker 설치
curl -fsSL https://get.docker.com | sh

# 2. 현재 사용자를 docker 그룹에 추가 (sudo 없이 docker 사용)
sudo usermod -aG docker $USER

# 3. 변경사항 적용 (터미널 재시작 또는)
newgrp docker

# 4. 설치 확인
docker --version
docker compose version
```

### Docker 설치 (macOS)

1. [Docker Desktop for Mac](https://www.docker.com/products/docker-desktop/) 다운로드
2. 설치 파일 실행 및 Applications로 드래그
3. Docker Desktop 실행
4. 터미널에서 확인: `docker --version`

### Docker 설치 (Windows)

1. WSL2 활성화 (PowerShell 관리자 모드):
   ```powershell
   wsl --install
   ```
2. [Docker Desktop for Windows](https://www.docker.com/products/docker-desktop/) 다운로드 및 설치
3. Docker Desktop 실행 → Settings → WSL Integration 활성화
4. WSL 터미널에서 확인: `docker --version`

### NVIDIA GPU 설정 (선택사항)

GPU를 사용하면 문서 처리 속도가 크게 향상됩니다.

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

### CPU 모드로 실행 (GPU 없는 경우)

`docker-compose.yml` 파일에서 GPU 관련 설정을 주석 처리:

```yaml
backend:
  # ... 기존 설정 ...
  # runtime: nvidia  # 이 줄 주석 처리
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

### OpenAI API 키 발급 방법

1. [OpenAI Platform](https://platform.openai.com/) 접속
2. 회원가입 또는 로그인
3. 우측 상단 프로필 → "View API keys" 클릭
4. "Create new secret key" 클릭
5. 생성된 키를 안전한 곳에 복사 (한 번만 표시됨!)
6. `backend/.env.docker` 파일에 입력

> 💡 **비용 참고**: GPT-4o 기준 약 $5/1M 입력 토큰, $15/1M 출력 토큰

---

## 📱 사용법

### 일반 사용자

#### 1. 회원가입

1. http://localhost:10310 접속
2. "회원가입" 버튼 클릭
3. 사용자명, 비밀번호, 이메일 입력
4. "가입하기" 클릭

#### 2. 문서 업로드

1. 로그인 후 채팅 화면 진입
2. 좌측 하단 📎 (첨부) 아이콘 클릭
3. PDF 파일 선택 후 업로드
4. 업로드 완료 메시지 확인 (약 1-2분 소요)

#### 3. 질문하기

```
예시 질문들:
- "이 문서의 주요 내용을 요약해줘"
- "2023년 예산은 얼마야?"
- "이 정책의 목적이 뭐야?"
```

### 관리자

#### 대시보드 기능

| 메뉴 | 기능 |
|------|------|
| **대시보드** | 전체 통계, 일별/월별 사용량, 활성 사용자 |
| **사용자 관리** | 사용자 목록, 활성화/비활성화, 권한 관리 |
| **문서 관리** | 업로드된 문서 목록, 삭제, 재인덱싱 |
| **비용 분석** | 모델별 API 사용량, 예상 비용 |

---

## 📚 API 문서

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

### 채팅 API

#### 메시지 전송 (스트리밍)

```bash
curl -X POST http://localhost:10311/api/chat/send \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "안녕하세요",
    "session_id": null
  }'
```

> 전체 API 문서는 http://localhost:10311/docs 에서 확인하세요.

---

## 🔧 문제 해결

### 자주 발생하는 문제

#### 1. Docker 컨테이너가 시작되지 않음

```bash
# 상태 확인
docker compose ps

# 로그 확인
docker compose logs backend

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

#### 3. 포트 충돌 오류

```bash
# 사용 중인 포트 확인
sudo lsof -i :10310
sudo lsof -i :10311

# 해결: docker-compose.yml에서 포트 변경
ports:
  - "8080:80"  # 10310 대신 8080 사용
```

#### 4. GPU가 인식되지 않음

```bash
# NVIDIA 드라이버 확인
nvidia-smi

# Docker에서 GPU 확인
docker run --rm --gpus all nvidia/cuda:12.0-base nvidia-smi
```

#### 5. 메모리 부족 오류

```bash
# 현재 메모리 사용량
docker stats

# 해결: docker-compose.yml에서 메모리 제한 조정
deploy:
  resources:
    limits:
      memory: 4G
```

### 로그 확인 방법

```bash
# 전체 로그
docker compose logs -f

# 특정 서비스 로그
docker compose logs -f backend
docker compose logs -f postgres

# 최근 100줄만
docker compose logs --tail=100 backend
```

---

## 🏗️ 프로젝트 구조

```
AI-Advisor/
├── backend/                 # FastAPI 백엔드
│   ├── routers/            # API 라우터
│   │   ├── auth.py         # 인증 (회원가입/로그인)
│   │   ├── rag_chat.py     # RAG 채팅
│   │   └── admin.py        # 관리자 API
│   ├── rag/                # RAG 파이프라인
│   │   ├── parsers/        # 문서 파서 (GPT-4o, PyMuPDF 등)
│   │   ├── retriever/      # 검색 엔진
│   │   └── hybrid_v4/      # HYBRID V4 RAG 서비스
│   ├── database/           # 데이터베이스 모델
│   ├── providers/          # LLM/임베딩 프로바이더
│   └── server.py           # FastAPI 메인
├── frontend/               # React 프론트엔드
│   ├── src/
│   │   ├── components/     # React 컴포넌트
│   │   ├── api/            # API 클라이언트
│   │   └── styles/         # CSS 스타일
│   └── public/
├── docker-compose.yml      # Docker 서비스 정의
├── .env.example            # 환경 변수 템플릿
└── README.md               # 이 문서
```

---

## 📄 라이선스

이 프로젝트는 MIT 라이선스로 배포됩니다. 자세한 내용은 [LICENSE](LICENSE) 파일을 참조하세요.

## 🤝 기여하기

버그 리포트, 기능 제안, Pull Request를 환영합니다!

1. 이 저장소를 Fork합니다
2. Feature 브랜치를 생성합니다 (`git checkout -b feature/AmazingFeature`)
3. 변경사항을 커밋합니다 (`git commit -m 'Add some AmazingFeature'`)
4. 브랜치를 Push합니다 (`git push origin feature/AmazingFeature`)
5. Pull Request를 생성합니다

## 📞 문의

- **Issues**: [GitHub Issues](https://github.com/Hallym-AIED-OSC/AI-Advisor/issues)
- **Discussions**: [GitHub Discussions](https://github.com/Hallym-AIED-OSC/AI-Advisor/discussions)

---

<p align="center">
  Made with ❤️ by <a href="https://github.com/Hallym-AIED-OSC">Hallym AI Education OSC</a>
</p>
