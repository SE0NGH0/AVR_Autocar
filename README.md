# 🚗 Auto Car Project  
**with ATmega128A**  

---

## 📌 프로젝트 개요

- AVR ATmega128A 마이크로컨트롤러 기반 임베디드 자율주행 자동차 시스템
- **Bluetooth**를 이용한 수동 주행 구현
- **Ultrasonic Sensor**(초음파 센서)를 기반으로 한 간단한 자율 주행 로직 구현
- FND, I2C LCD, LED, BUZZER 등 시각/청각적 인터페이스 포함

---

## 🧩 주요 하드웨어 구성

| 부품명             | 용도 |
|--------------------|------|
| ATmega128A         | MCU 메인 제어 |
| L298N              | DC 모터 드라이버 |
| DC 모터            | 바퀴 구동 |
| 초음파 센서 (3개)  | 거리 측정 (전방/좌/우) |
| Bluetooth 모듈     | 수동 주행용 무선 제어 |
| FND                | 방향 애니메이션 출력 |
| I2C LCD            | 방향 정보 출력 |
| LED                | 자율/수동 상태 표시 |
| BUZZER             | 경고음 및 멜로디 출력 |
| 버튼               | 자율주행 모드 전환 트리거 |

---

## 🧠 동작 모드 & FSM

### 수동 모드
- 기본 전원 ON 시 블루투스 수동 주행 모드 활성화
- 블루투스 입력 키:
  - `f`: 전진
  - `b`: 후진
  - `l`: 좌회전
  - `r`: 우회전
  - `s`: 정지

### 자율주행 모드
- 버튼을 통해 자율주행 모드 진입
- **초음파 센서 데이터 기반 주행 로직**:
  - 전방 20cm 이상, 좌/우 20cm 이상 → 전진
  - 좌/우 차이 10cm 이하 → 전진
  - 좌 > 우 → 좌회전
  - 우 > 좌 → 우회전
  - 전방 20cm 이하 → 후진 또는 회피

---

## 🛠 기능 요약

| 구성 요소 | 설명 |
|-----------|------|
| **초음파 센서** | 0.2초마다 거리 측정 → DC 모터 방향 제어 |
| **BUZZER** | 멜로디 출력 (구급차, 소방차, 어린이차 등) |
| **FND** | 주행 방향에 따른 애니메이션 출력 |
| **I2C LCD** | 방향 텍스트 출력 (FORWARD, BACKWARD 등) |
| **LED** | 자율주행 시 점등, 수동 주행 시 소등 |

---

## 💻 주요 코드 설명

### 모터 제어

- 좌/우 바퀴 IN 핀: `PORTF 0~3`
- PWM: 16bit 타이머, 분주비 64 사용

### 초음파 센서 제어

- 인터럽트 사용 (`INT4`, `INT5`, `INT6`)
- 거리 측정 → TCNT2 기반 거리 계산 후 조건 처리
- 분주비: 128

### 자율 주행 조건 처리

```c
if (front > 20) {
    if (left > 20 && right > 20) go_forward();
    else if (abs(left - right) < 10) go_forward();
    else if (left >= right) turn_left();
    else turn_right();
} else {
    if (left <= 20 && right <= 20) go_backward();
    else if (left > right) turn_left();
    else turn_right();
}
```

### BUZZER 제어

- Timer3 사용, 음계 기반 멜로디 출력

---

## 📽️ 시연 영상

- 🔗 [자율 주행 시연](https://youtube.com/shorts/6Wx-I0pbpkk)  
- 🔗 [FND / BUZZER / LCD 동작](https://youtube.com/shorts/5h3yXWO0DD4)  
- 🔗 [추가 LCD 시연 영상](https://youtube.com/shorts/7KOlTIEEiLM)  

---

## ⚠️ 문제점 및 개선 방향

### 문제점
- 초음파 센서의 불안정한 거리 측정 (오버플로우 발생)
- 주행 중 센서 신호 누락으로 인한 순간 정지 발생
- 배터리 용량 부족으로 테스트 지속 어려움

### 개선점
- 초음파 센서의 위치 다양화 (후면/측면 추가)
- 오버플로우 감지 및 보정 로직 추가
- 테스트 시에는 전원공급기를 통한 안정적 전원 확보

---
