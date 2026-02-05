# OpenClaw 문제 해결: Gateway 토큰 누락

## 문제 상황
OpenClaw 대시보드 접속 시 브라우저에 다음과 같은 에러가 표시됨:
```
disconnected (1008): unauthorized: gateway token missing (open a tokenized dashboard URL or paste token in Control UI settings)
```

## 원인
OpenClaw Gateway는 기본적으로 `token` 모드로 보안 인증을 요구합니다. 대시보드 URL에 `?token=...` 파라미터가 없으면 접속이 거부됩니다. 초기 실행 시 터미널에 표시된 토큰이 포함된 URL을 사용하지 않았을 때 주로 발생합니다.

## 해결 방법
1.  **토큰 확인**: 로컬 설정 파일에서 토큰 값을 찾습니다.
    - 파일 위치: `~/.openclaw/openclaw.json`
    - `gateway` -> `auth` -> `token` 항목 확인
    
    ```json
    "gateway": {
      "auth": {
        "mode": "token",
        "token": "여기에_토큰이_있습니다"
      }
    }
    ```

2.  **토큰을 포함하여 접속**: 대시보드 URL 뒤에 토큰을 붙여서 접속합니다.
    - URL 형식: `http://127.0.0.1:18789/?token=<토큰값>`
