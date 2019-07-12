---
title: 监视 Azure FXT 边缘文件管理器
description: 如何监视 Azure FXT 边缘文件管理器混合存储缓存的硬件状态
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: e6afd90c4e5a1b0759b3de7789ec37db4c04f2c7
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827443"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>监视 Azure FXT 边缘文件管理器硬件状态

Azure FXT 边缘文件管理器混合存储缓存系统具有多个内置于机箱，可帮助管理员了解了解硬件的运行状况的状态指示灯。

## <a name="system-health-status"></a>系统运行状况状态

若要监视高级别上的缓存操作，使用该软件控制面板的**仪表板**页上，如中所述[控件面板仪表板指南](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>硬件状态 Led

本部分介绍内置于 Azure FXT 边缘文件服务器硬件的各种状态指示灯。

### <a name="hard-drive-status-leds"></a>硬盘驱动器状态 Led

![硬盘驱动器正面、 水平、 带标注标签 2 的图片 （左上的角），1 （左下角） 和 3 （右侧）](media/fxt-monitor/fxt-drive-callouts.png)

每个驱动器承运人有两个状态指示灯： 活动指示器 （1） 和状态指示器 (2)。 

* 活动 LED (1) 灯时驱动器正在使用中。  
* 状态 LED (2) 通过使用下表中的代码指示在驱动器的条件。

| 驱动器状态 LED 状态              | 含义  |
|-------------------------------------|----------------------------------------------------------|
| 每秒两次绿色闪烁      | 标识驱动器*或* <br> 删除准备驱动器  |
| 关闭 （未亮起）                         | 系统未完成启动*或* <br>驱动器已准备好要删除 |
| 闪烁的绿色、 琥珀色，并关闭       | 预测的驱动器故障   |
| 每秒四次闪烁琥珀色 | 驱动器出现故障   |
| 稳定的绿色                         | 驱动器处于联机状态 |

驱动器 (3) 的右侧都标有驱动器的容量和其他信息。

驱动器编号显示在驱动器之间的空间上。 在 Azure FXT Edge Filer 中，驱动器 0 为左上角的驱动器，驱动器 1 位于其正下方。 编号将继续在该模式中。 

![FXT 机箱中一个硬盘槽的图片，其中显示了驱动器编号和容量标签](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>左侧的控件面板

左前控制面板具有各种状态 LED 指示灯 （1） 和大型亮起的系统运行状况指示符 (2)。 

![左边的状态窗格中，标签上的左窗格和标签右侧浅大型系统运行状况指示符的 2 的状态指示器的 1](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>控件面板状态指示器 

在左侧的状态指示符显示稳定琥珀色指示灯在该系统中是否存在错误。 下表描述了可能的原因和解决方案的错误。 

如果仍在尝试使用这些解决方案时遇到错误[请联系支持人员](fxt-support-ticket.md)有关的帮助。 

| 图标 | 描述 | 添加状态 | 可能的解决方案 |
|----------------|---------------|--------------------|----------------------|
| ![驱动器图标](media/fxt-monitor/fxt-hd-icon.jpg) | 驱动器状态 | 驱动器错误 | 检查系统事件日志以确定驱动器是否有错误，或 <br>运行相应的在线诊断测试;重新启动系统并运行嵌入式的诊断 (ePSA)，或 <br>如果在 RAID 阵列，则将配置驱动器，重新启动系统并输入主机适配器配置实用程序 |
|![温度图标](media/fxt-monitor/fxt-temp-icon.jpg) | 温度状态 | 热感错误-例如，风扇发生故障或环境温度超出范围 | 检查可寻址满足以下条件： <br>冷却风扇缺失或已失败 <br>系统的封面，以无线方式罩内存模块保留为空，或后删除填充托架 <br>环境温度过高 <br>外部空气流通挡住 |
|![电力图标](media/fxt-monitor/fxt-electric-icon.jpg) | 电源状态 | 电力错误-例如，电压超出范围，失败 PSU 或失败的电压 regulator |  检查系统事件日志或系统消息的特定问题。 如果没有 PSU 问题，检查 PSU 状态 LED，并且如果需要重新 PSU。 | 
|![内存图标](media/fxt-monitor/fxt-memory-icon.jpg) | 内存状态 | 内存不足错误 | 检查系统事件日志或系统消息的失败内存; 位置将内存模块的重新。 |
|![PCIe 图标](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe 状态 | PCIe 卡错误 | 重新启动系统;更新 PCIe 卡驱动程序;重新安装卡 |


### <a name="system-health-status-indicator"></a>系统运行状况状态指示符

左侧的控件面板右侧的大文字的按钮指示总体系统状态，也用作在系统 ID 模式下的单元定位器光。

按系统运行状况和 ID 按钮系统 ID 模式和系统运行状况模式之间进行切换。

|系统运行状况状态为 | 条件 |
|-------------------------------------------|-----------------------------------------------|
| 纯色的蓝色 | 正常操作： 系统已开启，通常情况下，操作系统和系统 ID 模式未处于活动状态。 <br/>如果你想要切换到系统 ID 模式，请按系统运行状况和 ID 按钮。 |
| 闪烁的蓝色 | 系统 ID 模式处于活动状态。 如果你想要切换到系统运行状况模式，按系统运行状况和 ID 系统按钮。 |
| 琥珀色 | 系统为防故障模式。 如果问题仍然存在，请[请联系 Microsoft 客户服务和支持](fxt-support-ticket.md)。 |
| 闪烁的琥珀色 | 系统故障。 检查系统事件日志以特定的错误消息。 有关生成系统固件和监视系统组件的代理的事件和错误消息的信息，请参阅 qrl.dell.com 的错误代码查找页面。 |


