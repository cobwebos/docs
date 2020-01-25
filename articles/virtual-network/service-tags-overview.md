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
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: dc64570ccf69c321f33b9689362def8c9caf975e
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715407"
---
# <a name="virtual-network-service-tags"></a>虚拟网络服务标记 
<a name="network-service-tags"></a>

服务标记表示给定 Azure 服务中的一组 IP 地址前缀。 Microsoft 管理服务标记包含的地址前缀，并在地址发生更改时自动更新服务标记，从而最大程度地降低频繁更新网络安全规则的复杂性。 

你可以使用服务标记来定义 [网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) 或 [Azure 防火墙](https://docs.microsoft.com/azure/firewall/service-tags)上的网络访问控制。 创建安全规则时，使用服务标记代替特定的 IP 地址。 通过在规则的相应 " *源*" 或 " *目标* " 字段中指定服务标记名称（例如， **ApiManagement**），可以允许或拒绝相应服务的流量。 

可以使用服务标记来实现网络隔离，并在访问具有公共终结点的 Azure 服务时，通过常规 Internet 保护 Azure 资源。 创建入站/出站网络安全组规则，拒绝发往/发往**Internet**的流量，允许流量流向或来自特定 Azure 服务的**AzureCloud**或其他[可用服务标记](#available-service-tags)。 

## <a name="available-service-tags"></a>可用服务标记
下表包含可用于[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)规则的所有服务标记。

列指示标记：

- 适用于涵盖入站或出站流量的规则。
- 支持[区域](https://azure.microsoft.com/regions)范围。
- 可在[Azure 防火墙](https://docs.microsoft.com/azure/firewall/service-tags)规则中使用。

默认情况下，服务标记反映整个云的范围。 某些服务标记还允许更精细的控制，方法是将相应的 IP 范围限制到指定的区域。 例如，服务标记**存储**代表整个云的 Azure 存储空间，但**WestUS**将范围缩小到仅限 WestUS 区域中的存储 IP 地址范围。 下表指示每个服务标记是否支持此类区域范围。  

| 태그 | 용도 | 可以使用入站或出站？ | 可以是区域？ | 是否可与 Azure 防火墙一起使用？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Azure API 管理的管理流量-专用部署。 | 둘 다 | 아닙니다. | 예 |
| **ApplicationInsightsAvailability** | Application Insights 可用性。 | 둘 다 | 아닙니다. | 아닙니다. |
| **AppService**    | Azure App Service 建议将此标记用于对 web 应用程序前端的出站安全规则。 | 아웃바운드 | 예 | 예 |
| **AppServiceManagement** | 专用于应用服务环境的部署的管理流量。 | 둘 다 | 아닙니다. | 예 |
| **AzureActiveDirectory** | Azure Active Directory | 아웃바운드 | 아닙니다. | 예 |
| **AzureActiveDirectoryDomainServices** | 专用于 Azure Active Directory 域服务的部署的管理流量。 | 둘 다 | 아닙니다. | 예 |
| **AzureAdvancedThreatProtection** | Azure 高级威胁防护。 | 아웃바운드 | 아닙니다. | 아닙니다. |
| **AzureBackup** |Azure 备份。<br/><br/>*注意：* 此标记依赖于**存储**和**AzureActiveDirectory**标记。 | 아웃바운드 | 아닙니다. | 예 |
| **AzureBotService** | Azure 机器人服务。 | 아웃바운드 | 아닙니다. | 아닙니다. |
| **AzureCloud** | 所有[数据中心公共 IP 地址](https://www.microsoft.com/download/details.aspx?id=56519)。 | 아웃바운드 | 예 | 예 |
| **AzureCognitiveSearch** | Azure 认知搜索（如果对技能组合使用索引器）。 | 둘 다 | 아닙니다. | 아닙니다. |
| **AzureConnectors** | 用于探测/后端连接的 Azure 逻辑应用连接器。 | 인바운드 | 예 | 예 |
| **AzureContainerRegistry** | Azure 容器注册表。 | 아웃바운드 | 예 | 예 |
| **AzureCosmosDB** | Azure Cosmos DB。 | 아웃바운드 | 예 | 예 |
| **AzureDatabricks** | Azure Databricks。 | 둘 다 | 아닙니다. | 아닙니다. |
| **AzureDataExplorerManagement** | Azure 数据资源管理器管理。 | 인바운드 | 아닙니다. | 아닙니다. |
| **AzureDataLake** | Azure Data Lake。 | 아웃바운드 | 아닙니다. | 예 |
| **AzureEventGrid** | Azure Event Grid. <br/><br/>*注意：* 此标记涵盖美国中南部、美国东部、美国东部2、美国西部2和美国中部的 Azure 事件网格端点。 | 둘 다 | 아닙니다. | 아닙니다. |
| **AzureFrontDoor** | Azure 前门。 | 둘 다 | 아닙니다. | 아닙니다. |
| **AzureInformationProtection** | Azure 信息保护。<br/><br/>*注意：* 此标记与**AzureActiveDirectory**和**AzureFrontDoor**标记相关。 还要将以下 Ip 列入允许列表： 13.107.6.181 & 13.107.9.181。 | 아웃바운드 | 아닙니다. | 아닙니다. |
| **AzureIoTHub** | Azure IoT 中心。 | 아웃바운드 | 아닙니다. | 아닙니다. |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*注意：* 此标记依赖于**AzureActiveDirectory**标记。 | 아웃바운드 | 예 | 예 |
| **AzureLoadBalancer** | Azure 基础结构负载均衡器。 标记将转换为 Azure 运行状况探测所源自的主机（168.63.129.16）的[虚拟 IP 地址](security-overview.md#azure-platform-considerations)。 这不包括与 Azure 负载均衡器资源的流量。 如果不使用 Azure 负载均衡器，则可以覆盖此规则。 | 둘 다 | 아닙니다. | 아닙니다. |
| **AzureMachineLearning** | Azure Machine Learning입니다. | 둘 다 | 아닙니다. | 예 |
| **AzureMonitor** | Log Analytics、Application Insights、AzMon 和自定义指标（g 终结点）。<br/><br/>*注意：* 对于 Log Analytics，此标记依赖于**存储**标记。 | 아웃바운드 | 아닙니다. | 예 |
| **AzurePlatformDNS** | 基本基础结构（默认） DNS 服务。<br/><br>您可以使用此标记来禁用默认 DNS。 使用此标记时要格外小心。 建议你阅读[Azure 平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 我们还建议你在使用此标记之前执行测试。 | 아웃바운드 | 아닙니다. | 아닙니다. |
| **AzurePlatformIMDS** | Azure 实例元数据服务（IMDS），它是一个基本基础结构服务。<br/><br/>您可以使用此标记来禁用默认 IMDS。 使用此标记时要格外小心。 建议你阅读[Azure 平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 我们还建议你在使用此标记之前执行测试。 | 아웃바운드 | 아닙니다. | 아닙니다. |
| **AzurePlatformLKM** | Windows 许可或密钥管理服务。<br/><br/>您可以使用此标记来禁用许可的默认值。 使用此标记时要格外小心。 建议你阅读[Azure 平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。  我们还建议你在使用此标记之前执行测试。 | 아웃바운드 | 아닙니다. | 아닙니다. |
| **AzureResourceManager** | Azure 资源管理器。 | 아웃바운드 | 아닙니다. | 아닙니다. |
| **AzureSiteRecovery** | Azure Site Recovery。<br/><br/>*注意：* 此标记依赖于**存储**、 **AzureActiveDirectory**和**EventHub**标记。 | 아웃바운드 | 아닙니다. | 아닙니다. |
| **AzureTrafficManager** | Azure 流量管理器探测 IP 地址。<br/><br/>有关流量管理器探测 IP 地址的详细信息，请参阅[Azure 流量管理器常见问题解答](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)。 | 인바운드 | 아닙니다. | 예 |  
| **BatchNodeManagement** | 专用于 Azure Batch 的部署的管理流量。 | 둘 다 | 아닙니다. | 예 |
| **CognitiveServicesManagement** | Azure 认知服务的流量的地址范围。 | 아웃바운드 | 아닙니다. | 아닙니다. |
| **Dynamics365ForMarketingEmail** | Dynamics 365 的营销电子邮件服务的地址范围。 | 아웃바운드 | 예 | 아닙니다. |
| **ElasticAFD** | 弹性 Azure 前门。 | 둘 다 | 아닙니다. | 아닙니다. |
| **EventHub** | Azure 事件中心。 | 아웃바운드 | 예 | 예 |
| **GatewayManager** | 针对专用于 Azure VPN 网关和应用程序网关的部署的管理流量。 | 인바운드 | 아닙니다. | 아닙니다. |
| **GuestAndHybridManagement** | Azure 自动化和来宾配置。 | 둘 다 | 아닙니다. | 예 |
| **HDInsight** | Azure HDInsight. | 인바운드 | 예 | 아닙니다. |
| **인터넷** | 虚拟网络外部的 IP 地址空间，并可通过公共 internet 访问。<br/><br/>地址范围包括[Azure 拥有的公共 IP 地址空间](https://www.microsoft.com/download/details.aspx?id=41653)。 | 둘 다 | 아닙니다. | 아닙니다. |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security。 | 아웃바운드 | 아닙니다. | 아닙니다. |
| **MicrosoftContainerRegistry** | Microsoft 容器映像的容器注册表。 <br/><br/>*注意：* 此外，请将以下 IP 列入允许列表：204.79.197.219。 | 아웃바운드 | 예 | 예 |
| **Service Bus** | 使用高级服务层的 Azure 服务总线通信。 | 아웃바운드 | 예 | 예 |
| **ServiceFabric** | Azure Service Fabric。 | 아웃바운드 | 아닙니다. | 아닙니다. |
| **Sql** | Azure SQL 数据库、Azure Database for MySQL、Azure Database for PostgreSQL 和 Azure SQL 数据仓库。<br/><br/>*注意：* 此标记表示服务，而不是特定于服务的实例。 예를 들어 태그는 특정 SQL 데이터베이스 또는 서버가 아닌 Azure SQL Database 서비스를 나타냅니다. | 아웃바운드 | 예 | 예 |
| **SqlManagement** | 适用于 SQL 专用部署的管理流量。 | 둘 다 | 아닙니다. | 예 |
| **스토리지** | Azure Storage. <br/><br/>*注意：* 此标记表示服务，而不是特定于服务的实例。 예를 들어 태그는 특정 Azure Storage 계정이 아닌 Azure Storage 서비스를 나타냅니다. | 아웃바운드 | 예 | 예 |
| **VirtualNetwork** | 虚拟网络地址空间（为虚拟网络定义的所有 IP 地址范围）、所有连接的本地地址空间、[对等互连](virtual-network-peering-overview.md)虚拟网络、连接到[虚拟网络网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)的虚拟网络、主机的[虚拟 IP 地址](security-overview.md#azure-platform-considerations)和[用户定义的路由](virtual-networks-udr-overview.md)上使用的地址前缀。 此标记可能还包含默认路由。 | 둘 다 | 아닙니다. | 아닙니다. |

>[!NOTE]
>在经典部署模型中（Azure 资源管理器之前），支持上表中列出的标记的子集。 这些标记的拼写不同：
>
>| 经典拼写 | 等效资源管理器标记 |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| 인터넷 | 인터넷 |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure 服务的服务标记表示所使用的特定云中的地址前缀。 例如，与 Azure 公有云上的**Sql**标记值相对应的基础 IP 范围将不同于 azure 中国云上的基础范围。

> [!NOTE]
> Azure Storage 또는 Azure SQL Database와 같은 서비스에 [가상 네트워크 서비스 엔드포인트](virtual-network-service-endpoints-overview.md)를 구현하는 경우 Azure는 서비스의 가상 네트워크 서브넷에 [경로](virtual-networks-udr-overview.md#optional-default-routes)를 추가합니다. 路由中的地址前缀与相应服务标记的地址前缀或 CIDR 范围相同。

## <a name="service-tags-on-premises"></a>本地服务标记  
你可以获取当前服务标记和范围信息，以将其作为本地防火墙配置的一部分包含在内。 此信息是对应于每个服务标记的 IP 范围的当前时间点列表。 可以通过编程方式或通过 JSON 文件下载获取信息，如以下各节所述。

### <a name="use-the-service-tag-discovery-api-public-preview"></a>使用服务标记发现 API （公共预览版）
可以通过编程方式检索当前服务标记列表以及 IP 地址范围详细信息：

- [REST (영문)](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> 在公共预览版中，发现 API 返回的信息可能低于 JSON 下载所返回的信息。 （请参阅下一节。）


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>使用可下载的 JSON 文件发现服务标记 
您可以下载包含当前服务标记列表以及 IP 地址范围详细信息的 JSON 文件。 这些列表每周更新并发布。 每个云的位置如下：

- [Azure 公共](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure 미국 정부](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure 중국](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure 독일](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>此信息的子集已在[Azure 公共](https://www.microsoft.com/download/details.aspx?id=41653)、 [Azure 中国](https://www.microsoft.com/download/details.aspx?id=42064)和[azure 德国](https://www.microsoft.com/download/details.aspx?id=54770)的 XML 文件中发布。 这些 XML 下载将在2020年6月30日弃用，在该日期后将不再可用。 应按照前面几节中所述，使用发现 API 或 JSON 文件下载进行迁移。

### <a name="tips"></a>팁 
- 可以通过在 JSON 文件中注明增加的*changeNumber*值来检测从一个发布到下一个发布的更新。 每个子节（例如**WestUS**）都有其自己的*changeNumber* ，随着发生更改的变化而递增。 更改任何子节时，文件的*changeNumber*的顶层将递增。
- 有关如何分析服务标记信息的示例（例如，在 WestUS 中获取存储的所有地址范围），请参阅[服务标记发现 API PowerShell](https://aka.ms/discoveryapi_powershell)文档。

## <a name="next-steps"></a>다음 단계
- 了解如何[创建网络安全组](tutorial-filter-network-traffic.md)。
