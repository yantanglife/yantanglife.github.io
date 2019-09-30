---
layout: post
title:  "Open PDF in a Browser"
date:   2019-9-30 12:00:00
categories: jekyll
permalink: /archivers/OpenPDF
comments: true
---

同学在群里问，有没有平台可以提供 PDF 的在线预览.
<!--more-->

刚开始没有理解他的意思，给他说 `github` 就可以，
但是并不是他想要的效果，后来又找了一些平台，貌似都不行.

一些 blog 平台也不支持 PDF 的在线预览.
那似乎是只能自己搭一个了吗?

回头一想，可以用 `github pages` 实现吗?    
上网一搜，便有了 
<a href="https://stackoverflow.com/questions/30745981/opening-pdf-in-a-browser-with-github-pages" target="_blank">
Opening PDF in a browser with Github Pages
</a>
.

***

只需要把 PDF 文件放在 `username.github.io` 项目下，例如 `/pdf/name.pdf`.

访问 `username.github.io/pdf/name.pdf` 就可以在线预览了.

[Open this PDF on the current page](../pdf/dropout.pdf)

<a href="../pdf/dropout.pdf" target="_blank">
Open this PDF on the new page
</a>

~~*这么简单，但是我还是要记录一下.*~~

***
不过 `github` 对仓库容量有所限制，每个仓库不要超过 1G.    
PDF 相比其他文件大了很多，因此不要随意存放.
 
<a href="https://help.github.com/en/articles/what-is-my-disk-quota" target="_blank">
GitHub Disk Quota
</a>

***