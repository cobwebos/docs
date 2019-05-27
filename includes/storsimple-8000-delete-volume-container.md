---
title: include 文件
description: include 文件
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 06/08/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: e683d17422321b780a1c01b3011292f2e2c631cb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156092"
---
若要删除卷容器，则必须
 - 删除卷容器中的卷。 如果卷容器具有关联的卷，请首先使那些卷脱机。 请按照[使卷脱机](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline)中的步骤操作。 卷处于脱机状态后，可以删除它们。 
 - 删除关联的备份策略和云快照。 检查卷容器是否具有关联的备份策略和云快照。 如果有，则[删除备份策略](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy)。 这还会删除云快照。 
 
如果卷容器没有关联的卷、备份策略和云快照，可将其删除。 可以执行以下过程来删除卷容器。

#### <a name="to-delete-a-volume-container"></a>删除卷容器
1. 转到 StorSimple Device Manager 服务并单击“设备”。 选择并单击“设备”，并转到“设置”>“管理”>“卷容器”。

    ![“卷容器”边栏选项卡](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. 从卷容器的表格式列表中，选择要删除的卷容器，右键单击“...”，并选择“删除”。

    ![删除卷容器](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. 如果卷容器没有关联的卷、备份策略和云快照，可将其删除。 当提示进行确认时，查看并选中陈述了删除卷容器带来的影响的复选框。 单击“删除”以删除卷容器。

    ![确认删除](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

卷容器的列表将更新以反映删除的卷容器。

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


