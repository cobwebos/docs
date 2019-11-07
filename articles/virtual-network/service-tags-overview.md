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
ms.openlocfilehash: 0eb15d1b9b56522b9caa1bb10890eb2b485714e8
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587519"
---
# <a name="virtual-network-service-tags"></a>虚拟网络服务标记 
<a name="network-service-tags"></a>

服务标记表示给定 Azure 服务中的一组 IP 地址前缀。 它有助于最大程度地减少对网络安全规则的频繁更新的复杂性。 你可以使用服务标记来定义[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)或[Azure 防火墙](https://docs.microsoft.com/azure/firewall/service-tags)上的网络访问控制。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 通过在规则的相应 "*源*" 或 "*目标*" 字段中指定服务标记名称（例如**ApiManagement**），可以允许或拒绝相应服务的流量。 Microsoft 管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记。 

## <a name="available-service-tags"></a>可用服务标记
下表包含可用于[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)规则的所有服务标记。

列指示标记是否是：

- 适用于涵盖入站或出站流量的规则
- 支持[区域](https://azure.microsoft.com/regions)范围 
- 可用于[Azure 防火墙](https://docs.microsoft.com/azure/firewall/service-tags)规则

默认情况下，服务标记反映整个云的范围。  某些服务标记还可以通过将相应的 IP 范围限制为指定的区域，从而获得更精细的控制。  例如，当服务标记**存储**代表整个云的 Azure 存储时， **WestUS**会将其从 WestUS 区域缩小为仅存储 IP 地址范围。  下面每个服务标记的说明都指示它们是否支持此类范围。  



| 标记 | 目的 | 可以使用入站或出站？ | 可以是区域？ | 是否可与 Azure 防火墙一起使用？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | APIM 专用部署的管理流量。 | 两者 | 否 | 是 |
| **AppService**    | 应用服务服务。 建议将此标记用于 WebApp 前端的出站安全规则。 | 出站 | 是 | 是 |
| **AppServiceManagement** | 应用服务环境专用部署的管理流量。 | 两者 | 否 | 是 |
| **AzureActiveDirectory** | Azure Active Directory 服务。 | 出站 | 否 | 是 |
| **AzureActiveDirectoryDomainServices** | 用于 Azure Active Directory 域服务专用部署的管理流量。 | 两者 | 否 | 是 |
| **AzureBackup** |Azure 备份服务。<br/><br/>*注意：* 此标记依赖于**存储**和**AzureActiveDirectory**标记。 | 出站 | 否 | 是 |
| **AzureCloud** | 所有[数据中心公共 IP 地址](https://www.microsoft.com/download/details.aspx?id=41653)。 | 出站 | 是 | 是 |
| **AzureConnectors** | 用于探测/后端连接的逻辑应用连接器。 | 入站 | 是 | 是 |
| **AzureContainerRegistry** | Azure 容器注册表服务。 | 出站 | 是 | 是 |
| **AzureCosmosDB** | Azure Cosmos 数据库服务。 | 出站 | 是 | 是 |
| **AzureDataLake** | Azure Data Lake 服务。 | 出站 | 否 | 是 |
| **AzureKeyVault** | Azure KeyVault 服务。<br/><br/>*注意：* 此标记依赖于**AzureActiveDirectory**标记。 | 出站 | 是 | 是 |
| **AzureLoadBalancer** | Azure 的基础结构负载均衡器。 此标记将转换为[主机的虚拟 IP 地址](security-overview.md#azure-platform-considerations) (168.63.129.16)，Azure 的运行状况探测源于该 IP。 如果不使用 Azure 负载均衡器，则可替代此规则。 | 两者 | 否 | 否 |
| **AzureMachineLearning** | Azure 机器学习服务。 | 出站 | 否 | 是 |
| **AzureMonitor** | Log Analytics、App Insights、AzMon 和自定义指标（g 终结点）。<br/><br/>*注意：* 对于 Log Analytics，此标记与**存储**标记相关。 | 出站 | 否 | 是 |
| **AzurePlatformDNS** | 基本基础结构（默认） DNS 服务。<br/><br>您可以使用此标记来禁用默认 DNS。 请在使用此标记时务必小心。 建议阅读[Azure 平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 建议在使用此标记之前进行测试。 | 出站 | 否 | 否 |
| **AzurePlatformIMDS** | IMDS，它是一个基本基础结构服务。<br/><br/>您可以使用此标记来禁用默认 IMDS。  请在使用此标记时务必小心。 建议阅读[Azure 平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 建议在使用此标记之前进行测试。 | 出站 | 否 | 否 |
| **AzurePlatformLKM** | Windows 许可或密钥管理服务。<br/><br/>您可以使用此标记来禁用许可的默认值。 请在使用此标记时务必小心。  建议阅读[Azure 平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 建议在使用此标记之前进行测试。 | 出站 | 否 | 否 |
| **AzureTrafficManaged** | Azure 流量管理器探测 IP 地址。<br/><br/>有关流量管理器探测 IP 地址的详细信息，请参阅 [Azure 流量管理器常见问题解答](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)。 | 入站 | 否 | 是 |  
| **BatchNodeManagement** | Azure Batch 专用部署的管理流量。 | 两者 | 否 | 是 |
| **CognitiveServicesManagement** | 认知服务的流量的地址范围 | 出站 | 否 | 否 |
| **Dynamics365ForMarketingEmail** | Dynamics 365 的营销电子邮件服务的地址范围。 | 出站 | 是 | 否 |
| **EventHub** | Azure EventHub 服务。 | 出站 | 是 | 是 |
| **GatewayManager** | VPN/应用网关专用部署的管理流量。 | 入站 | 否 | 否 |
| **Internet** | 虚拟网络外部的 IP 地址空间，并可通过公共 Internet 访问。<br/><br/>地址范围包括 [Azure 拥有的公共 IP 地址空间](https://www.microsoft.com/download/details.aspx?id=41653)。 | 两者 | 否 | 否 |
| **MicrosoftContainerRegistry** | Microsoft 容器注册表服务。 | 出站 | 是 | 是 |
| **服务总线** | 使用高级服务层的 Azure 服务总线服务。 | 出站 | 是 | 是 |
| **ServiceFabric** | Service Fabric 服务。 | 出站 | 否 | 否 |
| **Transact-sql** | Azure SQL 数据库、Azure Database for MySQL、Azure Database for PostgreSQL 和 Azure SQL 数据仓库服务。<br/><br/>*注意：* 此标记表示服务，而不是特定于服务的实例。 例如，标记可表示 Azure SQL 数据库服务，但不能表示特定的 SQL 数据库或服务器。 | 出站 | 是 | 是 |
| **SqlManagement** | 针对 SQL 专用部署的管理流量。 | 两者 | 否 | 是 |
| **存储** | Azure 存储服务。 <br/><br/>*注意：* 标记表示服务，而不是特定于服务的实例。 例如，标记可表示 Azure 存储服务，但不能表示特定的 Azure 存储帐户。 | 出站 | 是 | 是 |
| **VirtualNetwork** | 虚拟网络地址空间（为虚拟网络定义的所有 IP 地址范围）、所有连接的本地地址空间、[对等互连](virtual-network-peering-overview.md)虚拟网络或连接到[虚拟网络网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)的虚拟网络（[虚拟 IP）](security-overview.md#azure-platform-considerations)[用户定义路由](virtual-networks-udr-overview.md)上使用的主机和地址前缀的地址。 请注意，此标记可能还包含默认路由。 | 两者 | 否 | 否 |

>[!NOTE]
>在*经典*（Azure 预 Azure 资源管理器）环境中工作时，支持一组选择的上述标记。  它们使用替代拼写：

| 经典拼写 | 等效资源管理器标记 |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| INTERNET | Internet |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure 服务的服务标记表示来自所使用的特定云的地址前缀。 例如，Azure 公有云与 Azure 中国云之间的**Sql**标记值所对应的基础 IP 范围将有所不同。

> [!NOTE]
> 如果为某个服务（例如 Azure 存储或 Azure SQL 数据库）实现了[虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)，Azure 会将[路由](virtual-networks-udr-overview.md#optional-default-routes)添加到该服务的虚拟网络子网。 路由中的地址前缀与相应服务标记的地址前缀或 CIDR 范围相同。



## <a name="service-tags-in-on-premises"></a>本地服务标记  
你可以获取当前服务标记和范围信息，以将其作为本地防火墙配置的一部分包含在内。  此信息是对应于每个服务标记的 IP 范围的当前时间点列表。  可以通过编程方式或通过 JSON 文件下载获取此信息，如下所示。

### <a name="service-tag-discovery-api-public-preview"></a>服务标记发现 API （公共预览版）
可以通过 IP 地址范围详细信息以编程方式检索当前服务标记列表：

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> 在公共预览版中，发现 API 返回的信息可能与 JSON 下载（下面的内容）不是最新的。


### <a name="discover-service-tags-using-downloadable-json-files"></a>使用可下载的 JSON 文件发现服务标记 
您可以下载包含当前服务标记列表和 IP 地址范围详细信息的 JSON 文件。 每周更新并发布这些更新。  每个云的位置如下：

- [Azure 公共](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure 政府](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure 中国：](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure 德国](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>之前已在[Azure 公共](https://www.microsoft.com/download/details.aspx?id=41653)、 [Azure 中国](https://www.microsoft.com/download/details.aspx?id=42064)和[azure 德国](https://www.microsoft.com/download/details.aspx?id=54770)的 XML 文件中发布了此信息的子集。 这些 XML 下载将在2020年6月30日弃用，在该日期后将不再可用。 请迁移到使用发现 API 或 JSON 文件下载，如上所述。

### <a name="tips"></a>提示 
- 可以通过 JSON 文件中增加的*changeNumber*值从一个发布到下一个发布检测更新。 每个子节（如**WestUS**）都有其自己的*changeNumber* ，在发生更改时递增。  更改任何子节后，文件的*changeNumber*的顶层将递增。
- 有关如何分析服务标记信息（例如，在 WestUS 中获取存储的所有地址范围）的示例，请参阅[服务标记发现 API PowerShell](https://aka.ms/discoveryapi_powershell)文档。

## <a name="next-steps"></a>后续步骤
- 连接如何[创建网络安全组](tutorial-filter-network-traffic.md)。

