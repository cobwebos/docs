---
title: 通过 CloudSimple 私有云删除 Azure VMware 解决方案
description: 介绍如何删除 CloudSimple 私有云。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8a47968ec252f628da8a1a36570fb06eb4bb10bf
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886944"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>删除 CloudSimple 私有云

CloudSimple 提供了删除私有云的灵活性。  私有云由一个或多个 vSphere 群集组成。 每个群集可以有3到16个节点。 删除私有云时, 所有群集都将被删除。 

## <a name="before-you-begin"></a>开始之前

删除私有云会删除整个私有云。  私有云的所有组件都将被删除。  如果要保留任何数据, 请确保已将数据备份到本地存储或 Azure 存储。 

私有云的组件包括:

* CloudSimple 节点
* 虚拟机
* Vlan/子网
* 私有云上存储的所有用户数据
* VLAN/子网的所有防火墙规则附件


## <a name="sign-in-to-azure"></a>登录  Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="delete-a-private-cloud"></a>删除私有云 

1. [访问 CloudSimple 门户](access-cloudsimple-portal.md)。

2. 打开 "**资源**" 页。

3. 单击要删除的私有云

4. 在 "摘要" 页上, 单击 "**删除**"。

    ![删除私有云](media/delete-private-cloud.png)

5. 在 "确认" 页上, 输入私有云的名称, 然后单击 "**删除**"。 

    ![删除私有云-确认](media/delete-private-cloud-confirm.png)


私有云已标记为要删除。  删除过程将在三个小时后开始, 并删除私有云。

> [!CAUTION]
> 删除私有云后, 必须删除节点。  在从订阅中删除节点之前, 将继续执行节点计数。


## <a name="next-steps"></a>后续步骤

* [删除节点](delete-nodes.md)
