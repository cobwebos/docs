---
title: Azure 服务标记概述
titlesuffix: Azure Virtual Network
description: 了解服务标记。 服务标记有助于最大程度地降低安全规则创建的复杂性。
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: e629a9dd65a20883aa605e5d1d23248bcde575b4
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79365145"
---
# <a name="virtual-network-service-tags"></a>虚拟网络服务标记
<a name="network-service-tags"></a>

服务标记表示给定 Azure 服务中的一组 IP 地址前缀。 Microsoft 管理服务标记包含的地址前缀，并在地址发生更改时自动更新服务标记，从而最大程度地降低频繁更新网络安全规则的复杂性。

你可以使用服务标记来定义[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) 或 [Azure 防火墙](https://docs.microsoft.com/azure/firewall/service-tags)上的网络访问控制。 创建安全规则时，使用服务标记代替特定的 IP 地址。 通过在规则的相应 "*源*" 或 " *目标* " 字段中指定服务标记名称（例如， **ApiManagement**），可以允许或拒绝相应服务的流量。

可以使用服务标记来实现网络隔离，并在访问具有公共终结点的 Azure 服务时，通过常规 Internet 保护 Azure 资源。 创建入站/出站网络安全组规则，拒绝发往/发往**Internet**的流量，允许流量流向或来自特定 Azure 服务的**AzureCloud**或其他[可用服务标记](#available-service-tags)。

## <a name="available-service-tags"></a>可用服务标记
下表包含可用于[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)规则的所有服务标记。

列指示标记：

- 适用于涵盖入站或出站流量的规则。
- 支持[区域](https://azure.microsoft.com/regions)范围。
- 可在[Azure 防火墙](https://docs.microsoft.com/azure/firewall/service-tags)规则中使用。

默认情况下，服务标记反映整个云的范围。 某些服务标记还允许更精细的控制，方法是将相应的 IP 范围限制到指定的区域。 例如，服务标记**存储**代表整个云的 Azure 存储空间，但**WestUS**将范围缩小到仅限 WestUS 区域中的存储 IP 地址范围。 下表指示每个服务标记是否支持此类区域范围。  

| 标记 | 目的 | 可以使用入站或出站？ | 可以是区域？ | 是否可与 Azure 防火墙一起使用？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **操作组** | 操作组。 | 入站 | 否 | 否 |
| **ApiManagement** | Azure API 管理的管理流量-专用部署。 <br/><br/>*注意：* 此标记表示每个区域的控制平面的 Azure API 管理服务终结点。 这样，客户就可以对 API 管理服务中配置的 Api、操作、策略和 NamedValues 执行管理操作。  | 入站 | 是 | 是 |
| **ApplicationInsightsAvailability** | Application Insights 可用性。 | 入站 | 否 | 否 |
| **AppConfiguration** | 应用配置。 | 出站 | 否 | 否 |
| **AppService**    | Azure 应用服务。 建议将此标记用于对 web 应用程序前端的出站安全规则。 | 出站 | 是 | 是 |
| **AppServiceManagement** | 专用于应用服务环境的部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **AzureActiveDirectory** | Azure Active Directory。 | 出站 | 否 | 是 |
| **AzureActiveDirectoryDomainServices** | 专用于 Azure Active Directory 域服务的部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **AzureAdvancedThreatProtection** | Azure 高级威胁防护。 | 出站 | 否 | 否 |
| **AzureBackup** |Azure 备份。<br/><br/>*注意：* 此标记依赖于**存储**和**AzureActiveDirectory**标记。 | 出站 | 否 | 是 |
| **AzureBotService** | Azure 机器人服务。 | 出站 | 否 | 否 |
| **AzureCloud** | 所有[数据中心公共 IP 地址](https://www.microsoft.com/download/details.aspx?id=56519)。 | 出站 | 是 | 是 |
| **AzureCognitiveSearch** | Azure 认知搜索。 <br/><br/>此标记或此标记涵盖的 IP 地址可用于授予索引器安全访问数据源的权限。 有关更多详细信息，请参阅[索引器连接文档](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors)。 <br/><br/> *注意*：此服务标记的 ip 范围列表中不包含搜索服务的 ip，**还需要将其添加**到数据源的 ip 防火墙中。 | 入站 | 否 | 否 |
| **AzureConnectors** | 用于探测/后端连接的 Azure 逻辑应用连接器。 | 入站 | 是 | 是 |
| **AzureContainerRegistry** | Azure 容器注册表。 | 出站 | 是 | 是 |
| **AzureCosmosDB** | Azure Cosmos DB。 | 出站 | 是 | 是 |
| **AzureDatabricks** | Azure Databricks。 | 推送、请求和匿名 | 否 | 否 |
| **AzureDataExplorerManagement** | Azure 数据资源管理器管理。 | 入站 | 否 | 否 |
| **AzureDataLake** | Azure Data Lake Storage Gen1。 | 出站 | 否 | 是 |
| **AzureDevSpaces** | Azure Dev Spaces。 | 出站 | 否 | 否 |
| **AzureEventGrid** | Azure 事件网格。 <br/><br/>*注意：* 此标记涵盖美国中南部、美国东部、美国东部2、美国西部2和美国中部的 Azure 事件网格端点。 | 推送、请求和匿名 | 否 | 否 |
| **AzureFrontDoor** <br/> **AzureFrontDoor 后端** <br/> **AzureFrontDoor.FirstParty**  | Azure 前门。 | 推送、请求和匿名 | 否 | 否 |
| **AzureInformationProtection** | Azure 信息保护。<br/><br/>*注意：* 此标记依赖于**AzureActiveDirectory**、 **AzureFrontDoor**和**AzureFrontDoor**标记。 | 出站 | 否 | 否 |
| **AzureIoTHub** | Azure IoT 中心。 | 出站 | 否 | 否 |
| **AzureKeyVault** | Azure Key Vault。<br/><br/>*注意：* 此标记依赖于**AzureActiveDirectory**标记。 | 出站 | 是 | 是 |
| **AzureLoadBalancer** | Azure 基础结构负载均衡器。 标记将转换为 Azure 运行状况探测所源自的主机（168.63.129.16）的[虚拟 IP 地址](security-overview.md#azure-platform-considerations)。 这不包括与 Azure 负载均衡器资源的流量。 如果不使用 Azure 负载均衡器，则可以覆盖此规则。 | 推送、请求和匿名 | 否 | 否 |
| **AzureMachineLearning** | Azure 机器学习。 | 推送、请求和匿名 | 否 | 是 |
| **AzureMonitor** | Log Analytics、Application Insights、AzMon 和自定义指标（g 终结点）。<br/><br/>*注意：* 对于 Log Analytics，此标记依赖于**存储**标记。 | 出站 | 否 | 是 |
| **AzureOpenDatasets** | Azure 开放式数据集。<br/><br/>*注意：* 此标记依赖于**AzureFrontDoor**和**存储**标记。 | 出站 | 否 | 否 |
| **AzurePlatformDNS** | 基本基础结构（默认） DNS 服务。<br/><br>您可以使用此标记来禁用默认 DNS。 使用此标记时要格外小心。 建议你阅读[Azure 平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 我们还建议你在使用此标记之前执行测试。 | 出站 | 否 | 否 |
| **AzurePlatformIMDS** | Azure 实例元数据服务（IMDS），它是一个基本基础结构服务。<br/><br/>您可以使用此标记来禁用默认 IMDS。 使用此标记时要格外小心。 建议你阅读[Azure 平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 我们还建议你在使用此标记之前执行测试。 | 出站 | 否 | 否 |
| **AzurePlatformLKM** | Windows 许可或密钥管理服务。<br/><br/>您可以使用此标记来禁用许可的默认值。 使用此标记时要格外小心。 建议你阅读[Azure 平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。  我们还建议你在使用此标记之前执行测试。 | 出站 | 否 | 否 |
| **AzureResourceManager** | Azure 资源管理器。 | 出站 | 否 | 否 |
| **AzureSignalR** | Azure SignalR。 | 出站 | 否 | 否 |
| **AzureSiteRecovery** | Azure Site Recovery。<br/><br/>*注意：* 此标记依赖于**AzureActiveDirectory**、 **AzureKeyVault**、 **EventHub**、**GuestAndHybridManagement**和**存储**标记。 | 出站 | 否 | 否 |
| **AzureTrafficManager** | Azure 流量管理器探测 IP 地址。<br/><br/>有关流量管理器探测 IP 地址的详细信息，请参阅[Azure 流量管理器常见问题解答](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)。 | 入站 | 否 | 是 |  
| **BatchNodeManagement** | 专用于 Azure Batch 的部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **CognitiveServicesManagement** | Azure 认知服务的流量的地址范围。 | 出站 | 否 | 否 |
| **DataFactory**  | Azure 数据工厂 | 出站 | 否 | 否 |
| **DataFactoryManagement** | Azure 数据工厂的管理流量。 | 入站 | 否 | 否 |
| **Dynamics365ForMarketingEmail** | Dynamics 365 的营销电子邮件服务的地址范围。 | 出站 | 是 | 否 |
| **ElasticAFD** | 弹性 Azure 前门。 | 推送、请求和匿名 | 否 | 否 |
| **EventHub** | Azure 事件中心。 | 出站 | 是 | 是 |
| **GatewayManager** | 针对专用于 Azure VPN 网关和应用程序网关的部署的管理流量。 | 入站 | 否 | 否 |
| **GuestAndHybridManagement** | Azure 自动化和来宾配置。 | 出站 | 否 | 是 |
| **HDInsight** | Azure HDInsight。 | 入站 | 是 | 否 |
| **Internet** | 虚拟网络外部的 IP 地址空间，并可通过公共 internet 访问。<br/><br/>地址范围包括[Azure 拥有的公共 IP 地址空间](https://www.microsoft.com/download/details.aspx?id=41653)。 | 推送、请求和匿名 | 否 | 否 |
| **LogicApps** | 逻辑应用。 | 推送、请求和匿名 | 否 | 否 |
| **LogicAppsManagement** | 逻辑应用的管理流量。 | 入站 | 否 | 否 |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security。 | 出站 | 否 | 否 |
| **MicrosoftContainerRegistry** | Microsoft 容器映像的容器注册表。 <br/><br/>*注意：* 此标记依赖于**AzureFrontDoor. FirstParty**标记。 | 出站 | 是 | 是 |
| **PowerQueryOnline** | 联机 Power Query。 | 推送、请求和匿名 | 否 | 否 |
| **ServiceBus** | 使用高级服务层的 Azure 服务总线通信。 | 出站 | 是 | 是 |
| **ServiceFabric** | Azure Service Fabric。<br/><br/>*注意：* 此标记表示每个区域的控制平面的 Service Fabric 服务终结点。 这使客户能够从其 VNET （如终结点）为其 Service Fabric 群集执行管理操作。 https://westus.servicefabric.azure.com） | 推送、请求和匿名 | 否 | 否 |
| **Sql** | Azure SQL 数据库、Azure Database for MySQL、Azure Database for PostgreSQL 和 Azure SQL 数据仓库。<br/><br/>*注意：* 此标记表示服务，而不是特定于服务的实例。 例如，标记可表示 Azure SQL 数据库服务，但不能表示特定的 SQL 数据库或服务器。 此标记不适用于 SQL 托管实例。 | 出站 | 是 | 是 |
| **SqlManagement** | 适用于 SQL 专用部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **存储** | Azure 存储。 <br/><br/>*注意：* 此标记表示服务，而不是特定于服务的实例。 例如，标记可表示 Azure 存储服务，但不能表示特定的 Azure 存储帐户。 | 出站 | 是 | 是 |
| **StorageSyncService** | 存储同步服务。 | 推送、请求和匿名 | 否 | 否 |
| **WindowsVirtualDesktop** | Windows 虚拟桌面。 | 推送、请求和匿名 | 否 | 否 |
| **VirtualNetwork** | 虚拟网络地址空间（为虚拟网络定义的所有 IP 地址范围）、所有连接的本地地址空间、[对等互连](virtual-network-peering-overview.md)虚拟网络、连接到[虚拟网络网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)的虚拟网络、主机的[虚拟 IP 地址](security-overview.md#azure-platform-considerations)和[用户定义的路由](virtual-networks-udr-overview.md)上使用的地址前缀。 此标记可能还包含默认路由。 | 推送、请求和匿名 | 否 | 否 |

>[!NOTE]
>在经典部署模型中（Azure 资源管理器之前），支持上表中列出的标记的子集。 这些标记的拼写不同：
>
>| 经典拼写 | 等效资源管理器标记 |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure 服务的服务标记表示所使用的特定云中的地址前缀。 例如，与 Azure 公有云上的**Sql**标记值相对应的基础 IP 范围将不同于 azure 中国云上的基础范围。

> [!NOTE]
> 如果为某个服务（例如 Azure 存储或 Azure SQL 数据库）实现了[虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)，Azure 会将[路由](virtual-networks-udr-overview.md#optional-default-routes)添加到该服务的虚拟网络子网。 路由中的地址前缀与相应服务标记的地址前缀或 CIDR 范围相同。

## <a name="service-tags-on-premises"></a>本地服务标记  
你可以获取当前服务标记和范围信息，以将其作为本地防火墙配置的一部分包含在内。 此信息是对应于每个服务标记的 IP 范围的当前时间点列表。 可以通过编程方式或通过 JSON 文件下载获取信息，如以下各节所述。

### <a name="use-the-service-tag-discovery-api-public-preview"></a>使用服务标记发现 API （公共预览版）
可以通过编程方式检索当前服务标记列表以及 IP 地址范围详细信息：

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> 在公共预览版中，发现 API 返回的信息可能低于 JSON 下载所返回的信息。 （请参见下一节。）


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>使用可下载的 JSON 文件发现服务标记 
您可以下载包含当前服务标记列表以及 IP 地址范围详细信息的 JSON 文件。 这些列表每周更新并发布。 每个云的位置如下：

- [Azure 公共](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure 中国：](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure 德国](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>此信息的子集已在[Azure 公共](https://www.microsoft.com/download/details.aspx?id=41653)、 [Azure 中国](https://www.microsoft.com/download/details.aspx?id=42064)和[azure 德国](https://www.microsoft.com/download/details.aspx?id=54770)的 XML 文件中发布。 这些 XML 下载将在2020年6月30日弃用，在该日期后将不再可用。 应按照前面几节中所述，使用发现 API 或 JSON 文件下载进行迁移。

### <a name="tips"></a>提示 
- 可以通过在 JSON 文件中注明增加的*changeNumber*值来检测从一个发布到下一个发布的更新。 每个子节（例如**WestUS**）都有其自己的*changeNumber* ，随着发生更改的变化而递增。 更改任何子节时，文件的*changeNumber*的顶层将递增。
- 有关如何分析服务标记信息的示例（例如，在 WestUS 中获取存储的所有地址范围），请参阅[服务标记发现 API PowerShell](https://aka.ms/discoveryapi_powershell)文档。

## <a name="next-steps"></a>后续步骤
- 了解如何[创建网络安全组](tutorial-filter-network-traffic.md)。
