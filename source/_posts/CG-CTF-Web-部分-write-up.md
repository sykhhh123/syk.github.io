---
title: CG-CTF Web 部分 write up
date: 2018-07-04 15:50:51
tags: CTF
---

# 签到题 #

打开源码就能看到 flag

# md5 collision #

md5 碰撞， 这里用的是 PHP 对字符串 0exxx 使用科学计数法计算， 将 QNKCDZO 进行 md5 加密，密文为0e830400451993494058024219903391， 只需要再找一个 0e 开头的字符串即可：s878926199a

<!--more--> 

# 签到2 #

打开源码， 要输入 zhimakaimen ，但是输入框限制 maxnlength = 10 改为 11 即可

# 这题不是WEB #

上来直接塞了张图片， 用 winhex 打开拉到最后就看到 flag 了

# 层层递进 #

打开链接， F12 看网络项， 里面有一个奇怪的 404.html， 打开那个html， 查看源代码。

<br> ![test](/CG-CTF-Web-部分-write-up/层层递进.png) <br>

将 jquery- 后面的字符连起来就是 flag

# AAencode #

AAencode 是一种把代码变成颜文字的加密， 结合题目意思此处应该是一段 javascipt 代码， 直接粘贴到浏览器控制台中运行即可得到结果

注： 如果未能正确显示颜文字需要修改浏览器编码

# 单身二十年 #

点进去有个链接， 跳了一下就说 key 没在这， 用 burp 抓包发现一共发了三个请求。

<br> ![test](/CG-CTF-Web-部分-write-up/单身一百年也没用.png)  <br>

看一下 serach_key.php 的 Response 找到 flag

# 单身一百年也没用 #

抓包发现跳到 web9/index.php 有个 302， 打开 Response 就看到 flag 了

# 文件包含 #

发现 URL 中有经典文件包含漏洞， 利用 php://filter 输出页面， 打开 php://filter/read=conver.base64-encode/resource=show.php 发现里面没有什么内容， 再打开 index.php 发现 flag

# php decode #

直接丢到 php 里面跑报错， 说里面有个 ｛ ， 那 flag 估计在里面， 把 eval 换成 echo 得到 flag。

# COOKIE #

点进去发现啥都没有， burp 看一下 Cookie 发现是 0 ， 练习一下 tips ： 0 == not ， 把 0 改成 1 登录拿到 flag

# MYSQL #

进去先看 robots.txt ， 发现有 sql.php， 参数是 id， 但是 id 不能为 1024， 试了试其他的都没有内容显示， 估计 flag 在 1024 里面， 看前面代码把输入的 id 转换成整数再带入 sql 语句中， 但是检测的时候检测的是输入的 id 是否为 1024， 输入 id=1024.1 得到 flag

# GBK Injection #

既然是 GBK 肯定是宽字符， 输入 1' 发现被转义了， 输入 %df' 成功屏蔽转义符， 然后就先查数据库表， 有 ctf1~4， ctf1 里面没东西， ctf2 里面东西拿出来发现 flag： nctf{query_in_mysql} 但其实这不是这道题的 flag， 然后接着看后面 ctf4 里面有个 flag 列， 打开拿到真的 flag：nctf{gbk_3sqli}

# /x00 #

利用 ereg 在遇到 %00 时会默认为字符串结束， 构造字符串 1%00%23biubiubiu
<br>此外 好像可以利用 === 和 ！== 的严格比较， 将 nctf 设置为数组， 这样在 ereg 比较时返回的并不是 false， 下面 strpos 的返回值也不是 false 由此获取 flag

# bypass again #

还是利用 md5 0e 开头字符串在比较时会被转换成科学计数法法视为 0 
也可以利用 md5 无法处理数组 构造两个数组（这才是考点？）

# 变量覆盖 #

extract 能把数组中的符号表导入当前符号表， 这样只要把 thepassword_123 覆盖成和 pass 一样的值

# 上传绕过 #

这题比较麻烦。
普通的截断后缀好像并不好使。
<br>先上传个 jpg 得到以下信息
<br> ![test](/CG-CTF-Web-部分-write-up/上传绕过.png) <br>

储存地址分为 dirname， basename， extenion 来检验
<br>看一下发上去的包。
<br> ![test](/CG-CTF-Web-部分-write-up/上传绕过2.png) <br>

看样子 /upload/ 就是 dirname 部分， 在 /upload/ 后面添加 1.php 得到如下结果

<br> ![test](/CG-CTF-Web-部分-write-up/上传绕过3.png) <br>
可以发现 basename 变了， 那么尝试在 /upload/1.php 后添加 %00 进行截断 得到 flag

# SQL注入1 #

打开源码看一下， pass 被 md5 加密了但是 user 是直接使用的， 因此利用 user 注入， 注意闭合单引号和括号即可

# pass check  #

感觉是个弱类型比较， 直接 post 一个数组就可以了。

#  起名字真难  #

要求给出字符串不能包含 1~9（不是 0~9） 而且字符串与 54975581388 相等， trick 的地方在于 54975581388 的十六进制 为 0xccccccccc 而 0 也是可以被接受的字符。 

#  SQL Injection  #

以前并不知道的操作吧。  本来是 SELECT * FROM users WHERE name = '**xxx**' AND pass = '**xxx** '
<br>因为单引号被过滤， 只能用已有的单引号来相互闭合， 在 name 中填写 **\\** 得到
<br> SELECT * FROM users WHERE name = '**\' AND pass = '**xxx'
<br> 此时将 xxx 替换成注入部分即可

# 综合题 #

名副其实综合题。
<br> 先是一堆乱七八糟的符号， 丢到控制台跑一下得到一个 php 文件名， 打开后说 TIP 在脑袋里
<br> 看一下 Header， 里面 tip 写了 history of bash， 查了一下发现可能是有个 .bash_history 文件
<br> 再打开 发现是一条解压命令， 访问一下那个压缩包， 下载下来解压得到 flag

#  SQL注入2  #

直接 union select md5（1） 然后 password = 1 就可以了

# file_get_contents #

利用 php://input 直接把 meizijiu POST 上去就可以了

#  变量覆盖  #

emmm 看个源码， 好像提交个 name=meizijiu233 就可以了。。。

# Anonymous #

考了个匿名函数的名称， 格式是 \x00lambda_%d ， %d 是当前进程中第 %d 创建的匿名函数， 所以思路就是通过向 Pre-fork 模式的 apache 服务器发送大量请求,致使 apache 开启新的进程来处理请求， 利用 burp intruder 模块。