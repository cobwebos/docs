<!-- not suitable for Mooncake -->

<properties 
	pageTitle="使用 Express Route 时的网络配置详细信息" 
	description="在已连接到 ExpressRoute 线路的虚拟网络中运行 Azure 环境时的网络配置详细信息。" 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags
	ms.service="app-service"
	ms.date="06/21/2016"
	wacn.date=""/>

# 使用 ExpressRoute 的 Azure 环境的网络配置详细信息 

## 概述 ##
客户可以将 [Azure ExpressRoute][ExpressRoute] 线路连接到虚拟网络基础结构，从而将其本地网络扩展到 Azure。可以在这个[虚拟网络][virtualnetwork]基础结构的子网中创建 Azure 环境。然后，在 Azure 环境上运行的应用程序可与后端资源建立安全连接，而后端资源只能通过 ExpressRoute 连接来访问。

**注意：**在“v2”虚拟网络中无法创建 Azure 环境。目前只有使用 RFC1918 地址空间（即专用地址）的经典“v1”虚拟网络才支持 Azure 环境。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../includes/app-service-web-to-api-and-mobile.md)]

## 所需的网络连接 ##
在已连接到 ExpressRoute 的虚拟网络中，可能一开始不符合 Azure 环境的网络连接要求。Azure 环境需要以下所有项目才能正确工作：


-  在端口 80 和 443 上与全球 Azure 存储空间终结点建立的出站网络连接。这包括位于与 Azure 环境相同区域中的终结点，以及位于其他 Azure 区域的存储终结点。Azure 存储空间终结点在以下 DNS 域之下解析：table.core.chinacloudapi.cn、blob.core.chinacloudapi.cn、queue.core.chinacloudapi.cn 和 file.core.chinacloudapi.cn。  
-  与位于 Azure 环境相同区域中的 SQL 数据库终结点建立的出站网络连接。Sql DB 终结点在以下域之下解析：database.chinacloudapi.cn。
-  与 Azure 管理平面终结点（ASM 和 ARM 终结点）建立的出站网络连接。这包括与 management.core.chinacloudapi.cn 和 management.azure.com 建立的出站连接。 
-  与 *ocsp.msocsp.com*、*mscrl.microsoft.com* 和 *crl.microsoft.com* 建立的出站网络连接。需要此连接才能支持 SSL 功能。
-  虚拟网络的 DNS 设置必须能够解析前面几点所提到的所有终结点和域。如果无法解析这些终结点，Azure 环境创建尝试将失败，并且现有的 Azure 环境标记为状况不正常。
-  如果 VPN 网关的另一端有自定义 DNS 服务器存在，则必须可从包含 Azure 环境的子网连接该 DNS 服务器。 
-  出站网络路径不可经过内部公司代理，也不可使用强制通道发送到本地。否则会更改来自 Azure 环境的出站网络流量的有效 NAT 地址。更改 Azure 环境的出站网络流量的 NAT 地址会导致上述众多终结点的连接失败。这将造成 Azure 环境创建尝试失败，并且前面状况良好的 Azure 环境将标记为状况不正常。  
-  必须根据此[文][requiredports]所述，允许对 Azure 环境所需的端口进行入站网络访问。

确保已针对虚拟网络配置并维护有效的 DNS 基础结构即可符合 DNS 要求。如果 DNS 配置在创建 Azure 环境之后因为任何原因而更改，开发人员可以强制 Azure 环境选择新的 DNS 配置。使用位于 [Azure 门户][NewPortal]中“Azure 环境管理”边栏选项卡顶部的“重新启动”图标触发轮流环境重新启动，会导致环境选择新的 DNS 配置。

根据此[文][requiredports]所述，在 Azure 环境的子网上设置[网络安全组][NetworkSecurityGroups]以允许所需访问权限，即可满足入站网络访问的要求。

## 启用 Azure 环境的出站网络连接##
默认情况下，新创建的 ExpressRoute 线路将会通告允许出站 Internet 连接的默认路由。使用此配置，Azure 环境可以连接到其他 Azure 终结点。

但是，常见的客户配置是定义其自身的默认路由 (0.0.0.0/0)，以强制出站 Internet 流量改为流向本地。此流量一定会中断 Azure 环境，因为已在本地阻止出站流量，或者 NAT 到无法再使用各种 Azure 终结点的一组无法识别的地址。

解决方法是在子网上定义包含 Azure 环境的一个或多个用户定义路由 (UDR)。UDR 定义了要遵循的子网特定路由，而不是默认路由。

如果可能，建议使用以下配置：

- ExpressRoute 配置播发 0.0.0.0/0 并默认使用强制隧道将所有输出流量发送到本地。
- 已应用到包含 Azure 环境的子网的 UDR 使用 Internet 的下一个跃点类型定义 0.0.0.0/0（本文后面将提供示例）。

