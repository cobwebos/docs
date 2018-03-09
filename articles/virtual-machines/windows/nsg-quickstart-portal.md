---
title: "使用 Azure 门户打开 VM 的端口 | Microsoft Docs"
description: "了解如何在 Azure 门户中使用 Resource Manager 部署模型为 Windows VM 打开端口/创建终结点"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: 08f0af6ecdb45b263d39c3d2d6442f4ed555e3c3
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2018
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>如何使用 Azure 门户打开虚拟机端口
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>快速命令
也可以[使用 Azure PowerShell 执行这些步骤](nsg-quickstart-powershell.md)。

首先，创建网络安全组。 在门户中选择一个资源组，选择“添加”，搜索并选择“网络安全组”：

![添加网络安全组](./media/nsg-quickstart-portal/add-nsg.png)

输入网络安全组的名称、选择或创建资源组，并选择一个位置。 完成后，选择“创建”：

![创建网络安全组](./media/nsg-quickstart-portal/create-nsg.png)

选择新的网络安全组。 选择“入站安全规则”，然后选择“添加”按钮以创建规则：

![添加入站规则](./media/nsg-quickstart-portal/add-inbound-rule.png)

若要创建允许流量的规则，请执行以下操作：

- 选择“基本”按钮。 默认情况下，“高级”窗口会提供一些其他配置选项，例如用于定义特定源 IP 块或端口范围。
- 从下拉菜单中选择常见的“服务”，如 HTTP。 还可以选择“自定义”，提供要使用的特定端口。 
- 如果需要，请更改优先级或名称。 优先级会影响应用规则的顺序 - 数值越小，越先应用规则。
- 准备就绪后，选择“确定”即可创建规则：

![创建入站规则](./media/nsg-quickstart-portal/create-inbound-rule.png)

最后一步是将网络安全组与子网或特定网络接口相关联。 将网络安全组与子网相关联。 选择“子网”，然后选择“关联”：

![将网络安全组与子网相关联](./media/nsg-quickstart-portal/associate-subnet.png)

选择虚拟网络，并选择相应的子网：

![将网络安全组与虚拟网络相关联](./media/nsg-quickstart-portal/select-vnet-subnet.png)

现在已经创建了网络安全组、创建了允许端口 80 上的流量的入站规则，并将网关安全组与子网进行了关联。 连接到该子网的任何 VM 都可以通过端口 80 访问。

## <a name="more-information-on-network-security-groups"></a>有关网络安全组的详细信息
利用此处的快速命令，可以让流向 VM 的流量开始正常运行。 网络安全组提供许多出色的功能和粒度来控制资源的访问。 可以[在此处详细了解如何创建网络安全组和 ACL 规则](../../virtual-network/virtual-networks-create-nsg-arm-ps.md)。

对于高可用性 Web 应用程序，应将 VM 放置在 Azure 负载均衡器后。 当负载均衡器向 VM 分配流量时，网络安全组可以筛选流量。 有关详细信息，请参阅[如何在 Azure 中均衡 Linux 虚拟机负载以创建高可用性应用程序](tutorial-load-balancer.md)。

## <a name="next-steps"></a>后续步骤
在本示例中，创建了简单的规则来允许 HTTP 流量。 可以从下列文章中，找到有关创建更详细环境的信息：

* [Azure 资源管理器概述](../../azure-resource-manager/resource-group-overview.md)
* [什么是网络安全组 (NSG)？](../../virtual-network/virtual-networks-nsg.md)