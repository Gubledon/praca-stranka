# Diplomová práca, Návrh a implementácia firmvéru pre IoT zariadenie Hardwario GLIDER

> Centrálny prehľad diplomového projektu. Obsahuje PDF, aktuálne verzie materiálov, krátky popis, ciele a kalendár splnených a plánovaných úloh.

**Autor:** Nikolas Gubančok
**Pracovisko:** FMFI UK, Bratislava
**Akademický rok:** 2025 / 2026
**Školiteľ:** RNDr. Jozef Šiška

---

## Krátky popis projektu

Cieľom práce je navrhnúť a implementovať firmvér pre priemyselné IoT zariadenie Hardwario **GLIDER** postavené nad čipom Nordic **nRF9151 SiP** (Arm Cortex-M33, integrovaný LTE-M / NB-IoT modem). Firmvér beží v non-secure svete oddelenom od Trusted Firmware-M (TF-M) hardvérovou izoláciou TrustZone-M, komunikuje s GLIDER cloudom cez MQTT v protokole JSON-RPC 2.0 a ovláda fyzické periférie: štyri analógové vstupy, dva reléové výstupy a desať teplotných kanálov.

Práca okrem samotnej implementácie analyzuje **bezpečnostné požiadavky IoT firmvéru** a porovnáva aktuálny stav s odporúčaniami z troch recenzovaných vedeckých prác.

---

## Aktuálne verzie materiálov

| Dokument | Súbor | Popis |
|---|---|---|
| **Prehľad projektu (PDF)** | [`thesis_project.pdf`](./thesis_project.pdf) | Krátky popis, ciele, kalendár úloh |
| Prezentácia (PPTX) | [`glider_thesis.pptx`](./glider_thesis.pptx) | 13 slajdov v slovenčine |

---

## Ciele práce

1. **Navrhnúť firmvérovú architektúru** nad nRF Connect SDK pre nRF9151 s hardvérovou izoláciou TF-M (Secure / Non-Secure).
2. **Implementovať LTE-M komunikáciu** s MQTT brokerom Eclipse Mosquitto a JSON-RPC 2.0 protokolom (snake_case metódy).
3. **Riadiť periférie a konfiguráciu**, analógové vstupy, reléové výstupy, 1-Wire teplomery, perzistencia nastavení v NVS a synchronizácia s cloudom.
4. **Analyzovať bezpečnostné požiadavky**, identifikovať hrozby, porovnať aktuálny stav s literatúrou (Sicari 2015, Dinculeană & Cheng 2019, Pinto & Santos 2019) a navrhnúť kroky k zabezpečeniu (MQTTS, X.509, signed FOTA, PSA attestation).

---

## Kalendár úloh

### Splnené úlohy

- [x] Analýza zadania a hardvérovej platformy (nRF9151 SiP)
- [x] Návrh modulárnej firmvérovej architektúry (3 vlákna + system workqueue)
- [x] Skeleton projektu v nRF Connect SDK, build pre `nrf9151dk/nrf9151/ns` (TF-M)
- [x] Perzistencia nastavení cez Zephyr NVS settings subsystem
- [x] Shell rozhranie pre debug a manuálne testovanie
- [x] LTE-M pripojenie cez Connection Manager (asynchrónne, non-blocking)
- [x] Synchronizácia času z modemu (UTC)
- [x] MQTT klient nad `net/mqtt.h` s runtime-configurable parametrami z NVS
- [x] Auto-reconnect s exponenciálnym backoff (1 s → 5 min)
- [x] Last Will and Testament (retained offline status)
- [x] JSON-RPC 2.0 builder a parser (cJSON)
- [x] Publikovanie `device_status` (retained) s telemetriou z modemu
- [x] Periodický `device_report` (configurable interval)
- [x] GPIO výstupy out1, out2 + handler pre `set_output` RPC
- [x] Prijímanie `update_settings` z cloudu + persistence do NVS
- [x] SAADC vstupy in1 až in4 (12-bit ADC v `device_report`)
- [x] Štúdium 3 vedeckých článkov o bezpečnosti IoT firmvéru
- [x] Návrh prezentácie a sprievodného textu pre obhajobu

### Plánované úlohy

- [ ] Prahové alerting vstupov (`device_alert` pri prekročení limitu)
- [ ] 1-Wire teplomery DS18B20 (t1 až t10)
- [ ] Automation engine (pravidlá event → action)
- [ ] Kalendárne plánovanie akcií (RRule)
- [ ] Offline message queue (bufferovanie počas straty pripojenia)
- [ ] Watchdog a graceful shutdown
- [ ] FOTA download a MCUboot apply
- [ ] **Bezpečnosť:** Prechod na MQTTS (TLS 1.2, port 8883)
- [ ] **Bezpečnosť:** X.509 mutual authentication cez `modem_key_mgmt`
- [ ] **Bezpečnosť:** ECDSA-P256 podpis FOTA obrazov cez PSA Crypto / TF-M
- [ ] **Bezpečnosť:** Per-device topic ACL na Mosquitto brokeri
- [ ] **Bezpečnosť:** PSA Initial Attestation Token pri registrácii zariadenia
- [ ] Finalizácia textu diplomovej práce
- [ ] Príprava obhajoby

---

## Hardvér a technológie

- **SoC:** Nordic nRF9151 SiP (Arm Cortex-M33 @ 64 MHz, TrustZone-M)
- **SDK:** nRF Connect SDK v2.9 + Zephyr RTOS
- **Komunikácia:** LTE-M / NB-IoT → MQTT (Eclipse Mosquitto) → JSON-RPC 2.0
- **Bezpečnosť:** Trusted Firmware-M, MCUboot (plánované), PSA Crypto (plánované)
- **Cloud backend:** Express.js + MongoDB Atlas (mimo rozsahu BP)

---

## Citované práce

1. **Sicari, S., Rizzardi, A., Grieco, L. A., & Coen-Porisini, A.** (2015). Security, privacy and trust in Internet of Things: The road ahead. *Computer Networks*, 76, 146-164. https://doi.org/10.1016/j.comnet.2014.11.008
2. **Dinculeană, D., & Cheng, X.** (2019). Vulnerabilities and Limitations of MQTT Protocol Used between IoT Devices. *Applied Sciences*, 9(5), 848. https://doi.org/10.3390/app9050848
3. **Pinto, S., & Santos, N.** (2019). Demystifying Arm TrustZone: A Comprehensive Survey. *ACM Computing Surveys*, 51(6), Article 130. https://doi.org/10.1145/3291047

---

*Repozitár obsahuje len materiály k diplomovej práci. Samotný zdrojový kód firmvéru je hostovaný v internom GitLabe Hardwario.*
