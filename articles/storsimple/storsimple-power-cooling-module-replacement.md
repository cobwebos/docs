---
title: "更换 StorSimple 设备上的 PCM | Microsoft Docs"
description: "说明如何拆卸和更换 StorSimple 设备上的电源和散热模块 (PCM)"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 24a158cb-0b79-4908-bb5a-431e48760f6a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/18/2016
ms.author: alkohli
ms.openlocfilehash: 2a956de58b279a013913631a077d7b03c6327f72
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>更换 StorSimple 设备上的电源和散热模块
## <a name="overview"></a>概述
Microsoft Azure StorSimple 设备中的电源和散热模块 (PCM) 由电源和散热风扇组成，它们通过主机箱和 EBOD 机箱进行控制。 只有一个 PCM 模型通过了对每个机箱的认证。 主机箱通过了 764 W PCM 认证，EBOD 机箱通过了 580 W PCM 认证。 尽管主机箱和 EBOD 机箱的 PCM 不同，但更换过程是相同的。

本教程介绍了如何完成以下操作：

* 拆卸 PCM
* 安装更换件 PCM

> [!IMPORTANT]
> 拆卸和更换 PCM 之前，请查看 [StorSimple 硬件组件更换](storsimple-hardware-component-replacement.md)中的安全信息。
> 
> 

## <a name="before-you-replace-a-pcm"></a>更换 PCM 之前
更换 PCM 之前，应注意以下几个重要问题：

* 如果 PCM 的电源发生故障，仍安装此发生故障的模块，但拔下电源线。 风扇将继续由机箱供电，并继续提供相应的散热作用。 如果风扇出现故障，需立即更换 PCM。
* 拆卸 PCM 之前，断开 PCM 电源，方法是关闭主开关（如果存在），或者拔下电源线。 系统将收到即将关闭电源的警告。
* 更换发生故障的 PCM 之前，请确保另一个 PCM 可以正常工作以继续执行系统操作。 必须尽快将发生故障的 PCM 更换为完全可操作的 PCM。
* PCM 模块的更换只需几分钟即可完成，但必须在 10 分钟内拆掉有故障的 PCM，以防过热。
* 请注意，出厂时随附的更换件 764 W PCM 模块中不包括备用电池模块。 执行更换之前，需要从发生故障的 PCM 中取出电池，并将其插入更换件模块。 有关详细信息，请参阅如何[取出并插入备用电池模块](storsimple-battery-replacement.md)。

## <a name="remove-a-pcm"></a>拆卸 PCM
准备好从 Microsoft Azure StorSimple 设备拆卸电源和散热模块 (PCM) 时，请按照以下说明操作。

> [!NOTE]
> 移除 PCM 之前，请验证是否具有正确的更换件（用于主机箱的 764 W 或者用于 EBOD 机箱的 580 W）。
> 
> 

#### <a name="to-remove-a-pcm"></a>拆卸 PCM
1. 在 Azure 经典门户中，单击“设备” > “维护” > “硬件状态”。 在“共享组件”检查 PCM 组件的状态，以确定哪个 PCM 发生了故障：
   
   * 如果 PCM 0 中的电源发生故障，则“PCM 0 中的电源”状态会显示为红色。
   * 如果 PCM 1 中的电源发生故障，则“PCM 1 中的电源”状态会显示为红色。
   * 如果 PCM 1 上的风扇出现故障，则“PCM 0 的散热 0”或“PCM 0 的散热 1”状态会显示为红色。
