# 📱 [Case #02] 2019 맥북 (Intel Core i5) 용광로 발열 & 비행기 이륙 소음 케어

> **환경**: macOS (Catalina ~ Sonoma/Sequoia) | 2019 맥북 프로/에어 13" (Intel i5)  
> **핵심 성과**: 온도가 **95~100°C ➡️ 48~55°C**로 40°C 이상 감축 / 비행기 이륙 팬 소음 완전 차단 (0.9W~2W급 무소음)

---

## 1. 📌 케이스 개요 및 증상

* **대상 기기**: 2019 맥북 프로 13인치 / 2019 맥북 에어 13인치
* **CPU**: Intel Core i5 (8세대 Coffee Lake / Amber Lake)
* **고유 증상 (인텔 맥 특유의 용광로)**:
  * 웹 브라우저 탭 몇 개만 켜거나 줌(Zoom) 미팅, 유튜브 4K 시청 시 **온도가 95°C~100°C**까지 치솟음.
  * 쿨링 팬이 6,000 RPM(최대 속도)으로 격렬하게 돌며 **비행기 이륙 소음** 발생.
  * 무릎 위에 올려놓기 힘들 정도로 하판이 뜨거워짐.

---

## 2. 🔍 윈도우(ThrottleStop) vs 맥(macOS) 1:1 전력/클럭 운영 매핑표

> 💡 **전역 전력 천장(Global Power Ceiling) 원리**:  
> PL1/PL2 전력 제한(18~19W)은 하드웨어 전역 레지스터(MSR)에 상주하여 **AC/DC 모드 공통 최상단 안전 천장** 역할을 수행합니다. 배터리 모드에서는 CPU가 1~2W만 소모하므로 18W 천장 한참 아래에서 동작하여 아무런 방해 없이 완벽하게 상호 호환됩니다.

| 튜닝 목표 | Windows 11 (ThrottleStop 구현) | macOS (Intel Mac 구현 방법) |
| :---: | :---: | :---: |
| **하드웨어 전역 전력 천장**<br>*(Global Power Ceiling)* | **TPL 탭 공통 적용**<br>• PL1 `19W` / PL2 `22W` + Clamp<br>*(프로필 변경 시에도 19W 천장 유지)* | **VoltageShift MSR 전력 봉인**<br>• `./voltageshift power 18 22 32`<br>*(PL1 18W 전역 안전 천장 주입)* |
| **AC 충전 / eGPU 모드**<br>*(스윗스팟 성능 & 70°C 미만)* | **Profile 1 적용**<br>• SpeedShift EPP `160`<br>• Turbo Ratio `31` (3.1GHz 고정) | **VoltageShift EPP + Turbo 상한**<br>• `./voltageshift msr 0x774 0xA0` (EPP 160)<br>• `./voltageshift turbo 31` (3.1GHz 상한) |
| **DC 배터리 모드**<br>*(0.9W / 무소음 / 배터리 극대화)* | **Profile 2 적용**<br>• **`Disable Turbo`** (터보 OFF)<br>• SpeedShift EPP **`192`**<br>• 전력 **`0.9W`** / 온도 **`38~40°C`** | **VoltageShift EPP + Turbo Switcher**<br>• **`Turbo Boost Switcher`** ➡️ Disable<br>• `./voltageshift msr 0x774 0xC0` (EPP 192)<br>• 전력 **`1~2W`** / 온도 **`48~55°C`** 무소음 |
| **전원 자동 감지 스위칭** | Options `AC Profile 1` / `Battery Profile 2` | `VoltageShift launchd` 데몬 / macOS 전원 자동화 |
| **팬 속도 커스텀 선제 제어** | 삼성 세팅 `Fn + F11` (저소음 모드) | `Macs Fan Control` (CPU PECI 센서 55°C~82°C 커스텀 곡선) |

---

## 3. 🛠️ 소프트웨어 & 하드웨어 단계별 솔루션

### (1) [소프트웨어 1단계] Macs Fan Control 센서 곡선 세팅 (무료)
Apple 기본 뒷북 팬 제어를 미리 선제적으로 돌도록 커스텀 센서 곡선을 부여합니다.

* **프로그램**: `Macs Fan Control` (무료 다운로드)
* **센서 기준**: **`CPU PECI`** 또는 **`CPU Core Average`**
* **팬 시작 온도**: **`55°C`** (55도부터 팬이 조용하게 돌기 시작)
* **최대 속도 온도**: **`82°C`** (82도가 되면 미리 최대 RPM으로 소음 없이 열 방출)

### (2) [소프트웨어 2단계] Turbo Boost Switcher (터보부스트 제어)
사무/문서작업/영상 시청 시 클릭 한 번으로 인텔 터보 부스트를 끌 수 있는 필수 유틸리티입니다.

