---
title: "使用 Azure 门户打开 VM 的端口 | Microsoft Docs"
description: "了解如何在 Azure 门户中使用 Resource Manager 部署模型为 Windows VM 打开端口/创建终结点"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 7b7c0251c0b2616f82b03b7288cbfe3b3b22c9c4
ms.lasthandoff: 03/31/2017


---
# <a name="opening-ports-to-a-vm-in-azure-using-the-azure-portal"></a>在 Azure 中使用 Azure 门户打开 VM 的端口
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>快速命令
也可以[使用 Azure PowerShell 执行这些步骤](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

首先，创建网络安全组。 在门户中选择一个资源组、单击“添加”，然后搜索并选择“网络安全组”：

![添加网络安全组](./media/nsg-quickstart-portal/add-nsg.png)

输入网络安全组的名称、选择或创建资源组，然后选择一个位置。 完成后，单击“创建”：

![创建网络安全组](./media/nsg-quickstart-portal/create-nsg.png)

选择新的网络安全组。 选择“入站安全规则”，然后单击“添加”按钮以创建规则：

![添加入站规则](./media/nsg-quickstart-portal/add-inbound-rule.png)

提供新规则的名称。 默认已输入端口 80。 当向网络安全组添加其他规则时，可以在此边栏选项卡中更改源、协议和目标。 单击“确定”以创建规则：

![创建入站规则](./media/nsg-quickstart-portal/create-inbound-rule.png)

最后一步是将网络安全组与子网或特定网络接口相关联。 将网络安全组与子网相关联。 选择“子网”，然后单击“关联”：

![将网络安全组与子网相关联](./media/nsg-quickstart-portal/associate-subnet.png)

选择虚拟网络，然后选择相应的子网：

![将网络安全组与虚拟网络相关联](./media/nsg-quickstart-portal/select-vnet-subnet.png)

现在你已经创建了网络安全组、创建了允许端口 80 上的流量的入站规则，并将网关安全组与子网进行了关联。 连接到该子网的任何 VM 都可以通过端口 80 访问。

## <a name="more-information-on-network-security-groups"></a>有关网络安全组的详细信息
利用此处的快速命令，可以让流向 VM 的流量开始正常运行。 网络安全组提供许多出色的功能和粒度来控制资源的访问。 可以[在此处详细了解如何创建网络安全组和 ACL 规则](../../virtual-network/virtual-networks-create-nsg-arm-ps.md)。

可以将网络安全组和 ACL 规则定义为 Azure Resource Manager 模板的一部分。 详细了解如何[使用模板创建网络安全组](../../virtual-network/virtual-networks-create-nsg-arm-template.md)。

如果需要使用端口转发将唯一的外部端口映射至 VM 上的内部端口，则需要使用负载均衡器和网络地址转换 (NAT) 规则。 例如，你可能想对外公开 TCP 端口 8080，然后让流量定向到 VM 上的 TCP 端口 80。 可以了解如何[创建面向 Internet 的负载均衡器](../../load-balancer/load-balancer-get-started-internet-arm-ps.md)。

## <a name="next-steps"></a>后续步骤
在本示例中，你创建了简单的规则来允许 HTTP 流量。 你可以从下列文章中，找到有关创建更详细环境的信息：

* [Azure Resource Manager 概述](../../azure-resource-manager/resource-group-overview.md)
* [什么是网络安全组 (NSG)？](../../virtual-network/virtual-networks-nsg.md)
* [Azure Resource Manager 中负载均衡器的概述](../../load-balancer/load-balancer-arm.md)


