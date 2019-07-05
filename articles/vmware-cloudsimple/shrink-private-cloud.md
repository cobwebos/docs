---
title: 收缩 Azure VMware CloudSimple 私有云解决方案
description: 介绍如何收缩 CloudSimple 私有云。
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e639feb603f1654b4dcd40f16d8e3094307839a
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544512"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>收缩 CloudSimple 私有云

CloudSimple 提供动态收缩私有云的灵活性。  私有云包含一个或多个 vSphere 群集。 每个群集可以具有 3 到 16 个节点。 当收缩私有云，则将从现有群集中删除节点或删除整个群集。 

## <a name="before-you-begin"></a>开始之前

以下条件必须满足的私有云的收缩。  管理群集 （第一个群集） 创建私有云创建时不能删除。

* VSphere 群集必须具有三个节点。  不能收缩具有只有三个节点的群集。
* 消耗的存储总量不应在群集的收缩后超过总容量。 

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="shrink-a-private-cloud"></a>收缩私有云 

1. [访问 CloudSimple 门户](access-cloudsimple-portal.md)。

2. 打开**资源**页。

3. 单击要收缩的私有云

4. 在摘要页上单击**收缩**。

    ![收缩私有云](media/shrink-private-cloud.png)

5. 选择你想要缩小或删除该群集。 

    ![收缩私有云-选择群集](media/shrink-private-cloud-select-cluster.png)

6. 选择**删除一个节点**或**删除整个群集**。 

7. 验证群集容量

8. 单击**提交**收缩私有云。

私有云收缩启动。  你可以监视任务的进度。  收缩过程可能需要几个小时，具体取决于数据，需要对其进行重新同步上 vSAN。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware Vm](quickstart-create-vmware-virtual-machine.md)
* 详细了解[私有云](cloudsimple-private-cloud.md)