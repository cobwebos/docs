---
title: "更换 Microsoft Azure StorSimple 8000 系列设备上的电池 | Microsoft Docs"
description: "介绍如何移除、替换和维护 StorSimple 设备上的备用电池模块。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 174a3163082594ea6a49b7f5a78857848f8f0566
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>更换 StorSimple 设备上的备用电池模块

## <a name="overview"></a>概述
Microsoft Azure StorSimple 设备上的主机箱电源和散热模块 (PCM) 使用额外的电池组。 该电池组可在主机箱失去 AC 电源时供电，确保 StorSimple 设备能够保存数据。 此电池组称为“备用电池模块”。 备用电池模块仅存在于 StorSimple 设备的主机箱中（EBOD 机箱不含备用电池模块）。

本教程介绍了如何完成以下操作：

* 移除备用电池模块
* 安装新的备用电池模块
* 维护备用电池模块

> [!IMPORTANT]
> 移除和更换备用电池模块之前，请查看 [StorSimple 硬件组件更换简介](storsimple-8000-hardware-component-replacement.md)中的安全信息。


## <a name="remove-the-backup-battery-module"></a>移除备用电池模块
StorSimple 设备的备用电池模块是可现场更换的单元。 将其安装在 PCM 中之前，电池模块应存放在原包装中。 按以下步骤移除备用电池。

#### <a name="to-remove-the-backup-battery-module"></a>移除备用电池模块
1. 在 Azure 门户中，转到“StorSimple Device Manager 服务”边栏选项卡。 转到“设备”，然后从设备列表中选择设备。 导航到“监视器” > “硬件运行状况”。 在“共享组件”下，查看电池的状态。
2. 确定其中的电池发生故障的 PCM。 图 1 显示 StorSimple 设备的背面。
   
    ![设备主机箱模块底板](./media/storsimple-battery-replacement/IC740994.png)
   
    **图 1** 主设备的背面，显示 PCM 和控制器模块
   
   | 标签 | 说明 |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |控制器 0 |
   | 4 |控制器 1 |
   
    如图 2 中的编号 3 所示，与“电池故障”相对应的 PCM 0 上的监控指示灯 LED 应亮起。
   
    ![设备 PCM 监控指示灯 LED 底板](./media/storsimple-battery-replacement/IC740992.png)
   
    **图 2** PCM 的背面，显示监控指示灯 LED
   
   | 标签 | 说明 |
   |:--- |:--- |
   | 1 |交流电源故障 |
   | 2 |风扇故障 |
   | 3 |电池故障 |
   | 4 |PCM 正常 |
   | 5 |直流电源故障 |
   | 6 |电池功能正常 |
3. 若要移除电池发生故障的 PCM，请按[移除 PCM](storsimple-power-cooling-module-replacement.md#remove-a-pcm) 中的步骤操作。
4. 移除 PCM 之后，请抬起并旋转电池模块把手（如下图所示），并向上拉将电池移除。
   
    ![将电池从 PCM 移除](./media/storsimple-battery-replacement/IC741019.png)
   
    **图 3** 将电池从 PCM 移除
5. 将模块置于现场可更换的部件包装中。
6. 将有缺陷的装置退回 Microsoft 进行适当的维修和处理。

## <a name="install-a-new-backup-battery-module"></a>安装新的备用电池模块
执行以下步骤，在 StorSimple 设备的主机箱的 PCM 中安装更换用电池模块。

#### <a name="to-install-the-battery-module"></a>安装电池模块
1. 按正确方向将备用电池模块置于 PCM 中。
2. 按下电池模块把手，直至连接器就位。
3. 按[更换 StorSimple 设备上的电源和散热模块](storsimple-power-cooling-module-replacement.md)中的指导更换主机箱中的 PCM。
4. 更换完成后，在 Azure 门户中，转到设备，然后转到“监视器” > “硬件运行状况”。 验证电池的状态，确保安装成功。 状态为绿色表示电池功能正常。

## <a name="maintain-the-backup-battery-module"></a>维护备用电池模块
在 StorSimple 设备中，备用电池模块在断电时为控制器供电。 有了该模块，StorSimple 设备就可以以可控方式在关机前保存关键数据。 由于 PCM 中有两个完全充电的电池，因此系统可以应对连续断电两次的情况。

在 Azure 门户中，“监视器”边栏选项卡下的“硬件运行状况”指示电池是否功能不良，以及电池寿命是否即将到头。 电池状态由“共享组件”下的“PCM 0 中的电池”或“PCM 1 中的电池”指示。 如果电池寿命即将到头，此边栏选项卡会显示“已降级”状态；如果电池寿命已到头，此边栏选项卡会显示“故障”状态。

> [!NOTE]
> 电池可能会在只需充电的情况下报告“故障”。


如果出现“已降级”状态，则建议执行以下步骤的操作：

* 系统可能最近遇到断电的情况，或者电池可能正在进行定期维护。 继续操作之前，请观察系统 12 小时。
  
  * 如果已连续 12 小时连接到 AC 电源且控制器和 PCM 在运行，而状态仍为“已降级”，则需更换电池。 请[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)，了解如何更换备用电池模块。
  * 如果状态在 12 小时后变为“正常”，说明电池功能正常，只需进行维护性充电。
* 如果并没有发生 AC 电源中断的情况，同时 PCM 已启用并连接到 AC 电源，则需更换电池。 [联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)，订购更换用备用电池模块。

> [!IMPORTANT]
> 请按国家和地区法规处理出现故障的电池。

## <a name="next-steps"></a>后续步骤
详细了解 [StorSimple 硬件组件更换](storsimple-8000-hardware-component-replacement.md)。

