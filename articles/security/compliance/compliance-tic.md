---
title: Azure 的受信任 Internet 连接指南
description: Azure 和 SaaS 服务的受信任 Internet 连接指南
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: 637f837ec2421f0bef5131a33c709087b891aa0f
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505105"
---
# <a name="trusted-internet-connections-guidance"></a>受信任 Internet 连接指南

本文介绍了政府机构如何使用 Microsoft Azure 政府帮助实现受信任 Internet 连接 (TIC) 计划的符合性。 本文介绍如何在 Azure 服务架构 (IaaS) 和 Azure 平台即服务 (PaaS) 产品/服务中使用 Azure 政府。

## <a name="trusted-internet-connections-overview"></a>受信任 Internet 连接概述

TIC 计划旨在优化和标准化联邦政府机构使用的各个外部网络连接的安全性。 [预算局 (OMB) 备忘录 M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf) 中概述了该策略。

2007 年 11 月，OMB 建立了 TIC 计划，以增强联邦网络周边安全和事件响应功能。 TIC 旨在对所有入站和出站政府流量进行网络分析，以识别特定签名和基于模式的数据。 TIC 可发现行为异常，如僵尸网络活动。 授权各机构整合其外部网络连接，并通过入侵检测和防御设备（称为 EINSTEIN）路由所有流量。 这些设备托管在有限数量的网络终结点上，这些终结点称为“受信任 Internet 连接”。

TIC 的目标是让各机构了解：
- 谁在使用我的网络（已授权或未授权）？
- 何时访问我的网络？为什么？
- 访问哪些资源？

所有机构外部连接目前必须通过 OMB 批准的 TIC 进行路由。 联邦政府机构需要作为 TIC 访问提供程序 (TICAP) 参与 TIC 计划，或通过任一主要一级 Internet 服务提供程序签订服务。 这些提供程序称为托管受信任 Internet 协议服务 (MTIPS) 提供程序。 TIC 包括机构和 MTIPS 提供程序执行的强制性关键功能。 在当前版本的 TIC 中，每个 TICAP 和 MTIPS 都部署了 EINSTEIN 版本 2 入侵检测和 EINSTEIN 版本 3 加速 (3A) 入侵防御设备。 该机构与国土安全部 (DHS) 签订了一份“合作谅解备忘录”，将 EINSTEIN 功能部署到联邦系统。

作为其保护政府网络的部分职责，DHS 要求代理净流数据的原始数据馈送关联联邦企业中的事件，并使用专用工具执行分析。 DHS 路由器提供在进入或退出接口时收集 IP 网络流量的功能。 网络管理员可分析净流数据，以确定流量的源和目标、服务的类等信息。 净流数据被视为是类似于标头、源 IP、目标 IP 等的“非内容数据”。 非内容数据允许 DHS 了解以下内容：谁在做什么、持续多长时间。

该计划还包括采用本地基础结构的安全策略、指南和框架。 随着政府机构转向云以实现成本节约、运营效率和创新，TIC 的实现要求可能会减缓流量。 因此，政府用户访问基于云的数据的速度和灵活性受到限制。

## <a name="azure-networking-options"></a>Azure 网络选项

可通过三个主要选项连接到 Azure 服务：

- 直接 Internet 连接：通过开放的 Internet 连接直接连接到 Azure 服务。 该介质和连接是公共的。 依靠应用程序和传输级加密来确保隐私。 带宽受限于网站与 Internet 的连接。 使用多个活动提供程序来确保能够进行复原。
- 虚拟专用网络 (VPN)：使用 VPN 网关私下连接到 Azure 虚拟网络。
介质是公共的，因为它遍历站点的标准 Internet 连接，但在隧道中加密了连接，以确保隐私。 带宽受限于 VPN 设备和所选配置。 Azure 点到站点连接通常限制为 100 Mbps，而站点到站点连接限制为 1.25 Gbps。
- Azure ExpressRoute：ExpressRoute 直接连接到 Microsoft 服务。 由于连接是通过独立的纤程通道进行的，因此根据所使用的配置，该连接可为公共连接或专用连接。 带宽通常限制为最大 10 Gbps。

