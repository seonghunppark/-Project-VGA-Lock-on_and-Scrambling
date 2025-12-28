# -Project-LFSR-
영상처리 프로젝트 : SystemVerilog를 이용하여 실시간 표적 추적 및 LFSR 기반 보안 영상 통신 시스템 구축

### 🔹프로젝트 개요

---

- 실시간 영상 데이터 처리와 하드웨어 기반 보안 통신의 메커니즘을 깊이 있게 이해하는 것을 목표로 프로젝트를 수행하였습니다.
- FPGA를 활용해 **실시간 모션 감지 알고리즘**을 구현 및 **LFSR(Linear Feedback Shift Register)** 기반의 스트림 암호화 기술을 접목하여 데이터 전송 보안 하드웨어 설계하였습니다.

### 🔹 프로젝트 목표

---

**What?** 🎯

- **실시간 영상 수집 및 표적 추적 시스템 설계** : OV7670 카메라를 통해 입력되는 영상을 실시간으로 처리하여 동적 표적을 포착하고 디스플레이에 시각화
- **LFSR 기반 하드웨어 암호화/복호화 구현**: 영상 데이터를 픽셀 단위로 실시간 암호화하여 송신하고, 수신단에서 이를 동기화하여 복호화하는 보안 시스템 구축
- **신호 안정성 및 데이터 정밀 제어**: 비동기 신호 처리 과정에서 발생하는 Metastability 문제를 해결하고, 정밀한 타이밍 제어를 통해 신호 무결성 확보
- **시스템 통합 설계**: 모듈별 포트 맵핑 및 데이터 패스 설계를 통해 개별 기능을 하나의 Top Module로 통합

### 🔹 설계 및 구현 환경

---

**설계 (Design)**

- **SCCB Master**: 카메라 레지스터 제어 및 QQVGA 설정
- **Buffer Logic**: Ping-Pong Buffering 구조를 통한 Read/Write 동작 분리 및 실시간성 확보
- **Core Logic**: RGB-to-Grayscale 변환 및 Frame Difference 기반 모션 감지
- **Scrambling/Descrambling**: XOR 연산을 이용한 실시간 암·복호화 모듈

**구현 환경 (Tool Environment)**

- **Language**: SystemVerilog
- **Design Tool**: Vivado
- **Equipment**: Basys3 FPGA Board, OV7670 Camera

### 🔹 담당 역할

---

**[Team Leader]**

- Git/GitHub Development Environment Set Up
- Project Management: Project Milestone 설정, 역할 분담 및 Interface Port 정의

**[Hardware Logic Design]**

- Target Detection Marker Visualization Module Design
- Bullet Hole Effect를 위한 Shooting coordinate Register Array Module Design,

**[Signal Integrity]**

- LFSR Module Design
- 2-Stage Flip-Flop Synchronizer Design 적용 및 CDC 안정성 확보

**[TOP Integration]**

- Camera Controller, Buffer, Core, Scrambler 통합 TOP Module 설계
- Pin Mapping

## 🔹Block Diagram

---

![image.png](attachment:0088c4f2-5b79-46f8-85a0-0e65ac438270:image.png)

![image.png](https://github.com/seonghunppark/-Project-VGA-Lock-on_and-Scrambling/blob/86623a4465f08bbc4905aae2793693d575047b29/Master_VGA_TOPGUN.png))

## 🔹프로젝트 성과

---

- **OV7670 카메라 제어 및 Ping-Pong Buffer 구현**
- **프레임 간 차분 기법을 통해 실시간 동적 표적 추적기 구현**
- **LFSR을 이용해 실시간 영상 암호화 및 복호화 구조 구현**

## 🔹Trouble Shooting 및 배운 점

---

### **1. 암호키 초기화 동기 실패와 신호 무결성(Metastability) 해결**

- **문제상황**: 리셋 버튼 입력 시 암호화/복호화 모듈의 암호키가 동시에 초기화되지 않아 화면이 정상적으로 복구되지 않는 문제 발생
- **문제원인**: 생성된 10ns 폭의 Rising 신호가 PCLK의 상승 엣지와 맞물릴 때 **Metastability(CDC)** 문제가 발생하여 시스템이 불안정해짐을 분석함
- **해결방안**: 2-Stage Flip-Flop Synchronizer를 적용하고, 리셋 신호의 유효 폭을 40ns 이상으로 확장하여 클럭 도메인 간 안정적인 신호 전달을 보장함
- **성과**: 암·복호화 모듈의 암호키 타이밍 불일치 문제를 해결하여 $2^{12}$개의 암호키 조합이 완벽히 동기화되도록 구현함

### **2. 동적 표적 추적 시 잔상(Ghosting) 제거**

- **문제상황**: 움직이는 물체가 화면에서 사라졌음에도 마지막 위치에 'LOCK ON' 마크가 고정되어 남아 있는 현상 발생
- **문제원인**: 물체가 감지되지 않을 때 이전 프레임의 중심 좌표 데이터가 레지스터에 유지되어 발생하는 논리적 오류임을 정의함
- **해결방안**: `motion_valid` 플래그를 추가하여 움직임이 없을 때는 좌표값을 초기화하고, 원본 RGB 영상만 출력되도록 조건문 로직을 수정함
- **성과**: 실시간 환경에서 표적의 존재 여부에 따라 시각화 마커가 유연하게 동작하는 안정적인 추적 알고리즘을 완성함

**3. 하드웨어 설계와 소프트웨어적 협업의 융합**

- **배운 점**: FPGA 설계 시 단순 기능 구현보다 **Timing 분석과 자원 최적화**가 시스템 안정성에 직결된다는 것을 깨달음
- **리더십**: 팀장으로서 Git/GitHub를 도입해 코드 버전 관리를 체계화하고, 모듈 간 인터페이스를 명확히 정의하여 디버깅 효율을 극대화하는 경험을 쌓음

### 멤버

---

- 김승훈(하만세미콘아카데미 2기)
- 이상준(하만세미콘아카데미 2기)
- 조민준(하만세미콘아카데미 2기)
