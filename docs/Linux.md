## 命令

**netstat**-napt	查看TCP状态



## shell

###### ctrl-c：

发送 SIGINT 信号给前台进程组中的所有进程。常用于终止正在运行的程序；

强制中断程序的执行

###### **ctrl-z**：

发送 SIGTSTP信号给前台进程组中的所有进程，常用于挂起一个进程；

将任务中断,但是此任务并没有结束,他仍然在进程中他只是维持[挂起](https://so.csdn.net/so/search?q=挂起&spm=1001.2101.3001.7020)的状态

###### **ctrl-d**：

不是发送信号，而是表示一个特殊的[二进制](https://so.csdn.net/so/search?q=二进制&spm=1001.2101.3001.7020)值，表示 EOF，作用相当于在终端中输入exit后回车；

###### ctrl-\：

发送 SIGQUIT 信号给前台进程组中的所有进程，终止前台进程并生成 core 文件；

###### **ctrl-s**：

[中断](https://so.csdn.net/so/search?q=中断&spm=1001.2101.3001.7020)控制台输出；

###### ctrl-q：

[中断](https://so.csdn.net/so/search?q=中断&spm=1001.2101.3001.7020)控制台输出；

###### **ctrl-l**：

清屏