在国土安全部的“受信任 Internet 连接 (TIC) 引用体系结构文档，2.0 版”中，指定了几种方法可以满足 TIC 附录 H（云注意事项）的要求。 在本文中，DHS TIC 指南称为 TIC 2.0。

要实现从部门或代理 (D/A) 到 Azure 或 Office 365 的连接，而无需通过 D/A TIC 路由流量，D/A 必须使用加密隧道或云服务提供商 (CSP) 的专用连接。 CSP 服务可确保不向公共 Internet 提供与 D/A 云资产的连接，以便代理人员直接访问。

Office 365 符合 TIC 2.0 附录 H，该附录使用启用了 [Microsoft 对等互连](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains)的 ExpressRoute 或使用 TLS 1.2 加密所有流量的 Internet 连接。 D/A 网络上的 D/A 最终用户可通过其代理网络和 TIC 基础结构通过 Internet 进行连接。 阻止了 Office 365 的所有远程 Internet 访问并通过该代理路由。 通过 ExpressRoute 连接与启用的 Microsoft 对等互连（公共对等互连的一种）的连接，D/A 也可连接到 Office 365。  

仅对于 Azure，第二个选项 (VPN) 和第三个选项 (ExpressRoute) 在与限制 Internet 访问的服务结合使用时可以满足这些要求。

![Microsoft 受信任 Internet 连接 (TIC)](media/tic-diagram-a.png)

## <a name="azure-infrastructure-as-a-service-offerings"></a>Azure 服务架构产品/服务

使用 Azure IaaS 遵守 TIC 策略相对简单，因为 Azure 客户管理其自己的虚拟网络路由。

帮助确保符合 TIC 引用体系结构的主要要求是确保虚拟网络是 D/A 网络的专用扩展。 要成为专用扩展，该策略要求除了通过本地 TIC 网络连接之外，没有流量离开网络。 此过程称为强制隧道。 对于 TIC 符合性，该过程将 CSP 环境中任何系统的所有流量通过组织网络上的本地网关通过 TIC 路由到 Internet。

Azure IaaS TIC 符合性分为两个主要步骤：

- 步骤 1：配置。
- 步骤 2：审核。

### <a name="azure-iaas-tic-compliance-configuration"></a>Azure IaaS TIC 符合性：配置

要使用 Azure 配置符合 TIC 的体系结构，必须首先阻止对虚拟网络的直接 Internet 访问，然后通过本地网络强制 Internet 流量。

#### <a name="prevent-direct-internet-access"></a>防止直接 Internet 访问

Azure IaaS 网络通过子网组成的虚拟网络进行，虚拟机的网络接口控制器 (NIC) 与子网关联。

支持 TIC 符合性的最简单方案是确保虚拟机或其集合无法连接到任何外部资源。 使用网络安全组 (NSG) 确保与外部网络断开连接。 使用 NSG 控制虚拟网络中一个或多个 NIC 或子网的流量。 NSG 包含根据流量方向、协议、源地址和端口以及目标地址和端口允许或拒绝流量的访问控制规则。 可以随时更改 NSG 的规则，并将更改应用于所有关联的实例。 若要了解有关如何创建 NSG 的详细信息，请参阅[使用网络安全组筛选网络流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)。

#### <a name="force-internet-traffic-through-an-on-premises-network"></a>通过本地网络强制 Internet 流量

Azure 自动创建系统路由，并将路由分配到虚拟网络中的每个子网。 无法创建或删除系统路由，但可以使用自定义路由替代某些系统路由。 Azure 为每个子网创建默认系统路由。 使用特定的 Azure 功能时，Azure 向特定子网或每个子网添加可选默认路由。 这种类型的路由可确保：
- 虚拟网络中的流量保留在虚拟网络内。
- IANA 指定的专用地址空间（如 10.0.0.0/8）将被丢弃，除非包含在虚拟网络的地址空间中。
- 0.0.0.0/0 的“最后手段”路由到虚拟网络的 Internet 终结点。

