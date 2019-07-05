---
title: 创建 Azure 堡垒主机 |Microsoft Docs
description: 在本文中，了解如何创建 Azure 堡垒主机
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: cherylmc
ms.openlocfilehash: 4a52383e6ab24c6ae1e2be0b67293d65dfa04466
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477886"
---
# <a name="create-an-azure-bastion-host-preview"></a>创建 Azure 堡垒主机 （预览）

本文介绍如何创建 Azure 堡垒主机。 一旦您在虚拟网络中设置 Azure 堡垒服务，无缝的 RDP/SSH 体验可供同一虚拟网络中的所有 Vm。 此部署是按每个虚拟网络执行的，不是按每个订阅/帐户或虚拟机操作的。

有两种方法可以创建堡垒主机资源：

* 创建使用 Azure 门户的堡垒资源。
* 使用现有的 VM 设置 Azure 门户中创建堡垒资源。

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>开始之前

公共预览版仅限于以下 Azure 公共区域推出：

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>创建守护主机

此部分帮助你从 Azure 门户创建新的 Azure 堡垒资源。

1. 从主页中[Azure 门户-堡垒预览版](https://aka.ms/BastionHost)，单击 **+ 创建资源**。 请确保使用提供的用于访问在门户中为此预览版中，而不是常规的 Azure 门户的链接。

1. 上**新建**页上，在*在 Marketplace 中搜索*字段中，键入**堡垒**，然后单击**Enter**若要获取对搜索结果。

1. 从结果中，单击**堡垒 （预览版）** 。 请确保发布服务器是*Microsoft*和类别*网络*。

1. 上**堡垒 （预览版）** 页上，单击**创建**以打开**创建堡垒**页。

1. 上**创建堡垒**页上，配置新的堡垒资源。 指定堡垒资源的配置设置。

    ![创建堡垒](./media/bastion-create-host-portal/settings.png)

    * **订阅**：你想要用于创建新的堡垒资源的 Azure 订阅。
    * **资源组**：将在其中创建新的堡垒资源在 Azure 资源组。 如果没有现有的资源组，可以创建一个新。
    * **名称**：新的堡垒资源的名称
    * **区域**：该资源将在其中创建 Azure 公共区域。
    * **虚拟网络**：将在其中创建堡垒资源在虚拟网络。 您可以创建新的虚拟网络在门户中在此过程中，没有或不想要使用现有的虚拟网络的情况下。 如果使用现有的虚拟网络，请确保现有的虚拟网络具有足够可用地址空间来容纳堡垒子网的要求。
    * **子网**：新的堡垒主机资源将部署到的虚拟网络中的子网。 必须创建使用的名称值的子网**AzureBastionSubnet**。 此值可以让 Azure 知道堡垒将资源部署到的子网。 这是不同于网关子网。 我们强烈建议使用至少为/27 或更大子网 （/ 27、 / 26 等）。 创建**AzureBastionSubnet**没有任何网络安全组的情况下将路由表或委派。
    * **公共 IP 地址**：将在其 （通过端口 443） 中访问 RDP/SSH 守护资源的公共 IP。 创建新的公共 IP，或使用一个现有。 公共 IP 地址必须在要创建的堡垒资源所在的同一区域中。
    * **公共 IP 地址名称**：公共 IP 地址资源的名称。
    * **公共 IP 地址 SKU**:默认情况下预填充**标准**。
    * **分配**:默认情况下预填充**静态**。

1. 完成指定设置，请单击**查看 + 创建**。 此验证的值。 验证后，可以开始创建过程。
1. 在创建堡垒页上，单击**创建**。
1. 你将看到一条消息，告知你的部署正在进行。 状态将显示此页上创建的资源。 需要大约 5 分钟，对于要创建和部署的堡垒资源。

## <a name="createvmset"></a>创建使用虚拟机设置的堡垒主机

如果在门户中创建的堡垒主机，通过使用现有的 VM，各种设置将自动默认对应于虚拟机和/或虚拟网络。

1. 在中[Azure 门户-堡垒预览版](https://aka.ms/BastionHost)，导航到你的虚拟机，然后单击**Connect**。

    ![VM 连接](./media/bastion-create-host-portal/vmsettings.png)

1. 在右侧边栏中，单击**堡垒**，然后**使用堡垒**。

    ![Bastion](./media/bastion-create-host-portal/vmbastion.png)

1. 在堡垒页上，填写以下设置字段：

    * **名称**：你想要创建的堡垒主机的名称。
    * **子网**：到哪些堡垒资源将部署在虚拟网络内部子网。 必须具有名称创建的子网**AzureBastionSubnet**。 这可以让 Azure 知道要部署到的堡垒资源的子网。 这是不同于网关子网。 单击**管理子网配置**创建 Azure 堡垒子网。 我们强烈建议使用至少为/27 或更大子网 (/ 27、 / 26，等等。)。 创建**AzureBastionSubnet**没有任何网络安全组的情况下将路由表或委派。 单击**创建**若要创建子网，然后继续进行下一步的设置。

      ![Bastion](./media/bastion-create-host-portal/subnet.png)
      
    * **公共 IP 地址**：将在其 （通过端口 443） 中访问 RDP/SSH 守护资源的公共 IP。 创建新的公共 IP，或使用一个现有。 公共 IP 地址必须在要创建的堡垒资源所在的同一区域中。
    * **公共 IP 地址名称**：公共 IP 地址资源的名称。
1. 验证在屏幕上，单击**创建**。 等待大约 5 分钟，对于要创建和部署的堡垒资源。

## <a name="next-steps"></a>后续步骤

读取[堡垒常见问题](bastion-faq.md)
