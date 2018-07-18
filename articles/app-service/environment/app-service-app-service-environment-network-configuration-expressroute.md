---
title: 使用 Express Route 时的网络配置详细信息
description: 在已连接到 ExpressRoute 线路的虚拟网络中运行应用服务环境时的网络配置详细信息。
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.openlocfilehash: fcb9fa9004039205fa49f63c50d5907a8029a079
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32153212"
---
# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>使用 ExpressRoute 的应用服务环境的网络配置详细信息
## <a name="overview"></a>概述
客户可以将 [Azure ExpressRoute][ExpressRoute] 线路连接到虚拟网络基础结构，从而将其本地网络扩展到 Azure。  可以在这个[虚拟网络][virtualnetwork]基础结构的子网中创建应用服务环境。  然后，应用服务环境中运行的应用可以与只能通过 ExpressRoute 连接访问的后端资源建立安全连接。  

可以在 Azure 资源管理器虚拟网络**或**经典部署模型虚拟网络中创建应用服务环境。  随着 2016 年 6 月的最新更改，现在可以将 ASE 部署到使用公用地址范围或 RFC1918 地址空间（即，专用地址）的虚拟网络中。 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>所需的网络连接
在已连接到 ExpressRoute 的虚拟网络中，可能一开始不符合应用服务环境的网络连接要求。  应用服务环境需要以下所有项目才能正确工作：

* 在端口 80 和 443 上与全球 Azure 存储终结点建立的出站网络连接。  这包括位于与应用服务环境相同区域中的终结点，以及位于**其他** Azure 区域的存储终结点。  Azure 存储终结点在以下 DNS 域下解析：*table.core.windows.net*、*blob.core.windows.net*、*queue.core.windows.net* 和 *file.core.windows.net*。  
* 端口 445 上指向 Azure 文件服务的出站网络连接。
* 与位于应用服务环境相同区域中的 SQL 数据库终结点建立的出站网络连接。  Sql DB 终结点在以下域之下解析：*database.windows.net*。  这需要对端口 1433、11000-11999 和 14000-14999 的公开访问权限。  有关详细信息，请参阅[有关 SQL 数据库 V12 端口使用的这一文章](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md)。
* 与 Azure 管理平面终结点（ASM 和 ARM 终结点）建立的出站网络连接。  这包括指向 *management.core.windows.net* 和 *management.azure.com* 的出站连接。 
* 与 *ocsp.msocsp.com*、*mscrl.microsoft.com* 和 *crl.microsoft.com* 建立的出站网络连接。需要此连接才能支持 SSL 功能。
* 虚拟网络的 DNS 设置必须能够解析前面几点所提到的所有终结点和域。  如果无法解析这些终结点，应用服务环境创建尝试会失败，并且现有的应用服务环境标记为状况不正常。
* 需要端口 53 上的出站访问权限才能与 DNS 服务器通信。
* 如果 VPN 网关的另一端有自定义 DNS 服务器存在，则必须可从包含应用服务环境的子网连接该 DNS 服务器。 
* 出站网络路径不可经过内部公司代理，也不可使用强制通道发送到本地。  否则会更改来自应用服务环境的出站网络流量的有效 NAT 地址。  更改应用服务环境的出站网络流量的 NAT 地址会导致上述众多终结点的连接失败。  这会造成应用服务环境创建尝试失败，并且前面状况良好的应用服务环境将标记为状况不正常。  
* 根据此[文章][requiredports]中所述，必须允许对应用服务环境所需的端口进行入站网络访问。

确保已针对虚拟网络配置并维护有效的 DNS 基础结构即可符合 DNS 要求。  如果 DNS 配置在创建应用服务环境之后因为任何原因而更改，开发人员可以强制应用服务环境选择新的 DNS 配置。  在 [Azure 门户][NewPortal]中使用“应用服务环境管理”边栏选项卡顶部的“重启”图标触发滚动环境重启，将导致该环境选择新的 DNS 配置。

根据此[文章][requiredports]中所述，在应用服务环境的子网上配置[网络安全组][NetworkSecurityGroups]以允许所需访问权限，即可满足入站网络访问的要求。

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>启用应用服务环境的出站网络连接
默认情况下，新创建的 ExpressRoute 线路会通告允许出站 Internet 连接的默认路由。  使用此配置，应用服务环境可以连接到其他 Azure 终结点。

但是，常见的客户配置是定义其自身的默认路由 (0.0.0.0/0)，以强制出站 Internet 流量改为流向本地。  此流量一定会中断应用服务环境，因为已在本地阻止出站流量，或者 NAT 到无法再使用各种 Azure 终结点的一组无法识别的地址。

解决方法是在子网上定义包含应用服务环境的一个或多个用户定义路由 (UDR)。  UDR 定义了要遵循的子网特定路由，而不是默认路由。

