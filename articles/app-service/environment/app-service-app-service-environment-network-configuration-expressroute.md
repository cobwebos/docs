---
title: Azure ExpressRoute 的网络配置详细信息 - 应用服务
description: 已连接到 Azure ExpressRoute 线路的虚拟网络中 PowerApps 的应用服务环境的网络配置详细信息。
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
ms.custom: seodec18
ms.openlocfilehash: e0fa87facec73efdfff1a9908dcba92838215425
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130664"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>使用 Azure ExpressRoute 的 PowerApps 的应用服务环境网络配置详细信息

客户可以将 [Azure ExpressRoute][ExpressRoute] 线路连接到虚拟网络基础结构，以将其本地网络扩展到 Azure。 可以在[虚拟网络][virtualnetwork]基础结构的子网中创建应用服务环境。 然后，应用服务环境中运行的应用可以与只能通过 ExpressRoute 连接访问的后端资源建立安全连接。  

可在以下方案中创建应用服务环境：
- Azure 资源管理器虚拟网络。
- 经典部署模型虚拟网络。
- 使用公共地址范围或 RFC1918 地址空间（即专用地址）的虚拟网络。 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>所需的网络连接

在已连接到 ExpressRoute 的虚拟网络中，应用服务环境可能一开始并不符合网络连接要求。

应用服务环境需要以下网络连接设置才能正常运行：

* 在端口 80 和 443 上与全球 Azure 存储终结点建立的出站网络连接。 这些终结点位于应用服务环境所在的同一区域，以及其他 Azure 区域。 Azure 存储终结点在以下 DNS 域下解析：table.core.windows.net、blob.core.windows.net、queue.core.windows.net 和 file.core.windows.net。  

* 端口 445 上指向 Azure 文件服务的出站网络连接。

* 与应用服务环境所在的同一区域中的 Azure SQL 数据库终结点建立的出站网络连接。 SQL 数据库终结点在 database.windows.net 域下解析，这需要开放端口 1433、11000-11999 和 14000-14999 的访问权限。 有关 SQL 数据库 V12 端口用法的详细信息，请参阅[用于 ADO.NET 4.5 的非 1433 端口](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md)。

* 与 Azure 管理平面终结点（Azure 经典部署模型和 Azure 资源管理器终结点）建立的出站网络连接。 与这些终结点建立的连接包括 management.core.windows.net 和 management.azure.com 域。 

* 与 ocsp.msocsp.com、mscrl.microsoft.com 和 crl.microsoft.com 域建立的出站网络连接。 需要连接到这些域才能支持 SSL 功能。

* 虚拟网络的 DNS 配置必须能够解析本文中所述的所有终结点和域。 如果无法解析这些终结点，创建应用服务环境将会失败。 任何现有的应用服务环境将被标记为不正常。

* 需要端口 53 上的出站访问权限才能与 DNS 服务器通信。

* 如果 VPN 网关的另一端有自定义 DNS 服务器存在，则必须可从包含应用服务环境的子网连接该 DNS 服务器。 

* 出站网络路径不能遍历内部企业代理，也不可以使用本地的强制隧道。 这些操作会更改来自应用服务环境的出站网络流量的有效 NAT 地址。 更改应用服务环境出站网络流量的 NAT 地址会导致无法连接到许多终结点。 创建应用服务环境将会失败。 任何现有的应用服务环境将被标记为不正常。

* 必须允许应用服务环境通过入站网络访问所需的端口。 有关详细信息，请参阅[如何控制应用服务环境的入站流量][requiredports]。

若要满足 DNS 要求，请确保针对虚拟网络配置并维护有效的 DNS 基础结构。 如果创建应用服务环境之后更改了 DNS 配置，开发人员可以强制应用服务环境选择新的 DNS 配置。 可以在 [Azure 门户][NewPortal]中使用“应用服务环境管理”下的“重启”图标，触发滚动环境重新启动。 重新启动后，环境即会选择新的 DNS 配置。

若要满足入站网络访问要求，请在应用服务环境子网中配置[网络安全组 (NSG)][NetworkSecurityGroups]。 NSG 允许进行所需的访问，以[控制应用服务环境的入站流量][requiredports]。

## <a name="outbound-network-connectivity"></a>出站网络连接

默认情况下，新建的 ExpressRoute 线路会播发允许出站 Internet 连接的默认路由。 应用服务环境可以使用此配置连接到其他 Azure 终结点。

客户的常用配置是定义自己的默认路由 (0.0.0.0/0)，以强制出站 Internet 流量流向本地。 此流量一定会中断应用服务环境。 出站流量已在本地阻止，或者已 NAT 到不再适用于各种 Azure 终结点的一组无法识别的地址。

