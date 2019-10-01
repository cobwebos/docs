---
title: 使用 Azure 堡垒中的 Vm 和 Nsg |Microsoft Docs
description: 本文介绍如何使用 Azure 堡垒合并 NSG 访问
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: cherylmc
ms.openlocfilehash: 4f99b24435998fc4d0c7ab724c66a318586a80d4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694943"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>使用 NSG 访问和 Azure 堡垒（预览版）

使用 Azure 堡垒时，可以使用网络安全组（Nsg）。 有关详细信息，请参阅[安全组](../virtual-network/security-overview.md)。 

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

![体系结构](./media/bastion-nsg/nsg_architecture.png)

在此图中：

* Bastion 主机部署在虚拟网络中。
* 用户使用任何 HTML5 浏览器连接到 Azure 门户。
* 用户选择要连接到的虚拟机。
* 单击一下，在浏览器中打开 RDP/SSH 会话。
* Azure VM 无需公共 IP。

## <a name="nsg"></a>网络安全组

* **AzureBastionSubnet:** Azure 堡垒部署在特定 AzureBastionSubnet 中。  
    * **从公共 internet 传输流量：** Azure 堡垒会创建一个公共 IP，要求在公共 IP 上为入口通信启用端口443。 不需要在 AzureBastionSubnet 上打开端口3389/22。
    * **流向目标 Vm 的出口流量：** Azure 堡垒将通过专用 IP 到达目标 Vm。 Nsg 需要允许传出流量流向其他目标 VM 子网。
* **目标 VM 子网：** 这是包含要 RDP/SSH 连接到的目标虚拟机的子网。
    * **传入来自 Azure 堡垒的流量：** Azure 堡垒将通过专用 IP 进入目标 VM。 RDP/SSH 端口（分别为端口3389和22）需要通过专用 IP 在目标 VM 端打开。

## <a name="apply"></a>将 Nsg 应用于 AzureBastionSubnet

如果将 Nsg 应用于**AzureBastionSubnet**，则允许 Azure 控制平面和基础结构使用以下两个服务标记：

* **GatewayManager （仅资源管理器）** ：此标记表示 Azure 网关管理器服务的地址前缀。 如果为值指定 GatewayManager，则允许或拒绝流量发送到 GatewayManager。  如果要在 AzureBastionSubnet 上创建 Nsg，请为入站流量启用 GatewayManager 标记。

* **AzureCloud （仅资源管理器）** ：此标记表示 Azure 的 IP 地址空间，包括所有数据中心公共 IP 地址。 如果为值指定 AzureCloud，则允许或拒绝对 Azure 公共 IP 地址的流量。 如果要仅允许访问特定区域中的 AzureCloud，可以指定区域。 例如，如果你想要只允许访问美国东部区域中的 Azure AzureCloud，则可以指定 AzureCloud 作为服务标记。 如果要在 AzureBastionSubnet 上创建 Nsg，请为出站流量启用 AzureCloud 标记。 如果为入站连接到 Internet 打开端口443，则不需要为入站流量启用 AzureCloud 标记。

## <a name="next-steps"></a>后续步骤

有关 Azure 堡垒的详细信息，请参阅[常见问题解答](bastion-faq.md)
