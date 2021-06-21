---
title: Soft AP on Mac. 在 Mac 上啟動 Soft AP 及 CGI handler
categories: software
tags:
  - soft ap
  - mac
  - cgi
  - osx
  - ap mode
keywords:
  - soft ap
  - hostapd
  - mac
  - cgi
  - osx
  - apache
  - ap mode
date: 2021-06-19 09:18:23
img: https://drive.google.com/uc?export=view&id=1iLKCaNXhk72fZXyezU2uqzRk3XubYt2w
summary: 把 Mac 的 Wifi 當成 Soft AP 使用
---


## 摘要

啟動 Mac 的 Wifi 的 AP mode，其他裝置就可以發 request 到你的 Mac。

在 Mac 上啟動 Apache HTTP Server，其他裝置就可以發 HTTP request 到你的 Mac。

Apache 提供 HTTP request 的 forward，常見的就是 CGI。在指定的 Apache 知道的目錄下放置 CGI handler 的可執行檔，就可以處理透過撰寫此執行檔，在裡面處理該如何回應各式 request。

---

## 啟動 Mac 的 Wifi 的 AP mode
> Reference: [https://www.laszlopusztai.net/2016/02/14/creating-a-wi-fi-access-point-on-os-x/](https://www.laszlopusztai.net/2016/02/14/creating-a-wi-fi-access-point-on-os-x/)

[我寫這篇時 Mac OS X 版本為 Big Sur 11.2.3]

1. 在 Terminal 輸入以下兩行

``` bash
$ sudo networksetup -createnetworkservice Loopback lo0
$ sudo networksetup -setmanual Loopback 172.20.42.42 255.255.255.255
```

2. 到 `系統偏好設定` 的 `網路`，就可以看到多出 `回繞`

![系統偏好設定 -> 網路 -> 回繞](https://drive.google.com/uc?export=view&id=1R6-zpUS-xt4NvdYYNK-svzbpAzzIw-XR)


3. 到 `系統偏好設定` 的 `共享`，`Internet 共享` 中 `共享連線來源` 選擇 `回繞`，共享到 `Wi-fi`

![共享連線來源回繞到 Wi-fi](https://drive.google.com/uc?export=view&id=1jkE5N8doQSKxPACOrWDTlN1xRAlAyHqM)

`Wi-Fi 選項` 裡可以設定密碼

![設定密碼](https://drive.google.com/uc?export=view&id=1jjUIdOi3h5-UM2_V7ed4JF6gorqbMRH_)

把 `Internet 共享` 打勾，其他裝置掃描 Wifi AP 該就可以看到

<br />

## 在 Mac 上啟動 Apache HTTP Server

> Reference: [https://www.mdeditor.tw/pl/24pL/zh-tw](https://www.mdeditor.tw/pl/24pL/zh-tw)

Mac 上預設應已有 Apache 作為 http daemon，可以藉由 `$ httpd -v` 查看版本

``` bash
$ httpd -v
Server version: Apache/2.4.46 (Unix)
Server built: Dec 21 2020 18:03:44
```

httpd 的配置檔在 `/etc/apache2/httpd.conf`，透過 `sudo` 權限編輯

``` bash
$ sudo vim /etc/apache2/httpd.conf
```

找到下行並移除起始的註釋符號

``` bash
LoadModule cgi_module libexec/apache2/mod_cgi.so
```

在目錄 `/Library/WebServer/CGI-Executables` 目錄下建立檔案 `hello.py` , 這集就是 CGI Handler，這裡是雖然是用 Python，但他其實就看作是一個可執行檔，他就是一個可執行腳本，腳本的第一行就定義了該怎麼執行這個腳本，`hello.py` 內容為

``` python
#!/usr/local/bin/python3
 
print("Context-type:text/html")
print("")
 
print("<h2>Hello Python CGI</h2>")
```

改變他的權限

``` bash
$ sudo chmod 755 /Library/WebServer/CGI-Executables/hello.py
```

重啟 http daemon

``` bash
$ sudo apachectl -k restart
```

此時就可以測看看有沒有效果，`cURL` 就是個可以用來發 HTTP request 的指令，我們以此做測試

``` bash
$ curl -i http://localhost/cgi-bin/hello.py
```

結果就可以看到

``` bash
HTTP/1.1 200 OK
**Date**: Thu, 18 Mar 2021 00:10:00 GMT
**Server**: Apache/2.4.46 (Unix)
**Context-type**: text/html
**Transfer-Encoding**: chunked
<h2>Hello Python CGI</h2>
```

為什麼 http request 是往 `/cgi-bin` 發，可以回頭看 `httpd.conf`，裡面定義了

``` bash
ScriptAliasMatch ^/cgi-bin/((?!(?i:webobjects)).*$) "/Library/WebServer/CGI-Executables/$1"
 
<Directory "/Library/WebServer/CGI-Executables">
    AllowOverride None
    Options None
    Require all granted
</Directory>
```

<br />

如果過程中遇到什麼錯誤，也許可以透過查看 Apache log 來知道問題，log 位置在 `/var/log/apache2/error_log`

<br />

我們進一步看一下 Payload 的部分，把測試 request 改為

``` bash
$ curl -i 'http://localhost/cgi-bin/hello.py?req=123' > ./test.html
```

把 `hello.py` 改為

``` bash
#!/usr/local/bin/python3

import os

print("Context-type:text/html")
print("")

print("<h2>Hello Python CGI</h2>")

print("<b>Input</b><br>")
print("<ul>")
for key in os.environ.keys():
  print("<li><span style='color:green'>%30s </span> : %s </li>" % (key,os.environ[key]))
print("</ul>")
```

執行後打開 `test.html`，就可以看到

![test.html](https://drive.google.com/uc?export=view&id=1DRqT50vLRG2NiLvvTltWSztRz7cjPhQz)

上面就可以看到這是一個 GET 類別的 HTTP request，帶的 QUERY_STRING 是 req=123
