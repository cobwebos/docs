---
title: 创建 Azure 堡垒主机：门户
description: 本文介绍如何使用门户创建 Azure 堡垒主机
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 14a596d78fb1f560c62013e7e439ed60d3a29b8f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366137"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>使用门户创建 Azure 堡垒主机

本文介绍如何使用 Azure 门户创建 Azure 堡垒主机。 在虚拟网络中预配 Azure 堡垒服务后，同一虚拟网络中的所有 Vm 都可以使用无缝的 RDP/SSH 体验。 Azure Bastion 部署是按虚拟网络进行的，而不是按订阅/帐户或虚拟机进行的。

可以通过手动指定所有设置或使用对应于现有 VM 的设置在门户中创建新的堡垒主机资源。 若要使用 VM 设置创建堡垒主机，请参阅[快速入门](quickstart-host-portal.md)文章。 或者，可以使用[Azure PowerShell](bastion-create-host-powershell.md)创建 Azure 堡垒主机。

## <a name="before-you-begin"></a>开始之前

堡垒在以下 Azure 公共区域中提供：

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>创建堡垒主机

本部分可帮助你从 Azure 门户创建新的 Azure 堡垒资源。

1. 在 " [Azure 门户](https://portal.azure.com)" 菜单或从 "**主页**" 上，选择 "**创建资源**"。

1. 在 "**新建**" 页上的 "*搜索应用商店"* 字段中，键入**堡垒**，然后单击**Enter**转到搜索结果。

1. 在结果中，单击 "**堡垒**"。 确保发行商为“Microsoft”，类别为“网络”。

1. 在**堡垒**页面上，单击 "**创建**" 以打开 "**创建堡垒**" 页面。

1. 在 "**创建堡垒**" 页上，配置新的堡垒资源。 指定堡垒资源的配置设置。

    ![创建堡垒](./media/bastion-create-host-portal/settings.png)

    * **订阅**：要用来创建新的堡垒资源的 Azure 订阅。
    * **资源组**：要在其中创建新的堡垒资源的 Azure 资源组。 如果没有现有的资源组，则可以创建一个新的资源组。
    * **名称**：新堡垒资源的名称
    * **区域**：将在其中创建资源的 Azure 公共区域。
    * **虚拟网络**：将在其中创建堡垒资源的虚拟网络。 在此过程中，你可以在门户中创建新的虚拟网络，也可以使用现有的虚拟网络。 如果使用现有的虚拟网络，请确保现有虚拟网络有足够的可用地址空间来满足堡垒子网要求。
    * **子网**：要将新的堡垒主机资源部署到的虚拟网络中的子网。 必须使用 name 值**AzureBastionSubnet**创建子网。 此值允许 Azure 知道要将堡垒资源部署到哪个子网。 这不同于网关子网。 必须使用至少/27 或更大（/27、/26 等）的子网。
    
       创建不包含任何路由表或委托的**AzureBastionSubnet** 。 如果在**AzureBastionSubnet**上使用网络安全组，请参阅[使用 nsg](bastion-nsg.md)一文。
    * **公共 ip 地址**：访问 RDP/SSH 的堡垒资源（通过端口443）的公共 ip。 创建新的公共 IP，或使用现有的公共 IP。 公共 IP 地址必须与要创建的堡垒资源位于同一区域。
    * **公共 ip 地址名称**：公共 ip 地址资源的名称。
    * **公共 IP 地址 SKU**：默认情况下，此设置预填充为 "**标准**"。 Azure 堡垒只使用/支持标准公共 IP SKU。
    * **赋值**：默认情况下，此设置预填充为**静态**。

1. 完成指定设置后，单击 "**查看 + 创建**"。 这会验证值。 验证通过后，可以开始创建过程。
1. 在 "**创建堡垒**" 页上，单击 "**创建**"。
1. 你将看到一条消息，告知你的部署正在进行。 创建资源后，状态将显示在此页上。 创建和部署堡垒资源大约需要5分钟。

## <a name="next-steps"></a>后续步骤

* 有关其他信息，请参阅[堡垒常见问题解答](bastion-faq.md)。

* 若要在 Azure 堡垒子网中使用网络安全组，请参阅[使用 nsg](bastion-nsg.md)。