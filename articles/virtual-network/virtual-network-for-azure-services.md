---
title: Azure 服务的虚拟网络
titlesuffix: Azure Virtual Network
description: 了解如何在虚拟网络中部署专用 Azure 服务并了解这些部署提供的功能。
services: virtual-network
documentationcenter: na
author: mohnader
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2020
ms.author: kumud
ms.openlocfilehash: ddc7f5f8844c602defb99a56ea3f511f0ea88cbe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90084696"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>将专用 Azure 服务部署到虚拟网络

在[虚拟网络](virtual-networks-overview.md)中部署专用 Azure 服务时，可通过专用 IP 地址与服务资源进行私密通信。

![虚拟网络中部署的服务](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

在虚拟网络中部署服务可提供以下功能：

- 虚拟网络内的资源可以通过专用 IP 地址彼此进行私密通信。 例如，在虚拟网络中，在虚拟机上运行的 HDInsight 与 SQL Server 之间可直接传输数据。
- 本地资源可通过[站点到站点 VPN（VPN 网关）](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti)或 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 使用专用 IP 地址访问虚拟网络中的资源。
- 虚拟网络可使用专用 IP 地址进行[对等互连](virtual-network-peering-overview.md)，实现虚拟网络中资源之间的彼此通信。
- 虚拟网络中的服务实例通常由 Azure 服务完全托管。 这包括监视资源的运行状况并根据负载进行缩放。
- 服务实例部署在虚拟网络的子网中。 根据服务提供的指南，必须通过[网络安全组](security-overview.md#network-security-groups)对子网开放入站和出站网络访问。
- 某些服务还会对它们能够部署到其中的子网施加限制，限制策略、路由的应用，或者要求将 VM 和服务资源组合到同一子网中。 检查每个服务的特定限制，因为它们可能会随时间而改变。 此类服务的示例包括：Azure NetApp 文件、专用 HSM、Azure 容器实例、应用服务。 
- （可选）服务可能需要一个[委派子网](virtual-network-manage-subnet.md#add-a-subnet)作为显式标识符，用于表示子网可承载特定服务。 服务可以通过委托获得显式权限，可以在委托的子网中创建服务专属资源。
- 如需 REST API 响应的示例，请参阅[包含委托子网的虚拟网络](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet)。 可以通过[可用委托](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) API 获得一个内容广泛的列表，其中包含的服务使用委托子网模型。

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>可部署到虚拟网络中的服务

|类别|服务| 专用<sup>1</sup> 子网
|-|-|-|
| 计算 | 虚拟机：[Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[云服务](https://msdn.microsoft.com/library/azure/jj156091)：仅限虚拟网络（经典）<br/> [Azure Batch](../batch/nodes-and-pools.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| 否 <br/> 否 <br/> 否 <br/> 否<sup>2</sup>
| 网络 | [应用程序网关 - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure 防火墙](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  <br/> [Azure Bastion](../bastion/bastion-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[网络虚拟设备](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn)| 是 <br/> 是 <br/> 是 <br/> 是 <br/> 否
|数据|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL 托管实例](../azure-sql/managed-instance/connectivity-architecture-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| 是 <br/> 是 <br/> 
|Analytics | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |否<sup>2</sup> <br/> 否<sup>2</sup> <br/> 
| 标识 | [Azure Active Directory 域服务](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |否 <br/>
| 容器 | [Azure Kubernetes 服务 (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure 容器实例 (ACI)](https://www.aka.ms/acivnet)<br/>带有 Azure 虚拟网络 CNI [插件](https://github.com/Azure/acs-engine/tree/master/examples/vnet)的 [Azure 容器服务引擎](https://github.com/Azure/acs-engine)<br/>[Azure Functions](../azure-functions/functions-networking-options.md#virtual-network-integration) |否<sup>2</sup><br/> 是 <br/><br/> 否 <br/> 是
| Web | [API 管理](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Web 应用](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[应用服务环境](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure 逻辑应用](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|是 <br/> 是 <br/> 是 <br/> 是
| 已托管 | [Azure 专用 HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp 文件](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|是 <br/> 是 <br/>
| | |

<sup>1</sup>“专用”表示只能在此子网中部署服务专用资源，并且不能将其与客户 VM/VMSS 组合使用 <br/> 
<sup>2</sup> 建议的最佳做法是将这些服务置于专用子网中，但这并非服务的强制要求。
