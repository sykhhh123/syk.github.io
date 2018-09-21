---
title: CTF Web 萌新的小笔记
date: 2018-07-11 13:36:35
tags: CTF
---
# 目录 #
1. <a href="#文件上传">文件上传</a>
2.  

<a name="文件上传"></a>
# 文件上传 #
-	上传 php 文件并过滤 <?php ?> 标签的时候 可以使用 <script language="PHP"></script> 绕过
	<br>如果只过滤了小写的 php 可以用 strtolower('PHP') 来绕过
	<br>题目出处： 2017 百度杯9月场 Upload