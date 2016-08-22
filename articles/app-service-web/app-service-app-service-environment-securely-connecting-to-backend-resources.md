<!-- not suitable for Mooncake -->


<properties 
	pageTitle="从 Azure 环境安全连接到后端资源" 
	description="了解如何从 Azure 环境安全连接到后端资源。" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags
	ms.service="app-service"
	ms.date="07/11/2016"
	wacn.date=""/>  


# 从 Azure 环境安全连接到后端资源 #

## 概述 ##
由于 Azure 环境始终创建于 Azure Resource Manager 虚拟网络**或**经典部署模型[虚拟网络][virtualnetwork]中，因此从 Azure 环境发往其他后端资源的出站连接可以独占方式通过虚拟网络发送。随着 2016 年 6 月的最新更改，也可将 ASE 部署到使用公用地址范围或 RFC1918 地址空间（即专用地址）的虚拟网络。

例如，SQL Server 可能在已锁定端口 1433 的虚拟机群集上运行。此终结点可能已纳入 ACL，只允许从相同虚拟网络上的其他资源进行访问。

另举一例，机密终结点可能在内部运行并通过[站点到站点][SiteToSite]或 [Azure ExpressRoute][ExpressRoute] 连接来与 Azure 建立连接。因此，只有虚拟网络中已连接到站点到站点或 ExpressRoute 隧道的资源能够访问本地终结点。

在上述这些方案中，在 Azure 环境上运行的应用能够安全地连接到各种服务器和资源。从 Azure 环境中运行的应用程序发往相同虚拟网络中专用终结点（或连接到相同的虚拟网络）的出站流量，只通过虚拟网络发送。发往专用终结点的出站流量不通过公共 Internet 发送。

在从 Azure 环境发往虚拟网络中终结点的出站流量方面，有一点需要注意。Azure 环境无法访问与 Azure 环境处于同一子网中的虚拟机终结点。只要将 Azure 环境部署在供 Azure 环境独占使用的子网内，通常就不会出现任何问题。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 出站连接和 DNS 要求 ##
若要让 Azure 环境正常运行，需具有对各个终结点的出站访问权限。[ExpressRoute 的网络配置](/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/#required-network-connectivity)一文的“所需的网络连接”部分中提供了 ASE 所用外部终结点的完整列表。

Azure 环境要求为虚拟网络配置有效 DNS 基础结构。如果 DNS 配置在创建 Azure 环境之后因为任何原因而更改，开发人员可以强制 Azure 环境选择新的 DNS 配置。使用位于门户中“Azure 环境管理”边栏选项卡顶部的“重新启动”图标触发轮流环境重新启动，会导致环境选择新的 DNS 配置。

此外，还建议事先在 VNet 上设置任何自定义 DNS 服务器，然后创建 Azure 环境。如果在创建 Azure 环境时更改虚拟网络的 DNS 配置，将会导致 Azure 环境创建过程失败。同样地，如果自定义 DNS 服务器存在于 VPN 网关的另一端，且 DNS 服务器无法连接或使用，则 Azure 环境创建过程也会失败。

## 连接到 SQL Server
常见的 SQL Server 配置包含一个侦听端口 1433 的终结点：

![SQL Server 终结点][SqlServerEndpoint]  


有两种方法可限制发往此终结点的流量：


- [网络访问控制列表][NetworkAccessControlLists]（网络 ACL）

- [网络安全组][NetworkSecurityGroups]


## 使用网络 ACL 限制访问

使用网络访问控制列表可以保护端口 1433。以下示例将源自虚拟网络内部的客户端地址列入允许列表，并阻止对所有其他客户端的访问。

![网络访问控制列表示例][NetworkAccessControlListExample]  


在与 SQL Server 相同虚拟网络的 Azure 环境中运行的所有应用程序，都将能够使用 SQL Server 虚拟机的 **VNet 内部** IP 地址连接到 SQL Server 实例。

以下连接字符串示例使用其专用 IP 地址引用 SQL Server。

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

尽管虚拟机还有公共终结点，但使用公共 IP 地址的连接尝试将由于网络 ACL 而遭到拒绝。

## 使用网络安全组限制访问
另一种保护访问安全的方法是使用网络安全组。网络安全组可以应用到单个虚拟机，或应用到包含虚拟机的子网。

首先需要创建网络安全组：

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "China East" -Label "Example network security group for an app service environment"

使用网络安全组来限制仅允许访问 VNet 内部流量的操作十分简单。网络安全组中的默认规则只允许从相同虚拟网络中的其他网络客户端访问。

因此锁定 SQL Server 的访问权限，就如同将网络安全组及其默认规则应用到运行 SQL Server 的虚拟机或包含虚拟机的子网一样简单。

以下示例将网络安全组应用到包含的子网：

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
最终结果是一组可阻止外部访问，同时允许 VNet 内部访问的安全规则：

![默认网络安全规则][DefaultNetworkSecurityRules]  



## 入门
[应用程序服务环境自述文件](/documentation/articles/app-service-app-service-environments-readme/)中提供了有关 Azure 环境的所有文章和操作说明。

若要开始使用 Azure 环境，请参阅 [Azure 环境简介][IntroToAppServiceEnvironment]

有关控制 Azure 环境的入站流量的详细信息，请参阅[控制 Azure 环境的入站流量][ControlInboundASE]

有关 Azure 平台的详细信息，请参阅 [Azure Web 应用][AzureAppService]。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-- LINKS -->
[virtualnetwork]: /documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]: /documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: /documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: /documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: /documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]: /documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: /documentation/services/web-sites/
[ControlInboundASE]: /documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->

[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png

<!---HONumber=Mooncake_0815_2016-->