解决方法是在子网中定义包含应用服务环境的一个或多个用户定义的路由 (UDR)。 UDR 定义要遵循的子网特定路由，而不是默认路由。

如果可以，请使用以下配置：

* ExpressRoute 配置播发 0.0.0.0/0。 默认情况下，该配置强制通过隧道传递所有本地出站流量。
* 应用到包含应用服务环境的子网的 UDR 定义 0.0.0.0/0 以及 Internet 的下一跃点类型。 本文稍后将介绍此配置的一个示例。

此配置的组合效应是子网级 UDR 优先于 ExpressRoute 强制隧道。 可以保证应用服务环境的出站 Internet 访问成功。

> [!IMPORTANT]
> UDR 中定义的路由必须足够明确，以便优先于 ExpressRoute 配置所播发的任何路由。 下一部分所述的示例使用广泛的 0.0.0.0/0 地址范围。 此范围可能意外地被使用更具体地址范围的路由播发替代。
> 
> 交叉播发从公共对等路径到专用对等路径的路由的 ExpressRoute 配置不支持应用服务环境。 已配置公共对等互连的 ExpressRoute 配置会收到来自 Microsoft 的大量 Microsoft Azure IP 地址范围的路由播发。 如果这些地址范围在专用对等路径上交叉播发，则来自应用服务环境子网的所有出站网络数据包均强制通过隧道发送到客户的本地网络基础结构。 应用服务环境目前不支持此网络流。 一种解决方法是停止公共对等路径到专用对等路径的交叉播发路由。
> 
> 

有关用户定义的路由的背景信息，请参阅[虚拟网络流量路由][UDROverview]。  

若要了解如何创建和配置用户定义的路由，请参阅 [使用 PowerShell 通过路由表路由网络流量][UDRHowTo]。

## <a name="udr-configuration"></a>UDR 配置

本部分介绍应用服务环境的示例 UDR 配置。

### <a name="prerequisites"></a>必备组件

* 从 [Azure 下载页][AzureDownloads]安装 Azure PowerShell。 请选择下载发布日期为 2015 年 6 月或更晚的版本。 在“命令行工具” > “Windows PowerShell”下，选择“安装”以安装最新的 PowerShell cmdlet。

* 创建专供应用服务环境使用的唯一子网。 该唯一子网确保应用到子网的 UDR 只会打开应用服务环境的出站流量。

> [!IMPORTANT]
> 只有在完成配置步骤之后，才能部署应用服务环境。 这些步骤确保在尝试部署应用服务环境之前出站网络连接可用。

### <a name="step-1-create-a-route-table"></a>步骤 1：创建路由表

如以下代码片段所示，在“美国西部”Azure 区域创建名为 **DirectInternetRouteTable** 的路由表：

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>步骤 2：在表中创建路由

将路由添加到路由表，以启用出站 Internet 访问。  

配置对 Internet 的出站访问。 如以下代码片段所示，定义 0.0.0.0/0 的路由：

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0 是广泛的地址范围。 该范围将被 ExpressRoute 播发的更具体地址范围所替代。 使用 0.0.0.0/0 路由的 UDR 应与仅播发 0.0.0.0/0 的 ExpressRoute 配置结合使用。 

或者，可以下载 Azure 当前使用的完整 CIDR 范围列表。 可从 [Microsoft 下载中心][DownloadCenterAddressRanges]获取所有 Azure IP 地址范围的 XML 文件。  

> [!NOTE]
>
> Azure IP 地址范围会不断更改。 需要定期手动更新用户定义的路由才能保持同步。
>
> 单个 UDR 的默认上限为 100 个路由。 需要“汇总”Azure IP 地址范围以符合 100 个路由的限制。 UDR 定义的路由需要比 ExpressRoute 连接播发的路由更具体。
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>步骤 3：将表关联到子网

将路由表关联到用于部署应用服务环境的子网。 此命令将 **DirectInternetRouteTable** 表关联到将要包含应用服务环境的 **ASESubnet** 子网。

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>步骤 4：测试并确认路由

将路由表绑定到子网后，请测试并确认路由。

将虚拟机部署到子网，并确认以下状况：

* 本文所述的发往 Azure 和非 Azure 终结点的出站流量**不会**通过 ExpressRoute 线路传送。 如果来自子网的出站流量在本地使用强制隧道，则创建应用服务环境始终会失败。
* 本文所述的终结点 DNS 查找都可正常解析。 

完成配置步骤并确认路由后，请删除虚拟机。 创建应用服务环境时，子网需是“空的”。

现在可以开始部署应用服务环境！

## <a name="next-steps"></a>后续步骤

若要开始使用 PowerApps 的应用服务环境，请参阅[应用服务环境简介][IntroToAppServiceEnvironment]。

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/ 
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell 
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->
