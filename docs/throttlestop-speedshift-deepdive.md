# 📖 ThrottleStop & Intel SpeedShift EPP Deep-Dive 가이드

본 문서는 인텔 CPU의 전력/클럭 제어 아키텍처인 **SpeedShift (HWP)**, **EPP (Energy Performance Preference)** 레지스터, 그리고 **TPL (Turbo Power Limits)**의 작동 원리를 다룹니다.

---

## 1. SpeedStep vs SpeedShift (HWP)
* **SpeedStep (EIST / 구형)**: OS 소프트웨어가 20~100ms 간격으로 P-State를 전환 명령.
* **SpeedShift (HWP / 신형)**: CPU 칩셋 하드웨어가 1ms 단위로 전압 및 클럭을 자율 최적화.

---

## 2. EPP (Energy Performance Preference) 레지스터 구조

Intel `MSR 0x774` 레지스터는 0부터 255까지 8비트 수치를 사용합니다.

* **`0` (`0x00`)**: 0% 절전 (100% 성능) - 무조건 풀터보 클럭 유지
* **`80` (`0x50`)**: 약 30% 절전 - 게이밍 민첩 부스트
* **`128` (`0x80`)**: 50% 절전 - 인텔 표준 디폴트 밸런스
* **`160` (`0xA0`)**: **62.5% 절전 (추천★)** - C0% 부하량에 반응하여 클럭을 비례적으로 아껴 씀
* **`180` (`0xB4`)**: 70% 절전 - 저발열/무소음 우선
* **`255` (`0xFF`)**: 100% 절전 - 부스트 억제

---

## 3. TPL (Turbo Power Limits) 구조

* **`PL1 (Long Power)`**: 지속 유지 가능 전력 한도
* **`PL2 (Short Power)`**: 순간 허용 최대 전력 한도
* **`Tau (Turbo Time Limit)`**: PL2를 유지하는 유통기한 시간 (기본 32초)
* **`Clamp`**: 한도 초과 시 절대로 전력을 더 쓰지 못하게 물리 강제 봉인하는 기능

---

## 4. 윈도우 OS 스케줄러와 Race-to-Sleep
윈도우 스케줄러는 미세한 작업도 빠르게 처리하고 C-State(휴식)로 가기 위해 클럭을 튀어 오르게 합니다. `EPP = 160`은 이러한 스케줄러의 미세 자극을 완충하여 쾌적한 발열 케어를 가능하게 합니다.
