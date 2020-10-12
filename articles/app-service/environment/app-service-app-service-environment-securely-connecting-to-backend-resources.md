---
title: 连接到后端 v1
description: 了解如何从应用服务环境安全连接到后端资源。 本文档仅供使用旧版 v1 ASE 的用户使用。
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 9f8e288f771b9d584a0fd3430115f5fa60f68e47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88961799"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>从应用服务环境安全地连接到后端资源
由于应用服务环境始终创建于 Azure 资源管理器虚拟网络**或**经典部署模型[虚拟网络][virtualnetwork]中，因此从应用服务环境发往其他后端资源的出站连接可以独占方式通过虚拟网络发送。**** 截至2016年6月，还可以将 Ase 部署到使用公用地址范围或 RFC1918 地址空间 (专用地址) 的虚拟网络中。  

例如，SQL Server 可能在已锁定端口 1433 的虚拟机群集上运行。  此终结点可能已纳入 ACL，只允许从相同虚拟网络上的其他资源进行访问。  

另举一例，机密终结点可能在内部运行并通过[站点到站点][SiteToSite]或 [Azure ExpressRoute][ExpressRoute] 连接来与 Azure 建立连接。  因此，只有连接到站点到站点或 ExpressRoute 隧道的虚拟网络中的资源可以访问本地终结点。

对于所有这些方案，在应用服务环境上运行的应用可能会安全地连接到各种服务器和资源。 如果应用的出站流量在同一虚拟网络中的专用终结点应用服务环境运行，或连接到相同的虚拟网络，则它将仅流过虚拟网络。  到专用终结点的出站流量不会流经公共 Internet。

一个问题适用于从应用服务环境到虚拟网络中的终结点的出站流量。 应用服务环境无法访问与应用服务环境位于 **同一** 子网中的虚拟机的终结点。 如果将应用服务环境部署到由应用服务环境专门使用的子网中，则此限制通常不会出现问题。

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>出站连接和 DNS 要求
若要让应用服务环境正常运行，需具有对各个终结点的出站访问权限。 [Network Configuration for ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)（ExpressRoute 的网络配置）一文的“所需的网络连接”部分中提供了 ASE 所用外部终结点的完整列表。

应用服务环境要求为虚拟网络配置有效 DNS 基础结构。  如果 DNS 配置在创建应用服务环境之后发生更改，则开发人员可以强制应用服务环境选取新的 DNS 配置。 在门户的 "应用服务环境管理" 边栏选项卡顶部，选择 " **重新启动** " 图标以触发滚动环境重启，这将导致环境选取新的 DNS 配置。

还建议提前设置 vnet 中的任何自定义 DNS 服务器，然后再创建应用服务环境。  如果在创建应用服务环境期间更改虚拟网络的 DNS 配置，将导致应用服务环境创建过程失败。 在 VPN 网关的另一端，如果存在无法访问或不可用的自定义 DNS 服务器，则应用服务环境创建过程也会失败。

## <a name="connecting-to-a-sql-server"></a>连接到 SQL Server
常见的 SQL Server 配置包含一个侦听端口 1433 的终结点：

![SQL Server 终结点][SqlServerEndpoint]

有两种方法可限制发往此终结点的流量：

* [网络访问控制列表][NetworkAccessControlLists]（网络 ACL）
* [网络安全组][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>使用网络 ACL 限制访问
使用网络访问控制列表可以保护端口 1433。  下面的示例添加了客户端地址从虚拟网络内部发起的分配权限，并阻止了对所有其他客户端的访问。

![网络访问控制列表示例][NetworkAccessControlListExample]

在与 SQL Server 相同的虚拟网络中应用服务环境运行的任何应用程序都可能连接到 SQL Server 实例。 使用 SQL Server 虚拟机的 **VNet 内部** IP 地址。  

以下连接字符串示例使用其专用 IP 地址引用 SQL Server。

`Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient`

尽管虚拟机也有一个公共终结点，但由于网络 ACL 的原因，将拒绝连接尝试使用公共 IP 地址。 

## <a name="restricting-access-with-a-network-security-group"></a>使用网络安全组限制访问
另一种保护访问安全的方法是使用网络安全组。  网络安全组可以应用到单个虚拟机，或应用到包含虚拟机的子网。

首先，需要创建网络安全组：

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

使用网络安全组将访问权限限制为仅 VNet 内部流量很简单。  网络安全组中的默认规则只允许从相同虚拟网络中的其他网络客户端访问。

因此，锁定对 SQL Server 的访问很简单。 只需将网络安全组及其默认规则应用到运行 SQL Server 的虚拟机或包含虚拟机的子网。

以下示例将网络安全组应用到包含的子网：

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
```

最终的结果是一组可阻止外部访问，同时允许 VNet 内部访问的安全规则：

![默认网络安全规则][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>入门
若要开始使用应用服务环境，请参阅[应用服务环境简介][IntroToAppServiceEnvironment]

有关控制发往应用服务环境的入站流量的详细信息，请参阅[控制应用服务环境的入站流量][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: /previous-versions/azure/virtual-network/virtual-networks-acl
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png