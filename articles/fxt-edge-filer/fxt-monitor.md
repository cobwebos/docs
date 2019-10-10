---
title: 监视 Azure FXT Edge 文件服务器
description: 如何监视 Azure FXT Edge 文件服务器混合存储缓存的硬件状态
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 3f422339af2040ad81c585c0e193e6cb3667b135
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254872"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>监视 Azure FXT Edge 文件服务器硬件状态

Azure FXT Edge 文件服务器混合存储缓存系统将多个状态灯内置于机箱，以帮助管理员了解硬件的工作方式。

## <a name="system-health-status"></a>系统运行状况

若要在更高级别上监视缓存操作，请使用 "软件控制面板" 的 "**仪表板**" 页，如控制面板的 "[仪表板" 指南](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)中所述

## <a name="hardware-status-leds"></a>硬件状态 Led

本部分介绍 Azure FXT Edge 文件服务器硬件中内置的各种状态灯。

### <a name="hard-drive-status-leds"></a>硬盘状态 Led

![硬盘正面、水平、带有标注标签2（左上角）、1（左下角）和3（右侧）的图片](media/fxt-monitor/fxt-drive-callouts.png)

每个驱动器托架都有两个状态 Led：活动指示器（1）和状态指示器（2）。 

* 驱动器正在使用时，活动 LED （1）指示灯亮起。  
* 状态 LED （2）指示驱动器的条件，使用下表中的代码。

| 驱动器状态 LED 状态              | 含义  |
|-------------------------------------|----------------------------------------------------------|
| 每秒闪烁绿色两次      | 正在识别驱动器*或* <br> 正在准备要删除的驱动器  |
| 关闭（unlit）                         | 系统尚未完成启动*或* <br>已准备好删除驱动器 |
| 闪烁绿色、琥珀色和熄灭       | 预测驱动器故障   |
| 每秒闪烁四次琥珀色 | 驱动器失败   |
| 稳定绿色                         | 驱动器处于联机状态 |

驱动器（3）的右侧标有驱动器容量和其他信息。

驱动器编号显示在驱动器之间的空间上。 在 Azure FXT Edge Filer 中，驱动器 0 为左上角的驱动器，驱动器 1 位于其正下方。 此模式中的编号继续。 

![FXT 机箱中一个硬盘槽的图片，其中显示了驱动器编号和容量标签](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>左侧控制面板

左前控制面板具有各种状态 LED 指示器（1）和较大的灯系统运行状况指示符（2）。 

![左侧状态面板，左侧有1个标签状态指示器，2则在右侧添加较大的系统健康指示器光源](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>控制面板状态指示器 

如果系统中存在错误，则左侧的状态指示器显示稳定的琥珀色指示灯。 下表描述了可能的错误原因和解决方法。 

如果尝试这些解决方案后仍出现错误，请[联系支持人员](fxt-support-ticket.md)获取帮助。 

| 图标 | 描述 | 添加状态 | 可能的解决方案 |
|----------------|---------------|--------------------|----------------------|
| ![驱动器图标](media/fxt-monitor/fxt-hd-icon.jpg) | 驱动器状态 | 驱动器错误 | 检查系统事件日志以确定驱动器是否有错误，或 <br>运行相应的联机诊断测试;重新启动系统并运行嵌入式诊断（ePSA），或者 <br>如果驱动器是在 RAID 阵列中配置的，请重新启动系统并输入主机适配器配置实用程序 |
|![温度图标](media/fxt-monitor/fxt-temp-icon.jpg) | 温度状态 | 温度错误-例如，风扇出现故障或环境温度超出范围 | 检查以下可寻址条件： <br>冷却风扇丢失或已失败 <br>系统盖、导风槽、内存模块为空或背面填充托架已删除 <br>环境温度过高 <br>外部气流受到阻碍 |
|![电力图标](media/fxt-monitor/fxt-electric-icon.jpg) | 电气状态 | 电气错误-例如，电压超出范围、失败的 PSU 或发生故障的电压调节器 |  检查系统事件日志或系统消息中是否有特定的问题。 如果出现 PSU 问题，请检查 PSU 状态 LED，并根据需要重新拔插 PSU。 | 
|![内存图标](media/fxt-monitor/fxt-memory-icon.jpg) | 内存状态 | 内存错误 | 检查系统事件日志或系统消息中是否有失败的内存;重新拔插内存模块。 |
|![PCIe 图标](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe 状态 | PCIe 卡错误 | 重新启动系统;更新 PCIe 卡驱动程序;重新安装卡 |


### <a name="system-health-status-indicator"></a>系统运行状况状态指示器

左侧控制面板右侧的 "大点" 按钮指示整体系统状态，并且在系统 ID 模式下用作单元定位灯。

按 "系统运行状况" 和 "ID" 按钮在系统 ID 模式和系统运行状况模式间切换。

|系统运行状况状态 | 条件 |
|-------------------------------------------|-----------------------------------------------|
| 稳定蓝色 | 正常操作：系统已打开，正常运行，系统 ID 模式处于非活动状态。 <br/>如果要切换到系统 ID 模式，请按 "系统运行状况" 和 "ID" 按钮。 |
| 蓝色闪烁 | 系统 ID 模式处于活动状态。 如果要切换到系统运行状况模式，请按 "系统运行状况和系统 ID" 按钮。 |
| 稳定琥珀色 | 系统处于故障安全模式。 如果问题仍然存在，请[与 Microsoft 客户服务和支持联系](fxt-support-ticket.md)。 |
| 琥珀色闪烁 | 系统错误。 检查系统事件日志中是否有特定的错误消息。 有关系统固件和监视系统组件的代理生成的事件和错误消息的信息，请参阅错误代码查找页面，网址为 qrl.dell.com。 |


