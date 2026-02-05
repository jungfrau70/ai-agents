# n8n 설치 및 설정 가이드 (Docker)

이 문서는 Docker Compose를 사용하여 n8n을 설치하고, 로컬의 Ollama 서비스와 연동하는 방법을 설명합니다.

## 설치 개요
- **서비스**: n8n (Workflow Automation)
- **버전**: latest
- **URL**: [http://localhost:5678](http://localhost:5678)
- **설정 파일**: `~/ai-agents/docker-compose.yml`

## 설치 및 실행

### 1. Docker Compose 실행
이미 설정된 `docker-compose.yml` 파일을 사용하여 서비스를 시작합니다.

```bash
cd ~/ai-agents
# 현재 사용자의 쉘 설정 문제로 인해 command 명령어 사용 권장
command docker compose up -d
```

### 2. 접속 및 설정
브라우저에서 [http://localhost:5678](http://localhost:5678)로 접속하여 n8n 초기 설정을 진행하세요.

## Ollama 연동 방법

n8n 내부에서 로컬 Ollama를 사용하려면 **Host** 주소를 다음과 같이 설정해야 합니다:

- **Base URL**: `http://host.docker.internal:11434`

> **설명**: `docker-compose.yml`에 설정된 `extra_hosts` 옵션을 통해, 컨테이너 내부에서 호스트 머신(내 컴퓨터)의 로컬포트에 접근할 수 있습니다.

## 관리 명령어

- **중지**: `command docker compose stop`
- **재시작**: `command docker compose restart`
- **로그 확인**: `command docker compose logs -f n8n`

> [!WARNING]
> `.bashrc` 파일에 정의된 `docker` alias가 삭제된 디렉토리(`~/gotra`)를 참조하고 있어 일반 `docker compose` 명령어가 실패할 수 있습니다. 당분간 `command docker compose ...` 형식을 사용하거나 `.bashrc`의 해당 alias를 수정하는 것을 권장합니다.
