---
title: 封鎖/隱藏 特定網站出現在 Google Search 結果當中
categories: software
tags:
  - productivity
keywords:
  - google search
  - blocking
date: 2021-10-07 09:52:30
img: https://drive.google.com/uc?export=view&id=1GVHMeSNqncKPd-ylk4cXOHMa3XaxcndU
summary: 讓 Google 搜尋結果當中永遠不要找到特定網站的內容，可以避免掉一些農場文和閱讀體驗很差的網站
---

我真的非常討厭 Pixnet 的閱讀體驗，又慢、版面又醜，廣告蓋板就算了，廣告 load 又慢，很多時候 `x` 還點不到，想關掉還直接會點進廣告，反正就是個垃圾
所以試了幾個 block 工具，最後選的這個，覺得簡單好用

* [uBlackList](https://chrome.google.com/webstore/detail/ublacklist/pncfbmialoiaghdehhbnbhkkgmjanfhe/related?hl=zh-TW)

> 另外最近開使漸漸轉換到 `Edge`，因為 `Chrome` 在 MacOS Big Sure 開始之後的表現也讓我非常的不爽...
> `Edge` 因為也是基於 `Chrome` (`Chromium`)，所以 `Chrome` 的 Extension 也都可以裝在 `Edge`，只要在 `Edge` 的 `擴充功能` 設定中開啟 `允許來自其他存放區的擴充功能` 即可

安裝完 `uBlackList` 這個 plugin 之後，就可以去他的設定當中編輯想要封鎖的網站
他支援 regular expression，比如我要封鎖 pixnet，就輸入 `*://*.pixnet.net/*`

![封鎖 pixnet](https://drive.google.com/uc?export=view&id=15hXfwq55zqnQBQRoU09iRoQ7kQ00tFub)

> uBlackList 是從搜尋結果當中直接將其隱藏
> 有的 plugin 不是，有的 plugin 是還是會在搜尋結果中出現，只是點進去之後會顯示封鎖頁面，這種效果比較不是我們想要的

然後當我們試著做 Google Search，就會看到目前有幾個網站被封鎖
比如下面搜尋結果第一行就顯示『Blacklist 已經封鎖 1 個網站』，當然你如果有時還想看的話也是可以按 `顯示` 把他顯示出來

![例如 Google Search "馬祖" 的效果](https://drive.google.com/uc?export=view&id=1stSNhczrRm0827FA9kTkwAd1NMmlH7jv)

如果常常點到某些網站，其內容也讓你非常怒，也可以簡單透過顯示在每筆搜尋結果上方的 `封鎖這個網站`去把他 block 掉

<br/>

另外也推薦這個 [封鎖內容農場](https://chrome.google.com/webstore/detail/content-farm-blocker/opjaibbmmpldcncnbbglondckfnokfpm/related?hl=zh-TW)
他其實是差不多意思，等於是它已經幫你把一些內容農場的網址列好去封鎖
不過這個就是點擊後才攔截了，並不是直接將其從 Google 搜尋結果隱藏，但還是可以用來幫助比如從 Facebook 點到的連結的這種狀況
