---
title: 在 Azure 堡垒中使用 VM 和 NSG
description: 本文介绍如何将 NSG 访问与 Azure 堡垒合并
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: charwen
ms.openlocfilehash: 0188f9bc1c7c0e8d7fed9f590d078085b175614f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732194"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>使用 NSG 访问和 Azure 堡垒

使用 Azure 堡垒时，可以使用网络安全组 （NSG）。 有关详细信息，请参阅[安全组](../virtual-network/security-overview.md)。 

![体系结构](./media/bastion-nsg/nsg-architecture.png)

在此图中：

* 堡垒主机部署到虚拟网络。
* 用户使用任何 HTML5 浏览器连接到 Azure 门户。
* 用户导航到 Azure 虚拟机到 RDP/SSH。
* 连接集成 - 在浏览器内单击 RDP/SSH 会话
* Azure VM 无需公共 IP。

## <a name="network-security-groups"></a><a name="nsg"></a>网络安全组

本节显示用户和 Azure 堡垒之间的网络流量，以及虚拟网络中的目标 VM 的网络流量：

### <a name="azurebastionsubnet"></a><a name="apply"></a>Azure 堡垒子网

Azure 堡垒专门部署到***Azure Bastion 子网***。

* **入口流量：**

   * **来自公共互联网的入侵流量：** Azure 堡垒将创建一个公共 IP，该公共 IP 需要在公共 IP 上启用端口 443 才能用于入口流量。 端口 3389/22 不需要在 AzureBastion Subnet 上打开。
   * **来自 Azure 堡垒控制平面的入口流量：** 对于控制平面连接，启用端口 443 从**网关管理器**服务标记入站。 这使控制平面（即网关管理器）能够与 Azure 堡垒进行对话。

* **出口流量：**

   * **面向 VM 的出入口流量：** Azure 堡垒将通过专用 IP 到达目标 VM。 NSG 需要允许从流量到端口 3389 和 22 的其他目标 VM 子网。
   * **向 Azure 中的其他公共终结点的流量：** Azure 堡垒需要能够连接到 Azure 中的各种公共终结点（例如，用于存储诊断日志和计量日志）。 因此，Azure 堡垒需要出站到 443 到**Azure 云服务**标记。

### <a name="target-vm-subnet"></a>目标 VM 子网
这是包含要 RDP/SSH 的目标虚拟机的子网。

   * **来自 Azure 堡垒的入侵流量：** Azure 堡垒将通过专用 IP 到达目标 VM。 RDP/SSH 端口（端口 3389/22）需要在目标 VM 端通过专用 IP 打开。 最佳做法是，可以在此规则中添加 Azure 堡垒子网 IP 地址范围，以便仅允许 Bastion 能够在目标 VM 子网中的目标 VM 上打开这些端口。


## <a name="next-steps"></a>后续步骤

有关 Azure 堡垒的详细信息，请参阅[常见问题解答](bastion-faq.md)。
