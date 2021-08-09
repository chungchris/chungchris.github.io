---
title: (Embedded) Firmware Architecture and Design Patterns
categories: software
tags:
  - software architecture
keywords:
  - design patterns
  - firmware design
  - software design
  - embedded firmware
img:
top:
hide:
cover:
coverImg:
author:
password:
toc:
mathjax:
summary: Embedded Firmware Architecture 和相關 Design Patterns
reprintPolicy:
---

現在講到 design patterns 的時候常常是以 web service 為對象，比如在介紹 MVC, MVP, MVVM
但 design patterns 其實包含得更廣泛，小至 process 間怎麼溝通，大至一台 device 或一個 web service 的背後整體架構，都有 design patterns
藉由熟悉一些 design patterns，可以在設計階段依據自己專案的需求和特性，選擇適當的 design patterns 來應用

本篇基本上是以 embedded firmware 的角度出發來做整理
不過因為目前的處理器都很越來越強大，價格越來越便宜，所以現在講 embedded 和以往講 embedded 也不大相同。舊時我們講到 embedded 似乎都要很在意 memory space、power consumption 等問題，並且通常只能處理間單的邏輯。但現在講到 embedded，也有可能對象其實乘載於一個能處理非常複雜邏輯且效能非常好的平台上

避免過於發散，我們先將平台種類及差異做個簡介，有 MPU, MCU, SBC
然後在把 OS 差異做個簡介，分為 `Linux` 和 `RTOS`
當選定了平台和 OS，在接下來開始做韌體/軟體設計，要選擇 design patterns 的時候，在很多方面其實就自然會有了偏好

---

## 硬體平台

平台的差異基本上圍繞 CPU 的能力

### Microprocessor (MPU)

> a.k.a Micro Processing Unit

商業上我們再講 `CPU` 其實可能常常指的也是 `MPU`
可以理解為 `CPU` 比較是一個概念單元，而將 `CPU` 實現在積體電路上，通常就是以 `MPU` 這種表現形式。換句話說，`MPU` 作為一個硬體實體，用來完成課本上定義 `CPU` 應該完成的工作
所以其實 MPU 內包含 ALU (Arithmetic and Logical Unit)、Control unit、Registers，其中還需要整合實現 Bus、Clock Speed、Instruction Set、Cache 等功能，才而成就了一個可與其他硬體模組協作的 `CPU` 單位

### Microcontroller (MCU)

> a.k.a Microcontroller Unit

`MCU` 是一個積體電路 integrated circuit (IC) 裝置，與 `MPU` 在名字上的差別是一個是 Processing 一個是 Controller，所以大概就可以感覺到在功能性上，`MCU` 應該是更著重於用來處理一些運算不複雜，主要僅是進行一些簡單邏輯控制和切換的用途
`MCU` 是一塊完整功能的板子，提供匯流排（peripherals）可以連接其他硬體單元，上面有 CPU、Memory、Timer、I/O port、Serial Ports、Interrupt control 等單位

也就是說，`MPU` 和 `MCU` 其實不是同一個階層互相比較的東西：`MCU` 可以看作是一台微微微電腦，本身可以提供常見的電腦功能；而 `MPU` 是運算功能強大的一塊硬體，往往指的純是單負責邏輯運算的那一塊硬體，他需要被裝在主機板上，才能夠完整一台運算能力強大的電腦

### Single Board Computer (SBC)

既然都已經用到 Computer 這個字了，那 `SBC` 肯定是比 `MCU` 更像一台電腦，他基本上就是一台電腦了啦，`SBC` 和 `MCU` 都算是 SoC (System-on-chip)
你把家裡的桌上型電腦拆開，發現基本上主要就是一塊主機板（可能是技嘉或華碩），其他大東西就是電源供應器和風扇，那塊主機板本身就是 `SBC`，他上面可能整合 Intel Core i5 的 CPU
所以要把 `SBC` 理解為是 `MCU` 的進階版那基本上也八九不離十
比如樹莓派 Raspberry Pi 就是 SBC，但 Arduino 就感覺介於 `SBC` 和 `MCU` 之間，因為他提供的匯流排和 connectivity 比傳統 `MCU` 多，但整體能力上又不像典型 `SBC` 一樣強大。所以 `MCU` 和 `SBC` 本來就是漸進式的概念

