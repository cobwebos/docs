---
title: "停用和删除 StorSimple 设备 | Microsoft Docs"
description: "介绍了如何使用该服务通过先停用再删除 StorSimple 设备的方式来删除该设备。"
services: storsimple
documentationcenter: 
author: SharS
manager: timlt
editor: 
ms.assetid: 155cda38-c5ae-45dc-b7e8-6444494afc9e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2017
ms.author: anbacker
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c000a642aa088ac80cc7077453b87e9a47f96900
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="deactivate-and-delete-a-storsimple-8000-series-device-via-storsimple-manager-service"></a>通过 StorSimple Manager 服务停用和删除 StorSimple 8000 系列设备
## <a name="overview"></a>概述
用户可能希望停用某个 StorSimple 设备（例如，用户需要更换或升级设备，或者不再使用 StorSimple）。 如果出现这种情况，则需先停用设备，然后将其删除。 停用服务器时，会断开设备与相应 StorSimple Manager 服务之间的连接。 本教程介绍如何使用该服务通过先停用再删除 StorSimple 设备的方式来删除该设备。 

当停用设备后，存储在本地设备上的任何数据都不再可访问。 只能恢复与存储在云中的设备关联的数据。  

> [!WARNING]
> 停用是不可撤消的“永久性”操作。 停用的设备不能注册到 StorSimple Manager 服务，除非先将其重置为默认出厂设置。 
> 
> 出厂重置过程会删除以本地方式存储在设备上的所有数据。 因此，必须在停用设置之前对所有数据执行云快照操作。 这样即可在后面的某个阶段恢复所有数据。
> 
> 

本教程介绍了如何完成以下操作：

* 停用设备并删除数据
* 停用设备并保留数据

此外还介绍了如何在 StorSimple 虚拟设备上执行停用和删除操作。

> [!NOTE]
> 停用 StorSimple 物理或虚拟设备之前，请务必停止或删除依赖于该设备的客户端与主机。
> 
> 

## <a name="deactivate-and-delete-data"></a>停用和删除数据
若要彻底删除设备，不想在设备上保留数据，则请完成以下步骤。

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>停用设备并删除数据
1. 在停用设备之前，必须删除与设备关联的所有卷容器（以及卷）。 只有在删除关联的备份后，才能删除卷容器。
2. 通过以下操作停用设备：
   
   1. 在 StorSimple Manager 服务的“设备”页上，选择要停用的设备，并在页面底部单击“停用”。
   2. 将显示确认消息。 单击“是”继续。 停用过程将开始并且需要花费几分钟时间才能完成。
3. 停用之后即可完全删除该设备。 删除设备会将其从连接到该服务的设备列表中删除。 之后，该服务不再能够管理已删除的设备。 通过以下步骤删除设备：
   
   1. 在 StorSimple Manager 服务“设备”页上，选择要删除的已停用设备。
   2. 在页面的底部，单击“删除”。
   3. 系统会提示进行确认。 单击“是”继续。
      
      可能需要花费几分钟时间才能删除设备。

## <a name="deactivate-and-retain-data"></a>停用和保留数据
若要删除设备，但又想在设备上保留数据，则请完成以下步骤。

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>停用设备并保留数据
1. 停用设备。 设备的所有卷容器和快照都将保留。
   
   1. 在 StorSimple Manager 服务的“设备”页上，选择要停用的设备，并在页面底部单击“停用”。
   2. 将显示确认消息。 单击“是”继续。 停用过程将开始并且需要花费几分钟时间才能完成。
2. 现在可以故障转移卷容器和关联的快照。 相关过程请参阅 [StorSimple 设备的故障转移和灾难恢复](storsimple-device-failover-disaster-recovery.md)。
3. 停用和故障转移之后即可完全删除该设备。 删除设备会将其从连接到该服务的设备列表中删除。 之后，该服务不再能够管理已删除的设备。 可以完成以下步骤来删除设备：
   
   1. 在 StorSimple Manager 服务“设备”页上，选择要删除的已停用设备。
   2. 在页面的底部，单击“删除”。
   3. 系统会提示进行确认。 单击“是”继续。
      
      可能需要花费几分钟时间才能删除设备。

## <a name="deactivate-and-delete-a-virtual-device"></a>停用和删除虚拟设备
对于 StorSimple 虚拟设备，停用会释放虚拟机。 然后，可以删除虚拟机，并设置它时创建的资源。 停用虚拟设备之后，无法将它还原到以前的状态。 

停用导致以下操作：

* 将移除 StorSimple 虚拟设备。
* 删除为 StorSimple 虚拟设备创建的 OS 磁盘和数据磁盘。
* 保留预配期间创建的托管服务和虚拟网络。 如果不使用这些实体，应该手动将其删除。
* 保留为 StorSimple 虚拟设备创建的云快照。

## <a name="next-steps"></a>后续步骤
* 要将已停用的设备还原为出厂默认设置，请转到[将设备重置为出厂默认设置](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings)。
* 如需技术帮助，请[联系 Microsoft 支持部门](storsimple-contact-microsoft-support.md)。
* 若要了解有关如何使用 StorSimple Manager 服务的详细信息，请转到[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。 

