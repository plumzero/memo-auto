
AUTOSAR 经典平台采用三层、模块化架构。

- 应用层: 车辆功能由 SWC 实现，核心概念有 Ports、Interfaces、Runnable。Runnable 是 SWC 的函数，它描述 SWC 的行为，在运行时由 RTE 调度。
- 运行时环境(RTE): 提供了 SWC 的运行环境，是 VFB 的具体实现，SWC 之间通过 VFB 进行通信。
- 基础软件层(BSW): 根据不同的功能又细分为 4 部分:
  - 服务层: 包括实时操作系统OS(提供了所有基本服务，如中断处理、调度、系统时间和时钟同步、本地消息处理、错误检测机制等)、网络管理、内存管理(NVRAM)、诊断服务、ECU 状态管理等。
  - ECU 抽象层: 提供了 ECU 应用相关的服务，如 ECU 的输入输出。
  - MCAL 层: 对 ECU 所使用的主控芯片的抽象。
  - 复杂驱动层

一些概念
- Ports: 用于 SWC 和 SWC 之间的通信或 RTE 和 BSW 之间的通信。主要分为 5 种类型，分别是 Send/Receiver(Receiver)、Send/Receiver(Send)、Client/Server(Server)、Client/Server(Client)、Send/Receiver(Send & Receiver)。其中 S/R 接口是用来传输数据的(不使用队列的直接访问或缓存访问、使用队列)，C/S 接口是用来执行操作的(同步通信或异步通信)。
- Runnable: RTE 给 Runnable 提供触发事件，这些触发事件有: 初始化事件、定时器事件、接收数据事件、数据发送完成事件、操作调用事件、异步服务返回事件等。

AUTOSAR CP 进行实时编程注意:
- 预估内存使用并锁定内存到 RAM，避免运行时出现缺页或换页事件
- 使用全局变量或静态数组在进程启动时预先分配，而不是使用 malloc 函数
- 局部变量(尤其是大的对象)的压栈和出栈最好是对其指向对象的指针的操作，具体对象则定义为全局变量
- 网络传输时注意数据包大小不超过 MTU
- 避免使用缓存局部性差的设计模式，如桥接(PIMPL)