* **프로그램**: `Turbo Boost Switcher for Mac`
* **동작**: 메뉴바에서 **`Disable Turbo Boost`** 클릭
* **효과**: 
  * i5 터보 부스트가 꺼지면서 기본 클럭(1.4~2.4GHz)으로 작동.
  * **온도가 98°C ➡️ 55°C로 무려 40°C 감축!**
  * 팬 소음이 완전히 사라져 도서관/카페 사용 가능.

### (3) [고급 정밀 튜닝 3단계] VoltageShift MSR 전력/클럭/EPP 세부 제어 (Terminal)
윈도우의 ThrottleStop처럼 **PL1(지속 전력), PL2(순간 전력), 최대 클럭, EPP 레지스터, 언더볼팅**을 정밀하게 제어합니다.

> 💡 **왜 Volta가 아닌 VoltageShift인가?**  
> 과거 상용 GUI 앱인 `Volta`는 최신 macOS 커널 보안 패치 이후 업데이트가 멈춰 실행 오류가 발생합니다. 현재 최신 macOS(Monterey~Sonoma)까지 지속 호환되는 툴은 오픈소스인 **`VoltageShift`**가 유일합니다.

* **전역 전력 천장 봉인 (PL1 / PL2 전력 제한)**:
  * `./voltageshift power 18 22 32` (지속 18W / 순간 22W / 유지시간 32초 전역 안전 천장)
* **SpeedShift EPP 수치 강제 주입 (`MSR 0x774`)**:
  * AC 충전 모드 (EPP 160 / `0xA0`): `./voltageshift msr 0x774 0xA0`
  * DC 배터리 모드 (EPP 192 / `0xC0`): `./voltageshift msr 0x774 0xC0`
* **최대 부스트 클럭 제한 (FIVR 모드)**:
  * `./voltageshift turbo 31` (부스트 상한선을 3.1GHz로 고정)
* **CPU Core & Cache 언더볼팅**:
  * `./voltageshift offset -80 -50 -80`

### (4) [하드웨어 개조 선택] 서멀패드 하판 개조 (Thermal Pad Mod)
알루미늄 맥북 하판 전체를 거대한 히트싱크로 활용하는 커뮤니티 전설의 개조법입니다.

* **준비물**: 1.5mm ~ 2.0mm 두께의 고성능 서멀패드 (예: Thermalright Odyssey / Gelid)
* **방법**: 맥북 하판을 열고 CPU 히트파이프 위에 서멀패드를 부착하여 알루미늄 하판과 직접 밀착시킴.
* **효과**: 지속 연산 시 온도가 8~12°C 추가 하강.

---

## 🚀 [오픈소스 비전] VoltageShift GUI Wrapper 프로젝트 기획

현재 최신 macOS에서 오픈소스 `VoltageShift` CLI를 터미널 명령어로 치는 불편함을 해소하기 위해, **Swift / SwiftUI 기반 오픈소스 맥 전용 GUI 래퍼(GUI Wrapper) 앱**을 구축하는 비전 프로젝트입니다.

### 💡 주요 기능 기획
1. **메뉴바 콤팩트 제어**: 윈도우 ThrottleStop처럼 전력 모드 원클릭 스위칭
2. **슬라이더 기반 전력 제어**: PL1(18W), PL2(22W) 슬라이더 바 조작
3. **EPP 수치 지정**: `160` (AC) / `192` (DC) 원클릭 토글
4. **클럭 상한선 지정**: `3.1GHz` / `2.8GHz` 원클릭 프리셋 버튼
5. **실시간 온습도 & RPM 모니터링 그래프**

---

## 📊 최종 개선 성과 비교

| 세팅 상태 | 평균 온도 | 팬 소음 수준 | 추천 활용 환경 |
| :---: | :---: | :---: | :--- |
| **순정 상태** | **95°C ~ 100°C** | 비행기 이륙 소음 (6000 RPM) | ❌ 쓰로틀링 및 고열 심함 |
| **Macs Fan Control 커스텀** | **75°C ~ 82°C** | 중저음 조용한 바람 소리 | 🟢 게임 / 작업 시 |
| **VoltageShift MSR 봉인 (18W+EPP 160)** | **65°C ~ 72°C** | 아주 미세한 바람 소리 | 🏆 **고부하 작업 / 렌더링** |
| **Turbo Boost Off + EPP 192 모드** | **48°C ~ 55°C** | **팬 소음 0dB (완전 무소음)** | 🏆 **사무, 웹서핑, 도서관, 카페** |

---
*가이드 최종 업데이트: 2026-08-12*
