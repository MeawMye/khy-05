## 👋 About Me

안녕하세요. **Embedded / SoC Software 분야를 학습하고 있는 엔지니어 김하영**입니다.

전기전자공학과 소프트웨어공학을 전공했으며, 임베디드 시스템을 직접 구현하면서 하드웨어와 소프트웨어가 연결되는 과정에 관심을 가져왔습니다.

현재는 **팹리스 기업 연계 일경험 프로그램**에 참여하여 SBC·SoM 기반 임베디드 프로젝트를 수행하며 실무 역량을 쌓고 있습니다.

### 🔎 Interests

Embedded Systems, Firmware & Device Drivers, BSP, Linux Kernel, RTOS, Hardware–Software Interface

### 🎓 Training

* **팹리스 기업 연계 일경험 프로그램** `2026.08 – 2026.09`

### 🎯 Goal

하드웨어의 동작을 이해하고 이를 소프트웨어로 제어할 수 있는 **Embedded / SoC Software Engineer**로 성장하는 것을 목표로 하고 있습니다.

단순한 응용 프로그램 구현을 넘어 **Firmware, Device Driver, BSP, Linux Kernel 등 하드웨어와 가까운 소프트웨어 계층**을 이해하고 개발할 수 있는 엔지니어가 되고자 합니다.

## 🛠 Tech Stack

### Languages

![C](https://img.shields.io/badge/C-A8B9CC?style=flat-square\&logo=c\&logoColor=white)
![C++](https://img.shields.io/badge/C++-00599C?style=flat-square\&logo=cplusplus\&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square\&logo=python\&logoColor=white)

### Embedded

![STM32](https://img.shields.io/badge/STM32-03234B?style=flat-square\&logo=stmicroelectronics\&logoColor=white)
![STM32CubeIDE](https://img.shields.io/badge/STM32CubeIDE-03234B?style=flat-square\&logo=stmicroelectronics\&logoColor=white)
![STM32CubeMX](https://img.shields.io/badge/STM32CubeMX-03234B?style=flat-square\&logo=stmicroelectronics\&logoColor=white)

### Tools & Environment

![Git](https://img.shields.io/badge/Git-F05032?style=flat-square\&logo=git\&logoColor=white)
![VS Code](https://img.shields.io/badge/VS_Code-007ACC?style=flat-square\&logo=visualstudiocode\&logoColor=white)
![Visual Studio](https://img.shields.io/badge/Visual_Studio-5C2D91?style=flat-square\&logo=visualstudio\&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=flat-square\&logo=linux\&logoColor=black)
![VirtualBox](https://img.shields.io/badge/VirtualBox-183A61?style=flat-square\&logo=virtualbox\&logoColor=white)

## 💻 Personal Projects

### 1.🖥️ STM32 PC Monitor

**STM32F769I-DISCO와 FreeRTOS 기반 실시간 PC 시스템 모니터링**

PC에서 수집한 시스템 정보를 UART를 통해 STM32로 전송하고, 수신 데이터를 처리하여 LCD에 실시간으로 시각화하는 시스템을 구현했습니다.

**Architecture**

`PC (Python) → UART → STM32 → LCD`

**Implementation**

* Python `psutil`을 이용해 CPU / RAM 사용률 및 Network Traffic 데이터 수집
* `$CPU:35,RAM:62,NET:120#` 형식의 Packet Protocol 구성
* UART Interrupt 기반으로 데이터를 수신하고 Packet Parsing 수행
* UART ISR에서 수신된 데이터를 Communication Task로 전달하도록 구성
* FreeRTOS 기반으로 Communication / Data Processing / UI 동작 분리
* Queue / Signal을 활용하여 Task 간 데이터 전달 및 동기화
* System Status를 기반으로 LCD의 CPU / RAM / Network 정보를 실시간 갱신
* History 데이터를 Ring Buffer에 저장하여 시간에 따른 사용량 Graph 구현
* Network Traffic 변화에 따라 Graph 범위가 조정되는 Dynamic Scaling 구현

**Data Flow**

`UART ISR → Comm Task → Parser → System Status → UI Task → LCD`

---

### 2.💾 STM32 USB CDC & Internal Flash

**STM32F429ZI에서 USB CDC 통신 및 Internal Flash 제어 기능 구현**

PC와 STM32 간 USB 통신 경로를 구성하고, MCU 내부 Flash의 Erase / Write / Read 동작을 직접 구현했습니다. 통신과 저장 기능을 각각 모듈화하여 이후 명령 처리 구조로 확장할 수 있도록 구성했습니다.

**Architecture**

`Host ↔ USB CDC ↔ RX Buffer ↔ Application`

`Application ↔ Flash Driver ↔ Internal Flash`

**USB CDC Implementation**

* STM32 USB Device CDC를 이용해 PC와 Virtual COM Port 통신 구현
* USB OUT Endpoint를 통해 수신된 데이터를 별도의 **512-byte Ring Buffer**에 저장
* `cdcAvailable()`, `cdcRead()`, `cdcDataIn()`을 구현하여 Application에서 수신 데이터를 처리할 수 있도록 인터페이스 구성
* RX Buffer의 여유 공간을 확인하고 부족할 경우 `rx_full` flag를 설정하여 Overflow 상태 추적
* `CDC_Transmit_FS()`의 BUSY 상태를 고려하여 Timeout 기반 송신 재시도 로직 구현
* Host의 Baudrate / Parity / Stop Bit / Data Bit 등 Line Coding 정보 관리

**Internal Flash Implementation**

* STM32F429의 Flash Sector 정보를 Table 형태로 구성
* 입력된 Memory Address를 기준으로 Flash **Bank / Sector 판별**
* 지정한 Address Range와 Sector 영역의 중첩 여부를 판단하여 필요한 Sector만 Erase
* Flash Programming 제약을 고려하여 **Halfword(2-byte) 단위 Write 및 Address Alignment 처리**
* Memory-mapped Access 방식으로 지정 영역의 Flash 데이터 Read
* Flash 특성을 고려하여 `Erase → Program → Read` 흐름으로 동작 구성

**Software Structure**

```text
Application
    ↓
BSP / Hardware Interface
    ↓
CDC Driver / Flash Driver
    ↓
STM32 HAL
```


초기화와 Application Logic을 분리하고, CDC 및 Flash 기능을 Hardware Layer에 구성하여 상위 계층에서 사용할 수 있도록 프로젝트 구조를 나누었습니다.