![TIC 强制隧道](media/tic-diagram-c.png)

为确保所有流量都遍历 D/A TIC，所有离开虚拟网络的流量都需要通过本地连接进行路由。 创建自定义路由有两种方式：一是创建用户定义路由，二是在本地网关和 Azure VPN 网关之间交换边界网关协议 (BGP) 路由。 有关用户定义的路由的详细信息，请参阅[虚拟网络流量路由：用户定义的路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined)。 有关 BGP 的详细信息，请参阅[虚拟网络流量路由：边界网关协议](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol)。

#### <a name="add-user-defined-routes"></a>添加用户定义的路由

如果使用基于路由的虚拟网关，则可在 Azure 中强制隧道。 添加用户定义的路由 (UDR)，将 0.0.0.0/0 流量设置为路由到虚拟网关的“下一跃点”。 Azure 将用户定义的路由设置为优先于系统定义的路由。 所有非虚拟网络流量都会发送到虚拟网关，然后将流量路由到本地。 定义 UDR 后，将路由与 Azure 环境中所有虚拟网络内的现有子网或新子网关联。

![用户定义的路由和 TIC](media/tic-diagram-d.png)

#### <a name="use-the-border-gateway-protocol"></a>使用边界网关协议

如果使用 ExpressRoute 或启用了 BGP 的虚拟网关，则 BGP 是播发路由的首选机制。 对于 0.0.0.0/0 的 BGP 播发路由，ExpressRoute 和 BGP 感知虚拟网关将确保将此默认路由应用于虚拟网络中的所有子网。

### <a name="azure-iaas-tic-compliance-auditing"></a>Azure IaaS TIC 符合性：审核

Azure 提供了多种审核 TIC 符合性的方法。

#### <a name="view-effective-routes"></a>查看有效路由

