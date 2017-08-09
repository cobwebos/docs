---
title: "停用和删除 StorSimple 8000 系列设备 | Microsoft Docs"
description: "介绍了如何使用该服务通过先停用再删除 StorSimple 设备的方式来删除该设备。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 3c00867a29cf8343a57e74e2aabe3971ae6837af
ms.contentlocale: zh-cn
ms.lasthandoff: 08/03/2017

---
# <a name="deactivate-and-delete-a-storsimple-device"></a>停用和删除 StorSimple 设备

## <a name="overview"></a>概述

本文介绍如何停用和删除已连接到 StorSimple 设备管理器服务的 StorSimple 设备。 本文中的指导仅适用于 StorSimple 8000 系列设备，包括 StorSimple 云设备。 如果使用的是 StorSimple 虚拟阵列，请参阅[停用和删除 StorSimple 虚拟阵列](storsimple-virtual-array-deactivate-and-delete-device.md)。

停用服务器时，会断开设备与相应 StorSimple 设备管理器服务之间的连接。 用户可能希望停用某个 StorSimple 设备（例如，用户需要更换或升级设备，或者不再使用 StorSimple）。 如果出现这种情况，需要先停用设备，然后将其删除。

当停用设备后，存储在本地设备上的任何数据都不再可访问。 只能恢复与存储在云中的设备关联的数据。

> [!WARNING]
> 停用是不可撤消的“永久性”操作。 停用的设备不能注册到 StorSimple 设备管理器服务，除非将其重置为出厂默认设置。
>
> 出厂重置过程会删除以本地方式存储在设备上的所有数据。 因此，必须在停用设备之前创建所有数据的云快照。 使用此云快照可以在后面的某个阶段恢复所有数据。

阅读本教程之后，你将能够：

* 停用设备并删除数据。
* 停用设备并保留数据。

> [!NOTE]
> 停用 StorSimple 物理设备或云设备之前，请停止或删除依赖于该设备的客户端与主机。


## <a name="deactivate-and-delete-data"></a>停用和删除数据

若要彻底删除设备，不想在设备上保留数据，则请完成以下步骤。

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>停用设备并删除数据

1. 在停用设备之前，必须删除与设备关联的所有卷容器（以及卷）。 只有在删除关联的备份后，才能删除卷容器。

    > [!NOTE]
    > 停用 StorSimple 物理设备或云设备之前，请确保已删除卷容器中的数据实际已从设备中删除。 可以监视云使用量图表，当看到由于删除了备份云使用量下降时，便可以继续停用设备。 如果在此下降发生之前停用设备，数据将闲置在存储帐户中并累算费用。

2. 通过以下操作停用设备：
   
   1. 转到 StorSimple Device Manager 服务并单击“设备”。 在“设备”边栏选项卡中选择要停用的设备，单击右键，单击“停用”。

        ![停用 StorSimple 设备](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. 在“停用”边栏选项卡中，键入设备名称以确认，单击“停用”。 停用过程将开始并且需要花费几分钟时间才能完成。

        ![停用 StorSimple 设备](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. 停用之后即可完全删除该设备。 删除设备会将其从连接到该服务的设备列表中删除。 之后，该服务不再能够管理已删除的设备。 通过以下步骤删除设备：
   
   1. 转到 StorSimple Device Manager 服务并单击“设备”。 在“设备”边栏选项卡中选择要删除的已停用设备，单击右键，选择“删除”。

        ![停用 StorSimple 设备](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. 在“删除”边栏选项卡中，键入设备名称以确认，单击“删除”。 删除过程需要花费几分钟时间才能完成。

        ![停用 StorSimple 设备](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. 成功完成删除后，系统会发出通知。 设备列表也会更新以反映删除后的结果。

## <a name="deactivate-and-retain-data"></a>停用和保留数据

若要删除设备，但又想在设备上保留数据，请完成以下步骤：

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>停用设备并保留数据
1. 停用设备。 设备的所有卷容器和快照都会保留。
   
   1. 转到 StorSimple Device Manager 服务并单击“设备”。 在“设备”边栏选项卡中选择要停用的设备，单击右键，单击“停用”。

         ![停用 StorSimple 设备](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. 在“停用”边栏选项卡中，键入设备名称以确认，单击“停用”。 停用过程将开始并且需要花费几分钟时间才能完成。

         ![停用 StorSimple 设备](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. 现在可以故障转移卷容器和关联的快照。 相关过程请参阅 [StorSimple 设备的故障转移和灾难恢复](storsimple-8000-device-failover-disaster-recovery.md)。
3. 停用和故障转移之后即可完全删除该设备。 删除设备会将其从连接到该服务的设备列表中删除。 之后，该服务不再能够管理已删除的设备。 若要删除设备，请完成以下步骤：
   
   1. 转到 StorSimple Device Manager 服务并单击“设备”。 在“设备”边栏选项卡中选择要删除的已停用设备，单击右键，选择“删除”。

       ![停用 StorSimple 设备](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. 在“删除”边栏选项卡中，键入设备名称以确认，单击“删除”。 删除过程需要花费几分钟时间才能完成。

       ![停用 StorSimple 设备](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. 成功完成删除后，系统会发出通知。 设备列表也会更新以反映删除后的结果。

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>停用和删除云设备

对于 StorSimple 云设备，在门户中执行停用操作会解除分配并删除虚拟机，以及预配虚拟机时创建的资源。 停用云设备之后，无法将它还原到以前的状态。

![停用 StorSimple 云设备](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

停用导致以下操作：

* 从服务中删除 StorSimple 云设备。
* 删除 StorSimple 云设备的虚拟机。
* 删除为 StorSimple 云设备创建的 OS 磁盘和数据磁盘。
* 保留预配期间创建的托管服务和虚拟网络。 如果不使用这些实体，应该手动将其删除。
* 保留为 StorSimple 云设备创建的云快照。

停用云设备后，可以从设备列表中将其删除。 选择已停用的设备，单击右键，单击“删除”。 删除设备后，StorSimple 会发出通知，设备列表会更新。

## <a name="next-steps"></a>后续步骤

* 若要将已停用的设备还原为出厂默认设置，请转到[将设备重置为出厂默认设置](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)。
* 如需技术帮助，请[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)。
* 若要详细了解如何使用 StorSimple 设备管理器服务，请参阅[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。


