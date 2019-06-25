---
title: 使用 Vm 和 nsg 部署在 Azure 堡垒 |Microsoft Docs
description: 本文介绍如何结合使用 Azure 堡垒 NSG 访问
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: e7210b2b1be072f5326070d768d5fe12c386ee0b
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191598"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>使用 NSG 的访问和 Azure 堡垒 （预览版）

在使用 Azure 堡垒，可以使用网络安全组 (Nsg)。 有关详细信息，请参阅[安全组](../virtual-network/security-overview.md)。 

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

![体系结构](./media/bastion-nsg/nsg_architecture.png)

在此图中：

* 在虚拟网络中部署堡垒主机。
* 用户连接到在 Azure 门户中使用任何 HTML5 浏览器。
* 用户选择要连接到虚拟机。
* 单击一下，在浏览器中打开 RDP/SSH 会话。
* 在 Azure VM 上需要没有公共 IP。

## <a name="nsg"></a>网络安全组

* **AzureBastionSubnet:** 在特定 AzureBastionSubnet 中部署 azure 堡垒。  
    * **从公共 internet 的入站流量：** Azure 堡垒将创建需要端口 443 上的传入流量的公共 IP 启用的公共 IP。 端口 3389/22 不需要在 AzureBastionSubnet 上打开。
    * **到目标 Vm 的出口流量：** Azure 堡垒将到达目标 Vm 通过专用 IP。 Nsg 需要允许到其他目标 VM 子网的传出流量。
* **目标 VM 子网：** 这是包含目标虚拟机的 RDP/ssh 连接到所需的子网。
    * **从 Azure 堡垒入口流量：** Azure 堡垒将到达到目标 VM 通过专用 IP。 通过 RDP/SSH 端口 (端口 3389 和 22，分别) 需要在目标端 VM 上打开通过专用 IP。

## <a name="apply"></a>将 Nsg 应用到 AzureBastionSubnet

如果将应用到 Nsg **AzureBastionSubnet**，允许以下 Azure 控制平面和基础结构的两个服务标记：

* **（仅限资源管理器） GatewayManager**:此标记表示 Azure 网关管理器服务的地址前缀。 如果为值指定 GatewayManager，是允许还是拒绝到 GatewayManager 流量。

* **AzureCloud （仅限资源管理器）** :此标记表示 azure 包括所有数据中心的公共 IP 地址的 IP 地址空间。 值指定 AzureCloud，是允许还是拒绝流量到 Azure 公共 IP 地址。 如果只想要允许访问特定区域中的 AzureCloud，可以指定该区域。 例如，如果你想要只允许访问 Azure AzureCloud 美国东部区域中，您可以指定 AzureCloud.EastUS 作为服务标记。

## <a name="next-steps"></a>后续步骤

有关 Azure 堡垒详细信息，请参阅[常见问题](bastion-faq.md)