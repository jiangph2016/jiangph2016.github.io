---
layout: post
title: 【Python】signal模块
category: 笔记
tags: Python
keywords: signal
---
* content
{:toc}

## signal模块

重写进程接收到的信号，需要写一个handler函数，需要带两个参数`signum`和`frame`


例如下面的进程是每次接收到`SIGINT`的信号时，不再结束进程，而是计数接收到此信号的次数。

这里绑定了一个`SIGUSR1`的信号，到一个退出程序的函数上
```
import signal

def count_handler(signum,frame):
    global interupt
    interupt += 1
    print("stop {} times".format(interupt))

def exit1(signum,frame):
    sys.exit()


if __name__ == "__main__":
    signal.signal(signal.SIGINT,count_handler)
    signal.signal(signal.SIGUSR1,exit1)
    for i in range(100):
        time.sleep(1)
        print(os.getpid(),i)
```

## Linux下的信号量
使用`kill -l`命令查看Linux下的信号量


``` 
1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX
```

---

 - 其中`SIGUSR1`、`SIGUSR2`信号是留给用户使用的
 - 程序不可捕获、阻塞或忽略的信号有`SIGKILL`,`SIGSTOP` .运行时会提示`OSError: [Errno 22] Invalid argument`
 - 不能恢复至默认动作的信号有：`SIGILL`,`SIGTRAP ` 
 - 默认会导致进程流产的信号有：`SIGABRT`,`SIGBUS`,`SIGFPE`,`SIGILL`,`SIGIOT`,`SIGQUIT`,`SIGSEGV`,`SIGTRAP`,`SIGXCPU`,`SIGXFSZ`  
 - 默认会导致进程退出的信号有：`SIGALRM`,`SIGHUP`,`SIGINT`,`SIGKILL`,`SIGPIPE`,`SIGPOLL`,`SIGPROF`,`SIGSYS`,`SIGTERM`,`SIGUSR1`,`SIGUSR2`,`SIGVTALRM`   
 - 默认会导致进程停止的信号有：`SIGSTOP`,`SIGTST`,`SIGTTIN`,`SIGTTOU`  
 - 默认进程忽略的信号有：`SIGCHLD`,`SIGPWR`,`SIGURG`,`SIGWINCH`  