# Ollama 설치 및 설정 가이드 (Docker 기반)

이 문서는 시스템의 `sudo` 권한 없이 Ollama를 설치하고 사용하기 위해 **Docker**를 활용하는 방법을 설명합니다.

## 개요
기본적인 Ollama 설치 스크립트는 루트 권한(`sudo`)을 요구합니다. 이를 우회하기 위해 Docker 컨테이너로 Ollama 서버를 구동하고, 로컬 터미널에서 마치 네이티브 앱처럼 사용할 수 있도록 연결 스크립트(Wrapper Script)를 설정합니다.

## 전제 조건
- **Docker**가 설치되어 있어야 하며, 현재 사용자가 Docker를 실행할 권한이 있어야 합니다.
- 확인 방법:
  ```bash
  docker --version
  ```

## 설치 단계

### 1. Docker 이미지 풀 및 컨테이너 실행
Ollama의 공식 Docker 이미지를 다운로드하고 백그라운드 서비스로 실행합니다. 로컬의 `11434` 포트를 컨테이너와 연결하여 API 접근을 가능하게 합니다.

```bash
# 공식 Ollama 이미지 다운로드 및 컨테이너 실행
# -d: 백그라운드 실행
# -v ollama:/root/.ollama: 모델 데이터를 보존하기 위한 볼륨 설정
# -p 11434:11434: 포트 포워딩
# --name ollama: 컨테이너 이름을 'ollama'로 지정

docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
```

### 2. Wrapper 스크립트 작성
터미널에서 `ollama` 명령어를 직접 입력하여 도커 컨테이너 내부의 올라마를 제어할 수 있도록 스크립트를 생성합니다.

```bash
# 로컬 바이너리 디렉토리 생성 (없을 경우)
mkdir -p ~/.local/bin

# 스크립트 파일 생성
cat << 'EOF' > ~/.local/bin/ollama
#!/bin/bash
if [ -t 0 ]; then
  # 대화형 모드 (터미널 입력이 있을 때)
  exec docker exec -it ollama ollama "$@"
else
  # 비대화형 모드 (파이프라인 등)
  exec docker exec -i ollama ollama "$@"
fi
EOF
```

### 3. 실행 권한 부여 및 경로 확인
생성한 스크립트에 실행 권한을 부여합니다.

```bash
chmod +x ~/.local/bin/ollama
```

> **참고**: `~/.local/bin`이 PATH 환경변수에 포함되어 있어야 합니다. 보통 리눅스 배포판에는 기본적으로 포함되어 있으나, 실행이 안 된다면 `export PATH=$PATH:$HOME/.local/bin`을 `~/.bashrc` 등에 추가하세요.

## 사용 방법

이제 터미널에서 일반적인 설치 방식과 동일하게 `ollama` 명령어를 사용할 수 있습니다.

- **버전 확인**:
  ```bash
  ollama --version
  ```

- **모델 다운로드 및 실행 (예: Llama 3)**:
  ```bash
  ollama run llama3
  ```

- **설치된 모델 목록 보기**:
  ```bash
  ollama list
  ```

## 작동 원리
1. 사용자가 `ollama run llama3` 입력
2. `~/.local/bin/ollama` 스크립트가 실행됨
3. 스크립트가 `docker exec -it ollama ollama run llama3` 명령어로 변환하여 실행
4. Docker 컨테이너 내부의 Ollama가 요청을 처리하고 결과를 출력

## 트러블슈팅

- **"command not found" 오류**:
  `~/.local/bin` 경로가 PATH에 있는지 확인하세요. `echo $PATH` 명령어로 확인할 수 있습니다.

- **Docker 컨테이너가 중지된 경우**:
  컴퓨터 재부팅 등으로 컨테이너가 꺼져있다면 다시 시작해 주세요.
  ```bash
  docker start ollama
  ```
