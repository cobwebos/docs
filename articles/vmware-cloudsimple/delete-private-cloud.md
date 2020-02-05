---
title: 删除 Azure VMware 解决方案（AVS）私有云
description: 介绍如何删除 AVS 私有云。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77cbfb19c3861bac517142f7491e6b1a5fb4ca27
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024749"
---
# <a name="delete-an-avs-private-cloud"></a>删除 AVS 私有云

AVS 提供删除 AVS 私有云的灵活性。 AVS 私有云包含一个或多个 vSphere 群集。 每个群集可以有3到16个节点。 删除 AVS 私有云时，所有群集都将被删除。

## <a name="before-you-begin"></a>开始之前

删除 AVS 私有云会删除整个 AVS 私有云。 将删除 AVS 私有云的所有组件。 如果要保留任何数据，请确保已将数据备份到本地存储或 Azure 存储。

AVS 私有云的组件包括：

* AVS 节点
* 虚拟机
* Vlan/子网
* AVS 私有云上存储的所有用户数据
* VLAN/子网的所有防火墙规则附件

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="delete-an-avs-private-cloud"></a>删除 AVS 私有云

1. [访问 AVS 门户](access-cloudsimple-portal.md)。

2. 打开 "**资源**" 页。

3. 单击要删除的 AVS 私有云

4. 在 "摘要" 页上，单击 "**删除**"。

    ![删除 avs 私有云](media/delete-private-cloud.png)

5. 在 "确认" 页上，输入 AVS 私有云的名称，然后单击 "**删除**"。 

    ![删除 avs 私有云-确认](media/delete-private-cloud-confirm.png)

AVS 私有云已标记为要删除。 删除过程将在三个小时后开始，并删除 AVS 私有云。

> [!CAUTION]
> 删除 AVS 私有云后，必须删除节点。 在从订阅中删除节点之前，将继续对节点进行计数。

## <a name="next-steps"></a>后续步骤

* [删除节点](delete-nodes.md)
