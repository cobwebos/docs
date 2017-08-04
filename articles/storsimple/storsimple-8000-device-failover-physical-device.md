---
title: "将 StorSimple 故障转移和灾难恢复到 StorSimple 8000 系列物理设备 | Microsoft Docs"
description: "了解如何将 StorSimple 8000 系列物理设备故障转移到另一个物理设备。"
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
ms.date: 05/03/2017
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: f3ac9545a341fc24ca12c9f2547805d6956cd98a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017


---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>故障转移到 StorSimple 8000 系列物理设备

## <a name="overview"></a>概述

本教程介绍在发生灾难时，将 StorSimple 8000 系列物理设备故障转移到另一个 StorSimple 物理设备所要执行的步骤。 StorSimple 使用设备故障转移功能将数据中心内的源物理设备中的数据迁移到另一个物理设备。 本教程中的指导适用于运行软件版本 Update 3 和更高版本的 StorSimple 8000 系列物理设备。

若要详细了解设备故障转移以及如何在发生灾难后使用它恢复数据，请参阅 [StorSimple 8000 系列设备的故障转移和灾难恢复](storsimple-8000-device-failover-disaster-recovery.md)。

若要将一个 StorSimple 物理设备故障转移到 StorSimple 云设备，请参阅[故障转移到 StorSimple 云设备](storsimple-8000-device-failover-cloud-appliance.md)。 若要将某个物理设备故障转移到其自身，请参阅[故障转移到同一 StorSimple 物理设备](storsimple-8000-device-failover-same-device.md)。


## <a name="prerequisites"></a>先决条件

- 确保已查看有关设备故障转移的注意事项。 有关详细信息，请参阅[有关设备故障转移的一般注意事项](storsimple-8000-device-failover-disaster-recovery.md)。

- 必须在数据中心部署一个 StorSimple 8000 系列物理设备。 该设备必须运行 Update 3 或更高的软件版本。 有关详细信息，请转到[部署本地 StorSimple 设备](storsimple-8000-deployment-walkthrough-u2.md)。


## <a name="steps-to-fail-over-to-a-physical-device"></a>故障转移到物理设备的步骤

执行以下步骤可将设备还原到目标物理设备。

1. 验证要故障转移的卷容器是否有关联的云快照。 有关详细信息，请参阅[使用 StorSimple 设备管理器服务创建备份](storsimple-8000-manage-backup-policies-u2.md)。
2. 转到 StorSimple 设备管理器，单击“设备”。 在“设备”边栏选项卡中，转到与服务连接的设备的列表。
    ![选择设备](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. 选择并单击源设备。 源设备包含所要故障转移的卷容器。 转到“设置”>“卷容器”。
4. 选择要故障转移到另一个设备的卷容器。 单击该卷容器显示其中的卷列表。 选择一个卷，单击右键，单击“脱机”使该卷脱机。 针对卷容器中的所有卷重复此过程。
5. 针对要故障转移到另一个设备的所有卷容器重复上一步骤。
6. 返回到“设备”边栏选项卡。 在命令栏中，单击“故障转移”。
    ![单击“故障转移”](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. 在“故障转移”边栏选项卡中执行以下步骤：
   
   1. 单击“源”。 此时会显示与云快照关联的卷所在的卷容器。 只会显示可故障转移的容器。 在卷容器列表中，选择要故障转移的卷容器。 **列表中只显示与云快照和脱机卷关联的卷容器。**

       ![选择源](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. 单击“目标”。 对于在前一步骤中选择的卷容器，请从可用设备的下拉列表中选择目标设备。 列表中只会显示可提供足够的容量来容纳源卷容器的设备。

        ![选择目标](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. 最后，查看“摘要”下面的所有故障转移设置。 查看设置后，请选中指示所选卷容器中的卷已脱机的复选框。 单击 **“确定”**。

       ![查看故障转移设置](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple 会创建一个故障转移作业。 单击作业通知可通过“作业”边栏选项卡监视故障转移作业。

    如果故障转移的卷容器包含本地卷，则会显示容器中每个本地卷（分层卷除外）的还原作业。 这些还原作业可能需要较长时间才能完成。 故障转移作业可能会更快完成。 只有完成还原作业之后，这些卷才获得本地保证。

    ![监视故障转移作业](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. 完成故障转移后，转到“设备”边栏选项卡。
   
   1. 为故障转移过程选择用作目标设备的设备。

       ![选择设备](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. 转到“卷容器”边栏选项卡。 此时应列出所有卷容器以及旧设备中的卷。

       ![查看目标卷容器](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>后续步骤

* 执行故障转移后，可能需要[停用或删除 StorSimple 设备](storsimple-8000-deactivate-and-delete-device.md)。
* 有关如何使用 StorSimple 设备管理器服务的信息，请参阅[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。


