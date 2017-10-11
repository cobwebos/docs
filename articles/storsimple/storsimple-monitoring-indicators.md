---
title: "StorSimple 监视指示器 | Microsoft Docs"
description: "介绍了用来监视 StorSimple 设备状态的发光二极管 (LED) 和有声警报。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 0d44f94719bff0cb58b3727050598e55f4774e66
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>使用 StorSimple 监视指示器管理设备
## <a name="overview"></a>概述
StorSimple 设备包括了发光二极管 (LED) 和警报，可以使用它们监视 StorSimple 设备的模块和总体状态。 监视指示器位于设备的主机箱和 EBOD 机箱的硬件组件上。 监视指示器可以是 LED 或有声警报。

有三种用来指示模块状态的 LED 状态：绿色、从绿色闪烁到琥珀红、琥珀红。  

* 绿色 LED 表示正常运行状态。  
* 从绿色闪烁到琥珀红 LED 表示存在可能需要用户干预的非严重状况。  
* 琥珀红 LED 表示模块内存在严重故障。  

本文的剩余部分介绍了各种监视指示器 LED、它们在 StorSimple 设备上的位置、基于 LED 状态的设备状态以及任何关联的有声警报。

## <a name="front-panel-indicator-leds"></a>前面板指示器 LED
前面板（也称为*操作面板*）显示系统中所有模块的聚合状态。 StorSimple 主机箱和 EBOD 机箱的前面板是完全相同的，如下图所示。  

   ![设备前面板][1]

前面板包含以下指示器：  

1. “静音”按钮
2. 电源指示器 LED（绿色/琥珀红）
3. 模块故障指示器 LED（亮起红色-琥珀红/熄灭）
4. 逻辑故障指示器 LED（亮起红色-琥珀红/熄灭）
5. 单元 ID 显示  

