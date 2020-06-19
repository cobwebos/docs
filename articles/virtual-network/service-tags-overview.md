---
title: Azure 服务标记概述
titlesuffix: Azure Virtual Network
description: 了解服务标记。 服务标记可帮助尽量简化安全规则的创建。
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: ea756b965a2539886e695585c9b5f5034eac7684
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2020
ms.locfileid: "84263178"
---
# <a name="virtual-network-service-tags"></a>虚拟网络服务标记
<a name="network-service-tags"></a>

服务标记是来自给定 Azure 服务的一组 IP 地址前缀。 Microsoft 会管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记，从而尽量简化网络安全规则的频繁更新。

可使用服务标记来定义[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)或 [Azure 防火墙](https://docs.microsoft.com/azure/firewall/service-tags)上的网络访问控制。  创建安全规则时，使用服务标记来替代特定的 IP 地址。 通过在规则的相应“源”或“目标”字段中指定服务标记名称（例如 ApiManagement），可允许或拒绝相应服务的流量。  **  

可使用服务标记来实现网络隔离，保护 Azure 资源免受常规 Internet 侵害，同时访问具有公共终结点的 Azure 服务。 可创建入站/出站网络安全组规则，以拒绝进出 Internet 的流量并允许进出 AzureCloud 或特定 Azure 服务的其他[可用服务标记](#available-service-tags)的流量 。

## <a name="available-service-tags"></a>可用服务标记
下表列出了可在[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)规则中使用的所有服务标记。

列指出标记是否：

- 适用于涵盖入站或出站流量的规则。
- 支持[区域](https://azure.microsoft.com/regions)范围。
- 可在 [Azure 防火墙](https://docs.microsoft.com/azure/firewall/service-tags)规则中使用。

默认情况下，服务标记反映了整个云的范围。 某些服务标记还通过将相应的 IP 范围限于指定的区域，带给你更精细的控制。 例如，服务标记“Storage”表示整个云的 Azure 存储，而“Storage.WestUS”则将范围缩小到来自美国西部区域的存储 IP 地址范围 。 下表指出每个服务标记是否支持此类区域范围。  

| 标记 | 目的 | 是否可使用入站或出站？ | 是否可为区域性？ | 是否可与 Azure 防火墙一起使用？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ActionGroup** | 操作组。 | 入站 | 否 | 否 |
| **ApiManagement** | 专用于 Azure API 管理的部署的管理流量。 <br/><br/>*注意：* 此标记表示各区域控制平面的 Azure API 管理服务终结点。 通过它，客户可对 API 管理服务上配置的 API、操作、策略和 NamedValue 执行管理操作。  | 入站 | 是 | 是 |
| **ApplicationInsightsAvailability** | Application Insights 可用性。 | 入站 | 否 | 否 |
| **AppConfiguration** | 应用程序配置。 | 出站 | 否 | 否 |
| **AppService**    | Azure 应用服务。 建议对 Web 应用前端出站安全规则使用此标记。 | 出站 | 是 | 是 |
| **AppServiceManagement** | 专用于应用服务环境的部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **AzureActiveDirectory** | Azure Active Directory。 | 出站 | 否 | 是 |
| **AzureActiveDirectoryDomainServices** | 专用于 Azure Active Directory 域服务的部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **AzureAdvancedThreatProtection** | Azure 高级威胁防护。 | 出站 | 否 | 否 |
| **AzureBackup** |Azure 备份。<br/><br/>*注意：* 此标记依赖于 Storage 和 AzureActiveDirectory 标记 。 | 出站 | 否 | 是 |
| **AzureBotService** | Azure 机器人服务。 | 出站 | 否 | 否 |
| **AzureCloud** | 所有[数据中心公共 IP 地址](https://www.microsoft.com/download/details.aspx?id=56519)。 | 出站 | 是 | 是 |
| **AzureCognitiveSearch** | Azure 认知搜索。 <br/><br/>此标记或此标记覆盖的 IP 地址可用于向索引器授予安全访问数据源的权限。 有关更多详细信息，请参阅[索引器连接文档](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors)。 <br/><br/> *注意*：服务标记的 IP 未包含在该服务标记的 IP 范围列表中，还需要将它添加到数据源的 IP 防火墙中。 | 入站 | 否 | 否 |
| **AzureConnectors** | 用于探测/后端连接的 Azure 逻辑应用连接器。 | 入站 | 是 | 是 |
| **AzureContainerRegistry** | Azure 容器注册表。 | 出站 | 是 | 是 |
| **AzureCosmosDB** | Azure Cosmos DB。 | 出站 | 是 | 是 |
| **AzureDatabricks** | Azure Databricks。 | 推送、请求和匿名 | 否 | 否 |
| **AzureDataExplorerManagement** | Azure 数据资源管理器管理。 | 入站 | 否 | 否 |
| **AzureDataLake** | Azure Data Lake Storage Gen1。 | 出站 | 否 | 是 |
| **AzureDevSpaces** | Azure Dev Spaces。 | 出站 | 否 | 否 |
| **AzureEventGrid** | Azure 事件网格。 | 推送、请求和匿名 | 否 | 否 |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Azure Front Door。 | 推送、请求和匿名 | 否 | 否 |
| **AzureInformationProtection** | Azure 信息保护。<br/><br/>*注意：* 此标记依赖于 AzureActiveDirectory、AzureFrontDoor.Frontend 和 AzureFrontDoor.FirstParty 标记  。 | 出站 | 否 | 否 |
| **AzureIoTHub** | Azure IoT 中心。 | 出站 | 否 | 否 |
| **AzureKeyVault** | Azure Key Vault。<br/><br/>*注意：* 此标记依赖于 AzureActiveDirectory 标记。 | 出站 | 是 | 是 |
| **AzureLoadBalancer** | Azure 基础结构负载均衡器。 此标记将转换为[主机的虚拟 IP 地址](security-overview.md#azure-platform-considerations) (168.63.129.16)，其中 Azure 的运行状况探测源于该 IP。 这不包含流向 Azure 负载均衡器资源的流量。 如果没有使用 Azure 负载均衡器，则可替代此规则。 | 推送、请求和匿名 | 否 | 否 |
| **AzureMachineLearning** | Azure 机器学习。 | 推送、请求和匿名 | 否 | 是 |
| **AzureMonitor** | Log Analytics、Application Insights、AzMon 和自定义指标（GiG 终结点）。<br/><br/>*注意：* 对于 Log Analytics，此标记依赖于 Storage 标记。 | 出站 | 否 | 是 |
| **AzureOpenDatasets** | Azure 开放数据集。<br/><br/>*注意：* 此标记依赖于 AzureFrontDoor.Frontend 和 Storage 标记 。 | 出站 | 否 | 否 |
| **AzurePlatformDNS** | 基本基础结构（默认）DNS 服务。<br/><br>可使用此标记来禁用默认 DNS。 使用此标记时请格外小心。 建议阅读 [Azure 平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 还建议在使用此标记之前进行测试。 | 出站 | 否 | 否 |
| **AzurePlatformIMDS** | Azure 实例元数据服务 (IMDS)，它是一项基本基础结构服务。<br/><br/>可使用此标记来禁用默认 IMDS。 使用此标记时请格外小心。 建议阅读 [Azure 平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 还建议在使用此标记之前进行测试。 | 出站 | 否 | 否 |
| **AzurePlatformLKM** | Windows 许可或密钥管理服务。<br/><br/>可使用此标记来禁用许可的默认设置。 使用此标记时请格外小心。 建议阅读 [Azure 平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。  还建议在使用此标记之前进行测试。 | 出站 | 否 | 否 |
| **AzureResourceManager** | Azure 资源管理器。 | 出站 | 否 | 否 |
| **AzureSignalR** | Azure SignalR。 | 出站 | 否 | 否 |
| **AzureSiteRecovery** | Azure Site Recovery。<br/><br/>*注意：* 此标记依赖于 AzureActiveDirectory、AzureKeyVault、EventHub、GuestAndHybridManagement 和 Storage 标记    。 | 出站 | 否 | 否 |
| **AzureTrafficManager** | Azure 流量管理器探测 IP 地址。<br/><br/>有关流量管理器探测 IP 地址的详细信息，请参阅 [Azure 流量管理器常见问题解答](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)。 | 入站 | 否 | 是 |  
| **BatchNodeManagement** | 专用于 Azure Batch 的部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **CognitiveServicesManagement** | Azure 认知服务的流量的地址范围。 | 推送、请求和匿名 | 否 | 否 |
| **DataFactory**  | Azure 数据工厂 | 推送、请求和匿名 | 否 | 否 |
| **DataFactoryManagement** | Azure 数据工厂的管理流量。 | 出站 | 否 | 否 |
| **Dynamics365ForMarketingEmail** | Dynamics 365 市场营销电子邮件服务的地址范围。 | 出站 | 是 | 否 |
| **ElasticAFD** | 弹性 Azure Front Door。 | 推送、请求和匿名 | 否 | 否 |
| **EventHub** | Azure 事件中心。 | 出站 | 是 | 是 |
| **GatewayManager** | 专用于 Azure VPN 网关和应用程序网关的部署的管理流量。 | 入站 | 否 | 否 |
| **GuestAndHybridManagement** | Azure 自动化和来宾配置。 | 出站 | 否 | 是 |
| **HDInsight** | Azure HDInsight。 | 入站 | 是 | 否 |
| **Internet** | 虚拟网络外部、可通过公共 Internet 访问的 IP 地址空间。<br/><br/>地址范围包括 [Azure 拥有的公共 IP 地址空间](https://www.microsoft.com/download/details.aspx?id=41653)。 | 推送、请求和匿名 | 否 | 否 |
| **LogicApps** | 逻辑应用。 | 推送、请求和匿名 | 否 | 否 |
| **LogicAppsManagement** | 逻辑应用的管理流量。 | 入站 | 否 | 否 |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security。 | 出站 | 否 | 否 |
| **MicrosoftContainerRegistry** | 用于 Microsoft 容器映像的容器注册表。 <br/><br/>*注意：* 此标记依赖于 AzureFrontDoor.FirstParty 标记。 | 出站 | 是 | 是 |
| **PowerQueryOnline** | Power Query Online。 | 推送、请求和匿名 | 否 | 否 |
| **ServiceBus** | 使用高级服务层的 Azure 服务总线流量。 | 出站 | 是 | 是 |
| **ServiceFabric** | Azure Service Fabric。<br/><br/>*注意：* 此标记表示各区域控制平面的 Service Fabric 服务终结点。 通过它，客户可从其 VNET 针对 Service Fabric 群集执行管理操作（终结点，例如 https:// westus.servicefabric.azure.com） | 推送、请求和匿名 | 否 | 否 |
| **Sql** | Azure SQL 数据库、Azure Database for MySQL、Azure Database for PostgreSQL 和 Azure SQL 数据仓库。<br/><br/>*注意：* 此标记表示服务而不是服务的特定实例。 例如，标记可表示 Azure SQL 数据库服务，但不能表示特定的 SQL 数据库或服务器。 此标记不适用于 SQL 托管实例。 | 出站 | 是 | 是 |
| **SqlManagement** | 专用于 SQL 的部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **存储** | Azure 存储。 <br/><br/>*注意：* 此标记表示服务而不是服务的特定实例。 例如，标记可表示 Azure 存储服务，但不能表示特定的 Azure 存储帐户。 | 出站 | 是 | 是 |
| **StorageSyncService** | 存储同步服务。 | 推送、请求和匿名 | 否 | 否 |
| **WindowsVirtualDesktop** | Windows 虚拟桌面。 | 推送、请求和匿名 | 否 | 是 |
| **VirtualNetwork** | 虚拟网络地址空间（为虚拟网络定义的所有 IP 地址范围）、所有连接的本地地址空间、[对等互连](virtual-network-peering-overview.md)的虚拟网络、已连接到[虚拟网络网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)的虚拟网络、[主机的虚拟 IP 地址](security-overview.md#azure-platform-considerations)，以及[用户定义的路由](virtual-networks-udr-overview.md)上使用的地址前缀。 此标记可能还包含默认路由。 | 推送、请求和匿名 | 否 | 否 |

>[!NOTE]
>在经典部署模型中（Azure 资源管理器推出之前），支持上表中列出部分标记。 下列标记的拼写有变化：
>
>| 经典拼写 | 等效的资源管理器标记 |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure 服务的服务标记表示来自所使用的特定云的地址前缀。 例如，对 Azure 公共云上 SQL 标记值对应的基础 IP 范围将不同于 Azure 中国云上的基础范围。

> [!NOTE]
> 如果为某个服务（例如 Azure 存储或 Azure SQL 数据库）实现了[虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)，Azure 会将[路由](virtual-networks-udr-overview.md#optional-default-routes)添加到该服务的虚拟网络子网。 路由中的地址前缀与相应服务标记的地址前缀或 CIDR 范围相同。

## <a name="service-tags-on-premises"></a>本地服务标记  
可获取服务标记和范围的当前信息，将其包含在本地防火墙配置中。 此信息是与每个服务标记对应的 IP 地址的当前实时列表。 可通过编程方式或 JSON 文件下载来获取此信息，如下述部分所述。

### <a name="use-the-service-tag-discovery-api-public-preview"></a>使用服务标记发现 API（公共预览版）
可以编程方式检索包含服务标记及 IP 地址范围详细信息的最新列表：

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> 在公共预览版阶段，发现 API 返回的信息可能比 JSON 下载返回的信息旧一些。 （请查看下一部分。）


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>使用可下载的 JSON 文件来发现服务标记 
可下载包含服务标记及 IP 地址范围详细信息最新列表的 JSON 文件。 这些列表每周都会更新和发布。 每个云的位置如下：

- [Azure 公用](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure 中国：](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure 德国](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>XML 文件中已发布关于 [Azure 公用](https://www.microsoft.com/download/details.aspx?id=41653)、[Azure 中国](https://www.microsoft.com/download/details.aspx?id=42064)和 [Azure 德国](https://www.microsoft.com/download/details.aspx?id=54770)的部分此类信息。 这些 XML 下载内容将于 2020 年 6 月 30 日被弃用，该日期之后不再可用。 你应按照先前部分所述，迁移到使用发现 API 和 JSON 文件下载。

### <a name="tips"></a>提示 
- 可记下 JSON 文件中变大的 changeNumber 值，检测相邻两次发布中的更新内容。 每个子部分（例如 Storage.WestUS）都有自己的 changeNumber，该值在出现更改时递增。 任何子部分更改时，文件的 changeNumber 的顶层都将递增。
- 要通过示例了解如何分析服务标记信息（例如获取美国西部区域存储的所有地址范围），请参阅[服务标记发现 API PowerShell](https://aka.ms/discoveryapi_powershell) 文档。

## <a name="next-steps"></a>后续步骤
- 了解如何[创建网络安全组](tutorial-filter-network-traffic.md)。
