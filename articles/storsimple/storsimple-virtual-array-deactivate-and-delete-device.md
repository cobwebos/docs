---
title: 停用和删除 Microsoft Azure StorSimple 虚拟阵列 | Microsoft 文档
description: 介绍了如何使用该服务通过先停用再删除 StorSimple 设备的方式来删除该设备。
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: a929f5bc-03e2-4b01-b925-973db236f19f
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 8dea36f92b034f8c6cdb6875634848d37f4c6606
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23109875"
---
# <a name="deactivate-and-delete-a-storsimple-virtual-array"></a>停用和删除 StorSimple Virtual Array

## <a name="overview"></a>概述

停用 StorSimple 虚拟阵列时，会断开设备与相应 StorSimple Device Manager 服务之间的连接。 本教程介绍了如何完成以下操作：

* 停用设备 
* 删除已停用的设备

本文中的信息仅适用于 StorSimple 虚拟阵列。 有关 8000 系列的信息，请转到“如何[停用或删除设备](storsimple-deactivate-and-delete-device.md)”。

## <a name="when-to-deactivate"></a>何时停用？

停用是不可撤消的“永久性”操作。 不能将停用的设备再次注册到 StorSimple Device Manager 服务。 在以下应用场景中，可能需要停用并删除 StorSimple 虚拟阵列：

* **计划的故障转移**：设备处于联机状态并且你计划对此设备进行故障转移。 如果计划升级到更大的设备，则可能需要对设备进行故障转移。 数据所有权已转移且故障转移已完成后，会自动删除源设备。
* **未计划的故障转移**：设备处于脱机状态并且需要对此设备进行故障转移。 在灾难期间，当数据中心出现服务中断且主要设备已关闭时，可能会发生这种情况。 计划将设备故障转移到另一台设备。 数据所有权已转移且故障转移已完成后，会自动删除源设备。
* **解除授权**：你想要解除设备授权。 这需要先停用设备，然后将其删除。 停用设备后，将无法再访问本地存储的任何数据。 只能访问和恢复云中存储的数据。 如果计划在停用后保留设备数据，则应当在停用设备前创建所有数据的云快照。 使用此云快照可以在后面的某个阶段恢复所有数据。

## <a name="deactivate-a-device"></a>停用设备

若要停用设备，请执行以下步骤。

#### <a name="to-deactivate-the-device"></a>停用设备

1. 在服务中，转到“管理”>“设备”。 在“设备”边栏选项卡中，单击并选择要停用的设备。
   
    ![选择要停用的设备](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete7.png)
2. 在“设备仪表板”边栏选项卡中，单击“...更多”，并从列表中选择“停用”。
   
    ![单击“停用”](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete8.png)
3. 在“停用”边栏选项卡中，键入设备名称，并单击“停用”。 
   
    ![确认停用](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete1.png)
   
    停用过程将开始并且需要花费几分钟时间才能完成。
   
    ![停用正在进行](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete2.png)
4. 停用后，将刷新设备列表。
   
    ![停用完成](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete3.png)
   
    现在可以删除此设备了。

## <a name="delete-the-device"></a>删除设备

必须先停用设备，然后才能将其删除。 删除设备会将其从连接到该服务的设备列表中删除。 之后，该服务不再能够管理已删除的设备。 但是，与设备关联的数据将保留在云中。 然后此数据产生费用。

若要删除设备，请执行以下步骤。

#### <a name="to-delete-the-device"></a>删除设备

1. 在 StorSimple Device Manager 中，转到“管理”>“设备”。 在“设备”边栏选项卡中，选择要删除的已停用设备。
2. 在“设备仪表板”边栏选项卡中，单击“...更多”，并单击“删除”。
   
   ![选择要删除的设备](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete4.png)
3. 在“删除”边栏选项卡中，键入设备的名称以确认删除，并单击“删除”。 删除设备不会删除与该设备关联的云数据。 
   
   ![确认删除](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete5.png) 
4. 删除将开始并且需要花费几分钟时间才能完成。
   
   ![删除正在进行](./media/storsimple-virtual-array-deactivate-and-delete-device/deactivate-delete6.png)
   
    删除此设备后，可以查看已更新的设备列表。

## <a name="next-steps"></a>后续步骤

* 有关如何故障转移的信息，请转到 [StorSimple 虚拟阵列的故障转移和灾难恢复](storsimple-virtual-array-failover-dr.md)。

* 若要详细了解如何使用 StorSimple Device Manager 服务，请转到[使用 StorSimple Device Manager 服务管理 StorSimple 虚拟阵列](storsimple-virtual-array-manager-service-administration.md)。 

