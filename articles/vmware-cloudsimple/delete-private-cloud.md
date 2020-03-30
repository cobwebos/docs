---
title: 通过云简单私有云删除 Azure VMware 解决方案
description: 描述如何删除云简单私有云。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024749"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>删除云简单私有云

云简单提供了删除私有云的灵活性。  私有云由一个或多个 vSphere 群集组成。 每个群集可以有 3 到 16 个节点。 删除私有云时，将删除所有群集。

## <a name="before-you-begin"></a>开始之前

删除私有云将删除整个私有云。  私有云的所有组件都将被删除。  如果要保留任何数据，请确保将数据备份到本地存储或 Azure 存储。

私有云的组件包括：

* 云简单节点
* 虚拟机
* VLAN/子网
* 存储在私有云上的所有用户数据
* VLAN/子网的所有防火墙规则附件

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 中的[https://portal.azure.com](https://portal.azure.com)Azure 门户。

## <a name="delete-a-private-cloud"></a>删除私有云

1. [访问云简单门户](access-cloudsimple-portal.md)。

2. 打开 **"资源"** 页。

3. 单击要删除的私有云

4. 在摘要页上，单击"**删除**"。

    ![删除私有云](media/delete-private-cloud.png)

5. 在确认页面上，输入私有云的名称，然后单击"**删除**"。 

    ![删除私有云 - 确认](media/delete-private-cloud-confirm.png)

私有云标记为删除。  删除过程在三小时后开始，并删除私有云。

> [!CAUTION]
> 删除私有云后必须删除节点。  节点的计数将继续，直到从订阅中删除节点。

## <a name="next-steps"></a>后续步骤

* [删除节点](delete-nodes.md)