通过观察 [Azure 门户](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#diagnose-using-azure-portal)或 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell#diagnose-using-powershell) 中特定虚拟机、特定 NIC 或用户定义路由表的有效路由，确认已传播默认路由。 “有效路由”显示适用于相关实体的相关用户定义路由、播发 BGP 的路由和系统路由，如下图所示：

![有效路由](media/tic-screen-1.png)

> [!NOTE]
> 除非 NIC 与正在运行的虚拟机关联，否则无法查看 NIC 的有效路由。

#### <a name="use-azure-network-watcher"></a>使用 Azure 网络观察程序

Azure 网络观察程序提供了多种审核 TIC 符合性的工具。 有关详细信息，请参阅[此网络观察程序概述](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)。

##### <a name="capture-network-security-group-flow-logs"></a>捕获网络安全组流日志 

使用网络观察程序捕获网络流日志，这些日志指示围绕 IP 流量的元数据。 网络流日志包含目标和其他数据的源地址和目标地址。 可将此数据与来自虚拟网关、本地边缘设备或 TIC 的日志结合使用，以监视本地所有流量路由。 

## <a name="azure-platform-as-a-service-offerings"></a>Azure 平台即服务产品/服务

可通过 Internet 可访问的 URL 访问 Azure PaaS 服务（如 Azure 存储）。 具有已批准凭据的任何人都可以从任何位置访问资源（如存储帐户），而无需遍历 TIC。 因此，许多政府客户错误地认为 Azure PaaS 服务不符合 TIC 策略。 许多 Azure PaaS 服务都可符合 TIC 策略。 当体系结构与符合 TIC 的 IaaS 环境（[如前描述](https://docs.microsoft.com/azure/security/compliance/compliance-tic#azure-infrastructure-as-a-service-offerings)）相同，并且服务附加到 Azure 虚拟网络时，该服务是合规的。

当 Azure PaaS 服务与虚拟网络集成时，可从该虚拟网络私密访问该服务。 可通过用户定义的路由或 BGP 为 0.0.0.0/0 应用自定义路由。 自定义路由确保所本地有 Internet 绑定流量路由都可以遍历 TIC。 使用以下模式将 Azure 服务集成到虚拟网络中：

- 部署专用服务实例：越来越多的 PaaS 服务可部署为具有虚拟网络附加终结点的专用实例。 可在“独立”模式下为 PowerApps 部署应用服务环境，以允许将网络终结点限于虚拟网络。 然后，应用服务环境可以托管许多 Azure PaaS 服务，例如 Azure Web 应用、Azure API 管理和 Azure Functions。
- 使用虚拟网络服务终结点：越来越多的 PaaS 服务允许选项将其终结点移动到虚拟网络专有 IP，而不是公共地址。

下表列出了自 2018 年 5 月起支持将专用实例部署到虚拟网络或使用服务终结点的服务。

> [!Note]
> 可用性状态对应于销售的 Azure 服务。 Azure 政府中 Azure 服务的可用性状态尚未确定。

### <a name="support-for-service-endpoints"></a>对服务终结点的支持

|服务                        |可用性      |
|-------------------------------|------------------|
|Azure 密钥保管库                | 个人预览版  |
|Azure Cosmos DB                | 个人预览版  |
|标识服务              | 个人预览版  |
|Azure Data Lake                | 个人预览版  |
|Azure Database for PostgreSQL  | 个人预览版  |
|Azure Database for MySQL       | 个人预览版  |
|Azure SQL 数据仓库       | 公共预览版   |
|Azure SQL 数据库             | 正式发布版 (GA) |
|Azure 存储                  | GA               |

### <a name="support-for-virtual-network-injection"></a>对虚拟网络注入的支持

|服务                               |可用性      |
|--------------------------------------|------------------|
|Azure SQL 数据库托管实例   | 公共预览版   |
|Azure Kubernetes 服务 (AKS)        | 公共预览版   |
|Azure Service Fabric                  | GA               |
|Azure API 管理                  | GA               |
|Azure Active Directory                | GA               |
|Azure 批处理                           | GA               |
|应用服务环境               | GA               |
|Azure Redis 缓存                     | GA               |
|Azure HDInsight                       | GA               |
|虚拟机规模集             | GA               |
|Azure 云服务                  | GA               |


### <a name="virtual-network-integration-details"></a>虚拟网络集成详细信息

下图显示了访问 PaaS 服务的常规网络流。 显示了从虚拟网络注入和虚拟网络服务隧道进行的访问。 有关网络服务网关、虚拟网络和服务标记的详细信息，请参阅[网络和应用程序安全组：服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)。

![TIC 的 PaaS 连接选项](media/tic-diagram-e.png)

1. 使用 ExpressRoute 与 Azure 建立专用连接。 具有强制隧道功能的 ExpressRoute 专有对等互连用于强制 ExpressRoute 上的所有客户虚拟网络流量并返回本地。 无需 Microsoft 对等互连。
2. Azure VPN 网关与 ExpressRoute 和 Microsoft 对等互连一起使用时，可覆盖客户虚拟网络与本地边缘之间的端到端的 IPSec 加密。 
3. 使用 NSG 控制与客户虚拟网络的网络连接，该 NSG 可让客户根据 IP、端口和协议允许/拒绝连接。
4. 通过为客户服务创建服务终结点，客户虚拟网络扩展到 PaaS 服务。
5. 将 PaaS 服务终结点确认为“默认拒绝所有”，且仅允许从客户虚拟网络内的指定子网进行访问。 默认拒绝还包括源自 Internet 的连接。
6. 需要访问客户虚拟网络中资源的其他 Azure 服务应：  
   - 直接部署到虚拟网络中。
   - 根据相应 Azure 服务中的指南进行选择。

#### <a name="option-a-deploy-a-dedicated-instance-of-a-service-virtual-network-injection"></a>选项 A：部署服务的专用实例（虚拟网络注入）

虚拟网络注入可让客户选择性地将给定 Azure 服务的专用实例（例如 HDInsight）部署到其自己的虚拟网络中。 服务实例部署在客户虚拟网络的专用子网中。 虚拟网络注入允许通过非 Internet 的可路由地址来访问服务资源。 本地实例使用 ExpressRoute 或站点到站点 VPN 通过虚拟网络地址空间直接访问服务实例，而不是向公共 Internet 地址空间打开防火墙。 当专用实例连接到终结点时，可使用与用于 IaaS TIC 符合性相同的策略。 默认路由确保将 Internet 绑定的流量重定向到绑定到本地的虚拟网关。 可通过给定子网的 NSG 进一步控制入站和出站访问。

![虚拟网络注入概述](media/tic-diagram-f.png)

#### <a name="option-b-use-virtual-network-service-endpoints-service-tunnel"></a>选项 B：使用虚拟网络服务终结点（服务隧道）

越来越多的 Azure 多租户服务提供“服务终结点”。 服务终结点是集成到 Azure 虚拟网络的另一种方法。 虚拟网络服务终结点可通过直接连接将虚拟网络 IP 地址空间和标识扩展到服务。 从虚拟网络发往 Azure 服务的流量始终保留在 Azure 主干网络中。 

为服务启用服务终结点后，可通过由该服务公开的策略将该服务的连接限制为该虚拟网络。 Azure 服务在平台中强制执行访问检查。 仅当请求来自允许的虚拟网络或子网，或者来自用于标识本地流量的两个 IP（如果使用 ExpressRoute）时，才授予对锁定资源的访问权限。 使用此方法有效防止入站/出站流量直接离开 PaaS 服务。

![服务终结点概述](media/tic-diagram-g.png)

## <a name="cloud-native-tools-for-network-situational-awareness"></a>用于感知网络态势的云本机工具

Azure 提供云本机工具，以帮助确保你具备了解网络流量所需的态势感知功能。 这些工具无需符合 TIC 策略。 这些工具可极大地改进安全功能。

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](https://azure.microsoft.com/services/azure-policy/) 是一项 Azure 服务，可更好地审核组织并实施符合性计划。 Azure Policy 目前在市场上销售的 Azure 服务中提供公共预览版。 Azure Policy 在 Azure 政府中尚不可用。 客户现在可以规划和测试其 Azure Policy 规则，以确保未来的 TIC 符合性。 

Azure Policy 以订阅级别为目标。 该服务提供了一个集中的接口，用于执行符合性任务，包括：
- 管理计划
- 配置策略定义
- 审核符合性
- 强制符合性
- 管理异常

除了许多内置的定义外，管理员还可以使用简单的 JSON 模板定义自己的自定义定义。 在可能的情况下，Microsoft 建议优先执行审核，而非实施。

以下示例策略可用于 TIC 符合性方案：

|策略  |示例方案  |模板  |
|---------|---------|---------|
|强制执行用户定义的路由表。 | 确保所有虚拟网络点上的默认路由指向已批准的虚拟网关，以便路由到本地。    | 开始使用此[模板](https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table)。 |
|如果未对区域启用网络观察程序，则进行审核。  | 确保为所有使用的区域启用了网络观察程序。  | 开始使用此[模板](https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled)。 |
|每个子网上的 NSG X。  | 确保将阻止 Internet 流量的 NSG（或一组已批准的 NSG）应用于每个虚拟网络中的所有子网。 | 开始使用此[模板](https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet)。 |
|每个 NIC 上的 NSG X。 | 确保将阻止 Internet 流量的 NSG 应用于所有虚拟机上的所有 NIC。 | 开始使用此[模板](https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic)。 |
|将已批准的虚拟网络用于虚拟机网络接口。  | 确保所有 NIC 都位于已批准的虚拟网络上。 | 开始使用此[模板](https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics)。 |
|允许的位置。 | 确保将所有资源部署到符合虚拟网络和网络观察程序配置的区域。  | 开始使用此[模板](https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs)。 |
|不允许的资源类型，如 PublicIP。 | 禁止部署没有符合性计划的资源类型。 使用此策略禁止部署公共 IP 地址资源。 虽然 NSG 规则可用于有效阻止入站 Internet 流量，但阻止使用公共 IP 可进一步减少攻击面。   | 开始使用此[模板](https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type)。  |

### <a name="network-watcher-traffic-analytics"></a>网络观察程序流量分析

网络观察程序[流量分析](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/)使用流日志数据和其他日志来提供网络流量的高级概述。 此数据对审核 TIC 符合性并确定故障点非常有用。 可使用高级仪表板快速筛选正在与 Internet 通信的虚拟机，并获取 TIC 路由的重点列表。

![流量分析](media/tic-traffic-analytics-1.png)

使用“异地映射”快速识别虚拟机的 Internet 流量的可能物理目标。 可识别和会审可疑位置或模式更改：

![异地映射](media/tic-traffic-analytics-2.png)

使用“虚拟网络拓扑”快速调查现有虚拟网络：

![网络拓扑映射](media/tic-traffic-analytics-3.png)

### <a name="network-watcher-next-hop-tests"></a>网络观察程序下一跃点测试

网络观察程序监视的网络流入量区域可进行下一跃点测试。 在 Azur 门户中，可为网络观察程序输入示例网络流的源和目标，以解析下一跃点目标。 针对虚拟机和示例 Internet 地址运行此测试，以确保下一跃点目标是预期的网络虚拟网关。

![下一跃点测试](media/tic-network-watcher.png)

## <a name="conclusions"></a>结论

可轻松配置 Microsoft Azure、Office 365 和 Dynamics 365 的访问权限，以帮助符合 2018 年 5 月编写和定义的 TIC 2.0 附录 H 指南。 Microsoft 认识到 TIC 指南可能会发生更改。 在发布新指南后，Microsoft 致力于帮助客户及时了解新指南。

## <a name="appendix-trusted-internet-connections-patterns-for-common-workloads"></a>附录：常见工作负载的受信任 Internet 连接模式

| 类别 | 工作负载 | IaaS | 专用 PaaS /虚拟网络注入  | 服务终结点  |
|---------|---------|---------|---------|--------|
| 计算 | Azure Linux 虚拟机 | 是 | | |
| 计算 | Azure Windows 虚拟机 | 是 | | |
| 计算 | 虚拟机规模集 | 是 | | |
| 计算 | Azure Functions | | 应用服务环境 | |
| Web 和移动 | 内部 Web 应用程序 | | 应用服务环境| |
| Web 和移动 | 内部移动应用程序 | | 应用服务环境 | |
| Web 和移动 | API 应用程序 | | 应用服务环境 | |
| 容器 | Azure 容器服务 | | | 是 |
| 容器 | Azure Kubernetes 服务 (AKS) \* | | | 是 |
| 数据库 | Azure SQL 数据库 | | Azure SQL 数据库托管实例 \* | Azure SQL |
| 数据库 | Azure Database for MySQL | | | 是 |
| 数据库 | Azure Database for PostgreSQL | | | 是 |
| 数据库 | Azure SQL 数据仓库 | | | 是 |
| 数据库 | Azure Cosmos DB | | | 是 |
| 数据库 | Azure Redis 缓存 | | 是 | |
| 存储 | Azure Blob 存储 | 是 | | |
| 存储 | Azure 文件 | 是 | | |
| 存储 | Azure 队列存储 | 是 | | |
| 存储 | Azure 表存储 | 是 | | |
| 存储 | Azure 磁盘存储 | 是 | | |

\* 截至 2018 年 5 月的 Azure 政府公共预览版。
