---
layout: single
title: FastAPI 입문 가이드 - 빠르고 쉬운 Python 웹 프레임워크
categories: Backend
tags: [FastAPI, Python, Backend, Web]
toc: true
sidebar:
    nav: "counts"
---

# 🚀 FastAPI 입문 가이드 - 빠르고 쉬운 Python 웹 프레임워크

## 📝 FastAPI란?

[FastAPI](https://fastapi.tiangolo.com/)는 Python 기반의 현대적인 웹 프레임워크로, 다음과 같은 특징을 가지고 있습니다:

- **빠른 성능** (Starlette 기반 비동기 처리)
- **자동 문서화** (Swagger UI, Redoc)
- **타입 힌트를 활용한 유효성 검사**
- **Pydantic을 이용한 데이터 검증**
- **비동기 지원 (async/await)**

Flask나 Django보다 더 가볍고, API 서버에 특화된 프레임워크입니다.

---

## ⚙️ 설치 방법

```bash
pip install fastapi uvicorn
```

- `fastapi`: 프레임워크 본체
- `uvicorn`: ASGI 서버 (비동기 처리용 서버)

---

## 🧪 기본 예제

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello, FastAPI!"}

@app.get("/items/{item_id}")
def read_item(item_id: int, q: str = None):
    return {"item_id": item_id, "q": q}
```

### 실행 명령어:
```bash
uvicorn main:app --reload
```
- `main`: 파일 이름 (예: `main.py`)
- `app`: FastAPI 인스턴스
- `--reload`: 코드 변경 시 자동 재시작 (개발 편의성)

---

## 📑 자동 API 문서

- Swagger UI: [http://localhost:8000/docs](http://localhost:8000/docs)
- Redoc: [http://localhost:8000/redoc](http://localhost:8000/redoc)

FastAPI는 API 정의만으로 위와 같은 문서를 자동 생성해줍니다.

---

## 📌 주요 특징 요약

- 타입 기반 입력 유효성 검사
- Swagger 문서 자동 생성
- Pydantic으로 JSON 데이터 검증
- RESTful API 서버 구현에 최적화
- 비동기 처리 지원 (고성능 서버 가능)

---

## 🧠 마무리

FastAPI는 학습 곡선이 낮으면서도, 실전에서 빠르게 API 서버를 개발할 수 있는 매우 강력한 도구입니다. 

앞으로 사용자 인증, 데이터베이스 연결, 의존성 주입 등 다양한 고급 기능도 함께 알아보면 좋습니다.

> 💡 다음 글 예고: FastAPI에서 JWT 인증 구현하기