2. 在主机箱背面找到发生故障的 PCM。 如果运行的是 8600 模型，可通过查看显示于前面板 LED 显示屏的系统单元标识号识别主机箱。 主机箱上显示的默认单元 ID 是 **00**，而 EBOD 机箱上显示的默认单元 ID 是 **01**。 以下关系图和表对 LED 显示屏的前面板进行了说明。
   
    ![前 OPS 面板上的系统 ID](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **图 1** 设备的前面板  
   
   | 标签 | 说明 |
   |:--- |:--- |
   | 1 |“静音”按钮 |
   | 2 |系统电源 |
   | 3 |模块故障 |
   | 4 |逻辑故障 |
   | 5 |单元 ID 显示 |
3. 主机箱背面的监控指示器 LED 还可用于确定发生故障的 PCM。 请参阅以下关系图和表来了解如何使用 LED 找到发生故障的 PCM。 例如，如果对应**风扇故障**的 LED 灯亮，则风扇发生故障。 同样，如果对应**交流故障**的 LED 灯亮，则电源出现故障。 
   
    ![设备 PCM 监控指示器 LED 底板](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **图 2** PCM 背面（带指示器 LED）
   
   | 标签 | 说明 |
   |:--- |:--- |
   | 1 |交流电源故障 |
   | 2 |风扇故障 |
   | 3 |电池故障 |
   | 4 |PCM 正常 |
   | 5 |直流电源故障 |
   | 6 |电池功能正常 |
4. 请参阅 StorSimple 设备背面的以下关系图，找到发生故障的 PCM 模块。 PCM 0 位于左侧，而 PCM 1 位于右侧。 下表对各个模块进行了说明。
   
     ![设备主机箱模块底板](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **图 3** 设备背面（带插件模块） 
   
   | 标签 | 说明 |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |控制器 0 |
   | 4 |控制器 1 |
5. 关闭发生故障的 PCM 并断开电源线。 现在可拆卸 PCM 了。
6. 拇指按住闩锁，食指按住 PCM 把手一端，并一起挤压它们即可打开该把手。
   
    ![打开 PCM 把手](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **图 4** 打开 PCM 把手
7. 抓住该把手，并拆卸 PCM。
   
    ![拆卸设备 PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **图 5** 拆卸 PCM

## <a name="install-a-replacement-pcm"></a>安装更换件 PCM
按照以下说明在 StorSimple 设备中安装 PCM。 安装更换件 PCM 之前，确保已插入备用电池模块（仅适用于 764 W PCM）。 有关详细信息，请参阅如何[取出并插入备用电池模块](storsimple-battery-replacement.md)。

#### <a name="to-install-a-pcm"></a>安装 PCM
1. 验证是否有此机箱的正确更换件 PCM。 主机箱应使用 764 W PCM，EBOD 机箱应使用 580 W PCM。 不要尝试在主机箱中使用 580 W PCM，或在 EBOD 机箱中使用 764 W PCM。 下图显示了在 PCM 所附的标签上查找此信息的位置。
   
    ![设备 PCM 标签](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **图 6** PCM 标签
2. 检查机箱是否损坏，特别注意检查连接器。 
   
   > [!NOTE]
   > **如果有连接器管脚弯曲，请勿安装该模块。**
   > 
   > 
3. PCM 把手打开后，将模块滑入机箱。
   
    ![安装设备 PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **图 7** 安装 PCM
4. 手动关闭 PCM 把手。 把手闩锁啮合时，应该能听到咔嗒声。 
   
   > [!NOTE]
   > 若要确保连接器管脚已啮合，可轻轻拉动把手，而不释放闩锁。 如果 PCM 滑出，则意味着连接器啮合前已关闭该闩锁。
   > 
   > 
5. 将电源线连接到电源和 PCM。
6. 固定应变消除模块。 
7. 打开 PCM。
8. 验证更换是否成功：在 StorSimple Manager 服务的 Azure 经典门户中，导航到“设备” > “维护” > “硬件状态”。 在“共享组件”下，PCM 的状态应显示为绿色。 
   
   > [!NOTE]
   > 更换件 PCM 可能需要几分钟时间才能完全初始化。
   > 
   > 

## <a name="next-steps"></a>后续步骤
详细了解 [StorSimple 硬件组件更换](storsimple-hardware-component-replacement.md)。

