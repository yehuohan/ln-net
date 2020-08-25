

# Nordic

[Nordic nRF](https://www.nordicsemi.com/Products)使用BLE协议相关。

- nRF5 SDK: Nordic芯片SDK
- nRF Command Line Tools: 包括JLink驱动、nrfjprog、mergehex等工具
- nRF Toolbox: BLE应用工具
- nRF Connect: BLE调试工具，有手机端和桌面版
- nRFgo Studio: Flash烧写工具（可用PC版nRF Connect代替）
- nRFutil: DFU工具，可通过pip安装（暂时[不支持64位python](https://github.com/NordicSemiconductor/pc-nrfutil/issues/213)）
- nRF Sniffer: 空中抓包工具，有硬件和软件两部分

## nRF5 SDK

- nRF5 SDK: 用于Nordic nRF51/52系列芯片的开发。
- SoftDevice: 是Nordic蓝牙协议栈的名称， SDK中包含了该版本支持的所有SoftDevice。

### SoftDevice

**(1) 第一种协议栈命名：** `Sxyz`

- x: 协议栈类型
  - x=1: BLE协议
  - x=2: ANT协议
  - x=3: 同时支持BLE&ANT协议
- y: BLE角色
  - y=1: 从设备peripheral，也称slave
  - y=2: 主设备central，也称master
  - y=3: 同时支持主设备和从设备
- z: 芯片类型
  - z=0: nRF51系统
  - z=2: nRF52系统

例如，S132表示同时支持主从设备的nRF51 BLE协议栈。

**(2) 第二种协议栈命名：**`Sxyz`

`x`同第(1)种规定，`yz`的值与芯片型号一样。

例如，S140代表这个协议栈专门用于nRF52840。由于52840 Flash空间很大，没有必要做各种细分的协议栈，S140协议栈是一个大而全的协议栈，包含蓝牙所有功能。

### 目录结构

- components: Nordic的SDK源代码
- examples: 应用实例
- external: 第三方库或源代码
- external_tools: 第三方软件工具，如cmsisconfig是一个sdk_config.h配置文件

SDK有一些deprecated目录，是为了兼容老产品；
SDK有一些experimental目录，是一些新特性或新实例代码，需要进行充分测试。


## Coding

```
    +------------------------------------------+
    |                Application               |
    +------------------------------------------+
    | +-------------+ +------------------------+
    | |             | |     SoftDevice API     |
    | | Application | +-------------+----------+
    | | Drivers     | | SoftDevice  | BLE      |
    | |             | | Manager     | Protocol |
    | |             | | Soc Library | Stack    |
    | +-------------+ +------------------------+
    +------------------------------------------+
    |                  CMSIS                   |
    +------------------------------------------+
    |                 nRF5 SoC                 |
    +------------------------------------------+
```

所有`SoftDevice API`都是以`sd_`开头，例如：

- 与BLE协议栈有关的API： `sd_ble_gap_adv_start(…)`
- 与外设相关的API：`sd_flash_write(…)`

### LOG

```
// 使用RTT作为LOG后端
#define NRF_LOG_BACKEND_RTT_ENABLED 1
// 使用UART作为LOG后端
#define NRF_LOG_BACKEND_UART_ENABLED 1
// 初始化log
log_init();
// 打印log
NRF_LOG_INFO("Demo started.\n");
```

### 广播

- 初始化

```
ble_stack_init();   //初始化协议栈，PHY~L2CAP相关设置
gap_params_init();  //配置GAP参数
gatt_init();        //初始化GATT
```

### 扫描

```
ble_stack_init();   //初始化协议栈，PHY~L2CAP相关设置
                    // ??? 为什么不用初始化GAP和GATT
```


**参考**

- [nRF5 SDK软件架构及softdevice工作原理](https://www.cnblogs.com/iini/p/9332463.html)
- [如何调试nRF5 SDK](https://www.cnblogs.com/iini/p/9279618.html)
