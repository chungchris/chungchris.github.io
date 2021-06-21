---
title: {{ title }}
categories:
tags:
keywords: # SEO 时需要
img: # https://drive.google.com/uc?export=view&id=<...>
top: # true. 推荐文章（文章是否置顶），如果 `top` 值为 `true`，则会作为首页推荐文章
hide: # false. 隐藏文章，如果`hide`值为`true`，则文章不会在首页显示
cover: # false. 表示该文章是否需要加入到首页轮播封面中
coverImg: # 表示该文章在首页轮播封面需要显示的图片路径，如果没有，则默认使用文章的特色图片
author: #
password: # 文章阅读密码，如果要对文章设置阅读验证密码的话，就可以设置 `password` 的值，该值必须是用 `SHA256` 加密后的密码，防止被他人识破。前提是在主题的 `config.yml` 中激活了 `verifyPassword` 选项
toc: # true. 是否开启 TOC，可以针对某篇文章单独关闭 TOC 的功能。前提是在主题的 `config.yml` 中激活了 `toc` 选项
mathjax: # false. 是否开启数学公式支持 ，本文章是否开启 `mathjax`，且需要在主题的 `_config.yml` 文件中也需要开启才行
summary: # 文章摘要，自定义的文章摘要内容，如果这个属性有值，文章卡片摘要就显示这段文字，否则程序会自动截取文章的部分内容作为摘要
reprintPolicy: # 文章转载规则， 可以是 cc_by, cc_by_nd, cc_by_sa, cc_by_nc, cc_by_nc_nd, cc_by_nc_sa, cc0, noreprint 或 pay 中的一个
---
