---
title: 使用 Azure 门户打开 VM 的端口 | Microsoft Docs
description: 了解如何在 Azure 门户中使用 Resource Manager 部署模型为 Windows VM 打开端口/创建终结点
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: cac17403425f53593d4f48692b4216a92c8624e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61481702"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>如何使用 Azure 门户打开虚拟机端口
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>登录 Azure
通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-network-security-group"></a>创建网络安全组

1. 搜索并选择 VM 的资源组，选择“添加”  ，然后搜索并选择“网络安全组”  。

2. 选择“创建”  。

    此时将打开“创建网络安全组”  窗口。

    ![创建网络安全组](./media/nsg-quickstart-portal/create-nsg.png)

2. 输入网络安全组的名称。 

3. 选择或创建一个资源组，然后选择位置。

4. 选择“创建”  以创建网络安全组。

## <a name="create-an-inbound-security-rule"></a>创建入站安全规则

1. 选择新的网络安全组。 

2. 选择“入站安全规则”  ，并选择“添加”  。

    ![添加入站规则](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. 选择“高级”。  

4. 从下拉菜单中选择常见的“服务”，如 HTTP   。 如果要提供要使用的特定端口，也可以选择“自定义”  。 

5. （可选）更改**优先级**或**名称**。 优先级会影响应用规则的顺序：数值越小，越先应用规则。

6. 选择“添加”  以创建规则。

## <a name="associate-your-network-security-group-with-a-subnet"></a>将网络安全组与子网相关联

最后一步是将网络安全组与子网或特定网络接口相关联。 对于此示例，我们将网络安全组与子网相关联。 

1. 选择“子网”，然后选择“关联”   。

    ![将网络安全组与子网相关联](./media/nsg-quickstart-portal/associate-subnet.png)

2. 选择虚拟网络，并选择相应的子网。

    ![将网络安全组与虚拟网络相关联](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    现在，连接到该子网的任何 VM 都可以通过端口 80 访问。

## <a name="additional-information"></a>其他信息

也可以[使用 Azure PowerShell 执行本文中的步骤](nsg-quickstart-powershell.md)。

使用本文中介绍的命令可以快速获取流向 VM 的流量。 网络安全组提供许多出色的功能和粒度来控制对资源的访问。 有关详细信息，请参阅[使用网络安全组筛选网络流量](../../virtual-network/tutorial-filter-network-traffic.md)。

对于高可用性 Web 应用程序，请考虑将 VM 放在 Azure 负载均衡器后面。 当负载均衡器向 VM 分配流量时，网络安全组可以筛选流量。 有关详细信息，请参阅[在 Azure 中均衡 Windows 虚拟机负载以创建高可用性应用程序](tutorial-load-balancer.md)。

## <a name="next-steps"></a>后续步骤
在本文中，已经创建了网络安全组、创建了允许端口 80 上的 HTTP 流量的入站规则，并将该规则与子网进行了关联。 

可以从下列文章中，找到有关创建更详细环境的信息：
- [Azure 资源管理器概述](../../azure-resource-manager/resource-group-overview.md)
- [安全组](../../virtual-network/security-overview.md)