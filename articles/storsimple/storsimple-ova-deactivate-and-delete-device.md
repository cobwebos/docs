---
title: "停用和删除 StorSimple Virtual Array | Microsoft Docs"
description: "介绍了如何使用该服务通过先停用再删除 StorSimple 设备的方式来删除该设备。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: bf5ddb32-da4b-446f-ab91-215e9020e1c8
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/20/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 87aa287acff47ae471830ef09bf674e2c97abddd


---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>停用和删除 StorSimple Virtual Array
## <a name="overview"></a>概述
停用 StorSimple Virtual Array 时，将会断开设备与相应 StorSimple Manager 服务之间的连接。 停用是不可撤消的“永久性”操作。 停用的设备不能再次注册到 StorSimple Manager 服务。

在以下应用场景中，可能需要停用并删除 StorSimple 虚拟设备：

* 你的设备处于联机状态并且你计划对此设备进行故障转移。 如果计划升级到更大的设备，则可能需要执行此操作。 在传输设备数据并完成故障转移后，可以删除设备。
* 你的设备处于脱机状态并且你计划对此设备进行故障转移。 当因为数据中心内发生中断、主设备关闭而发生灾难时，可能会发生此情况。 你计划将设备故障转移到另一台设备。 在传输设备数据并完成故障转移后，可以删除设备。
* 你希望使设备退役然后将其删除。 

当停用设备后，存储在本地的任何数据都将不再可访问。 只有存储在云中的数据可以恢复。 如果计划在停用后保留设备数据，则应当在停用设备前创建所有数据的云快照。 这样，你将可以在后面的某个阶段恢复所有数据。

本教程介绍了如何完成以下操作：

* 停用设备 
* 删除已停用的设备

## <a name="deactivate-a-device"></a>停用设备
可以执行以下步骤来停用设备。

#### <a name="to-deactivate-the-device"></a>停用设备
1. 转到“设备”页。 选择希望停用的设备。
   
    ![选择要停用的设备](./media/storsimple-ova-deactivate-and-delete-device/deactivate1m.png)
2. 在页面底部，单击“停用”。
   
    ![单击“停用”](./media/storsimple-ova-deactivate-and-delete-device/deactivate2m.png)
3. 将显示确认消息。 单击“是”以继续。 
   
    ![确认停用](./media/storsimple-ova-deactivate-and-delete-device/deactivate3m.png)
   
    停用过程将开始并且需要花费几分钟时间才能完成。
   
    ![停用正在进行](./media/storsimple-ova-deactivate-and-delete-device/deactivate4m.png)
4. 在停用后，设备列表将刷新。 
   
    ![停用完成](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)
   
    现在可以删除此设备了。 

## <a name="delete-the-device"></a>删除设备
要删除设备，必须先将其停用。 删除设备会将其从连接到该服务的设备列表中删除。 之后，该服务不再能够管理已删除的设备。 不过，与设备关联的数据将保留在云中。 请注意，此数据将会产生费用。 

可以完成以下步骤来删除设备：

#### <a name="to-delete-the-device"></a>删除设备
1. 在 StorSimple Manager 服务“设备”页上，选择要删除的已停用设备。
   
   ![选择要删除的设备](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)
2. 在页面的底部，单击“删除”。
   
   ![单击“删除”](./media/storsimple-ova-deactivate-and-delete-device/deactivate6m.png)
3. 系统将提示进行确认。 键入设备名称来确认设备删除。 注意，删除设备不会删除与该设备关联的云数据。 单击对号图标以继续。
   
   ![确认删除](./media/storsimple-ova-deactivate-and-delete-device/deactivate7m.png) 
4. 可能需要花费几分钟时间才能删除设备。 
   
   ![删除正在进行](./media/storsimple-ova-deactivate-and-delete-device/deactivate8m.png)
   
    删除设备后，设备列表将刷新。
   
   ![删除完成](./media/storsimple-ova-deactivate-and-delete-device/deactivate9m.png)

## <a name="next-steps"></a>后续步骤
* 若要了解有关如何使用 StorSimple Manager 服务的详细信息，请转到[使用 StorSimple Manager 服务管理 StorSimple Virtual Array](storsimple-ova-manager-service-administration.md)。 




<!--HONumber=Nov16_HO3-->


