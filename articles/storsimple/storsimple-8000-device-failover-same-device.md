---
title: "StorSimple 故障转移，8000 系列设备的灾难恢复 |Microsoft 文档"
description: "了解如何进行故障转移到同一设备 StorSimple 设备。"
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
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: acc8929dc3476e9590e8e4d9526b38b7c0719570
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>故障转移到同一设备 StorSimple 物理设备

## <a name="overview"></a>概述

本教程介绍灾难时故障转移到其自身的 StorSimple 8000 系列物理设备所需的步骤。 StorSimple 使用设备故障转移功能将数据从数据中心内的源物理设备迁移到另一个物理设备。 在本教程中的指南适用于 StorSimple 8000 系列物理设备运行软件版本更新 3 及更高版本。

若要了解有关设备故障转移以及如何使用它从灾难中恢复的详细信息，请转到[StorSimple 8000 系列设备的故障转移和灾难恢复](storsimple-8000-device-failover-disaster-recovery.md)。

若要故障转移到另一个物理设备的物理设备，请转到[故障转移到相同的 StorSimple 物理设备](storsimple-8000-device-failover-physical-device.md)。 若要故障转移到 StorSimple 云设备的 StorSimple 物理设备，请转到[故障转移到 StorSimple 云设备](storsimple-8000-device-failover-cloud-appliance.md)。


## <a name="prerequisites"></a>必备条件

- 请确保已经查看了设备故障转移时的注意事项。 有关详细信息，请转到[用于设备故障转移的常见注意事项](storsimple-8000-device-failover-disaster-recovery.md)。


## <a name="steps-to-fail-over-to-the-same-device"></a>故障转移到同一设备的步骤

如果你需要故障转移到同一设备，请执行以下步骤。

1. 在你的设备中执行所有卷的云快照。 有关详细信息，请转到[使用 StorSimple 设备管理器服务来创建备份](storsimple-8000-manage-backup-policies-u2.md)。
2. 将你的设备重置为出厂默认设置。 按照中的详细的说明[如何重置为出厂默认设置的 StorSimple 设备](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)。
3. 请转到 StorSimple 设备管理器服务，然后选择**设备**。 在**设备**边栏选项卡，旧设备应显示为**脱机**。

    ![脱机源设备](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. 配置你的设备，并再次将其注册到你的 StorSimple 设备管理器服务。 新注册的设备应显示为**已准备好设置**。 新设备的设备名称与旧设备相同，但追加一个数字来指示已重置为出厂默认值并再次注册设备。

    ![已准备好设置的新注册的设备](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. 有关新的设备，完成设备设置。 有关详细信息，请转到[第 4 步： 完成最低要求的设备设置](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup)。 上**设备**边栏选项卡，设备状态将更改为**联机**。

   > [!IMPORTANT]
   > **首先，完成最小配置，或者将 DR 可能会失败。**

    ![新注册的设备设置为联机](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. 选择旧的设备 （脱机状态），然后从命令栏中，单击**故障转移**。 在**故障转移**边栏选项卡，选择旧设备作为源和目标设备指定为新注册的设备。

    ![故障转移摘要](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    有关详细说明，请参阅[故障转移到另一个物理设备](#fail-over-to-another-physical-device)。

7. 将创建一个设备还原作业，你可以监视从**作业**边栏选项卡。

8. 在作业成功完成后，访问新设备并导航到**卷容器**边栏选项卡。 验证从旧设备的所有卷容器已都迁移到新设备。

   ![卷容器迁移](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. 在故障转移完成后，你可以停用并从门户中删除旧的设备。 选择旧设备 （脱机），右键单击，，然后选择**停用**。 设备停用后，会更新设备的状态。

     ![停用的源设备](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. 选择停用的设备，右键单击，然后选择**删除**。 这将从设备列表中删除设备。

    ![删除源设备](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>后续步骤

* 已执行故障转移后，你可能需要[停用或删除你的 StorSimple 设备](storsimple-8000-deactivate-and-delete-device.md)。
* 有关如何使用 StorSimple 设备管理器服务的信息，请转到[StorSimple 设备管理器服务用于管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。

