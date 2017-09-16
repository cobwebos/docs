---
title: "更换 StorSimple 8000 系列设备上的磁盘驱动器 | Microsoft Docs"
description: "说明如何更换 StorSimple 主机箱或 EBOD 机箱上的磁盘驱动器。"
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
ms.date: 073/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: a8616eb51b177a9447a7c466c9d934b9139afedf
ms.contentlocale: zh-cn
ms.lasthandoff: 09/02/2017

---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>更换 StorSimple 8000 系列设备上的磁盘驱动器

## <a name="overview"></a>概述
本教程介绍如何移除和更换 Microsoft Azure StorSimple 设备上功能异常或发生故障的硬盘驱动器。 若要更换磁盘驱动器，需执行以下操作：

* 取消防伪锁
* 移除磁盘驱动器
* 安装更换用磁盘驱动器

> [!IMPORTANT]
> 移除和更换磁盘驱动器之前，请查看 [StorSimple 硬件组件更换](storsimple-8000-hardware-component-replacement.md)中的安全信息。
 

## <a name="disengage-the-antitamper-lock"></a>取消防伪锁
此过程说明在更换磁盘驱动器时如何卡入和取消 StorSimple 设备上的防伪锁。 防伪锁装在驱动器托架把手中，可以通过把手的闩锁部分中的小孔接触。 驱动器在提供给用户时，防伪锁处于锁定位置。

#### <a name="to-unlock-the-antitamper-lock"></a>打开防伪锁
1. 小心地将锁匙（Microsoft 提供的“防伪”T10 起子）插入把手中的小孔，直至锁槽中。 
   
   如果防伪锁被激活，小孔中会亮起红色指示灯。
  
    ![锁定的磁盘驱动器](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **图 1** 防伪锁卡入到位
   
   | 标签 | 说明 |
   |:--- |:--- |
   | 1 |指示灯小孔 |
   | 2 |防伪锁 |
2. 以逆时针方向旋转锁钥，直至锁钥上方小孔中的红色指示灯不亮。
3. 拔出锁钥。
   
    ![解锁磁盘驱动器](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **图 2** 解锁的磁盘驱动器
4. 现在可以移除磁盘驱动器。

按相反顺序执行相关步骤，将锁卡入。

## <a name="remove-the-disk-drive"></a>移除磁盘驱动器
StorSimple 设备支持类似 RAID 10 的存储空间配置。 这意味着它可以正常使用一个故障磁盘、固态驱动器 (SSD) 或硬盘驱动器 (HDD)。

> [!IMPORTANT]
> * 如果系统存在多个故障磁盘，请勿同时从系统中移除多个 SSD 或 HDD。 这样做可能导致数据丢失。
> * 请确保将更换用 SSD 置于以前装有 SSD 的插槽中。 同样，请将更换用 HDD 置于以前装有 HDD 的插槽中。
> * 在 Azure 门户中，插槽从 0 到 11 编号。 因此，如果门户显示设备的插槽 2 中的磁盘发生故障，则请在第三个插槽（从左上开始数）中查找故障磁盘。
> 
> 

可以在系统运行时移除和更换驱动器。

#### <a name="to-remove-a-drive"></a>移除驱动器
1. 若要确定失败的磁盘，请在 Azure 门户中，转到设备的“设置”>“硬件运行状况”。 由于磁盘故障可能发生在主机箱和/或 EBOD 机箱（如果使用的是 8600 机型）中，因此请在“共享组件”和“EBOD 共享组件”下查看磁盘状态。 不管是在哪个机箱中，故障磁盘都会显示为红色状态。
2. 找到主机箱或 EBOD 机箱前面的驱动器。 
3. 如果该磁盘已解锁，则继续执行下一步。 如果该磁盘已锁定，请按[取消防伪锁](#disengage-the-antitamper-lock)中的步骤将其解锁。
4. 按驱动器托架模块上的黑色闩锁，拉动驱动器托架把手，将其从底盘前部拉出。
   
    ![放开磁盘驱动器把手](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **图 3** 放开驱动器把手
5. 当驱动器托架把手完全展开时，将驱动器托架滑出底盘。 
   
    ![将磁盘滑出磁盘驱动器](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **图 4** 将磁盘驱动器滑出托架

## <a name="install-the-replacement-disk-drive"></a>安装更换用磁盘驱动器
将 StorSimple 设备中发生故障的驱动器移除后，请按此过程将其更换为新驱动器。

#### <a name="to-insert-a-drive"></a>插入驱动器
1. 确保驱动器托架把手已完全展开，如下图所示。
   
    ![把手已展开的磁盘驱动器](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **图 5** 把手已展开的驱动器
2. 将驱动器托架滑入底盘中。
   
    ![将磁盘滑入磁盘驱动器托架中](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **图 6** 将驱动器托架滑入底盘中
3. 插入驱动器托架后，合上驱动器托架把手，同时继续将驱动器托架推入底盘中，直到驱动器托架把手卡入锁定位置。
4. 使用 Microsoft（防伪 Torx 起子）提供的锁钥将锁定螺丝顺时针旋转四分之一圈，使托架把手固定到位。
5. 验证更换是否成功、驱动器是否可正常工作。 访问 Azure 门户并导航到“设备设置” > “硬件运行状况”。 在“共享组件”或“EBOD 共享组件”下，驱动器状态应为绿色，表示一切正常。

   
   > [!NOTE]
   > 更换后，可能需要数小时磁盘状态才会变为绿色。
  
## <a name="next-steps"></a>后续步骤
详细了解 [StorSimple 硬件组件更换](storsimple-8000-hardware-component-replacement.md)。


