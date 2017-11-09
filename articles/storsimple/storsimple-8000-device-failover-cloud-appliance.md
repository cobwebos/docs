---
title: "将 StorSimple 故障转移和灾难恢复到 StorSimple 云设备 | Microsoft Docs"
description: "了解如何将 StorSimple 8000 系列物理设备故障转移到云设备。"
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: ec8bebf2854e84a37e84b45564e80fc20b63d8d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>故障转移到 StorSimple 云设备

## <a name="overview"></a>概述

本教程介绍在发生灾难时，将 StorSimple 8000 系列物理设备故障转移到 StorSimple 云设备所要执行的步骤。 StorSimple 使用设备故障转移功能将数据中心内的源物理设备中的数据迁移到 Azure 中运行的云设备。 本教程中的指导适用于运行软件版本 Update 3 和更高版本的 StorSimple 8000 系列物理设备与云设备。

若要详细了解设备故障转移以及如何在发生灾难后使用它恢复数据，请参阅 [StorSimple 8000 系列设备的故障转移和灾难恢复](storsimple-8000-device-failover-disaster-recovery.md)。

若要将一个 StorSimple 物理设备故障转移到另一个物理设备的，请转到[故障转移到 StorSimple 物理设备](storsimple-8000-device-failover-physical-device.md)。 若要将某个设备故障转移到其自身，请参阅[故障转移到同一 StorSimple 物理设备](storsimple-8000-device-failover-same-device.md)。

## <a name="prerequisites"></a>先决条件

- 确保已查看有关设备故障转移的注意事项。 有关详细信息，请参阅[有关设备故障转移的一般注意事项](storsimple-8000-device-failover-disaster-recovery.md)。

- 运行此过程之前，必须事先创建并配置一个 StorSimple 云设备。 如果运行 Update 3 或更高版本的软件，请考虑使用 8020 云设备进行灾难恢复。 8020 型号提供 64 TB 存储，并使用高级存储。 有关详细信息，请参阅[部署和管理 StorSimple 云设备](storsimple-8000-cloud-appliance-u2.md)。

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>故障转移到云设备的步骤

执行以下步骤可将设备还原到目标 StorSimple 云设备。

1.  验证要故障转移的卷容器是否有关联的云快照。 有关详细信息，请参阅[使用 StorSimple 设备管理器服务创建备份](storsimple-8000-manage-backup-policies-u2.md)。
2. 转到 StorSimple Device Manager 服务并单击“设备”。 在“设备”边栏选项卡中，转到与服务连接的设备的列表。
    ![选择设备](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. 选择并单击源设备。 源设备包含所要故障转移的卷容器。 转到“设置”>“卷容器”。

    ![选择设备](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. 选择要故障转移到另一个设备的卷容器。 单击该卷容器显示其中的卷列表。 选择一个卷，单击右键，单击“脱机”使该卷脱机。

    ![选择设备](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. 针对卷容器中的所有卷重复此过程。

     ![选择设备](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. 针对要故障转移到另一个设备的所有卷容器重复上一步骤。

7. 返回到“设备”边栏选项卡。 在命令栏中，单击“故障转移”。

    ![单击“故障转移”](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. 在“故障转移”边栏选项卡中执行以下步骤：
   
    1. 单击“源”。 选择要故障转移的卷容器。 **列表中只显示与云快照和脱机卷关联的卷容器。**
        ![选择源](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. 单击“目标”。 从可用设备的下拉列表中选择目标云设备。 **列表中只会显示可提供足够的容量来容纳源卷容器的设备。**

        ![选择目标](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. 查看“摘要”下面的故障转移设置，选中指示所选卷容器中的卷已脱机的复选框。 

        ![查看故障转移设置](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. 随后会创建一个故障转移作业。 若要监视故障转移作业，请单击作业通知。

    ![监视故障转移作业](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. 完成故障转移后，返回到“设备”边栏选项卡。

    1. 选择用作故障转移目标的设备。

       ![选择设备](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. 单击“卷容器”。 此时应列出所有卷容器以及旧设备中的卷。

       如果故障转移的卷容器包含本地固定卷，这些卷将作为分层卷故障转移。 StorSimple 云设备不支持本地固定卷。

       ![查看目标卷容器](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>后续步骤

* 执行故障转移后，可能需要[停用或删除 StorSimple 设备](storsimple-8000-deactivate-and-delete-device.md)。

* 有关如何使用 StorSimple 设备管理器服务的信息，请参阅[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。

