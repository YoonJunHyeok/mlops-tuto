# 추가 필요한 패키지 설치

```bash
pip install "fastapi[all]"
```

# 실행

```bash
uvicorn main:app --reload
```

# docker

```bash
docker build -t fastapi-tuto .
```

```bash
docker run -d \
  --name fastapi-tuto \
  -p 8000:8000 \
  fastapi-tuto
```
