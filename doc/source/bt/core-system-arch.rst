
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
========================================

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
    :align: center
