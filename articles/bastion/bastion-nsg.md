---
title: 使用 Azure 堡垒中的 Vm 和 Nsg
description: 本文介绍如何使用 Azure 堡垒合并 NSG 访问
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: charwen
ms.openlocfilehash: 0188f9bc1c7c0e8d7fed9f590d078085b175614f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732194"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>使用 NSG 访问和 Azure 堡垒

使用 Azure 堡垒时，可以使用网络安全组（Nsg）。 有关详细信息，请参阅[安全组](../virtual-network/security-overview.md)。 

![体系结构](./media/bastion-nsg/nsg-architecture.png)

在此图中：

* 堡垒主机将部署到虚拟网络。
* 用户使用任何 HTML5 浏览器连接到 Azure 门户。
* 用户导航到 Azure 虚拟机并转到 RDP/SSH。
* 连接集成-单击浏览器中的 "单一单击 RDP/SSH 会话"
* Azure VM 无需公共 IP。

## <a name="network-security-groups"></a><a name="nsg"></a>网络安全组

本部分介绍用户与 Azure 堡垒之间的网络流量，以及如何在虚拟网络中对 Vm 进行目标：

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Azure 堡垒专门部署到***AzureBastionSubnet***。

* **入口流量：**

   * **从公共 Internet 传输流量：** Azure 堡垒会创建一个公共 IP，要求在公共 IP 上为入口通信启用端口443。 不需要在 AzureBastionSubnet 上打开端口3389/22。
   * **从 Azure 堡垒控制平面传输流量：** 对于控制平面连接，请启用**GatewayManager**服务标记中的端口443入站。 这将启用控制平面，即网关管理器能够与 Azure 堡垒通信。

* **出口流量：**

   * 流向**目标 vm 的出口流量：** Azure 堡垒将通过专用 IP 到达目标 Vm。 Nsg 需要允许传出流量发送到其他目标 VM 子网，以获得端口3389和22。
   * 传入**Azure 中其他公共终结点的流量：** Azure 堡垒需要能够连接到 Azure 中的各种公共终结点（例如，用于存储诊断日志和计量日志）。 出于此原因，Azure 堡垒需要出站到443的**AzureCloud**服务标记。

### <a name="target-vm-subnet"></a>目标 VM 子网
这是包含要 RDP/SSH 连接到的目标虚拟机的子网。

   * **传入来自 Azure 堡垒的流量：** Azure 堡垒将通过专用 IP 进入目标 VM。 需要在目标 VM 端通过专用 IP 打开 RDP/SSH 端口（分别为端口3389/22）。 作为最佳做法，你可以在此规则中添加 Azure 堡垒子网 IP 地址范围，以仅允许堡垒在目标 VM 子网中的目标 Vm 上打开这些端口。


## <a name="next-steps"></a>后续步骤

有关 Azure 堡垒的详细信息，请参阅[常见问题解答](bastion-faq.md)。
