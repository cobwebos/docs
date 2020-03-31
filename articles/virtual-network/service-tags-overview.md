---
title: Azure 服务标记概述
titlesuffix: Azure Virtual Network
description: 了解服务标记。 服务标记有助于尽量降低创建安全规则时的复杂性。
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
ms.openlocfilehash: 568fc880711d42941fd9aef2ea19b8ac3123793a
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384239"
---
# <a name="virtual-network-service-tags"></a>虚拟网络服务标记
<a name="network-service-tags"></a>

服务标记代表给定 Azure 服务中的一组 IP 地址前缀。 Microsoft 管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记，从而最大限度地减少频繁更新网络安全规则的复杂性。

可以使用服务标记在[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) 或 [Azure 防火墙](https://docs.microsoft.com/azure/firewall/service-tags)上定义网络访问控件。 创建安全规则时，使用服务标记代替特定的 IP 地址。 通过在规则的相应*源* 或 *目标* 字段中指定服务标记名称（例如**ApiManagement），** 可以允许或拒绝相应服务的流量。

在访问具有公共终结点的 Azure 服务时，可以使用服务标记实现网络隔离并保护 Azure 资源免受常规 Internet 的监视。 创建入站/出站网络安全组规则，以拒绝进出**Internet**的流量，并允许流量进出**AzureCloud**或特定 Azure[服务的其他可用服务标记](#available-service-tags)。

## <a name="available-service-tags"></a>可用的服务标记
下表列出了可在[网络安全组](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)规则中使用的所有服务标记。

列指示标记是否：

- 适用于涵盖入站或出站流量的规则。
- 支持[区域](https://azure.microsoft.com/regions)范围。
- 可在 [Azure 防火墙](https://docs.microsoft.com/azure/firewall/service-tags)规则中使用。

默认情况下，服务标记反映整个云的范围。 某些服务标记还可以通过将相应 IP 范围限制为指定的区域，来实现更精细的控制。 例如，服务标记**存储**表示整个云的 Azure 存储，但**存储.WestUS**将范围缩小到仅存储 IP 地址范围（来自 WestUS 区域）。 下表指示每个服务标记是否支持此区域范围。  

| 标记 | 目的 | 可以使用入站或出站吗？ | 可以是区域性的吗？ | 是否可在 Azure 防火墙中使用？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **行动组** | 操作组。 | 入站 | 否 | 否 |
| **ApiManagement** | Azure API 管理的管理流量 - 专用部署。 <br/><br/>*注：* 此标记表示每个区域控制平面的 Azure API 管理服务终结点。 这使客户能够对 API、操作、策略、在 API 管理服务上配置的命名值执行管理操作。  | 入站 | 是 | 是 |
| **应用程序洞察可用性** | 应用程序见解可用性。 | 入站 | 否 | 否 |
| **应用配置** | 应用配置。 | 出站 | 否 | 否 |
| **应用服务**    | Azure 应用服务。 建议将此标记用于 Web 应用前端的出站安全规则。 | 出站 | 是 | 是 |
| **AppServiceManagement** | 应用服务环境专用部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **AzureActiveDirectory** | Azure Active Directory。 | 出站 | 否 | 是 |
| **AzureActiveDirectoryDomainServices** | Azure Active Directory 域服务专用部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **Azure 高级威胁保护** | Azure 高级威胁防护。 | 出站 | 否 | 否 |
| **Azure 备份** |Azure 备份。<br/><br/>*注：* 此标记依赖于**存储**和**Azure ActiveDirectory**标记。 | 出站 | 否 | 是 |
| **AzureBot服务** | 创建数字代理。 | 出站 | 否 | 否 |
| **AzureCloud** | 所有[数据中心公共 IP 地址](https://www.microsoft.com/download/details.aspx?id=56519)。 | 出站 | 是 | 是 |
| **Azure 认知搜索** | Azure 认知搜索。 <br/><br/>此标记或此标记涵盖的 IP 地址可用于授予索引器安全访问数据源的权限。 有关详细信息，请参阅[索引器连接文档](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors)。 <br/><br/> *注意*：搜索服务的 IP 不包括在此服务标记的 IP 范围列表中，**还需要添加到**数据源的 IP 防火墙中。 | 入站 | 否 | 否 |
| **AzureConnectors** | 用于探测/后端连接的 Azure 逻辑应用连接器。 | 入站 | 是 | 是 |
| **AzureContainerRegistry** | Azure 容器注册表。 | 出站 | 是 | 是 |
| **AzureCosmosDB** | Azure Cosmos DB。 | 出站 | 是 | 是 |
| **Azure 数据块** | Azure Databricks。 | 推送、请求和匿名 | 否 | 否 |
| **Azure 数据资源管理器管理** | Azure 数据资源管理器管理。 | 入站 | 否 | 否 |
| **Azure 数据湖** | Azure 数据存储第 1 代。 | 出站 | 否 | 是 |
| **Azure 空间** | Azure 开发空间。 | 出站 | 否 | 否 |
| **Azure 事件网格** | Azure 事件网格。 <br/><br/>*注：* 此标记仅涵盖美国中南部、美国东部、美国东部 2、美国西部 2 和美国中部中的 Azure 事件网格终结点。 | 推送、请求和匿名 | 否 | 否 |
| **Azure 前门.前端** <br/> **Azure 前门.后端** <br/> **Azure 前门.第一方**  | Azure 前门。 | 推送、请求和匿名 | 否 | 否 |
| **AzureInformationProtection** | Azure 信息保护。<br/><br/>*注：* 此标记依赖于**Azure ActiveDirectory、Azure** **FrontDoor.前端**和**Azure FrontDoor.第一方**标记。 | 出站 | 否 | 否 |
| **AzureIoTHub** | Azure IoT 中心。 | 出站 | 否 | 否 |
| **Azure 密钥库** | Azure Key Vault。<br/><br/>*注：* 此标记依赖于**Azure ActiveDirectory**标记。 | 出站 | 是 | 是 |
| **Azure 负载平衡器** | Azure 基础结构负载均衡器。 该标记转换为 Azure 运行状况探测器源自的[主机的虚拟 IP 地址](security-overview.md#azure-platform-considerations)（168.63.129.16）。 这不包括 Azure 负载均衡器资源的流量。 如果不使用 Azure 负载均衡器，则可以重写此规则。 | 推送、请求和匿名 | 否 | 否 |
| **Azure 机器学习** | Azure 机器学习。 | 推送、请求和匿名 | 否 | 是 |
| **Azure 监视器** | Log Analytics、Application Insights、AzMon 和自定义指标（GiG 终结点）。<br/><br/>*注：* 对于日志分析，此标记依赖于**存储**标记。 | 出站 | 否 | 是 |
| **Azure 开放数据集** | Azure 打开数据集。<br/><br/>*注：* 此标记依赖于**Azure FrontDoor.前端**和**存储**标记。 | 出站 | 否 | 否 |
| **Azure 平台DNS** | 基本基础结构（默认）DNS 服务。<br/><br>您可以使用此标记禁用默认 DNS。 使用此标记时要小心。 我们建议您阅读 Azure[平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 我们还建议您在使用此标记之前执行测试。 | 出站 | 否 | 否 |
| **Azure 平台** | Azure 实例元数据服务 （IMDS），这是一项基本基础结构服务。<br/><br/>您可以使用此标记禁用默认的 IMDS。 使用此标记时要小心。 我们建议您阅读 Azure[平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 我们还建议您在使用此标记之前执行测试。 | 出站 | 否 | 否 |
| **Azure 平台** | Windows 许可或密钥管理服务。<br/><br/>您可以使用此标记禁用许可的默认值。 使用此标记时要小心。 我们建议您阅读 Azure[平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。  我们还建议您在使用此标记之前执行测试。 | 出站 | 否 | 否 |
| **Azure 资源管理器** | Azure 资源管理器。 | 出站 | 否 | 否 |
| **AzureSignalR** | Azure 信号R。 | 出站 | 否 | 否 |
| **Azure 网站恢复** | Azure 站点恢复。<br/><br/>*注：* 此标记依赖于**AzureActiveDirectory、AzureKeyVault、****事件Hub、****AzureKeyVault****来宾和混合管理和****存储**标记。 | 出站 | 否 | 否 |
| **AzureTrafficManager** | Azure 流量管理器探测 IP 地址。<br/><br/>有关流量管理器探测 IP 地址的详细信息，请参阅 [Azure 流量管理器常见问题解答](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)。 | 入站 | 否 | 是 |  
| **BatchNodeManagement** | Azure Batch 专用部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **认知服务管理** | Azure 认知服务流量的地址范围。 | 出站 | 否 | 否 |
| **DataFactory**  | Azure 数据工厂 | 推送、请求和匿名 | 否 | 否 |
| **数据工厂管理** | Azure 数据工厂的管理流量。 | 出站 | 否 | 否 |
| **Dynamics365 用于营销电子邮件** | Dynamics 365 营销电子邮件服务的地址范围。 | 出站 | 是 | 否 |
| **弹性AFD** | 弹性 Azure 前门。 | 推送、请求和匿名 | 否 | 否 |
| **事件中心** | Azure 事件中心。 | 出站 | 是 | 是 |
| **网关管理器** | 专用于 Azure VPN 网关和应用程序网关的部署的管理流量。 | 入站 | 否 | 否 |
| **宾客和混合管理** | Azure 自动化和来宾配置。 | 出站 | 否 | 是 |
| **HDInsight** | Azure HDInsight。 | 入站 | 是 | 否 |
| **Internet** | 虚拟网络外部且可由公共互联网覆盖的 IP 地址空间。<br/><br/>地址范围包括[Azure 拥有的公共 IP 地址空间](https://www.microsoft.com/download/details.aspx?id=41653)。 | 推送、请求和匿名 | 否 | 否 |
| **LogicApps** | 逻辑应用。 | 推送、请求和匿名 | 否 | 否 |
| **逻辑应用管理** | 逻辑应用的管理流量。 | 入站 | 否 | 否 |
| **微软云应用安全** | Microsoft Cloud App Security。 | 出站 | 否 | 否 |
| **微软集装箱注册** | 用于 Microsoft 容器映像的容器注册表。 <br/><br/>*注：* 此标记依赖于**Azure FrontDoor.第一方**标记。 | 出站 | 是 | 是 |
| **电源查询在线** | 联机电源查询。 | 推送、请求和匿名 | 否 | 否 |
| **服务总线** | 使用高级服务层级的 Azure 服务总线流量。 | 出站 | 是 | 是 |
| **ServiceFabric** | Azure Service Fabric。<br/><br/>*注：* 此标记表示每个区域控制平面的服务结构服务终结点。 这使客户能够对其服务结构群集执行其 VNET（终结点，例如） 的管理操作。 https://westus.servicefabric.azure.com） | 推送、请求和匿名 | 否 | 否 |
| **Sql** | Azure SQL 数据库、Azure Database for MySQL、Azure Database for PostgreSQL和 Azure SQL 数据仓库。<br/><br/>*注：* 此标记表示服务，但不表示服务的特定实例。 例如，标记可表示 Azure SQL 数据库服务，但不能表示特定的 SQL 数据库或服务器。 此标记不适用于 SQL 托管实例。 | 出站 | 是 | 是 |
| **SqlManagement** | SQL 专用部署的管理流量。 | 推送、请求和匿名 | 否 | 是 |
| **存储** | Azure 存储。 <br/><br/>*注：* 此标记表示服务，但不表示服务的特定实例。 例如，标记可表示 Azure 存储服务，但不能表示特定的 Azure 存储帐户。 | 出站 | 是 | 是 |
| **存储同步服务** | 存储同步服务。 | 推送、请求和匿名 | 否 | 否 |
| **视窗虚拟桌面** | Windows 虚拟桌面。 | 推送、请求和匿名 | 否 | 否 |
| **虚拟网络** | 虚拟网络地址空间（为虚拟网络定义的所有 IP 地址范围）、所有连接的本地地址空间、[对等互连](virtual-network-peering-overview.md)的虚拟网络、已连接到[虚拟网络网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)的虚拟网络、[主机的虚拟 IP 地址](security-overview.md#azure-platform-considerations)以及在[用户定义的路由](virtual-networks-udr-overview.md)上使用的地址前缀。 此标记还可能包含默认路由。 | 推送、请求和匿名 | 否 | 否 |

>[!NOTE]
>在经典部署模型中（Azure 资源管理器之前），支持上表中列出的标记的子集。 这些标记的拼写不同：
>
>| 经典拼写方式 | 等效的资源管理器标记 |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure 服务的服务标记表示来自所使用的特定云的地址前缀。 例如，与 Azure 公有云上的 Sql 标记值对应的基础 IP 范围将不同于 Azure 中国云上的基础范围****。

> [!NOTE]
> 如果为某个服务（例如 Azure 存储或 Azure SQL 数据库）实现了[虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)，Azure 会将[路由](virtual-networks-udr-overview.md#optional-default-routes)添加到该服务的虚拟网络子网。 路由中的地址前缀与相应服务标记的地址前缀或 CIDR 范围相同。

## <a name="service-tags-on-premises"></a>本地服务标记  
可以获取当前服务标记和范围信息，并将其包含为本地防火墙配置的一部分。 此信息是对应于每个服务标记的 IP 范围的最新列表（截止目前）。 可以通过编程方式或通过 JSON 文件下载获取信息，如以下各节所述。

### <a name="use-the-service-tag-discovery-api-public-preview"></a>使用服务标记发现 API（公共预览版）
可以编程方式检索最新的服务标记列表和 IP 地址范围详细信息：

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure 电源外壳](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> 在公共预览版中，发现 API 返回的信息可能不如 JSON 下载所返回的信息新。 （请参阅下一节。）


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>使用可下载的 JSON 文件发现服务标记 
可以下载包含最新服务标记列表和 IP 地址范围详细信息的 JSON 文件。 这些列表每周更新和发布。 每个云的位置如下：

- [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure 美国政府](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure 中国](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure 德国](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>此信息的子集已发布在[Azure 公共](https://www.microsoft.com/download/details.aspx?id=41653)[、Azure 中国和](https://www.microsoft.com/download/details.aspx?id=42064)Azure[德国的](https://www.microsoft.com/download/details.aspx?id=54770)XML 文件中。 这些 XML 下载内容将在 2020 年 6 月 30 日弃用，在该日期过后将不再提供。 应按照前面几节中所述，使用发现 API 或 JSON 文件下载进行迁移。

### <a name="tips"></a>提示 
- 可以通过增大 JSON 文件中的 changeNumber** 值，检测各个发布的更新。 每个子节（例如 **，Storage.WestUS）** 都有自己的*更改编号*，随着更改发生而递增。 当任意子节发生更改时，文件的顶级 changeNumber 将会递增**。
- 有关如何分析服务标记信息的示例（例如，获取 WestUS 中存储的所有地址范围），请参阅[服务标记发现 API PowerShell](https://aka.ms/discoveryapi_powershell)文档。

## <a name="next-steps"></a>后续步骤
- 了解如何[创建网络安全组](tutorial-filter-network-traffic.md)。
