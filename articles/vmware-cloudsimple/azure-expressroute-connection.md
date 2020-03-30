---
title: Azure VMware 解决方案（按云简单 - 使用 ExpressRoute 将私有云连接到 Azure 网络）
description: 介绍如何使用 ExpressRoute 将云简单私有云环境连接到 Azure 虚拟网络
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9bb68ec68f4de646239477ceeaac50a7a33989fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015229"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>使用 ExpressRoute 将云简单私有云环境连接到 Azure 虚拟网络

云简单私有云可以使用 Azure ExpressRoute 连接到 Azure 虚拟网络。  这种高带宽、低延迟连接允许您从私有云环境访问 Azure 订阅中运行的服务。

虚拟网络连接允许您：

* 使用 Azure 作为私有云上虚拟机的备份目标。
* 在 Azure 订阅中部署 KMS 服务器以加密私有云 vSAN 数据存储。
* 使用混合应用程序，其中应用程序的 Web 层在公共云中运行，而应用程序和数据库层在私有云中运行。

![Azure 快速路由连接到虚拟网络](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>设置虚拟网络连接

要设置与私有云的虚拟网络连接，您需要授权密钥、对等电路 URI 和对 Azure 订阅的访问。 此信息可在云简单门户中的虚拟网络连接页面上找到。 有关说明，请参阅[获取 Azure 虚拟网络到云简单连接的对等信息](virtual-network-connection.md)。 如果您在获取信息时遇到任何问题，请提交<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支持请求</a>。

> [!TIP]
> 如果您已经拥有 Azure 虚拟网络、网关子网和虚拟网络网关，则可以跳到步骤 4。

1. 在 Azure 订阅上创建虚拟网络，并验证您选择的地址空间与私有云的地址空间是否不同。  如果您已经拥有 Azure 虚拟网络，则可以使用现有虚拟网络。  有关详细信息，请参阅[使用 Azure 门户创建虚拟网络](../virtual-network/quick-create-portal.md)。
2. 在 Azure 虚拟网络上创建网关子网。  如果 Azure 虚拟网络中已有网关子网，则可以使用现有子网。 有关详细信息，请参阅[创建网关子网](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)。
3. 在虚拟网络上创建虚拟网络网关。  如果您有现有的虚拟网络网关，则可以使用现有虚拟网络网关。 有关详细信息，请参阅[创建虚拟网络网关](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)。
4. 通过将[虚拟网络连接到电路-不同订阅](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)中所述的授权密钥，创建虚拟网络和私有云之间的连接。

> [!WARNING]
> 如果您使用的是现有的虚拟网络网关，并且它与 CloudSimple ExpressRoute 电路具有与 CloudSimple ExpressRoute 电路相同的位置的 ExpressRoute 连接，则不会建立该连接。  创建新的虚拟网络，然后按照前面的步骤操作。

## <a name="test-the-virtual-network-connection"></a>测试虚拟网络连接

创建连接后，您可以通过在 **"设置**"下选择 **"属性**"来检查连接的状态。  状态和预配状态应显示**成功**。

![连接状态](media/azure-expressroute-connection.png)

要测试虚拟网络连接，

1. 在 Azure 订阅中创建虚拟机。
2. 查找私有云 vCenter 的 IP 地址（请参阅欢迎电子邮件）。
3. 从 Azure 虚拟网络中创建的虚拟机对云 vCenter 进行 ping。
4. 从在私有云 vCenter 中运行的虚拟机中对 Azure 虚拟机进行 ping。

如果您在建立连接时出现任何问题，请提交<a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">支持请求</a>。
