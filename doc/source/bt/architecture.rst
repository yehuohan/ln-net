
蓝牙架构
========


:基本概念:

* BR: Basic Rate
* EDR: Enhanced Data Rate

  EDR是optional，BR和EDR可以同时存在。

* AMP: Alternate MAC and PHY layer extension

  AMP是alternate，BR/EDR和AMP只能二选一使用。
  BR->EDR->AMP是提升传输速率的发展过程，也将BR/EDR、AMP称为经典蓝牙。

* LE: Low Energy

  LE用于低功耗场景。
  （LE相比BR，差异很大，可以认为是两种不同的蓝牙技术，但都属于蓝牙技术）


核心系统架构（Core System Architecture）
----------------------------------------

::

    +-----------------------+
    | Bluetooth Application |
    +-----------------------+
    |    Bluetooth Core     |
    |  +-----------------+  |
    |  |      Host       |  |
    |  +-----------------+  |
    |  |   Controller    |  |
    |  +-----------------+  |
    +-----------------------+

* Application: 蓝牙应用层协议；
* Core: 蓝牙核心协议，关注对蓝牙核心技术的描述和规范；

  一个蓝牙Core包含1个Host，1个Primary Controller，0或多个Sencondary Controllers。

* Core.Controller: 负责RF、Baseband等偏硬件的规范，抽象出逻辑链路（Logical Links）；
* Core.Host: 在逻辑链路基础上封装；

..  image:: img/core.png
    :scale: 80 %
    :align: center


数据传输架构（Data Transport Architecture）
-------------------------------------------

::

    +----------+---------------------+
    | APP      | Profiles            |
    +----------+---------------------+
    | L2CAP    | L2CAP Channels      |
    +----------+---------------------+
    |          | Logical Links       |
    | Logical  +---------------------+
    |          | Logical Transports  |
    +----------+---------------------+
    |          | Physical Links      |
    |          +---------------------+
    | Physical | Physical Channels   |
    |          +---------------------+
    |          | Physical Transports |
    +----------+---------------------+

* APP: 基于L2CAP提供的channel，实现各种应用功能
* L2CAP: 逻辑链路和适配协议，负责管理逻辑层提供的逻辑链路，类似TCP/IP中端口的概念
* Logical: 提供多个设备之间、和物理无关的逻辑传输通道（逻辑链路）
* Physical: 负责提供数据传输的物理通道（信道）

数据传输架构中的一些具体协议如下图所示：

..  image:: img/data-detail.png
    :scale: 80 %
    :align: center


物理层（Physical）
''''''''''''''''''

:Physical Transports:

::

    The BR/EDR Physical Transport encapsulates the BR/EDR Physical Channels.
    Transfers using the BR/EDR Physical Transport use the BR/EDR Generic Packet Structure.
    The LE Physical Transport encapsulates the LE Physical Channels.
    Transfers using the LE Physical Transport use the LE Generic Packet Structure.

Transports按照Generic Packet Structure封装来自Channels的数据。


:Physical Channels(物理信道):

BR/EDR, AMP, LE的RF均使用2.4GHz~2.4835GHz的频率范围，且定义了各自不同的物理信道。

* BR/EDR
    频率范围分成79个Channel，每个1MHz。设立Lower Guard Band为2MHz，Upper Guard Band为3.5MHz。

* AMP
    AMP为高速数据传设计，物理层直接采用802.11(WIFI)的PHY规范。

* LE
    频率范围分成40个Channel，每个2MHz。设立Lower Guard Band为2MHz，Upper Guard Band为3.5MHz。

    + `LE Piconet Channel`: 用于已经连接的设备之间的通信。
    + `LE Advertisement Broadcast Channel`: 用于无连接的广播通信，如蓝牙设备的发现、连接等。


:Physical Links(物理链路):

::

    A physical link represents a baseband connection between Bluetooth devices.
    A physical link is always associated with exactly one physical channel
    (although a physical channel may support more than one physical link).

- 跳频技术（hopping）：一个物理链路，并不是固定的占用某一个channel，而是以一定规律跳动。


逻辑层（Logical）
'''''''''''''''''

逻辑层的主要功能，是在已连接的蓝牙设备间（LE Advertisement Broadcast可以看做一类特殊的连接），基于 `Physical Links` ，建立Logical Links，根据传输类型，Logical Links主要包含3类：

- 用于管理底层物理链路的控制类传输：AMP-C, ACL-C, PSB-C, LE-C, ADVB-C
- 传输用户数据的用户类传输：AMP-U, ACL-U, PSB-U, LE-U, ADVU-U
- 其它特殊的传输类型：流式传输(stream), PBD(Profile Broadcast Data)

每个Logic Link在下层对应一个Logical Transport，这些Logical Transport具有一些属性值，如流控、应答/重传机制等。


L2CAP层
'''''''

L2CAP全称为Logical Link Control and Adaptation Protocol(逻辑链路控制和适配协议)。

在ACL-U, ASB-U, LE-U, AMP-U的Logical Links上，L2CAP层抽象出数据传输通道，包括单播（Unicast）和多播（Broadcast）。APP层通过一套面向channel-oriented的接口与L2CAP交互。
