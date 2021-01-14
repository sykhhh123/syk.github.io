---
title: xinu代码阅读
date: 2018-10-06 10:17:57
tags:
---

<!--more--> 

-	system/main.c
	-	启动 shell 进程
-	include/process.h
	-	定义了 process table 的结构
-	system/resched.c
	-	resched：重新按优先级安排进程
	- 	resched_cntl: 控制是否允许重新安排
-	system/resume.c
	- 	resume: 不再挂起指定进程，让它处于 ready 状态
-	system/suspend.c
-	system/kill.c	
- 	system/xdone.c
	- 	提示所有用户进程结束后 halt
-	system/create.c
	- 	初始状态 PR_SUSP
-	system/userret.c
	- 	强制结束当前进程
- 	system/chprio.c 
	-  改变进程优先级
- 	system/wait.c
-	system/signal.c
	- 	释放一个等待队列中的进程？
-	system/semcreate
- 	system/semdelete  
-  system/semreset
-  system/send
	- 	发送消息并 ready 一个进程 
-  system/recvclr.c
	- 	如果有消息则返回消息， 没有则返回ok, 然后清空消息 

-	要添加系统调用 
	-	在 system 中创建函数
	- 	在 include/xinu.h 中 include 进来
	-  在 shell 中创建 xsh_xxxx.c
	-  在 shell.c 的 cmdtab 中加入
	-  在 Makefile 中加入对应文件编译