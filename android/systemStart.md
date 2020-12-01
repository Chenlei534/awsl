# 系统和应用程序启动

## Android系统启动

### 基本概念

1. **init进程**
    * 什么是init进程：init进程是Android系统中用户空间的第一个进程，负责Android系统启动流程中的主要进程和功能的启动工作。
    * init进程主要工作：启动挂载所需文件目录、初始化启动属性服务、启动Zygote进程（孵化器进程）。
    * 什么是init.rc：init.rc是系统初始化配置文件，由Android初始化语言编写的脚本，配置了init进程各种操作，是init进程的入口。
2. **属性服务**
    * 什么是属性服务：是用来记录用户和软件等信息，保证即使系统重启也能根据记录进行相应的初始化操作。
    * 系统属性类型：普通属性、控制属性。控制属性用来执行一些命令，如：开关机动画等。
    * 属性服务的实现：通过创建非阻塞的Socket来实现属性服务。
3. **Zygote进程**
    * 什么是Zygote进程：Zygote进程又叫孵化器，它可以通过fork（复制进程）自身的方式来创建`应用程序进程`和`SystemServe进程`。
    * Zygote进程特点：Zygote进程启动时会创建虚拟机，所以它通过fork来创建其他进程可以在内部获取虚拟机实例副本、Zygote的main方法由Java编写，开启了Java框架层。
    * Zygote主要工作：创建Java虚拟机并注册JNI方法、通过JNI调用ZygoteInit.main()进入Java框架层、启动SystemServe进程、创建Serve端Socket`等待AMS请求`创建新的应用程序。
4. **SystemServe进程**
    * 什么是SystemServe进程：SystemServe进程主要用于创建系统服务，如：AMS、WMS、PMS等。
    * SystemServe主要工作和流程：创建`Binder线程池`、创建SystemServiceManager、启动各种系统服务。
    * 什么是SystemServiceManager：SystemServiceManager用于对系统服务创建、启动和生命周期管理。

### 流程分析

![SYSTEM_START](/images/systemStart1.png)

------------

## 应用程序进程启动

### 基本概念

1. **ActivityManagerService**
    * 简称AMS，负责Activity、Service等创建、广播的接收和发送、Activity栈的维护、跨进程通信等工作
2. **ZygoteState**
    * ZygoteState是Zygote的静态内部类，用于表示与Zygote进程通信的状态。
3. **ZygoteProcess**
    * 用于保持与Zygote进程的通信状态


### 启动过程

AMS在启动应用程序的时候会检查该应用程序所需的`应用程序进程`是否存在，如果不存在则请求Zygote进程启动应用程序进程。     
应用程序进程的启动分为两个部分：        

1. **AMS发送应用程序启动请求到Zygote进程**
    1. 创建应用程序进程的用户ID（uid）
    2. 对用户组ID（gisd）经行赋值
    3. 将应用进程启动参数保存到列表（argsForZygote，便于数据传输）
    4. 与Zygote建立Socket连接（返回ZygoteState）
    5. 将应用进程启动参数通过Socket发送到Zygote（发送到ZygoteState）

![PROCESS_START](/images/processStart1.png)

2. **Zygote进程fork启动应用程序进程**
    1. Zygote创建服务端Socket并等待AMS请求（通过ZygoteServer创建）
    2. Zygote接收到AMS请求并处理请求数据（ZygoteServer接收AMS请求运行ZygoteConnection的runOnce()处理请求数据）
    3. 创建应用程序进程（通过应用进程启动参数并且fork当前进程来创建子进程）
    4. 创建Binder线程池（Binder线程池创建时会将当前线程注册到Binder驱动中）
    5. 启动ActivityThread（通过反射调用ActivityThread.main()方法启动该类）
