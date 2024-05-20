# Producer & Consumer

## 1. 주키퍼와 브로커 띄우기

```bash
docker compose -p kafka-naive -f naive-docker-compose.yaml up -d
```

- p는 project 이름

## 2. Topic

### 2.1 Topic 생성하기

```bash
docker compose -p kafka-naive exec broker kafka-topics --create --topic topic-test --bootstrap-server broker:29092 --partitions 1 --replication-factor 1
```

- broker: 생성된 브로커 서비스의 이름
- kafka-topics: 토픽에 대한 명령을 실행
- --create: 토픽 생성
- --topic: topic-test를 토픽의 이름으로
- --bootstrap-server: 브로커 서비스에 대한 호스트 이름과 포트를 지정
- --partitions: 토픽 내에 파티션 개수를 설정
- --replication-factor: Replication Factor 를 지정

### 2.2 생성된 Topic 확인

```bash
docker compose -p kafka-naive exec broker kafka-topics --describe --topic topic-test --bootstrap-server broker:29092
```

## 3. Consumer 생성

### 3.1 브로커 컨테이너 내부 접속

```bash
docker compose -p kafka-naive exec broker /bin/bash
```

### 3.2 Consumer 실행

```bash
kafka-console-consumer --topic topic-test --bootstrap-server broker:29092
```

- `kafka-console-consumer` 를 이용하여 `topic-test` 토픽을 subscribe
- 수신을 대기하는 상태가 된다.

## 4. Producer 생성

### 4.1 브로커 컨테이너 내부 접속

```bash
docker compose -p kafka-naive exec broker /bin/bash
```

### 4.2 Producer 실행

```bash
kafka-console-producer --topic topic-test --broker-list broker:29092
```

## 5. 종료

```bash
docker compose -p part7-naive down -v
```

# Connect & Connector

## 1. 서비스 생성

```bash
docker compose -p kafka -f kafka-docker-compose.yaml up -d
```

## 2. Source Connector

- Connect 에 API 호출을 통해 생성

### 2.1 생성

```bash
curl -X POST http://localhost:8083/connectors -H "Content-Type: application/json" -d @source_connector.json
```

### 2.2 생성 확인

- Connector 목록 확인

```bash
curl -X GET http://localhost:8083/connectors
```

- postgres-source-connector의 정보를 확인

```bash
curl -X GET http://localhost:8083/connectors/postgres-source-connector
```

## 3. Topic에 쌓인 데이터 확인 w/ kafkacat

### 3.1 모든 Topic list 확인

```bash
kcat -L -b localhost:9092
```

### 3.2 postgres-source-iris_data 토픽에 쌓인 데이터를 확인

```bash
kcat -b localhost:9092 -t postgres-source-iris_data
```

## 3. Sink Connector

### 3.1 Target DB 생성

```bash
docker compose -p target-db -f target-docker-compose.yaml up -d
```

### 3.2 Sink Connector 생성

- Connect 에 API 호출을 통해 생성

```bash
curl -X POST http://localhost:8083/connectors -H "Content-Type: application/json" -d @sink_connector.json
```

### 3.2 Sink Connector 생성 확인

```bash
curl -X GET http://localhost:8083/connectors
```

### 3.3 Target DB 확인

```bash
PGPASSWORD=targetpassword psql -h localhost -p 5433 -U targetuser -d targetdatabase
```
