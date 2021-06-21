---
title: Basic Network Security. 基礎網路安全筆記
categories: software
tags:
  - network security
  - rsa
  - signature
  - SSL
  - TLS
  - 加密
  - 網路安全
  - 數位簽章
  - 數位憑證
  - software
keywords:
  - network security
  - rsa
  - signature
  - SSL
  - TLS
  - 加密
  - 網路安全
  - 數位簽章
  - 數位憑證
date: 2021-06-19 09:18:23
img: https://drive.google.com/uc?export=view&id=1loldv9BvkDKcAf3qCvVFfO-v8_085YXV
summary: 傳輸數據時的 4 個安全性問題- 竊聽、欺騙、竄改、抵賴
---


## 傳輸數據時的 4 個安全性問題

* 竊聽 eavesdrop
* 欺騙 spoofing：偽裝成別人
* 竄改 falsification
* 抵賴 repudiation

## 如何因應

* 竊聽 eavesdrop：加密 encryption 內容，plain text => encrypt => cipher text
* 欺騙 spoofing：數位簽章
* 竄改 falsification：數位簽章
* 抵賴 repudiation：數位簽章

---

## 加密

### Shared-key cryptosystem (AES, DES)

* 加密解密用同一把 key
* 運算速度較快

## Public-key cryptosystem (RSA)

* 又稱 Asymmetric cryptosystem
* 加密使用公開金鑰，解密使用私密金鑰。所以是由文件接收者製作 key pair，把公開金鑰傳給發送者
* 運算速度較慢
* 問題是「中間人攻擊」，也就是發送者無從確定使用的公開金鑰是否真為接收者的公開金鑰。若其實使用到惡意第三方的公開金鑰加密文件，且傳輸內容也被攔截，那惡意第三方即可解密讀得內容

## Hybrid cryptosystem (SSL, a.k.a TLS)

* 用 shared-key 加密文件，傳送 shared-key 給對方時使用對方的公開金鑰來加密 shared-key

## Diffie-Hellman key exchange

如果今天某數學方法可達成這樣的特姓

* 可將 P 及 S 兩金鑰合成一個新金鑰 PS
* 即使有 PS 及 P，也無法推導出 S（對 P 亦然）
* 合成後的金鑰可以再進一步合成，比如 PS 可以再進一步合成 P 變成 PPS
* 合成順序不同但產生的金鑰相同，比如 ABC 等於 BAC

基於此特性

1. A 準備金鑰 P 傳給 B
2. A/B 各自準備私密金鑰 SA/SB
3. A/B 用 P 合成各自的 SA/SB 變成 PSA/PSB
4. A/B 交換 PSA/PSB
5. A/B 用各自的 SA/SB 合成收到的 PSA/PSB 變成兩個人都擁有 PSASB
6. 中間惡意人無法透過 P, PSA, PSB 還原出 SA/SB

## Message Authentication Code (MAC)

雙方使用同一把金鑰計算出文件的 MAC 值，文件通常已經是加密文件，此處用來產生 MAC 的金鑰與加密文件的金鑰無關。雙方如果算出的 MAC 值一樣就知道傳送過程中該密文數據是完整且未經竄改

## Digital Signature 數位簽章

是 MAC 加上不可抵賴性機制，因為 MAC 雙方的金鑰是同一把，對於一則訊息，其實以結果來看沒有方向性，也就是訊息本身無法說明是誰製作

也是使用 Asymmetric cryptosystem，但相反，由發送方製作 key pair，使用私密金鑰去加密訊息，產出就是一個數位簽章，把簽章和原本的訊息都傳給對方。對方接收到之後，使用發送方的公開金鑰解密簽章，如果解出來的解果與訊息相同，那簽名驗證完成

實際上簽章的產生部會對整個訊息做加密運算，因為耗時，只需要對訊息的 hash 值做加密產生簽章即可

加密解密，在 Digital Signature 和 Public-key cryptosystem 時使用的方式相反，並非所有的加密法都支援這種特性，RSA 支援

## Digital Certification 數位憑證（PKI 公開金鑰基礎架構）

因為既然是使用 Public-key cryptosystem，那就一樣存在中間人攻擊的疑慮，此時要導入一個第三方的憑證機構，來幫助證明某公開金鑰的擁有者為某人

1. A 向憑證機構 CA 申請發行憑證，即提出 CSR。CSR 就是本身的 public key 加上一些個人資訊如 email
2. 憑證機構用某種其他方式確認 CSR 是否為本人所有，確認後用 CA 的私密金鑰，把 public key 製作成數位簽章，並加回個人資訊以及 CA 資訊，成為一個數位憑證回傳 A
3. A 把數位憑證傳給 B，B 一方面確認其中記載的個人資訊是 A，一方面透過憑證資訊另外下載取得 CA 的公開金鑰，用以驗證該憑證，如果驗證成功就確認該憑證為該 CA 所發行
4. B 取出憑證中 A 的 public key

這裡的問題是出在「下載取得 CA 的公開金鑰」，你怎麼知道下載到的就是該 CA 的公開金鑰？所以又到一樣的問題，這裡說是下載，但其實該 CA 提供其公開金鑰的方式也是透過數位憑證，而該憑證又是由其他 CA 所簽發，所以最終都會聚集到幾間最大家的值幸賴的 CA，稱為 root CA，這種 CA 不會再有上級 CA 來證明其真實性，只能自己證明自己，這種憑證稱為根憑證 root certificate。根憑證被大多端點信任，所以如果驗證憑證的過程最終追溯到根憑證，那就驗證完成。你也可以自己成為 root CA 自己製作證明自己的根憑證，只是你也就只能拿來簽發給自己數位憑證的用途而已。

當我們用瀏覽器透過某 URL 造訪一個網站，瀏覽器會確認伺服器回傳的內容，其數位憑證符合該 URL 網域。什麼意思，上述的個人申請的數位憑證可以說是將個人資料如 email 與某 public key 綁定，證明此 public key 為某 email 所有。同樣的道理，某 public key 也可以與某網域 domain 綁定，當造訪某網站，瀏覽器除了要求內容外也要求其數位憑證，並同步向 CA 驗證其數位憑證是否為該網域所有，若是，可能用其中的 public key 解密內容將其明文顯示。