如果可能，建议使用以下配置：

* ExpressRoute 配置播发 0.0.0.0/0 并默认使用强制隧道将所有输出流量发送到本地。
* 已应用到包含应用服务环境的子网的 UDR 使用 Internet 的下一个跃点类型定义 0.0.0.0/0（本文后面将提供示例）。

这些步骤的组合效应是子网级 UDR 优先于 ExpressRoute 强制隧道，因而确保来自应用服务环境的出站 Internet 访问。

> [!IMPORTANT]
> UDR 中定义的路由**必须**足够明确，以便优先于 ExpressRoute 配置所播发的任何路由。  以下示例使用广泛 0.0.0.0/0 地址范围，因此使用更明确的地址范围，有可能意外地被路由播发重写。
> 
> **交叉播发从公共对等路径到专用对等路径的路由**的 ExpressRoute 配置不支持应用服务环境。  已配置公共对等互连的 ExpressRoute 配置将收到来自 Microsoft 的大量 Microsoft Azure IP 地址范围的路由播发。  如果这些地址范围在专用对等路径上交叉播发，最后的结果是来自应用服务环境子网的所有输出网络数据包都会使用强制隧道发送到客户的本地网络基础结构。  应用服务环境目前不支持此网络流。  此问题的一个解决方法是停止从公共对等路径到专用对等路径的交叉播发路由。
> 
> 

有关用户定义路由的背景信息，请参阅此[概述][UDROverview]。  

有关创建和配置用户定义路由的详细信息，请参阅此[操作方法指南][UDRHowTo]。

## <a name="example-udr-configuration-for-an-app-service-environment"></a>应用服务环境的示例 UDR 配置
**先决条件**

1. 从 [Azure 下载页面][AzureDownloads]安装 Azure Powershell（日期为 2015 年 6 月或更晚）。  在“命令行工具”的“Windows Powershell”下，有一个“安装”链接可用于安装最新的 Powershell cmdlet。
2. 建议创建唯一的子网，以专供应用服务环境使用。  这可确保应用到子网的 UDR 只会打开应用服务环境的出站流量。
3. **重要说明**：只有在完成以下配置步骤**之后**，才可以部署应用服务环境。  这可确保在尝试部署应用服务环境之前出站网络连接可用。

**步骤 1：创建命名路由表**

以下代码片段会在 Azure 美国西部区域创建名为“DirectInternetRouteTable”的路由表：

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**步骤 2：在路由表中创建一个或多个路由**

需要将一个或多个路由添加到路由表中，才能启用出站 Internet 访问。  

配置 Internet 出站访问的建议方法是定义 0.0.0.0/0 的路由，如下所示。

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

请记住，0.0.0.0/0 是广泛的地址范围，因此被 ExpressRoute 所播发的更明确地址范围所重写。  若要反复迭代前面的建议，具有 0.0.0.0/0 路由的 UDR 应配合也只播发 0.0.0.0/0 的 ExressRoute 配置使用。 

或者，可以下载完整和更新的由 Azure 使用中的 CIDR 范围列表。  从 [Microsoft 下载中心][DownloadCenterAddressRanges]可获取包含所有 Azure IP 地址范围的 Xml 文件。  

但请注意，这些范围随着时间改变，因此需要定期手动更新用户定义路由才能保持同步。此外，因为单个 UDR 有 100 个路由的默认上限，因此需要“汇总”Azure IP 地址范围以符合 100 个路由的限制，请记住，UDR 定义的路由必须比 ExpressRoute 所播发的路由更明确。  

**步骤 3：将路由表关联到包含应用服务环境的子网**

最后一个配置步骤是将路由表关联到部署应用服务环境的子网。  以下命令将“DirectInternetRouteTable”关联到最终会包含应用服务环境的“ASESubnet”。

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**步骤 4：最后的步骤**

将路由表绑定到子网后，建议先测试并确认预期效果。  例如，将虚拟机部署到子网，并确认：

* 本文前面提到的 Azure 和非 Azure 终结点的出站流量**不会**流向 ExpressRoute 线路。  请务必确认此行为，因为如果来自子网的出站流量仍使用强制隧道发送到本地，应用服务环境创建始终会失败。 
* 已正确解析前面所述终结点的所有 DNS 查找。 

确认上述步骤后，需要删除虚拟机，因为在创建应用服务环境时，子网必须是“空的”。

然后继续创建应用服务环境！

## <a name="getting-started"></a>入门
若要开始使用应用服务环境，请参阅[应用服务环境简介][IntroToAppServiceEnvironment]

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[AzureDownloads]: http://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[NewPortal]:  https://portal.azure.com


<!-- IMAGES -->