这些步骤的组合效应是子网级 UDR 优先于 ExpressRoute 强制隧道，因而确保来自 Azure 环境的出站 Internet 访问。

**重要说明：**UDR 中定义的路由**必须**足够明确，以便优先于 ExpressRoute 配置所播发的任何路由。以下示例使用广泛 0.0.0.0/0 地址范围，因此使用更明确的地址范围，有可能意外地被路由播发重写。

**非常重要：****未正确交叉播发从公共对等路径到专用对等路径的路由**的 ExpressRoute 配置不支持 Azure 环境。已配置公共对等互连的 ExpressRoute 配置将收到来自 Microsoft 的大量 Microsoft Azure IP 地址范围的路由播发。如果这些地址范围在专用对等路径上未正确交叉播发，最后的结果是来自 Azure 环境子网的所有输出网络数据包都不会正确地使用强制隧道发送到客户的本地网络基础结构。此网络流将破坏 Azure 环境。此问题的解决方法是停止从公共对等路径到专用对等路径的交叉播发路由。

有关用户定义路由的背景信息，请参阅此[概述][UDROverview]。

有关创建和配置用户定义路由的详细信息，请参阅此[操作方法指南][UDRHowTo]。

## Azure 环境的示例 UDR 配置 ##

**先决条件**

1. 从 [Azure 下载页面][AzureDownloads]安装最新的 Azure Powershell（日期为 2015 年 6 月或更晚）。在“命令行工具”的“Windows Powershell”下，有一个“安装”链接可用于安装最新的 Powershell cmdlet。

2. 建议你创建唯一的子网，以专供 Azure 环境使用。这可确保应用到子网的 UDR 只会打开 Azure 环境的出站流量。
3. **重要说明**：只有在完成以下配置步骤**之后**，才可以部署 Azure 环境。这可确保在尝试部署 Azure 环境之前出站网络连接可用。

**步骤 1：创建命名路由表**

以下代码段将在中国北部 Azure 区域创建名为“DirectInternetRouteTable”的路由表：

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**步骤 2：在路由表中创建一个或多个路由**

需要将一个或多个路由添加到路由表中，才能启用出站 Internet 访问。

配置 Internet 出站访问的建议方法是定义 0.0.0.0/0 的路由，如下所示。
  
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

请记住，0.0.0.0/0 是广泛的地址范围，因此被 ExpressRoute 所播发的更明确地址范围所重写。若要反复迭代前面的建议，具有 0.0.0.0/0 路由的 UDR 应配合也只播发 0.0.0.0/0 的 ExressRoute 配置使用。

或者，可以下载完整和更新的由 Azure 使用中的 CIDR 范围列表。从 [Microsoft 下载中心][DownloadCenterAddressRanges]可获取包含所有 Azure IP 地址范围的 Xml 文件。

但请注意，这些范围随着时间改变，因此需要定期手动更新 UDR 才能保持同步。此外，因为单个 UDR 有 100 个路由的上限，因此需要“汇总”Azure IP 地址范围以符合 100 个路由的限制，请记住，UDR 定义的路由必须比 ExpressRoute 所播发的路由更明确。


**步骤 3：将路由表关联到包含 Azure 环境的子网**

最后一个配置步骤是将路由表关联到部署 Azure 环境的子网。以下命令将“DirectInternetRouteTable”关联到最终会包含 Azure 环境的“ASESubnet”。

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**步骤 4：最后的步骤**

将路由表绑定到子网后，建议先测试并确认预期效果。例如，将虚拟机部署到子网，并确认：


- 本文前面提到的 Azure 和非 Azure 终结点的出站流量**不会**流向 ExpressRoute 线路。请务必确认此行为，因为如果来自子网的出站流量仍使用强制隧道发送到本地，Azure 环境创建始终会失败。 
- 已正确解析前面所述终结点的所有 DNS 查找。 

确认上述步骤后，需要删除虚拟机，因为在创建 Azure 环境时，子网必须是“空的”。
 
然后继续创建 Azure 环境！

## 入门
[应用程序服务环境自述文件](/documentation/articles/app-service-app-service-environments-readme)中提供了有关 Azure 环境的所有文章和操作说明。

若要开始使用 Azure 环境，请参阅 [Azure 环境简介][IntroToAppServiceEnvironment]

有关 Azure 平台的详细信息，请参阅 [Azure Web 应用][AzureAppService]。

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/networking/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: /documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: /documentation/articles/virtual-networks-nsg/
[UDROverview]: /documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: /documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: /documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: /downloads/
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653
[NetworkSecurityGroups]: /documentation/articles/virtual-networks-nsg/
[AzureAppService]: /documentation/services/web-sites/
[IntroToAppServiceEnvironment]: /documentation/articles/app-service-app-service-environment-intro/
[NewPortal]: https://portal.azure.cn
 

<!-- IMAGES -->

<!---HONumber=Mooncake_0627_2016-->