# 死锁与IPC(lec 20) spoc 思考题


- 有"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的ucore_code和os_exercises的git repo上。

## 个人思考题

### 死锁概念 
 - 尝试举一个生活中死锁实例。
 - 可重用资源和消耗资源有什么区别？

### 可重用和不可撤销；
 - 资源分配图中的顶点和有向边代表什么含义？
 - 出现死锁的4个必要条件是什么？

### 死锁处理方法 
 - 死锁处理的方法有哪几种？它们的区别在什么地方？
 - 安全序列的定义是什么？

### 进程的最大资源需要量小于可用资源与前面进程占用资源的总合；
 - 安全、不安全和死锁有什么区别和联系？

### 银行家算法 
 - 什么是银行家算法？
 - 安全状态判断和安全序列是一回事吗？

### 死锁检测 
 - 死锁检测与安全状态判断有什么区别和联系？

> 死锁检测、安全状态判断和安全序列判断的本质就是资源分配图中的循环等待判断。

### 进程通信概念 
 - 直接通信和间接通信的区别是什么？
  本质上来说，间接通信可以理解为两个直接通信，间接通信中假定有一个永远有效的直接通信方。
 - 同步和异步通信有什么区别？
### 信号和管道 
 - 尝试视频中的信号通信例子。
 - 写一个检查本机网络服务工作状态并自动重启相关服务的程序。
 - 什么是管道？

### 消息队列和共享内存 
 - 写测试用例，测试管道、消息队列和共享内存三种通信机制进行不同通信间隔和通信量情况下的通信带宽、通信延时、带宽抖动和延时抖动方面的性能差异。
 
## 小组思考题

 - （spoc） 每人用python实现[银行家算法](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab7/deadlock/bankers-homework.py)。大致输出可参考[参考输出](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab7/deadlock/example-output.txt)。除了`YOUR CODE`部分需要填写代码外，在算法的具体实现上，效率也不高，可进一步改进执行效率。

```
代码完成如下，为了节省篇幅，方便阅读，只将需要填写的部分附上：
def ExecuteProcess(self,index):

    #check if less avaliable than Request
    #YOUR CODE 2012011275
    for Ri in range(len(self.need[index])):
        if self.need[index][Ri] > self.avaliable[Ri]:
            return False
    #check END here

    #allocating what they need.
    #YOUR CODE 2012011275
    for Ri in range(len(self.need[index])):
        self.allocated[index][Ri] += self.need[index][Ri]
        self.avaliable[Ri] -= self.need[index][Ri]
        self.need[index][Ri] = 0
    #allocating END here
    return True

def TempSafeCheckAfterRelease(self):
    #check if at least one request can be done after previous process done. not check whole sequences.
    #if every element of Requests can't accepted after previous process done, this mean it is not safe state
    #YOUR CODE 2012011275
    finishedNum = 0
    for Ti in range(len(self.max)):
        if self.finished[Ti]:
            finishedNum += 1
            continue
        canDone = 1
        for Ri in range(len(self.need[Ti])):
            if self.need[Ti][Ri] > self.avaliable[Ri]:
                canDone = 0
                break
        if canDone == 1:
            return True
    if finishedNum == len(self.max):
        return True
    #check END here
    return False
```


 - (spoc) 以小组为单位，请思考在lab1~lab5的基础上，是否能够实现IPC机制，请写出如何实现信号，管道或共享内存（三选一）的设计方案。
 
```
共享内存：通过系统调用，申请一段内存为共享内存，用于与其他进程的通信，该段内存地址空间将通过修改页表的方式映射到该进程的虚拟地址空间，该段内存地址空间有一个唯一的标志id（存于PCB中），其他进程如果需要进行通信，就要先通过该id申请并且建立映射关系（通过相关函数获取PCB中的这个id参数），这样就可以共享这一段内存空间了。但是在实际上使用的时候，需要借助同步互斥来进行读、写操作。
```

 
 - (spoc) 扩展：用C语言实现某daemon程序，可检测某网络服务失效或崩溃，并用信号量机制通知重启网络服务。[信号机制的例子](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab7/ipc/signal-ex1.c)

 - (spoc) 扩展：用C语言写测试用例，测试管道、消息队列和共享内存三种通信机制进行不同通信间隔和通信量情况下的通信带宽、通信延时、带宽抖动和延时抖动方面的性能差异。[管道的例子](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab7/ipc/pipe-ex2.c)
 