電腦時代基本上圍繞 Intel 打轉，所以比如華碩的主機板，整合了 Intel 的處理器，他就變成一台華碩的筆電
如果是不需要像筆電那麼強大的簡單電子裝置，那他不需要用到 Intel 的處理器，也不需要華碩那麼功能完整的主機板，所以就有 SBC 廠商開發各種功能的小整合性板子，提供給不同複雜度的用途
Intel 的處理器是 x86 架構，後來手機時代 ARM 推出了與之不同採用 RISC 指令集的處理器架構，而 `SBC` 搭載的處理器是 Intel 還是 ARM 是都有的，看用途來做選擇就好
如果也不需要 SBC 那麼強，那也有很多 MCU 可以選，各 MCU 也幾乎都搭配自己的 IDE，基本環境都已經準備好

實務上開發階段可以依據產品特性及複雜度選擇使用 MCU 開發板或 SBC 開發板來開發，做到 PoC 階段後，就可以開始找 HW 的人討論產品的 HW 規格，然後進入 EVT (Engineering Verification Test) 階段，根據實際有使用到的開發板上的硬體來規劃 PCBA

---

## 作業系統 (OS)

### RTOS (Real Time Operating System)

RTOS 是以 task priority 為第一守則。如果一個高優先順序的 task 已準備好要運作，那在很短的反應間隔中，它就會從低優先順序的 task 中取得 CPU 的使用權。且一直到工作完成以前，這個 task 都能持續使用處理器資源，除非有另一個更重要的任務出現。這個策略即是 priority-based preemptive scheduling

不只是優先級排程，因為如果只有優先級排程，那如 Linux 也有給 process 定義優先級啊
注意 RTOS 強調反應時間的可預期性，當一個事件抵達，要觸發更高優先級的任務，從事件發生，直到搶佔資源開始執行，這段時間也要受到保證

### Linux

在 Linux 等 GPOS (General Purpose Operating System) 中，scheduler 通常採用「公平策略」（fairness policy）來遞送執行緒到CPU，這樣的策略能讓 throughput 更高，但對於具有高優先需求和時間迫切的執行緒來說，卻無法得到保證。通常也叫做 Time Sharing Operating System

Linux 的架構比 RTOS 複雜，雖然 RTOS 也稱為多工系統，RTOS 的基本執行單位為 task，所有 task 共享位址空間，且基本上是單核心，雖然是 multi-task，但因為不是 regular time sharing，所以甚至不算concurrent，更不可能是 parallel
而 Linux 程式的執行單位為 process，每個 process 都有自己的位址空間，所以 multi-thread 可以靠 time sharing 達成 concurrent 之餘，如果硬體有 multi cores，他也可以做到 parallel
所以可以預期 Linux 要做到 soft 的 real time 也是可以的，改變 kernel 的配置就能達到

---

## 選擇

Linux 和 RTOS 的差異，本質上不是用產品的複雜度來區分
但是，現實中發現，需要使用到 General Purpose Operating System 的，往往是複雜功能的系統，比如有 UI，要同時響應多種需求，系統需要支援各種不同的動作，重視此用者體驗，又各種不同的外部裝置連結
而 RTOS 則更常運用在單調場景，其實這也蠻直覺，當用途一多，每個都高優先級，那排程的完成時間也就無法保證，那他也就不會是 RTOS
所以 RTOS 的源碼常常體積更小，這造成又迴向回來變成如果想節省記憶體資源的小裝置，即使沒有即時響應的需求，但因為 RTOS 體積比較小，所以就採用 RTOS。所以其實現在已經變得每提到 RTOS 很多時候其實是在講 Embedded OS，而不見得真的有 real time 需求，而可能只是他的工作很間單、硬體也很輕量，所以不需要用到完整 feature 的 OS

