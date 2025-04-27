# SSTI (Server-Side Template Injection) 취약점 실습 보고서

## 1. 실습 개요

- **주제**: Flask 기반 웹 서버의 SSTI(Server-Side Template Injection) 취약점 검증
- **목표**: Jinja2 템플릿 엔진의 취약점을 이용하여 서버 측 명령어를 실행하는 PoC 검증

---

## 2. 환경 구성

- **OS**: Windows 10 (PowerShell 사용)
- **Docker**: Docker Desktop for Windows
- **Python**: 3.x 버전
- **Flask 버전**: 3.0.3

### 사용한 주요 파일

| 파일명 | 설명 |
|:---|:---|
| docker-compose.yml | Flask 서버를 컨테이너로 띄우기 위한 설정 파일 |
| Dockerfile | Flask 앱을 컨테이너화하기 위한 설정 파일 |
| src/app.py | SSTI 취약점이 존재하는 Flask 웹 어플리케이션 |
| poc.py | SSTI 취약점을 이용해 공격을 수행하는 PoC 스크립트 |

---

## 3. 동작 흐름

### 3.1 Docker Compose로 서버 실행

```bash
docker compose up --build
![image](https://github.com/user-attachments/assets/5f373eb5-0024-417c-84fd-953ca1efab9c)
