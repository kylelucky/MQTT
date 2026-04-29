# MQTT 실습 프로젝트 (Mosquitto + WebSocket + Web Client)

## 📌 개요

본 프로젝트는 MQTT(Message Queuing Telemetry Transport) 프로토콜을 활용하여
로컬 환경에서 **브로커 구축 → CLI 통신 → 웹(WebSocket) 연동**까지 전체 흐름을 실습한 프로젝트이다.

단순 메시지 송수신을 넘어,

* MQTT Broker 구성
* Publish / Subscribe 구조 이해
* WebSocket 기반 브라우저 연동
  까지 수행하였다.

---

## 🧠 MQTT 개념 정리

MQTT는 **Publish / Subscribe 기반의 경량 메시징 프로토콜**이다.

* Publisher: 메시지 전송
* Subscriber: 메시지 수신
* Broker: 메시지 중계 서버

```text
Publisher → Broker → Subscriber
```

Topic 기반으로 메시지를 라우팅한다.

---

## 🏗️ 시스템 구조

```text
[CLI Publisher] ──(TCP 1883)──▶
                               [Mosquitto Broker]
[CLI Subscriber] ◀─(TCP 1883)──

[Web Client] ◀─(WebSocket 9001)──
```

* 1883: MQTT (TCP)
* 9001: MQTT over WebSocket

---

## ⚙️ 환경 구성

### 1. MQTT Broker

* Mosquitto 설치
* Windows Service로 실행

### 2. 설정 파일 (`mosquitto.conf`)

```conf
listener 1883
protocol mqtt

listener 9001
protocol websockets

allow_anonymous true
```

---

## 🔧 실행 방법

### 1. Broker 실행

```bash
net start mosquitto
```

---

### 2. Subscriber 실행

```bash
mosquitto_sub -h localhost -t chanwoo/test
```

---

### 3. Publisher 실행

```bash
mosquitto_pub -h localhost -t chanwoo/test -m "hello mqtt"
```

---

## 🌐 Web Client (WebSocket)

### HTML 코드

```html
<script src="mqtt.min.js"></script>

<script>
const client = mqtt.connect('ws://localhost:9001');

client.on('connect', () => {
    console.log('Connected');
    client.subscribe('chanwoo/test');
});

client.on('message', (topic, message) => {
    console.log(message.toString());
});
</script>
```

---

## 📊 주요 실습 내용

### 1. 기본 Publish / Subscribe

* CLI 기반 메시지 송수신 확인

### 2. Retain Message

* 마지막 메시지 상태 유지

### 3. QoS (Quality of Service)

* 메시지 전달 신뢰도 설정

### 4. WebSocket 연동

* 브라우저에서 MQTT 메시지 수신

---

## 🔍 트러블슈팅

### ❌ Connection Refused

* 원인: 인증 설정 문제
* 해결:

```conf
allow_anonymous true
```

---

### ❌ WebSocket 연결 실패

* 원인: 9001 포트 미설정
* 해결:

```conf
listener 9001
protocol websockets
```

---

### ❌ mosquitto_sub 명령어 인식 불가

* 원인: PATH 미설정
* 해결: 설치 경로에서 실행 또는 환경변수 등록

---

### ❌ JS 라이브러리 로드 실패

* 원인: CDN 접근 실패
* 해결: mqtt.min.js 로컬 다운로드

---

## 🚀 결과

* MQTT Broker 정상 구축
* CLI 기반 Publish / Subscribe 통신 확인
* WebSocket 기반 웹 실시간 메시지 수신 성공

---

## 📌 배운 점

* MQTT는 단순 메시지 전달이 아닌 **Topic 기반 메시지 라우팅 구조**를 가진다
* TCP 기반이지만 WebSocket을 통해 웹과 연동 가능하다
* Broker 설정(listener, 인증)이 전체 시스템 동작에 큰 영향을 준다
* 실시간 시스템에서 상태 유지(Retain)와 신뢰성(QoS)이 핵심 요소이다

---

## 🔮 확장 가능성

* IoT 디바이스 (ESP32, Raspberry Pi) 연동
* 사용자 인증 (ID/PW, ACL)
* TLS 기반 보안 통신
* 실시간 데이터 시각화 (Dashboard)

---