從 CPU 的角度來看，如果你只需要 8-bit, 16-bit 的 CPU 就能完成你的產品需求，那肯定是找 RTOS / Embedded OS，甚至不需要 OS (bare metal)；如果是 32 或 64-bit，那就不一定了，取決於產品功能的實現需不需要完整 feature 的 OS。Memory 也是一個關鍵，太小的 RAM 也是沒辦法跑 Linux 的

從開發的角度來看，Linux 的資源肯定是最多的，幾乎可以說只有 Business Logic 需要自己寫的。而且擴充性也很好，畢竟他本身就是一個功能完整的 OS。如果是 RTOS，那開發社群就會比較小，很多甚至受限於 BSP，必須索求板子開發商的客戶協助

比如 [Arduino](https://www.arduino.cc/) 就是一個泛用的 MCU 開發板，Arduino UNO 使用 RISC 指令集 8-bit microcontrollers，2KB RAM。Arduino 可以說是沒有 OS (bare metal)，他的 IDE 就是讓你直接用 C 編寫低層指令，當然要裝 OS 也不是不行，只是勢必是去找 RTOS 類型比如 [FreeRTOS](https://www.freertos.org/)

商用比如 *Marvell* 所提供的 MCU 方案 *MW30x*，採用 ARM Cortex-M4。*Marvell* 就提供了 WMSDK (Wireless Module Software Development Kit)，裡面包含了編譯環境、Driver、OS (RTOS)、Framework。聯發科也有 LinkIt 7697，也是相近定位使用 ARM Cortex-M4，352KB RAM，支援 Arduino IDE

比如樹莓派 [Raspberry Pi](https://www.raspberrypi.org/) 就是 SBC，*Raspberry Pi 4* 有 ARM Cortex-A 64-bit 四核心，2GB 以上的 RAM，4k60 的 HDMI。Raspberry Pi 提供自己的 *Raspberry Pi OS*，是基於 *Debian* 的版本，又叫做 *Raspbian*。OS 提供了 GUI 桌面，還預載的開發語言 Python（但只要始能跑在 ARMv6 的語言，自然都能被 porting 到 Raspberry Pi 上）

[BeagleBone](https://beagleboard.org/) 也是 SBC，是 *TI* 生產的開發板，使用 ARM Cortex-A 和 Cortex-M 的處理器，有 512MB 以上的 RAM，在上面跑 Linux 甚至 Android 都是沒問題的，拿到手時上面預載了 *Ubuntu*。他是一個在定位上比 Raspberry Pi 更開放的 SBC，Raspberry Pi 定位上想帶入更多入們者，降低成為一個 Maker 的門檻，原先並非設計為一個 open-source hardware platform；而 BeagleBone 就是以 open-source hardware 為定位，所以更傾向作為提供以量產為目標的產品在 PoC 階段的開發。不過這也只是原始定位，終究他能做到什麼事情還是回到 HW spec 本身，以這個角度來看很多時候兩者是都可以的。不過基本上還是可以建議如果是業外的入門者，可以先使用 Raspberry Pi，有需要華麗 UI 的也可以先選擇 Raspberry Pi

---

## Firmware Architecture

> 這裏使用 Firmware Architecture 是為了明確和 Software Architecture 區隔，因為當提到 Software Architecture，常常是指比如 [Multitier architecture
](https://en.wikipedia.org/wiki/Multitier_architecture) Presentation layer, Application layer, Business layer, Data access layer 這種領域；又或者 MVC, MVP, MVVM 這個領域。所以本篇使用 Firmware Architecture 來指側重於乘載於硬體平台的整個軟體系統的架構

最接近硬體的部分是 Driver, OS 和 BSP (Board Support Package)，中間是 Middleware, System Software, Framework, Libraries，最上面是 Application, Business Logic
其實常常只是概念性的區分，所以才會有不同的名詞
比如 BSP 常常已經是完整 Driver 和 OS 的打包，甚至包含到了 System Software, Framework, Libraries
Framework, Libyaries 要表達的東西相同，是是提供一些通用性、無關 Business Logic 的功能，比如 network 相關的 API、Database 相關的 API。因為 API 的實現可能會跟 Driver 對接（畢竟不太適當讓 Application 直接去操作 Driver），所以又有 Middleware 的感覺。而有時候也不會直接 call driver 的 API，比如 Linux 環境，OS 為我們準備好許多方便的 System Software 可以使用，比如掃描 Wifi AP 我們可能會用 `iw`

---

## Software Architecture Patterns

這個範疇通常是討論 Business Logic 中 Business Logic 這一層了，或者頂多 Framework 也納入。因為 Driver 基本上邏輯簡單，且常常 BSP 或者是硬件商已經準備好了，而與 OS 相關的則是貼 OS 走，所以當討論到 Software Architecture，大概就是在討論 Framework, Application, Business Logic 裡的 module 要怎麼設計。各種 design pattern 各有特色，但若是從 embedded firmware 的角度出發，可能還是會有一些偏好

軟體模組的設計，基本上以工作內容為主要考量做切分，完全不同的工作最好就是由不同模組負責，這也維持了服務的獨立性，一個模組的失敗不會影響到另一個模組的運行，而且獨立測試起來也更為容易，這個訴求我們稱為『解耦和』Decouple。
當我們從功能的角度切入，一個模組提供的服務應該能夠被簡單明瞭的說明，如果發現說明時需要加上一堆前言、條件，有一大堆例外，那可以就不是一個好的定義。定義模組提供的服務，規模應該要剛剛好：不能太大，因為太大有可能就是包入太多其實並不相關的東西，讓原本可以獨立運作的事情產生了耦合，牽一髮而動全身，不利設測試和維護。這也關係到服務的共享，如果把多個功能包在同一個服務裡，那需要到這些功能的其中一者時，可能都導致同一時間其他功能無法被即時響應，而實際上兩個功能之間並不存在依賴關係，這就是不必要的耦合。也不能太小，同質性高的工作如果被拆分，輕者可能導致資源的冗余，重者可能導致衝突，因為同值性高，代表他們收到的 request、request 的來源、處理的邏輯、處理過程需用到的資源、內部的狀態、結果的返還...等等，有可能都是一樣的
也從易於測試的角度切入，去定義我們的模組。有時候另外也會以 interface 的角度切入，比如一份工作，他的 request 有可能來自於 HTTP 或 硬件中斷，那這種也可要造成模組設計的進一步切分。雖然說是獨立測試，但一個裝置的運作是各個模組的通力合作，往往不可能只跑起來一個模組，比如一個 middleware 模組，他可能一定需要 driver 模組也跑起來，他才有把法測試。不過我們可以藉由掛載一個 dummy 的模組，來達到獨立測試，這就讓 unit test 和 integration test 可以分開。RD 可以完成 unit test，掛載必須的其他 dummy 模組，模擬 input，確保此 module 的 spec 上定義的 input/output 都有符合預期；QA 完成 integration 測試，專注於各 module 整合後，協作起來所產生的問題

### Layered



### Event-driven



---

## Reference

* [All about CPUs: Microprocessor, Microcontroller and Single Board Computer](https://www.seeedstudio.com/blog/2020/10/27/all-about-cpus-microprocessor-microcontroller-and-single-board-computer/)
* [RTOS vs. GPOS：嵌入式設計的最佳選擇？ --- 歐敏銓](https://www.ctimes.com.tw/DispArt/tw/0503051421NP.shtml)
* [How to choose the right firmware architecture for your IoT device --- Steven L. Kelsey](https://locolabs.com/how-to-choose-the-right-firmware-architecture-for-your-iot-device-2/)
* [Design Pattern Catalogue --- PHILLIP JOHNSTON](https://embeddedartistry.com/fieldatlas/design-pattern-catalogue/)