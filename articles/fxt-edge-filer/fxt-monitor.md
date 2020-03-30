---
title: 监视 Azure FXT Edge Filer
description: 如何监视 Azure FXT 边缘文件服务器混合存储缓存的硬件状态
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 3f422339af2040ad81c585c0e193e6cb3667b135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72254872"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>监视 Azure FXT 边缘文件程序硬件状态

Azure FXT 边缘 Filer 混合存储缓存系统内置了多个机箱状态指示灯，以帮助管理员了解硬件的工作原理。

## <a name="system-health-status"></a>系统运行状况状态

要监视更高级别的缓存操作，请使用软件控制面板的**仪表板**页面，如[控制面板仪表板指南](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)中所述

## <a name="hardware-status-leds"></a>硬件状态 LED

本节介绍 Azure FXT 边缘文件程序硬件中内置的各种状态指示灯。

### <a name="hard-drive-status-leds"></a>硬盘状态 LED

![硬盘正面、水平、标注标签 2（左上角）、1（左下角）和 3（右侧）的图片](media/fxt-monitor/fxt-drive-callouts.png)

每个驱动器托架有两个状态 LED：活动指示器 （1） 和状态指示器 （2）。 

* 当驱动器正在使用时，活动 LED （1） 指示灯亮起。  
* 状态 LED （2） 使用下表中的代码指示驱动器的状况。

| 驱动状态 LED 状态              | 含义  |
|-------------------------------------|----------------------------------------------------------|
| 每秒闪烁绿色两次      | 识别驱动器*或* <br> 准备拆卸驱动器  |
| 关闭（未点亮）                         | 系统尚未完成启动*或* <br>驱动器已准备好被移除 |
| 闪烁绿色、琥珀色和关闭       | 预测驱动器故障   |
| 每秒呈琥珀色闪烁四次 | 驱动器失败   |
| 纯绿色                         | 驱动器已联机 |

驱动器 （3） 的右侧标有驱动器的容量和其他信息。

驱动器编号显示在驱动器之间的空间上。 在 Azure FXT Edge Filer 中，驱动器 0 为左上角的驱动器，驱动器 1 位于其正下方。 编号继续以该模式进行。 

![FXT 机箱中一个硬盘槽的图片，其中显示了驱动器编号和容量标签](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>左侧控制面板

左前控制面板具有各种状态 LED 指示灯 （1） 和大型照明系统运行状况指示灯 （2）。 

![左侧状态面板，左侧有 1 个标记状态指示灯，右侧 2 个标记大型系统运行状况指示灯](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>控制面板状态指示器 

如果系统中出现错误，左侧的状态指示灯将显示稳定的琥珀色光。 下表描述了错误的可能原因和解决方案。 

如果在尝试这些解决方案后仍有错误，[请与支持人员联系](fxt-support-ticket.md)以寻求帮助。 

| 图标 | 描述 | 添加状态 | 可能的解决方案 |
|----------------|---------------|--------------------|----------------------|
| ![驱动器图标](media/fxt-monitor/fxt-hd-icon.jpg) | 驱动器状态 | 驱动器错误 | 检查系统事件日志以确定驱动器是否有错误，或者 <br>运行适当的联机诊断测试;重新启动系统并运行嵌入式诊断 （ePSA），或 <br>如果驱动器在 RAID 阵列中配置，请重新启动系统并输入主机适配器配置实用程序 |
|![温度图标](media/fxt-monitor/fxt-temp-icon.jpg) | 温度状态 | 热误差 - 例如，风扇出现故障或环境温度超范围 | 检查以下可寻址条件： <br>冷却风扇丢失或出现故障 <br>系统盖、空气罩、内存模块空白或背面填充支架被移除 <br>环境温度过高 <br>外部气流受阻 |
|![电图标](media/fxt-monitor/fxt-electric-icon.jpg) | 电气状态 | 电气错误 - 例如，电压超范围、PSU 故障或电压调节器故障 |  检查系统事件日志或系统消息中是否出现特定问题。 如果出现 PSU 问题，请检查 PSU 状态 LED，并根据需要重新安装 PSU。 | 
|![内存图标](media/fxt-monitor/fxt-memory-icon.jpg) | 内存状态 | 内存错误 | 检查系统事件日志或系统消息中故障内存的位置;重新安装内存模块。 |
|![PCIe 图标](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe 状态 | PCIe 卡错误 | 重新启动系统;更新 PCIe 卡驱动程序;重新安装卡 |


### <a name="system-health-status-indicator"></a>系统运行状况指示器

左侧控制面板右侧的大照明按钮指示整体系统状态，并在系统 ID 模式下用作单元定位灯。

按系统运行状况和 ID 按钮在系统 ID 模式和系统运行状况模式之间切换。

|系统运行状况状态 | 条件 |
|-------------------------------------------|-----------------------------------------------|
| 实心蓝色 | 正常运行：系统处于打开状态，工作正常，系统 ID 模式未激活。 <br/>如果要切换到系统 ID 模式，请按系统运行状况和 ID 按钮。 |
| 闪烁的蓝色 | 系统 ID 模式处于活动状态。 如果要切换到系统运行状况模式，请按系统运行状况和系统 ID 按钮。 |
| 纯琥珀色 | 系统处于故障安全模式。 如果问题仍然存在，请联系[Microsoft 客户服务和支持](fxt-support-ticket.md)。 |
| 呈琥珀色闪烁 | 系统故障。 检查系统事件日志中有关特定错误消息。 有关由系统固件和监视系统组件的代理生成的事件和错误消息的信息，请参阅 qrl.dell.com 中的"错误代码查找"页。 |


