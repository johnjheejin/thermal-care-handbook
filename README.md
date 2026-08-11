# 🌡️ Universal Computer Thermal & Performance Care Handbook
> **윈도우(Windows), 맥(macOS), 노트북, eGPU, 데스크톱 통합 발열 케어 & 성능 최적화 종합 가이드북**

![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)
![Platform: Windows | macOS](https://img.shields.io/badge/Platform-Windows%20%7C%20macOS-informational)
![Focus: Thermals | Throttling | SweetSpot](https://img.shields.io/badge/Focus-Thermals%20%7C%20Throttling%20%7C%20SweetSpot-brightgreen)

---

## 📌 소개 (Introduction)

**"왜 내 컴퓨터는 조금만 일해도 팬이 비명을 지르고 온도가 90°C까지 치솟을까?"**

본 핸드북은 초경량 노트북, eGPU 연결 환경, 맥북, 고성능 데스크톱 등 **다양한 실전 하드웨어 케이스(Case Studies)에서 발생하는 발열, 팬 소음, 쓰로틀링(Throttling) 현상을 잡고 최적의 지속 성능(Sweet Spot)을 찾는 범용 가이드북**입니다.

새로운 기기 및 환경(Windows, macOS, eGPU, 서멀 튜닝 등)을 만날 때마다 실측 검증된 케이스 스터디가 지속적으로 추가 업데이트됩니다.

---

## 🎯 핵심 튜닝 철학 (Core Philosophy)

1. **무조건적인 클럭 낮추기 지양**: 단순 하향 평준화가 아닌, 전력 소비와 성능 비중의 **스윗스팟(Sweet Spot)**을 발굴합니다.
2. **실측 데이터 기반 진단**: 온도, 전력(W), 코어 가동률(C0%), 클럭(FID)을 기반으로 한 객관적 진단을 수행합니다.
3. **OS 및 폼팩터 맞춤 대응**: 윈도우 스케줄러(Race-to-Sleep), 인텔 SpeedShift, AMD P-State, 맥북 팬 제어 특성에 맞춘 커스텀 대응.

---

## 📚 검증된 실전 케이스 스터디 (Case Studies)

| 케이스 ID | OS / 환경 | 대상 사양 | 핵심 해결 성과 | 세부 가이드 링크 |
| :---: | :---: | :--- | :--- | :---: |
| **#CASE-01** | Windows 11<br>*(eGPU)* | **삼성 노트북9 Always 15"**<br>(i7-8550U + GTX 1080 eGPU) | **온도 95°C ➡️ 53°C~65°C (-30°C 감축)**<br>SpeedShift EPP 160 + PL1 19W 봉인 | [📖 케이스 보기](cases/nt900x5t-i7-8550u-egpu.md) |
| **#CASE-02** | macOS | **2019 맥북 프로/에어 13"**<br>(Intel Core i5) | **온도 98°C ➡️ 52°C~60°C (무소음 달성)**<br>Macs Fan Control + Turbo Boost Off | [📖 케이스 보기](cases/macbook-pro-2019-i5.md) |
| **#CASE-03** | Windows | *(Coming Soon - Desktop / Gaming Laptop cases)* | 확장 예정 | - |

---

## 📖 주제별 가이드북 (Deep-Dive Documents)

* **[Intel SpeedShift & EPP 레지스터 완벽 해설](docs/throttlestop-speedshift-deepdive.md)**  
  * EPP 수치(`0`~`255`)의 백분율 의미, 16진수 레지스터, C0% 비례 클럭 조절 메커니즘
* **[TPL (Turbo Power Limits) 전력 제한 가이드](docs/throttlestop-speedshift-deepdive.md#3-tpl-turbo-power-limits-%EC%A0%84%EB%A0%A5-%EC%A0%9C%EC%96%B4-%EB%AA%85%EC%84%B8)**  
  * PL1(지속 전력), PL2(순간 전력), Tau(32초 시한장치) 및 Clamp 봉인 기법
* **[윈도우 OS 스케줄러와 Race-to-Sleep 특성](docs/throttlestop-speedshift-deepdive.md#6-%EC%9E%90%EC%A3%BC-%EB%AC%BB%EB%8A%94-%EC%A7%88%EB%AC%B8-faq)**  
  * 미세 부하에서 클럭이 튀는 이유와 EPP 필터링 원리

---

## 🤝 기여 및 업데이트 (Contributing)

본 핸드북은 새로운 컴퓨터 환경을 세팅하고 발열을 잡을 때마다 지속적으로 업데이트됩니다.  
이슈(Issue)나 PR(Pull Request)을 통한 새로운 케이스 공유 및 토론은 언제나 환영합니다!

---
*Maintained by [@johnjheejin](https://github.com/johnjheejin)*
