---
title: 创建 Azure 堡垒主机 |Microsoft Docs
description: 本文介绍如何创建 Azure 堡垒主机
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: cherylmc
ms.openlocfilehash: 4c283e840208ab9f53d084ca17221ea2c835b794
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814139"
---
# <a name="create-an-azure-bastion-host-preview"></a>创建 Azure 堡垒主机 (预览)

本文介绍如何创建 Azure 堡垒主机。 在虚拟网络中预配 Azure 堡垒服务后, 同一虚拟网络中的所有 Vm 都可以使用无缝 RDP/SSH 体验。 此部署是按每个虚拟网络执行的，不是按每个订阅/帐户或虚拟机操作的。

可以通过两种方法创建堡垒主机资源:

* 使用 Azure 门户创建堡垒资源。
* 使用现有 VM 设置在 Azure 门户中创建堡垒资源。

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>开始之前

公共预览版仅限于以下 Azure 公共区域:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>创建堡垒主机

本部分可帮助你从 Azure 门户创建新的 Azure 堡垒资源。

1. 在[Azure 门户-堡垒预览](https://aka.ms/BastionHost)的主页中, 单击 " **+ 创建资源**"。 请确保使用提供的链接访问此预览版的门户, 而不是常规 Azure 门户。

1. 在 "**新建**" 页上的 "*搜索应用商店"* 字段中, 键入**堡垒**, 然后单击**Enter**转到搜索结果。

1. 在结果中, 单击 "**堡垒 (预览)** "。 请确保发布者是*Microsoft* , 并且类别为 "*网络*"。

1. 在 "**堡垒 (预览)** " 页上, 单击 "**创建**" 以打开 "**创建堡垒**" 页面。

1. 在 "**创建堡垒**" 页上, 配置新的堡垒资源。 指定堡垒资源的配置设置。

    ![创建堡垒](./media/bastion-create-host-portal/settings.png)

    * **订阅**：要用于创建新的堡垒资源的 Azure 订阅。
    * **资源组**：将在其中创建新的堡垒资源的 Azure 资源组。 如果没有现有的资源组, 则可以创建一个新的资源组。
    * **名称**：新堡垒资源的名称
    * **区域**：将在其中创建资源的 Azure 公共区域。
    * **虚拟网络**：将在其中创建堡垒资源的虚拟网络。 在此过程中, 你可以在门户中创建新的虚拟网络, 以防你没有或不想使用现有虚拟网络。 如果使用现有的虚拟网络, 请确保现有虚拟网络有足够的可用地址空间来满足堡垒子网要求。
    * **子网**：虚拟网络中新的堡垒主机资源将部署到的子网。 必须使用 name 值**AzureBastionSubnet**创建子网。 此值允许 Azure 知道要将堡垒资源部署到哪个子网。 这不同于网关子网。必须至少使用一个/27 或更大的子网 (/27、/26 等)。 创建不包含任何路由表或委托的**AzureBastionSubnet** 。 在**AzureBastionSubnet**上使用网络安全组时, 请参阅[使用 nsg](bastion-nsg.md)。
    * **公共 IP 地址**：要在其上访问 RDP/SSH 的堡垒资源 (通过端口 443) 的公共 IP。 创建新的公共 IP, 或使用现有的公共 IP。 公共 IP 地址必须与要创建的堡垒资源位于同一区域。
    * **公共 IP 地址名称**：公共 IP 地址资源的名称。
    * **公共 IP 地址 SKU**:默认预填充为 "**标准**"。 Azure 堡垒只使用/支持标准公共 IP SKU。
    * **分配**:默认预填充为**静态**。

1. 完成指定设置后, 单击 "**查看 + 创建**"。 这会验证值。 验证通过后, 可以开始创建过程。
1. 在 "创建堡垒" 页上, 单击 "**创建**"。
1. 你将看到一条消息, 告知你的部署正在进行。 创建资源后, 状态将显示在此页上。 创建和部署堡垒资源大约需要5分钟。

## <a name="createvmset"></a>使用 VM 设置创建堡垒主机

如果使用现有 VM 在门户中创建堡垒主机, 则会自动为虚拟机和/或虚拟网络设置不同的默认设置。

1. 在[Azure 门户](https://aka.ms/BastionHost)中, 导航到虚拟机, 然后单击 "**连接**"。

    ![VM 连接](./media/bastion-create-host-portal/vmsettings.png)

1. 在右侧边栏上, 单击 "**堡垒**", 然后**使用 "堡垒**"。

    ![Bastion](./media/bastion-create-host-portal/vmbastion.png)

1. 在 "堡垒" 页上, 填写以下设置字段:

    * **名称**：要创建的堡垒主机的名称。
    * **子网**：虚拟网络中的子网, 堡垒资源将部署到该网络。 必须用名称**AzureBastionSubnet**创建子网。 这样, Azure 便可以知道要将堡垒资源部署到哪个子网。 这不同于网关子网。 单击 "**管理子网配置**" 以创建 Azure 堡垒子网。 我们强烈建议你至少使用/27 或更大的子网 (/27、/26 等)。 创建没有任何网络安全组、路由表或委托的**AzureBastionSubnet** 。 单击 "**创建**" 以创建子网, 然后继续下一个设置。

      ![Bastion](./media/bastion-create-host-portal/subnet.png)
      
    * **公共 IP 地址**：要在其上访问 RDP/SSH 的堡垒资源 (通过端口 443) 的公共 IP。 创建新的公共 IP, 或使用现有的公共 IP。 公共 IP 地址必须与要创建的堡垒资源位于同一区域。
    * **公共 IP 地址名称**：公共 IP 地址资源的名称。
1. 在验证屏幕上, 单击 "**创建**"。 等待大约5分钟, 以创建并部署堡垒资源。

## <a name="next-steps"></a>后续步骤

阅读[堡垒常见问题解答](bastion-faq.md)
