---
title: 通过 CloudSimple 私有云缩小 Azure VMware 解决方案
description: 介绍如何收缩 CloudSimple 私有云。
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1ae2f87a3719853f4a91cb8ba801be6d578597d3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825685"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>缩小 CloudSimple 私有云

CloudSimple 提供动态收缩私有云的灵活性。  私有云由一个或多个 vSphere 群集组成。 每个群集可以有3到16个节点。 收缩私有云时，从现有群集中删除节点或删除整个群集。 

## <a name="before-you-begin"></a>开始之前

若要压缩私有云，必须满足以下条件。  无法删除创建私有云时创建的管理群集（第一个群集）。

* VSphere 群集必须具有三个节点。  只有三个节点的群集无法收缩。
* 使用的总存储空间不应超过群集缩小后的总容量。 

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="shrink-a-private-cloud"></a>收缩私有云

1. [访问 CloudSimple 门户](access-cloudsimple-portal.md)。

2. 打开 "**资源**" 页。

3. 单击要收缩的私有云

4. 在 "摘要" 页上，单击 "**收缩**"。

    ![缩小私有云](media/shrink-private-cloud.png)

5. 选择要收缩或删除的群集。 

    ![收缩私有云-选择群集](media/shrink-private-cloud-select-cluster.png)

6. 选择 "**删除一个节点**" 或 **"删除整个群集"** 。 

7. 验证群集容量

8. 单击 "**提交**" 以缩小私有云。

启动私有云的压缩。  可以在任务中监视进度。  收缩过程可能需要几个小时，具体取决于数据（需要在 vSAN 上 resynced）。

> [!NOTE]
> 如果通过删除数据中心中的最后一个或唯一一个群集来收缩私有云，则不会删除该数据中心。  


## <a name="next-steps"></a>后续步骤

* [在 Azure 上使用 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 了解有关[私有云](cloudsimple-private-cloud.md)的详细信息
