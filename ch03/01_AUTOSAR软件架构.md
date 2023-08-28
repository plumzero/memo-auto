
传统的 ECU 架构有以下两个缺点: 抽象程度低；基础软件模块少。针对这些问题，AUTOSAR 规范提出了抽象程度更高的解决措施，划分出更多的基础模块。为了实现应用软件和硬件模块的解耦，汽车电子软件架构被抽象成 4 层，从上至下依次为: 应用层(Application Layer)、运行时环境(Runtime Environment,RTE)、基础软件层(Basic Software,BSW)以及微控制器(Microcontroller)。应用层完全独立于硬件，只有基础软件层与硬件相关，而 RTE 实现这两者的隔离。

![](img/AUTOSAR层面图.jpg)

AUTOSAR 面临的挑战及其优点如下:

| 非AUTOSAR面临的挑战 | AUTOSAR方案 | AUTOSAR优势 |
|:------------------|:-----------|:------------|
| 对功能需求缺乏追溯手段，没有兼容性的工具链 | 需求交互格式标准化(ARXML) | 从内容和格式上改进了规范，为无缝的工具链提供可能 |
| 基础软件模块不能复用，带来的时间和精力浪费 | 基础软件(BSW) | 提高软件质量，供应商提供基础软件 |
| 升级主芯片带来大量的重新设计 | 微控制器抽象层(MCAL) | 主芯片可以随意替换，MCAL由芯片厂家提供 |
| 集成工作反馈 | 运行时环境(RTE) | 集成自动化 |
| 软件耦合性大 | 接口标准化 | 不同供应商可交互组件 |

### 模块构成

AUTOSAR 架构的分层式设计，用于支持完整的软件和硬件模块的独立性。如下图如下，中间 RTE(Runtime Environment)作为虚拟功能总线 VFB(Virtual Functional Bus)的实现，隔离了上层的应用软件层(Application Layer)与下层的基础软件层(Basic Software)，摆脱了以往 ECU 软件开发与验证时对硬件系统的依赖。

![](img/架构分层标准.jpg)

#### Application Layer(应用层)

应用层中的功能由各软件组件(Software Component,SWC)实现，组件中封装了部分或者全部汽车电子功能。其中包括对具体功能的实现以及对应描述，如控制大灯、空调等部分的运作，但与汽车硬件系统没有连接。

![](img/应用层的组成.jpg)

1.软件组件

软件组件由最小逻辑单元(Atomic Component)组成。最小逻辑单元有 Application、Sensor/Actuator 两种类型。其中 Application 是算法实现类型，能在各 ECU 上`自由映射`。Sensor/Actuator 是为 Application 提供 I/O 端口类型，用于与 ECU 绑定，但不像 Application 那样能在各 ECU 上自由映射。整个 SWC 的逻辑集合组合成 Composition。

![](img/SWC的组成.jpg)

2.端口

端口用来和其他 SWC 通信。通信内容分为 Data Elements 与 Operations。其中，Data Elements 用发送端——接收端(Sensor/Receiver)通信方式，Operations 用客户端——服务器(Client/Server)通信方式。如下图所示。

![](img/SWC通信方式.jpg)

发送端——接收端(Sender/Receiver)用来传输数据，具有一个通信端口可以包含多种数据类型的特点。但如果一个数据类型要通过总线传输，那么它必须与一个信号对应起来，数据类型既可以是简单的数据类型(integer,float)，也可以是复杂类型(array,record)。通信方式是 1:n 或 n:1 。如下图所示。

![](img/发送端-接收端特征图.jpg)

在上面的图示中，当车门打开时，会发送一个信号给调光器。调光器接收到信号后，调用 RTE 接口，调整灯的亮度。

客户端——服务器端口(Client/Server)用来提供 Operation 服务，具有一个客户端——服务器端口可以包含多种 Operation 和同步或异步通信的特点，一个客户端——服务器端口可以包含多种 Operations 操作，Operations 操作也可被单独调用。通信方式是 1:n 或 n:1 。

![](img/客户端-服务器端口.jpg)

3.可运行实体

可运行实体(Runnable Entities)简称 Runnables。可运行实体包含实际实现的函数，可以是具体的逻辑算法或是实际操作。可运行实体由 RTE 周期性或是事件触发调用，如接收到数据。如下图所示。

![](img/可运行实体.jpg)

### Runtime Environment(中间件)