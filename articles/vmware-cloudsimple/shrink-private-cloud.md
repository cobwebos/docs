---
title: 缩减 Azure VMware 解决方案（AVS）私有云
description: 介绍如何收缩 AVS 私有云。
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0ea764081cd0b4d5c6d44cd7364d1e9a89a3cec3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014260"
---
# <a name="shrink-an-avs-private-cloud"></a>压缩 AVS 私有云

AVS 提供动态缩减 AVS 私有云的灵活性。 AVS 私有云包含一个或多个 vSphere 群集。 每个群集可以有3到16个节点。 收缩 AVS 私有云时，从现有群集中删除节点或删除整个群集。 

## <a name="before-you-begin"></a>开始之前

压缩 AVS 私有云之前必须满足以下条件。 创建 AVS 私有云时，将创建管理群集（第一个群集）。 不能删除它。

* VSphere 群集必须具有三个节点。 只有三个节点的群集无法收缩。
* 收缩群集后，使用的总存储空间不应超过总容量。
* 检查是否有任何分布式资源计划程序（DRS）规则阻止了虚拟机的 vMotion。 如果有规则，请禁用或删除规则。 DRS 规则包括用于主机关联规则的虚拟机。


## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="shrinking-an-avs-private-cloud"></a>压缩 AVS 私有云

1. [访问 AVS 门户](access-cloudsimple-portal.md)。

2. 打开 "**资源**" 页。

3. 单击要收缩的 AVS 私有云

4. 在 "摘要" 页上，单击 "**收缩**"。

    ![压缩 AVS 私有云](media/shrink-private-cloud.png)

5. 选择要收缩或删除的群集。 

    ![压缩 AVS 私有云-选择群集](media/shrink-private-cloud-select-cluster.png)

6. 选择 "**删除一个节点**" 或 **"删除整个群集"** 。 

7. 验证群集容量

8. 单击 "**提交**" 以压缩 AVS 私有云。

将启动 AVS 私有云的压缩。 可以在任务中监视进度。 收缩过程可能需要几个小时，具体取决于数据（需要在 vSAN 上 resynced）。

> [!NOTE]
> 1. 如果通过删除数据中心中的最后一个或唯一一个群集来收缩私有云，则不会删除该数据中心。
> 2. 如果发生任何 DRS 规则冲突，则不会从群集中删除节点，任务说明显示删除节点将违反群集上的 DRS 规则。    


## <a name="next-steps"></a>后续步骤

* [在 Azure 上使用 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 了解有关[AVS 私有云](cloudsimple-private-cloud.md)的详细信息
