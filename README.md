# 차량용 통신시스템(SOME/IP, DoIP)
🚗**Original Repository**: [autonomous-vehicle-Communication](https://github.com/team-2niverse/autonomous-vehicle-Communication.git) 

<br>

## 📝 프로젝트 개요
* **수행 기간:** 2025. 09. 15. ~ 2025. 10. 21. (현대오토에버, 현대엔지비, 한국전파진흥협회) 
* **프로젝트 목표:** [앞서 개발한 RC카 시스템](https://github.com/minhee-123/project-automotive-embedded.git)에 이더넷 기반의 차량 서비스 지향 통신(SOME/IP), 원격 업데이트(OTA) 및 진단 통신(DoIP) 시스템 구현
* **주요 내용:**
  * **SOME/IP**: 서비스 지향 미들웨어 기반의 차량 내 데이터 통신 시스템 구축
  * **OTA(Over-The-Air)**: 무선 통신을 통한 원격 펌웨어 업데이트 기능 구현
  * **DoIP/UDS**: ISO13400 및 ISO14229 표준을 준수하는 차량 진단 시스템 개발
<br>

---

## 👤 나의 역할 및 수행 업무
* **담당 역할:** ISO13400(DoIP) 및 ISO14229(UDS) 표준 기반의 **진단 통신 시스템 설계 및 구현**
* **수행 업무:**
    * DoIP 및 UDS 프로토콜 표준 기반 진단 메시지 설계 (7개 서비스, 19개 기능)
    * 진단기(PC) 및 게이트웨이(RPI) 프로그램 개발 (TCP/IP Socket, 멀티스레딩 활용)
    * Wireshark 패킷 분석을 통한 통신 프로토콜 검증 및 응답(긍정/부정) 테스트
    * Confluence를 활용한 V-Model 단계별 산출물 관리
<br>

---

## 🛠️ 사용 기술 및 환경

| 분류 | 상세 내용 |
| :--- | :--- |
| **Languages** | C, C++, Python |
| **MCU & SBC** | Infineon TC375, Raspberry Pi 4 |
| **Protocols & Communication** | DoIP, UDS, SOME/IP, TCP/IP, Ethernet, Wi-Fi |
| **Tools** | Visual Studio Code, AURIX Development Studio, UDE Visual Platform, Wireshark, Git, Confluence |
<br>

---

## ⚙️ 시스템 아키텍처
**System Architecture**<br>
<img width="647" height="483" alt="image" src="https://github.com/user-attachments/assets/0f8929ef-c3c9-4b9c-8395-27b5961f2779" />
<br>

**HW Architecture**<br>
<img width="717" height="380" alt="image" src="https://github.com/user-attachments/assets/c20645d4-53f8-4bd2-b38d-739e55c1205f" />
<br>

**OTA/SOME IP Sequence Diagram**<br>
* OTA Sequence Diagram
* SOME/IP Sequence Diagram
<img width="432" height="363" alt="image" src="https://github.com/user-attachments/assets/f964e434-2326-4e02-b1a7-011c12abb0a4" />
<img width="321" height="381" alt="image" src="https://github.com/user-attachments/assets/9ee50d53-234a-465c-9807-b6a3efe62527" /><br>
<br>

**DoIP Sequence Diagram**<br>
* **진단기-상위제어기(게이트웨이)**
  * 진단기(PC): 클라이언트
  * 게이트웨이(RP4): 서버
  * 게이트웨이에서 진단기의 요청 수신 및 UDS 응답 메시지를 진단기로 송신
* **상위제어기(게이트웨이)-하위제어기(ECU)**
  * 게이트웨이(RP4): 클라이언트
  * ECU(TC375): 서버
  * 게이트웨이에서 UDS 메시지를 ECU로 송신 및 응답 메시지를 수신
<img width="561" height="415" alt="image" src="https://github.com/user-attachments/assets/54c3a4fe-bd35-4cdc-ab0e-4eb582da10b9" />
<br>

**DoIP SW Flowchart**<br>
<img width="692" height="465" alt="image" src="https://github.com/user-attachments/assets/5281b7fb-743b-451a-ad68-98c194e6ab03" />



<br>

---

## 🎬 주요 기능 및 결과물
**DoIP 진단통신 데모 영상**
* 총 7개의 SID(19개의 세부 기능) 시연
* 데모 영상 및 데모 영상 속 PC 화면 영상

[![DoIP 진단통신 데모 영상](https://img.youtube.com/vi/lH8hOmgvuVs/0.jpg)](https://youtu.be/lH8hOmgvuVs)
[![DoIP 진단통신 데모 영상(PC 화면)](https://img.youtube.com/vi/n2w35QZUq9Q/0.jpg)](https://youtu.be/n2w35QZUq9Q)

**SOC/OTA 데모 영상**
* SOC 기능 확인 및 기능 업데이트(OTA) 시연

[![SOC/OTA 데모 영상](https://img.youtube.com/vi/iJHa55MYXpQ/0.jpg)](https://youtu.be/iJHa55MYXpQ)

<br>

---

## 🔍 트러블슈팅 및 회고

#### 📌 Case #1. 공유 소켓 자원 접근에 따른 경쟁 상태(Race Condition) 해결
* **문제 상황:** Extended Session 유지 중 사용자의 진단 명령 전송과 백그라운드의 Tester Present 메시지 전송이 동일한 TCP/IP 소켓을 공유하며 데이터 충돌 발생
* **원인 분석:** 메인 스레드와 2초 주기의 백그라운드 스레드가 동기화 없이 소켓 자원에 동시 접근하여 경쟁 상태 발생
* **해결 방법:**
  * **Mutex Lock 도입**: 소켓 자원 접근 전 Lock을 획득하게 하여 스레드 간 상호 배제 보장
  * **단일 명령 처리 보장**: 메인 스레드는 진단 메시지 전송 후 응답 수신까지 완료해야 Lock을 반환하도록 설계하여 단일 명령 처리 보장
  * **타임아웃 설정**: 소켓에 5초 타임아웃을 설정하여 응답 미수신 시 블로킹 방지 및 시스템 안정성 강화
<br>