设备的前面板 LED 与 EBOD 机箱的前面板 LED 之间的主要差异是 LED 显示屏上显示的**系统单元标识号**。 设备上显示的默认单元 ID 是 **00**，而 EBOD 机箱上显示的默认单元 ID 是 **01**。 当设备处于打开状态时，可以据此快速区分设备与 EBOD 机箱。 当设备处于关闭状态时，可以使用[打开新设备](storsimple-turn-device-on-or-off.md#turn-on-a-new-device)中提供的信息来区分设备与 EBOD 机箱。  

## <a name="front-panel-led-status"></a>前面板 LED 状态
可以使用下表识别设备或 EBOD 机箱的前面板上的 LED 指示的状态。  

| 系统电源 | 模块故障 | 逻辑故障 | 警报 | 状态 |
| --- | --- | --- | --- | --- |
| 琥珀红 |熄灭 |熄灭 |不适用 |交流电源断开，正在以备用电源运行；或者交流电源接通，并且控制器模块已移除。 |
| 绿色 |亮起 |亮起 |不适用 |操作面板通电 (5s) 测试状态 |
| 绿色 |熄灭 |熄灭 |不适用 |已通电，所有功能良好 |
| 绿色 |亮起 |不适用 |PCM 故障 LED、风扇故障 LED |任何 PCM 故障、风扇故障、温度过高或过低 |
| 绿色 |亮起 |不适用 |I/O 模块 LED |任何控制器模块故障 |
| 绿色 |亮起 |不适用 |不适用 |机箱逻辑故障 |
| 绿色 |闪烁 |不适用 |控制器模块上的模块状态 LED。 PCM 故障 LED、风扇故障 LED |安装了未知类型的控制器模块、I2C 总线故障、控制器模块重要产品数据 (VPD) 配置错误 |

## <a name="power-cooling-module-pcm-indicator-leds"></a>电源冷却模块 (PCM) 指示器 LED
电源冷却模块 (PCM) 指示器 LED 位于主机箱或 EBOD 机箱的背部，在每个 PCM 模块上。 本主题讨论了如何使用以下 LED 监视 StorSimple 设备的状态。  

* 主机箱的 PCM LED
* EBOD 机箱的 PCM LED

## <a name="pcm-leds-for-the-primary-enclosure"></a>主机箱的 PCM LED
StorSimple 设备有一个 764W PCM 模块和额外的电池。 下图显示了设备的 LED 面板。  

   ![主机箱上的 PCM LED][2]

LED 图例：

1. 交流电源故障
2. 风扇故障
3. 电池故障
4. PCM 正常
5. 直流电源故障
6. 电池良好  

PCM 的状态是在 LED 面板上指示的。 设备 PCM LED 面板有六个 LED。 其中四个 LED 显示电源和风扇的状态。 剩余的两个 LED 指示 PCM 中的备用电池模块的状态。 可以使用以下各表确定 PCM 的状态。  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>电源和风扇的 PCM 指示器 LED
| 状态 | PCM 正常（绿色） | 交流电源故障（琥珀色） | 风扇故障（琥珀色） | 直流电源故障（琥珀色） |
| --- | --- | --- | --- | --- |
| 没有交流电（到机箱） |熄灭 |熄灭 |熄灭 |熄灭 |
| 没有交流电（仅限此 PCM） |熄灭 |亮起 |熄灭 |亮起 |
| 交流电源已向 PCM 供电 - 正常 |亮起 |熄灭 |熄灭 |熄灭 |
| PCM 故障（风扇故障） |熄灭 |熄灭 |亮起 |不适用 |
| PCM 故障（安培数过高、电压过高、电流过大） |熄灭 |亮起 |亮起 |亮起 |
| PCM（风扇转速失调） |亮起 |熄灭 |熄灭 |亮起 |
| 备用模式 |闪烁 |熄灭 |熄灭 |熄灭 |
| PCM 固件下载 |熄灭 |闪烁 |闪烁 |闪烁 |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>备用电池的 PCM 指示器 LED
| 状态 | 电池良好（绿色） | 电池故障（琥珀色） |
| --- | --- | --- |
| 电池不存在 |熄灭 |熄灭 |
| 电池存在并已充电 |亮起 |熄灭 |
| 电池正在充电或处于维护放电状态 |闪烁 |熄灭 |
| 电池“软”故障（可恢复） |熄灭 |闪烁 |
| 电池“硬”故障（不可恢复） |熄灭 |亮起 |
| 电池已卸下 |闪烁 |熄灭 |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>EBOD 机箱的 PCM LED
EBOD 机箱有一个 580W PCM，没有额外的电池。 EBOD 机箱的 PCM 面板具有仅针对电源和风扇的指示器 LED。 下图显示了这些 LED。

   ![EBOD 机箱上的 PCM LED][3] 

可以使用下表确定 PCM 的状态。  

| 状态 | PCM 正常（绿色） | 交流电源故障（琥珀色） | 风扇故障（琥珀色） | 直流电源故障（琥珀色） |
| --- | --- | --- | --- | --- |
| 没有交流电（到机箱） |熄灭 |熄灭 |熄灭 |熄灭 |
| 没有交流电（仅限此 PCM） |熄灭 |亮起 |熄灭 |亮起 |
| 交流电源已向 PCM 供电 - 正常 |亮起 |熄灭 |熄灭 |熄灭 |
| PCM 故障（风扇故障） |熄灭 |熄灭 |亮起 |X |
| PCM 故障（安培数过高、电压过高、电流过大） |熄灭 |亮起 |亮起 |亮起 |
| PCM（风扇转速失调） |亮起 |熄灭 |熄灭 |亮起 |
| 备用模式 |闪烁 |熄灭 |熄灭 |熄灭 |
| PCM 固件下载 |熄灭 |闪烁 |闪烁 |闪烁 |

## <a name="controller-module-indicator-leds"></a>控制器模块指示器 LED
StorSimple 设备包含了用于主控制器和 EBOD 控制器模块的 LED   

### <a name="monitoring-leds-for-the-primary-controller"></a>主控制器的监视 LED
下图可帮助你识别主控制器上的 Led。 （所有组件列出以帮助你识别方向。）  

   ![监视 LED - 主控制器][4]

可以使用下表来确定控制器模块是否正常运行。  

### <a name="controller-indicator-leds"></a>控制器指示器 LED
| LED | 说明 |
| --- | --- |
| ID LED（蓝色） |指示所标识的模块。 如果蓝色 LED 在某个正在运行的控制器上闪烁，则该控制器是活动控制器，另一个是备用控制器。 有关详细信息，请参阅[识别设备上的活动控制器](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)。 |
| 故障 LED（琥珀色） |表示控制器中存在故障。 |
| 正常 LED（绿色） |稳定的绿色表示控制器正常。 闪烁的绿色表示存在控制器 VPD 配置错误。 |
| SAS 活动 LED（绿色） |稳定的绿色表示连接中当前没有活动。 闪烁的绿色表示连接中有正在进行的活动。 |
| 以太网状态 LED |右侧指示链路/网络活动：稳定的绿色表示链路处于活动状态，闪烁的绿色表示存在网络活动。 左侧指示网络速度：黄色表示 1000 Mb/s，绿色表示 100 Mb/s，熄灭表示 10 Mb/s。 即使未启用网络接口，此灯也可能会闪烁，具体取决于组件型号。 |
| POST LED |当打开控制器时用来指示启动进度。 如果 StorSimple 设备未能启动，此 LED 可帮助 Microsoft 支持部门来识别在启动过程中的哪个点发生了故障。 |

> [!IMPORTANT]
> 如果故障 LED 亮起，则表示控制器模块存在问题，重新启动控制器也许能够解决问题。 如果重新启动控制器没有解决此问题，请联系 Microsoft 支持部门。  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>EBOD（EBOD 机箱）的监视 LED
每台 6 Gb/s SAS EBOD 控制器都具有用于指示其状态的 LED，如下图所示。  

  ![监视 LED - EBOD 机箱][5]

可以使用下表来确定 EBOD 控制器模块是否正常运行。  

### <a name="ebod-controller-module-indicator-leds"></a>EBOD 控制器模块指示器 LED
| 状态 | I/O 模块正常（绿色） | I/O 模块故障（琥珀色） | 主机端口活动（绿色） |
| --- | --- | --- | --- |
| 控制器模块正常 |亮起 |熄灭 |- |
| 控制器模块故障 |熄灭 |亮起 |- |
| 无外部主机端口连接 |- |- |熄灭 |
| 外部主机端口连接 – 无活动 |- |- |亮起 |
| 外部主机端口连接 – 有活动 |- |- |闪烁 |
| 控制器模块元数据错误 |闪烁 |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>主机箱和 EBOD 机箱的磁盘驱动器指示器 LED
StorSimple 设备在主机箱和 EBOD 机箱中都有磁盘驱动器。 每个磁盘驱动器都包含监视指示器 LED，如本部分所述。 

对于磁盘驱动器，驱动器状态由安装在每个驱动器承载模块的前部的一个绿色 LED 和一个琥珀红 LED 予以指示。 下图显示了这些 LED。

  ![磁盘驱动器 LED][6]

可以使用下表来确定每个磁盘驱动器的状态，该状态又影响总体的前面板 LED 状态。  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>EBOD 机箱的磁盘驱动器指示器 LED
| 状态 | 活动正常 LED（绿色） | 故障 LED（琥珀红） | 关联的操作面板 LED |
| --- | --- | --- | --- |
| 未安装驱动器 |熄灭 |熄灭 |无 |
| 驱动器已安装且正常运行 |有活动时闪烁亮起/熄灭 |X |无 |
| SCSI 机箱服务 (SES) 设备标识已设置 |亮起 |闪烁亮起 1 秒/熄灭 1 秒 |无 |
| SES 设备故障位已设置 |亮起 |亮起 |逻辑故障（红色） |
| 电源控制电路故障 |熄灭 |亮起 |模块故障（红色） |

## <a name="audible-alarms"></a>有声警报
StorSimple 设备包含了与主机箱和 EBOD 机箱关联的有声警报。 有声警报位于两个机箱的前面板（也称为操作面板）上。 有声警报指示何时存在故障状况。 下列状况会激活警报：  

* 风扇故障
* 电压超出范围
* 温度过高或过低状况
* 热超限
* 系统故障
* 逻辑故障
* 电源故障
* 电源冷却模块 (PCM) 的移除  

下表描述了各种警报状态。  

### <a name="alarm-states"></a>警报状态
| 警报状态 | 操作 | 按下了静音按钮时的操作 |
| --- | --- | --- |
| S0 |正常模式：静音 |鸣叫两声 |
| S1 |故障模式：亮起 1 秒/熄灭 1 秒 |转变为 S2 或 S3（请参阅“注意”部分） |
| S2 |提醒模式：间歇性鸣叫 |无 |
| S3 |静音模式：静音 |无 |
| S4 |严重故障模式：持续告警 |不可用：静音未处于活动状态 |

> [!NOTE]
> * 在警报状态 S1 下，如果在 2 分钟内没有按静音按钮，则状态会自动转变为 S2 或 S3。  
> * 清除故障状况后，警报状态 S1 到 S4 将恢复为 S0。  
> * 严重故障状态 S4 可以由任何其他状态转变而来。  


可以通过按下操作面板上的静音按钮使有声警报静音。 如果没有手动操作静音开关，则两分钟后会自动静音。 当警报静音后，它会继续间歇性地短暂鸣叫以指示问题仍然存在。 当所有问题都清除后，警报将静音。

下表描述了各种警报状况。

### <a name="alarm-conditions"></a>警报状况
| 状态 | 严重性 | 警报 | 操作面板 LED |
| --- | --- | --- | --- |
| PCM 警报 – 单个 PCM 的直流电源断开 |故障 – 未损失冗余 |S1 |模块故障 |
| PCM 警报 – 单个 PCM 的直流电源断开 |故障 – 损失了冗余 |S1 |模块故障 |
| PCM 风扇故障 |故障 – 损失了冗余 |S1 |模块故障 |
| SBB 模块检测到 PCM 故障 |故障 |S1 |模块故障 |
| 移除了 PCM |配置错误 |无 |模块故障 |
| 机箱配置错误 |故障 – 严重 |S1 |模块故障 |
| 低等警告温度警报 |警告 |S1 |模块故障 |
| 高等警告温度警报 |警告 |S1 |模块故障 |
| 温度过高警报 |故障 – 严重 |S1 |模块故障 |
| I2C 总线故障 |故障 – 损失了冗余 |S1 |模块故障 |
| 操作面板通信错误 (I2C) |故障 – 严重 |S1 |模块故障 |
| 控制器错误 |故障 – 严重 |S1 |模块故障 |
| SBB 接口模块故障 |故障 – 严重 |S1 |模块故障 |
| SBB 接口模块故障 – 未剩余可以正常运行的模块 |故障 – 严重 |S4 |模块故障 |
| 移除了 SBB 接口模块 |警告 |无 |模块故障 |
| 驱动器电源控制故障 |警告 – 驱动器电源未断开 |S1 |模块故障 |
| 驱动器电源控制故障 |故障 – 严重；驱动器电源断开 |S1 |模块故障 |
| 移除了驱动器 |警告 |无 |模块故障 |
| 电力不足 |警告 |无 |模块故障 |

## <a name="next-steps"></a>后续步骤
详细了解 [StorSimple 硬件组件和状态](storsimple-8000-monitor-hardware-status.md)。

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


