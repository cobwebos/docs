---
title: 创建 Azure 堡垒主机：门户
description: 在本文中，了解如何使用门户创建 Azure 堡垒主机
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 14a596d78fb1f560c62013e7e439ed60d3a29b8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366137"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>使用门户创建 Azure 堡垒主机

本文介绍如何使用 Azure 门户创建 Azure 堡垒主机。 在虚拟网络中预配 Azure Bastion 服务后，同一虚拟网络中的所有 VM 都可以使用无缝的 RDP/SSH 体验。 Azure Bastion 部署是按虚拟网络进行的，而不是按订阅/帐户或虚拟机进行的。

您可以通过手动指定所有设置或使用与现有 VM 对应的设置在门户中创建新的堡垒主机资源。 要使用 VM 设置创建堡垒主机，请参阅[快速入门](quickstart-host-portal.md)文章。 或者，可以使用 Azure [PowerShell](bastion-create-host-powershell.md)创建 Azure 堡垒主机。

## <a name="before-you-begin"></a>开始之前

堡垒在以下 Azure 公共区域中可用：

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>创建 Bastion 主机

此部分可帮助您从 Azure 门户创建新的 Azure 堡垒资源。

1. 在[Azure 门户](https://portal.azure.com)菜单或**主页**上，选择 **"创建资源**"。

1. 在 **"新建**"页上，在 *"搜索市场"* 字段中，键入 **"堡垒**"，然后单击 **"输入**"以获取搜索结果。

1. 从结果中，单击 **"堡垒**"。 确保发行商为“Microsoft”，类别为“网络”。****

1. 在 **"堡垒**"页上，单击"**创建**"以打开"**创建堡垒**"页。

1. 在"**创建堡垒**"页上，配置新的堡垒资源。 指定堡垒资源的配置设置。

    ![创建堡垒](./media/bastion-create-host-portal/settings.png)

    * **订阅**：要用于创建新堡垒资源的 Azure 订阅。
    * **资源组**：将在其中创建新堡垒资源的 Azure 资源组。 如果没有现有资源组，则可以创建新的资源组。
    * **名称**： 新堡垒资源的名称
    * **区域**：将在 其中创建资源的 Azure 公共区域。
    * **虚拟网络**：将在其中创建堡垒资源的虚拟网络。 在此过程中，您可以在门户中创建新的虚拟网络，或使用现有的虚拟网络。 如果使用现有虚拟网络，请确保现有虚拟网络有足够的可用地址空间来满足堡垒子网要求。
    * **子网**：将部署新堡垒主机资源的虚拟网络中的子网。 您必须使用名称值**AzureBastion Subnet**创建子网。 此值使 Azure 知道要将堡垒资源部署到哪个子网。 这与网关子网不同。 您必须使用至少 /27 或更大的子网（/27、/26 等）。
    
       创建**AzureBastion Subnet，** 没有任何路由表或授权。 如果在**AzureBastionSubnet**上使用网络安全组，请参阅[使用 NSG](bastion-nsg.md)的文章。
    * **公共 IP 地址**：将访问 RDP/SSH 的堡垒资源的公共 IP（通过端口 443）。 创建新的公共 IP 或使用现有 IP。 公共 IP 地址必须与您正在创建的堡垒资源位于同一区域。
    * **公共 IP 地址名称**：公共 IP 地址资源的名称。
    * **公共 IP 地址 SKU**：此设置在默认情况下已预填充到**标准**。 Azure 堡垒仅使用/支持标准公共 IP SKU。
    * **赋值**：此设置在默认情况下预填充为 **"静态**"。

1. 完成指定设置后，单击"审阅 **+ 创建**"。 这将验证值。 验证完成后，可以开始创建过程。
1. 在"**创建堡垒**"页上，单击"**创建**"。
1. 您将看到一条消息，通知您部署正在进行。 创建资源时，状态将显示在此页面上。 创建和部署堡垒资源大约需要 5 分钟。

## <a name="next-steps"></a>后续步骤

* 有关详细信息，请阅读[堡垒常见问题解答](bastion-faq.md)。

* 要将网络安全组与 Azure 堡垒子网一起使用，请参阅[使用 NSG。](bastion-nsg.md)