---
title: "Azure AD 域服务：网络指导原则 | Microsoft 文档"
description: "Azure Active Directory 域服务的网络注意事项"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: maheshu
ms.openlocfilehash: 5f9236c5cf660be00db6e09d61df617b64d978e9
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2017
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Azure AD 域服务的网络注意事项
## <a name="how-to-select-an-azure-virtual-network"></a>如何选择 Azure 虚拟网络
以下指导原则可帮助选择要与 Azure AD 域服务配合使用的虚拟网络。

### <a name="type-of-azure-virtual-network"></a>Azure 虚拟网络类型
* **资源管理器虚拟网络**：可以在使用 Azure 资源管理器创建的虚拟网络中启用 Azure AD 域服务。
* 不能在经典 Azure 虚拟网络中启用 Azure AD 域服务。
* 可将其他虚拟网络连接到启用了 Azure AD 域服务的虚拟网络。 有关详细信息，请参阅[网络连接](active-directory-ds-networking.md#network-connectivity)部分。
* **区域虚拟网络**：如果打算使用现有的虚拟网络，请确保它是区域虚拟网络。

  * 使用传统地缘组机制的虚拟网络不能与 Azure AD 域服务配合使用。
  * 要使用 Azure AD 域服务，请[将传统虚拟网络迁移到区域虚拟网络](../virtual-network/virtual-networks-migrate-to-regional-vnet.md)。

### <a name="azure-region-for-the-virtual-network"></a>虚拟网络的 Azure 区域
* 部署 Azure AD 域服务托管域的 Azure 区域与选择启用该服务的虚拟网络所在的区域相同。
* 请在 Azure AD 域服务支持的 Azure 区域中选择虚拟网络。
* 请参阅“ [Azure 服务（按区域）](https://azure.microsoft.com/regions/#services/) ”页面，了解 Azure AD 域服务可用的 Azure 区域。

### <a name="requirements-for-the-virtual-network"></a>虚拟网络的要求
* **与 Azure 工作负荷的邻近性**：选择当前正在托管/将要托管需要访问 Azure AD 域服务的虚拟机的虚拟网络。 如果你的工作负载部署在与托管域不同的虚拟网络中，还可以选择连接虚拟网络。
* **自定义/自带 DNS 服务器**：确保未针对虚拟网络配置自定义 DNS 服务器。 自定义 DNS 服务器的一个示例是在虚拟网络中部署的 Windows Server VM 上运行的 Windows Server DNS 实例。 Azure AD 域服务不会与任何在虚拟网络内部署的自定义 DNS 服务器集成。
* **域名相同的现有域：**确保现有域的名称与该虚拟网络上使用的域名不同。 例如，假设名为“contoso.com”的域已在选定的虚拟网络上使用。 然后，尝试在该虚拟网络上启用具有相同域名（即“contoso.com”）的 Azure AD 域服务托管域。 尝试启用 Azure AD 域服务会失败。 发生这种失败的原因是该名称与该虚拟网络上的域名冲突。 在此情况下，必须使用不同的名称来设置 Azure AD 域服务托管域。 或者，可以取消预配现有的域，并继续启用 Azure AD 域服务。

> [!WARNING]
> 启用域服务之后，无法将其转移到其他虚拟网络。
>
>

## <a name="network-security-groups-and-subnet-design"></a>网络安全组和子网设计
[网络安全组 (NSG)](../virtual-network/virtual-networks-nsg.md) 包含一系列访问控制列表 (ACL) 规则，这些规则可以允许或拒绝虚拟网络中流向 VM 实例的网络流量。 NSG 可以与子网或该子网中的各个 VM 实例相关联。 当 NSG 与某个子网相关联时，ACL 规则将应用到该子网中的所有 VM 实例。 另外，可以通过将 NSG 直接关联到单个 VM，对流向该 VM 的流量进行进一步的限制。

![建议的子网设计](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

### <a name="guidelines-for-choosing-a-subnet"></a>有关选择子网的准则
* 将 Azure AD 域服务部署到 Azure 虚拟网络中的**独立专用子网**。
* 不要将 NSG 应用到专用子网。 如果必须将 NSG 应用到专用子网，请确保**不要阻止维护和管理域时所要使用的端口**。
* 不要过度限制专用子网中可供托管域使用的 IP 地址数。 这种限制会阻止服务向托管域提供两个域控制器。
* 不要在虚拟网络的**网关子网中启用 Azure AD 域服务**。

> [!WARNING]
> 将 NSG 与已启用 Azure AD 域服务的子网相关联时，可能会导致 Microsoft 无法维护和管理域。 此外，Azure AD 租户与托管域之间的同步会中断。 **如果在部署中应用的 NSG 会阻止 Azure AD 域服务更新和管理域，SLA 将不适用于此类部署。**
>
>

### <a name="ports-required-for-azure-ad-domain-services"></a>Azure AD 域服务所需的端口
Azure AD 域服务需要使用以下端口来维护和管理托管域。 确保在启用托管域的子网中未阻止这些端口。

| 端口号 | 目的 |
| --- | --- |
| 443 |与 Azure AD 租户同步 |
| 3389 |用于管理域 |
| 5986 |用于管理域 |
| 636 |对托管域进行安全 LDAP (LDAPS) 访问 |

端口 5986 用来使用托管域上的 PowerShell 远程处理来执行管理任务。 托管域的域控制器通常不侦听此端口。 只有当需要为托管域执行管理或维护操作时，服务才会在托管域控制器上打开此端口。 一旦操作完成，服务会立即在托管域控制器上关闭此端口。

端口 3389 用于到托管域的远程桌面连接。 大部分时间下，此端口在托管域上也保持关闭状态。 仅当我们需要连接到托管域来进行故障排除时（为了响应你发出的服务请求而发起），服务才会启用此端口。 此机制并非经常使用，因为管理和监视任务是使用 PowerShell 远程处理执行的。 只有在极少数情况下，当我们需要远程连接到你的托管域来进行高级故障排除时，才会使用此端口。 一旦故障排除操作完成，此端口会立即关闭。


### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>具有 Azure AD 域服务的虚拟网络的示例 SNG
下表介绍一个示例 NSG，可为具有 Azure AD 域服务托管域的虚拟网络配置该 NSG。 此规则允许来自所需端口的入站流量，以确保托管域保持修补和更新，并可由 Microsoft 进行监视。 默认的“DenyAll”规则适用于来自 Internet 的所有其他入站流量。

此外，该 NSG 还演示如何通过 Internet 锁定安全 LDAP 访问。 如果未禁用通过 Internet 对托管域的安全 LDAP 访问，则跳过此规则。 NSG 包含一组规则，允许仅从指定的一组 IP 地址通过 TCP 端口 636 进行入站 LDAPS 访问。 NSG 规则允许从指定 IP 地址通过 Internet 进行 LDAPS 访问，此规则的优先级比 DenyAll NSG 规则的优先级高。

![通过 Internet 进行安全 LDAPS 访问的示例 NSG](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

详细信息 - [创建网络安全组](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)。


## <a name="network-connectivity"></a>网络连接
Azure AD 域服务托管域只能在 Azure 中的单个虚拟网络中启用。

### <a name="scenarios-for-connecting-azure-networks"></a>Azure 网络连接方案
在以下任一部署方案中，连接 Azure 虚拟网络即可使用托管域：

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>在多个 Azure 虚拟网络中使用托管域
可将其他 Azure 虚拟网络连接到已启用 Azure AD 域服务的 Azure 虚拟网络。 通过此 VPN/VNet 对等连接，可让你将托管域与部署在其他虚拟网络中的工作负荷一起使用。

![经典虚拟网络连接](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>在基于 Resource Manager 的虚拟网络中使用托管域
可将基于 Resource Manager 的虚拟网络连接到已启用 Azure AD 域服务的 Azure 经典虚拟网络。 通过此连接可以使用托管域，工作负荷将部署到基于 Resource Manager 的虚拟网络。

![Resource Manager 到经典虚拟网络的连接](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>网络连接选项
* **使用虚拟网络对等互连的 VNet 到 VNet 连接：**虚拟网络对等互连是通过 Azure 主干网络在同一区域连接两个虚拟网络的一种机制。 对等互连后，出于所有连接目的，两个虚拟网络会显示为一个。 这两个虚拟网络仍作为单独资源管理，但这些虚拟网络中的虚拟机可直接通过专用 IP 地址彼此通信。

    ![使用对等互连的虚拟网络连接](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [详细信息 - 虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)
    
* **使用站点到站点 VPN 连接的 VNet 到 VNet 连接：**将虚拟网络连接到虚拟网络（VNet 到 VNet）类似于将虚拟网络连接到本地站点位置。 这两种连接类型都使用 VPN 网关来提供使用 IPsec/IKE 的安全隧道。

    ![使用 VPN 网关的虚拟网络连接](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [详细信息 - 使用 VPN 网关连接虚拟网络](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>相关内容
* [Azure 虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)
* [为经典部署模型配置 VNet 到 VNet 连接](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Azure 网络安全组](../virtual-network/virtual-networks-nsg.md)
* [创建网络安全组](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)
