---
title: 从备份还原 - Azure 应用服务
description: 了解如何从快照还原应用。
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: 4b3b10177b119c9362f807206a915d4b0b367e16
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639754"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>在 Azure 中从快照还原应用
本文介绍如何在 [Azure 应用服务](../app-service/overview.md)中从快照还原应用。 可以根据应用的某个快照将应用还原到以前的状态。 不需启用快照备份，平台会自动保存所有应用的快照，用于数据恢复。

快照为增量影子副本，与常规[备份](manage-backup.md)相比有多项优势：
- 不会因文件锁定而造成文件复制错误。
- 没有存储大小限制。
- 不需配置。

从快照还原适用于在**高级**层或更高层中运行的应用。 有关向上缩放应用的信息，请参阅[在 Azure 中向上缩放应用](manage-scale-up.md)。

## <a name="limitations"></a>限制

- 此功能目前为预览版。
- 只能还原到同一应用或属于该应用的槽。
- 用于服务在执行还原操作时会停止目标应用或目标槽。
- 应用服务保留三个月的快照，用于平台数据恢复。
- 只能还原过去 30 天的快照。
- 在应用服务环境上运行的应用服务不支持快照。
 

## <a name="restore-an-app-from-a-snapshot"></a>从快照还原应用

1. 在 [Azure 门户](https://portal.azure.com)中应用的“设置”页上，单击“备份”以显示“备份”页。 然后，在“快照(预览)”部分下单击“还原”。
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. 在“还原”页中选择要还原的快照。
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. 在“还原目标”中指定应用还原的目标。
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > 如果选择“覆盖”，则会清除并覆盖应用的当前文件系统中的所有现有数据。 在单击“确定”之前，请确保该操作是想要执行的操作。
   > 
   > 
      
   > [!Note]
   > 由于目前的技术限制，只能还原到同一缩放单元的应用。 将来的版本会取消此限制。
   > 
   > 
   
    可以选择要还原到槽的**现有应用**。 在使用此选项之前，应该已在应用中创建一个槽。

4. 可以选择还原站点配置。
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. 单击 **“确定”** 。
