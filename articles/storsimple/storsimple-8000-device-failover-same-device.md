---
title: StorSimple 8000 系列设备的故障转移和灾难恢复 | Microsoft Docs
description: 了解如何将 StorSimple 设备故障转移到同一设备。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: acc8929dc3476e9590e8e4d9526b38b7c0719570
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23108195"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>将 StorSimple 物理设备故障转移到同一设备

## <a name="overview"></a>概述

本教程介绍在发生灾难时，将 StorSimple 8000 系列物理设备故障转移到它自身所要执行的步骤。 StorSimple 使用设备故障转移功能将数据中心内的源物理设备中的数据迁移到另一个物理设备。 本教程中的指导适用于运行软件版本 Update 3 和更高版本的 StorSimple 8000 系列物理设备。

若要详细了解设备故障转移以及如何在发生灾难后使用它恢复数据，请参阅 [StorSimple 8000 系列设备的故障转移和灾难恢复](storsimple-8000-device-failover-disaster-recovery.md)。

若要将一个物理设备故障转移到另一个物理设备，请参阅[故障转移到同一 StorSimple 物理设备](storsimple-8000-device-failover-physical-device.md)。 若要将一个 StorSimple 物理设备故障转移到 StorSimple 云设备，请参阅[故障转移到 StorSimple 云设备](storsimple-8000-device-failover-cloud-appliance.md)。


## <a name="prerequisites"></a>先决条件

- 确保已查看有关设备故障转移的注意事项。 有关详细信息，请参阅[有关设备故障转移的一般注意事项](storsimple-8000-device-failover-disaster-recovery.md)。


## <a name="steps-to-fail-over-to-the-same-device"></a>故障转移到同一设备的步骤

如果需要故障转移到同一设备，请执行以下步骤。

1. 为设备中的所有卷创建云快照。 有关详细信息，请参阅[使用 StorSimple 设备管理器服务创建备份](storsimple-8000-manage-backup-policies-u2.md)。
2. 将设备重置为出厂默认设置。 请遵循 [how to reset a StorSimple device to factory default settings](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)（如何将 StorSimple 设备重置为出厂默认设置）中的详细说明。
3. 转到 StorSimple 设备管理器服务，选择“设备”。 在“设备”边栏选项卡上，旧设备应显示为“脱机”。

    ![源设备脱机](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. 配置设备，再次将它注册到 StorSimple 设备管理器服务。 新注册的设备应显示为“准备好设置”。 新设备的设备名称与旧设备相同，但后面追加了一个数字，表示该设备已重置为出厂默认设置并重新注册。

    ![新注册的设备已做好设置准备](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. 对于新设备，请完成设备设置。 有关详细信息，请参阅[步骤 4：完成最低要求的设备设置](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup)。 在“设备”边栏选项卡中，设备状态更改为“联机”。

   > [!IMPORTANT]
   > **首先完成最低的配置，否则灾难恢复可能失败。**

    ![新注册的设备联机](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. 选择旧设备（脱机状态），在命令栏中单击“故障转移”。 在“故障转移”边栏选项卡中，选择旧设备作为源，将目标设备指定为新注册的设备。

    ![故障转移摘要](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    有关详细说明，请参阅[故障转移到另一个物理设备](#fail-over-to-another-physical-device)。

7. 会创建一个可以通过“作业”边栏选项卡监视的设备还原作业。

8. 成功完成作业后，请访问新设备，并导航到“卷容器”边栏选项卡。 验证旧设备中的所有卷容器是否已迁移到新设备。

   ![已迁移卷容器](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. 完成故障转移后，可以从门户中停用并删除旧设备。 选择旧设备（脱机），单击右键，选择“停用”。 停用设备后，会更新其状态。

     ![已停用源设备](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. 选择已停用的设备，单击右键，选择“删除”。 这会从设备列表中删除该设备。

    ![已删除源设备](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>后续步骤

* 执行故障转移后，可能需要[停用或删除 StorSimple 设备](storsimple-8000-deactivate-and-delete-device.md)。
* 有关如何使用 StorSimple 设备管理器服务的信息，请参阅[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。

