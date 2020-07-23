---
title: 方案： Azure 防火墙-Interhub 路由
titleSuffix: Azure Virtual WAN
description: 具有 Azure 防火墙的多个中心之间的路由路由方案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 1057dc28682c90374b2f8e8e0297f3d769c08bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567113"
---
# <a name="scenario-azure-firewall---interhub-preview"></a>方案： Azure Firewall-Interhub （预览版）

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在此方案中，目标是在包含 Azure 防火墙的多个中心之间进行路由。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="scenario-architecture"></a><a name="architecture"></a>方案体系结构

在此方案中，我们假定以下配置：

* 每个中心都有多个使用 Azure 防火墙的中心。
* 你使用的是安全虚拟中心。
* 已设置适用于专用流量（VNet）、Internet 和分支流量的相应策略。
* VNet 到 Internet （V2I）、VNet 到分支（V2B）、分支到 VNet （B2V），所有这些都通过 Azure 防火墙。

其他注意事项：

* 对于使用 Azure 防火墙的集线器间方案，假设辐射 Vnet 不关联到单独的路由表。 （例如，与**路由表 A 关联的** **vnet 1**和与**路由表 B**关联的**vnet 2** ）。 所有 Vnet 都关联到 Azure 防火墙的路由所在的同一个路由表。
* 通过 Azure 防火墙进行保护当前仅限于**分支 < > VNet**和**Internet**通信。 当前不支持通过 Azure 防火墙的分支到分支流。

**图1**

architecture 

## <a name="scenario-workflow"></a><a name="workflow"></a>方案工作流

若要配置此方案，请考虑以下步骤：

### <a name="step-1"></a><a name="step-1"></a>步骤 1

假设你已通过 Azure 防火墙管理器保护了连接，则第一步是确保所有分支和 VNet**连接都不会传播。** 这是确保通过 Azure 防火墙设置流量所需的。

1. 选择中心并编辑**None**路由表。
1. 更新**传播**以选择所有分支和所有 vnet。

### <a name="step-2"></a><a name="step-2"></a>步骤2

为每个中心设置自定义路由表。

1. 对于**中心 1**，创建路由表**RT_Hub1**。

    * 如果使用的是 Azure 防火墙管理器，它会自动创建 0.0.0.0/0 的路由，并在中心的默认路由表中**AZFW1**下一个跃点。 我们将在步骤3中修改此设置。 对于**RT_Hub1**，使用下一个跃点**AZFW1**为 0.0.0.0/0 显式创建一个条目。 此设置将通过 AZFW1 激活 V2V、B2V 和 V2I。
    * 由于你希望中心**2**分支和 Vnet 通过**AZFW2** （而不是 Via AZFW1）到达**集线器 1** （而不是 via），因此需要为**中心 2**分支（10.5.0.0/16->AZFW2）和 vnet （10.2.0.0/16->AZFW2）添加另一个2聚合路由。

1. 对于**中心 2**，创建路由表**RT_Hub2**。

    * 如果使用的是 Azure 防火墙管理器，它会自动创建 0.0.0.0/0 的路由，并在中心的默认路由表中**AZFW2**下一个跃点。 我们将在步骤3中修改此设置。 对于**RT_Hub2**，使用下一个跃点**AZFW2**为 0.0.0.0/0 显式创建一个条目。 此设置将通过**AZFW2**激活 V2V、B2V 和 V2I。
    * 由于你希望中心2的**AZFW2**保护集线器间流量，因此你无需显式添加与上一个中心1步骤中的第二个项目符号类似的步骤。

### <a name="step-3"></a><a name="step-3"></a>步骤3

修改每个集线器中的**默认路由表**，将分支的静态路由添加**到 VNet** （B2V），并通过 Azure 防火墙**分支到 Internet** （B2I）流。 当前不支持通过 Azure 防火墙进行分支到分支。

1. 对于**中心1默认路由表**：

    * 通过 AZFW2： 10.5.0.0/16->AZFW2**分支到中心2分支**。 此配置为集线器2防火墙设置路由。
    * 通过 AZFW2： 10.2.0.0/16->AZFW2**分支到中心 2 vnet**。 此配置为集线器2防火墙设置路由。
    * 分支**到分支（本地）/分支到 VNet （本地）/** 分支到 Internet： 0.0.0.0/0->AZFW1。

2. 对于**中心2默认路由表**：

    * 分支到分支（本地和远程）/分支到 VNet （本地和远程）/分支到 Internet： 0.0.0.0/0->AZFW2。

这将导致路由配置更改，如下图所示

**图2**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/workflow.png" alt-text="workflow":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
* 有关如何配置虚拟中心路由的详细信息，请参阅[如何配置虚拟中心路由](how-to-virtual-hub-routing.md)。
