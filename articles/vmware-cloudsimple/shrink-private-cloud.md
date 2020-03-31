---
title: 按云简单私有云收缩 Azure VMware 解决方案
description: 描述如何收缩云简单私有云。
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 602dca105e91c55c591388a833a36e71f951da8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014260"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>缩小云简单私有云

CloudSimple 提供了动态收缩私有云的灵活性。  私有云由一个或多个 vSphere 群集组成。 每个群集可以有 3 到 16 个节点。 收缩私有云时，将从现有群集中删除节点或删除整个群集。 

## <a name="before-you-begin"></a>开始之前

缩小私有云必须满足以下条件。  无法删除创建私有云时创建的管理群集（第一个群集）。

* vSphere 群集必须具有三个节点。  只能具有三个节点的群集无法收缩。
* 群集收缩后，消耗的总存储容量不应超过总容量。
* 检查任何分布式资源计划程序 （DRS） 规则是否阻止虚拟机的 vMotion。  如果存在规则，请禁用或删除这些规则。  DRS 规则包括用于承载关联规则的虚拟机。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 中的[https://portal.azure.com](https://portal.azure.com)Azure 门户。

## <a name="shrink-a-private-cloud"></a>收缩私有云

1. [访问云简单门户](access-cloudsimple-portal.md)。

2. 打开 **"资源"** 页。

3. 单击要缩小的私有云

4. 在摘要页上，单击 **"收缩**"。

    ![收缩私有云](media/shrink-private-cloud.png)

5. 选择要收缩或删除的群集。 

    ![收缩私有云 - 选择群集](media/shrink-private-cloud-select-cluster.png)

6. 选择**删除一个节点**或删除**整个群集**。 

7. 验证群集容量

8. 单击"**提交**"以缩小私有云。

私有云的收缩开始。  您可以监视任务的进度。  收缩过程可能需要几个小时，具体取决于数据，需要在 vSAN 上重新同步。

> [!NOTE]
> 1. 如果通过删除数据中心中的最后一个或唯一的群集来缩小私有云，则不会删除数据中心。
> 2. 如果发生任何 DRS 规则冲突，将不会从群集中删除节点，并且任务说明显示删除节点将违反群集上的 DRS 规则。    


## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 了解有关[私有云](cloudsimple-private-cloud.md)的更多
