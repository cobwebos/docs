---
title: 创建 Azure 堡垒主机：门户
description: 本文介绍如何创建 Azure 堡垒主机，该主机提供与虚拟网络中所有虚拟机的 RDP/SSH 连接。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 04/24/2020
ms.author: cherylmc
ms.openlocfilehash: 1e0bc9a17a12afac6d91867b19aba0f90b7f6edb
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270744"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>使用门户创建 Azure Bastion 主机

本文介绍如何使用 Azure 门户创建 Azure Bastion 主机。 在虚拟网络中预配 Azure Bastion 服务后，即可在该虚拟网络中的所有 VM 上获得无缝的 RDP/SSH 体验。 Azure Bastion 部署是按虚拟网络进行的，而不是按订阅/帐户或虚拟机进行的。

可通过手动指定所有设置，或使用与现有 VM 相对应的设置在门户中创建新的 Bastion 主机资源。 若要使用 VM 设置创建 Bastion 主机，请参阅[快速入门](quickstart-host-portal.md)一文。 或者，可使用 [Azure PowerShell](bastion-create-host-powershell.md) 来创建 Azure Bastion 主机。

## <a name="before-you-begin"></a>准备阶段

可在下面的 Azure 公共区域中使用 Bastion：

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>创建 Bastion 主机

本部分帮助你通过 Azure 门户创建新的 Azure Bastion 资源。

1. 在 [Azure 门户](https://portal.azure.com)菜单或“主页”页上，选择“创建资源” 。

1. 在“新建”页面上的“搜索市场”字段中，键入“Bastion”，然后单击 Enter 转到搜索结果 。

1. 从结果中单击“Bastion”。 确保发行商为“Microsoft”，类别为“网络”。 

1. 在“Bastion”页面上单击“创建”，打开“创建 Bastion”页面  。

1. 在“创建 Bastion”页面上，配置新的 Bastion 资源。 指定 Bastion 资源的配置设置。

    ![创建 Bastion](./media/bastion-create-host-portal/settings.png)

    * **订阅**：你需要用于新建 Bastion 资源的 Azure 订阅。
    * **资源组**：将在其中新建 Bastion 资源的 Azure 资源组。 如果目前没有资源组，可新建一个。
    * **名称**：新 Bastion 资源的名称
    * **区域**：将在其中创建资源的 Azure 公共区域。
    * **虚拟网络**：将在其中创建 Bastion 资源的虚拟网络。 你可在此过程中通过门户创建新的虚拟网络，也可使用现有虚拟网络。 如果是后者，请确保现有虚拟网络有足够多的空闲地址空间来满足 Bastion 子网的要求。
    * **子网**：虚拟网络中的子网，新的 Bastion 主机将部署到该子网中。 该子网将专用于 Bastion 主机，且必须命名为 AzureBastionSubnet。 该子网必须至少为 /27。
    
       AzureBastionSubnet 不支持[用户定义的路由](../virtual-network/virtual-networks-udr-overview.md#custom-routes)，但支持[网络安全组](bastion-nsg.md)。
    * **公共 IP 地址**：Bastion 资源的公共 IP，将在该 IP 上通过端口 443 访问 RDP/SSH。 新建公共 IP，或使用现有公共 IP。 公共 IP 地址必须与要创建的 Bastion 资源位于同一区域。
    * **公共 IP 地址名称**：公共 IP 地址资源的名称。
    * **公共 IP 地址 SKU**：默认情况下，该设置预填充为“标准”。 Azure Bastion 仅使用/支持标准公共 IP SKU。
    * **分配**：默认情况下，该设置预填充为“静态”。

1. 指定设置后，单击“查看 + 创建”。 这会验证值。 验证通过后，可开始创建过程。
1. 在“创建 Bastion”页面上，单击“创建” 。
1. 你将看到一条消息，其中指出你的部署正在进行中。 创建资源后，此页面上将显示状态。 创建和部署 Bastion 资源大约需要 5 分钟的时间。

## <a name="next-steps"></a>后续步骤

* 有关其他信息，请参阅 [Bastion 常见问题解答](bastion-faq.md)。

* 若要在 Azure Bastion 子网中使用网络安全组，请参阅[使用 NSG](bastion-nsg.md)。
