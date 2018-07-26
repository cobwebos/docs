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
ms.openlocfilehash: 9d71efa35713500911c67d1df15612b64c8e97da
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990823"
---
# <a name="trusted-internet-connection-guidance"></a>受信任的 Internet 连接指南

## <a name="background"></a>背景

受信任的 Internet 连接 (TIC) 计划，旨在优化和标准化联邦政府机构当前使用的各个外部网络连接的安全性。 OMB（预算局）[备忘录 M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf) 中概述了该策略。

2007 年 11 月，OMB 建立了 TIC 计划，以增强联邦网络周边安全和事件响应功能。 TIC 旨在对所有入站和出站政府流量进行网络分析，以识别特定签名和基于模式的数据，并发现行为异常，例如僵尸网络活动。 授权各机构，整合其外部网络连接，并通过在有限数量的网络终结点上（称为受信任的 Internet 连接）托管的入侵检测和防御设备（称为 EINSTEIN）路由所有流量。

简而言之，TIC 的目标是让各机构了解：
- 谁在使用我的网络（已授权或未授权）？
- 何时访问我的网络？为什么？
- 正在访问哪些资源？

今天，所有机构外部连接必须通过 OMB 批准的 TIC 进行路由。 联邦政府机构需要作为 TIC 访问提供程序 (TICAP) 参与 TIC 计划，或通过与称为托管受信任的 Internet 协议服务 (MTIPS) 提供程序的任一主要一级 Internet 服务提供程序签订服务。  TIC 包括机构和 MTIPS 提供程序目前执行的强制性关键功能。 在当前版本的 TIC 中，每个 TICAP 和 MTIPS 上均部署了 EINSTEIN 版本 2 入侵检测和 EINSTEIN 版本 3 加速 (3A) 入侵防御设备，并且该机构与国土安全部 (DHS) 签订了一份协议备忘录，以将 EINSTEIN 功能部署到联邦系统。

作为其保护政府网络的部分职责，DHS 要求代理 Netflow 数据的原始数据馈送关联联邦企业中的事件，并使用专用工具执行分析。 DHS 路由器提供在进入或退出接口时收集 IP 网络流量的功能。 通过分析净流数据，网络管理员可以确定流量的源和目标、服务的类等信息。净流数据被认为是“非内容性的数据”（例如，标头、源 IP、目标 IP 等），并允许 DHS 知道内容周围的信息（也就是说，谁做了什么，做了多久）。

该计划还包括采用本地基础结构的安全策略、指南和框架。 随着政府机构转向云以实现成本节约、运营效率和创新，TIC 的实现要求在某些情况下减缓了网络流量，限制了政府用户访问其基于云的数据的速度和灵活性。

本文介绍了政府机构如何使用 Microsoft Azure 政府在 IaaS 和 PaaS 服务中帮助实现 TIC 策略的符合性。

## <a name="summary-of-azure-networking-options"></a>Azure 网络选项摘要

连接到 Azure 服务时，有三个主要选项：

1. 直接 Internet 连接：通过开放的 Internet 连接直接连接到 Azure 服务。 该介质是公共的，连接也是公共的。 依靠应用程序和传输级加密来确保隐私。 带宽受限于站点与 Internet 的连接性，可使用多个活动的提供程序来确保复原
1. 虚拟专用网络：使用 VPN 网关私下连接到 Azure 虚拟网络。
介质是公共的，因为它遍历站点的标准 Internet 连接，但在隧道中加密了连接，以确保隐私。 带宽受限于 VPN 设备和所选配置。 Azure 点到站点连接通常限制为 100 Mbps，而站点到站点连接限制为 1.25 Gbps。
1. Microsoft ExpressRoute：ExpressRoute 直接连接到 Microsoft 服务。 由于连接是通过独立的纤程通道进行的，因此根据所使用的配置，该连接可为公共连接或专用连接。 带宽通常限制为最大 10 Gbps。

在国土安全部的“受信任 Internet 连接 (TIC) 引用体系结构文档，2.0 版”中，有几种方法可以满足受信任的 Internet 连接附录 H（云注意事项）的要求。 在本文档中，DHS TIC 指南将被称为 TIC 2.0。

要实现从部门或代理 (D/A) 到 Azure 或 Office 365 的连接，而无需通过 D/A TIC 路由流量，D/A 必须使用加密隧道和/或云服务提供商 (CSP) 的专用连接。 CSP 服务可确保不向公共 Internet 提供与 D/A 云资产的连接，以便代理人员直接访问。

O365 符合 TIC 2.0 附录 H，该附录使用启用了 [Microsoft 对等互连](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains)的 Express Route 或使用 TLS 1.2 加密所有流量的 Internet 连接。  D/A 网络上的 D/A 最终用户可通过其代理网络和 TIC 基础结构通过 Internet 进行连接。 阻止了 O365 的所有远程 Internet 访问并通过该代理路由。 通过 Express Route 与启用的 Microsoft 对等互连（公共对等互连的一种）的连接，D/A 也可连接到 O365。  

仅对于 Azure，选项2 (VPN) 和 3 (ExpressRoute) 在与限制 Internet 访问的服务结合使用时可以满足这些要求。

![Microsoft 受信任的 Internet 连接 (TIC) 关系图](media/tic-diagram-a.png)

## <a name="how-azure-infrastructure-as-a-service-offerings-can-help-with-tic-compliance"></a>Azure 服务架构产品/服务如何帮助实现 TIC 符合性

使用 IaaS 遵守 TIC 策略相对简单，因为 Azure 客户管理其自己的虚拟网络路由。

帮助确保符合 TIC 引用体系结构的主要要求是确保虚拟网络 (VNet) 成为部门或机构网络的专用扩展。 要成为_专用_扩展，该策略要求除了通过本地 TIC 网络连接之外，没有流量离开网络。 此过程称为“强制隧道”，当用于 TIC 符合性时，是指将 CSP 环境中任何系统的所有流量通过组织网络上的本地网关通过 TIC 路由到 Internet 的过程。

Azure IaaS TIC 符合性可分为两个主要步骤：

1. 配置
1. 审核

### <a name="azure-iaas-tic-compliance-configuration"></a>Azure IaaS TIC 符合性配置

要使用 Azure 配置符合 TIC 的体系结构，必须首先阻止对虚拟网络的直接 Internet 访问，然后通过本地网络强制 Internet 流量。

#### <a name="prevent-direct-internet-access"></a>防止直接 Internet 访问

Azure IaaS 网络通过子网组成的虚拟网络进行，虚拟机的网络接口控制器 (NIC) 与子网相关联。

支持 TIC 符合性的最简单方案是确保虚拟机或其集合无法连接到任何外部资源。 使用网络安全组 (NSG) 可以确保与外部网络断开连接，网络安全组可用于控制虚拟网络中一个或多个 NIC 或子网的流量。 NSG 包含根据流量方向、协议、源地址和端口以及目标地址和端口允许或拒绝流量的访问控制规则。 可以随时更改 NSG 的规则，所做的更改适用于所有关联的实例。  要了解有关如何创建 NSG 的详细信息，请参阅此文[创建 NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)。

#### <a name="force-internet-traffic-through-on-premises-network"></a>通过本地网络强制 Internet 流量

Azure 自动创建系统路由，并将路由分配到虚拟网络中的每个子网。 无法创建系统路由，也无法删除系统路由，但可以使用自定义路由替代某些系统路由。 当你使用特定的 Azure 功能时，Azure 为每个子网创建默认系统路由，并向特定子网或每个子网添加其他可选默认路由。 此路由确保虚拟网络中的流量保留在虚拟网络内，IANA 指定的专用地址空间（如 10.0.0.0/8）将被丢弃（除非包含在虚拟网络的地址空间中），并且 0.0.0.0/0 的“最后手段”路由到虚拟网络的 Internet 终结点。

![TIC 强制隧道](media/tic-diagram-c.png)

为确保所有流量都遍历 D/A TIC，所有离开虚拟网络的流量都需要通过本地连接进行路由。  创建自定义路由有两种方式：一是创建用户定义路由，二是在本地网关和 Azure 虚拟网关之间交换边界网关协议 (BGP) 路由。 有关用户定义的路由的详细信息，请访问 https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined。 有关边界网关协议的详细信息，请访问 https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol。

#### <a name="user-defined-routes"></a>用户定义路由

如果使用的是基于路由的虚拟网关，则可以在 Azure 中通过添加用户定义路由 (UDR) 设置 0.0.0.0/0 流量来完成强制隧道，以路由到虚拟网关的“下一跃点”。 Azure 将用户定义的路由的优先级设置为高于系统定义的路由，因此这将导致所有非 VNet 流量被发送到虚拟网关，然后可将其路由到本地。 定义后，此用户定义的路由必须与 Azure 环境中所有虚拟网络中现有或新创建的所有子网相关联。

![用户定义的路由和 TIC](media/tic-diagram-d.png)

#### <a name="border-gateway-protocol"></a>边界网关协议

如果使用的是 ExpressRoute 或启用了边界网关协议 (BGP) 的虚拟网关，则 BGP 是播发路由的首选机制。 如果 BGP 播发路由为 0.0.0.0/0，则 ExpressRoute 和 BGP 感知虚拟网关将确保将此默认路由应用于虚拟网络中的所有子网。

### <a name="azure-iaas-tic-compliance-auditing"></a>Azure IaaS TIC 符合性审核

Azure 提供了多种审核 TIC 符合性的方法。

#### <a name="effective-routes"></a>有效路由

要确认已传播默认路由，可观察特定 VM、特定 NIC 或用户定义的路由表的“有效路由”。 可通过 https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal 中所述的 Azure 门户或 https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell 中所述的 PowerShell 来完成此操作。 “有效路由”边栏选项卡将允许查看适用于相关实体的相关用户定义路由、播发 BGP 的路由和系统路由，如下所示。

![路由屏幕截图](media/tic-screen-1.png)

注意：除非与正在运行的 VM 关联，否则无法查看 NIC 的有效路由。

#### <a name="network-watcher"></a>网络观察程序

Azure 网络观察程序提供了多种可用于审核 TIC 符合性的工具。  在 https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview 处了解有关网络观察程序的详细信息。

##### <a name="network-security-groups-flow-logs"></a>网络安全组流日志 

Azure 网络观察程序提供捕获网络流日志的功能，以指示 IP 流量周围的元数据。 除了其他数据，网络流日志还包含目标的源地址和目标地址。 与来自虚拟网关、本地边缘设备和/或 TIC 的日志相结合，将有助于监视是否确实是在本地路由所有流量的。 

## <a name="how-azure-platform-as-a-service-offerings-can-help-with-tic-compliance"></a>Azure 平台即服务产品/服务如何帮助实现 TIC 符合性

可通过 Internet 可访问的 URL 访问 Azure PaaS 服务（如 Azure 存储）。 具有已批准凭据的任何人都可以从任何位置访问资源（如存储帐户），而无需遍历 TIC。 因此，许多政府客户错误地认为 Azure PaaS 服务不符合 TIC 策略。 事实上，如果许多 Azure PaaS 服务可以连接到虚拟网络 (VNet)，则可以使用与上述符合 TIC 的 IaaS 环境相同的体系结构来符合 TIC 策略。 将 Azure PaaS 服务与 Azure VNet 集成，可以从该 VNet 私密访问该服务，并允许通过用户定义的路由或 BGP 来应用 0.0.0.0/0 的自定义路由，确保在本地路由所有受 Internet 限制的流量，以遍历 TIC。  可使用以下模式将某些 Azure 服务集成到 Vnet 中：

- **部署专用服务实例**：可将越来越多的 PaaS 服务部署为具有 VNet 附加终结点的专用实例。 例如，可在“独立”模式下部署应用服务环境 (ASE)，从而使其网络终结点受限于 VNet。 然后，此 ASE 可托管多项 Azure PaaS 服务，例如 Web 应用、API 和 Azure Functions。
- **VNet 服务终结点**：越来越多的 PaaS 服务允许选项将其终结点移动到 VNet 专有 IP，而不是公共地址。

截至 2018 年 5 月，支持将专用实例部署到 VNet 或服务终结点的服务如下：*（可用性代表 Azure 商业版，Azure 政府可用性待定）。

### <a name="service-endpoints"></a>服务终结点

|服务                   |状态            |
|--------------------------|------------------|
|Azure KeyVault            | 个人预览版  |
|Cosmos DB                 | 个人预览版  |
|标识                  | 个人预览版  |
|Azure Data Lake           | 个人预览版  |
|Sql Postgress/Mysql       | 个人预览版  |
|Azure SQL 数据仓库  | 公共预览版   |
|Azure SQL                 | GA               |
|存储                   | GA               |

### <a name="vnet-injection"></a>VNet 注入

|服务                            |状态            |
|-----------------------------------|------------------|
|SQL 托管实例               | 公共预览版   |
|Azure 容器服务 (AKS)       | 公共预览版   |
|Service Fabric                     | GA               |
|API 管理                     | GA               |
|Azure Active Directory             | GA               |
|Azure 批处理                        | GA               |
|ASE                                | GA               |
|Redis                              | GA               |
|HDI                                | GA               |
|计算虚拟机规模集  | GA               |
|计算云服务              | GA               |

### <a name="vnet-integration-details"></a>VNet 集成详细信息

下图演示了使用 VNet 注入和 VNet 服务隧道访问 PaaS 服务的一般网络流。  有关网络服务网关、VNet 和服务标记的其他信息，请访问 https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags。

![TIC 的 PaaS 连接选项](media/tic-diagram-e.png)

1. 使用 ExpressRoute 与 Azure 建立专用连接。 具有强制隧道功能的 ExpressRoute 专有对等互连用于强制 ExpressRoute 上的所有客户 VNet 流量返回本地。 无需 Microsoft 对等互连。
2. Azure VPN 网关与 ExpressRoute Microsoft 对等互连一起使用，可用于覆盖客户 VNet 与本地边缘之间的端到端的 IPSec 加密。 
3. 使用网络安全组 (NSG) 控制与客户 VNet 的网络连接，该安全组可让客户根据 IP、端口和协议允许/拒绝连接。
4. 通过为客户服务创建服务终结点，客户 VNet 扩展到 PaaS 服务。
5. 将 PaaS 服务终结点确认为默认拒绝所有，且仅允许从客户 VNet 内的指定子网进行访问。  默认拒绝还包括源自 Internet 的连接。
6. 需要访问客户 VNet 中资源的任何其他 Azure 服务应为：  
  a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 直接部署到 VNet 中  
  b. 根据相应 Azure 服务中的指南进行选择。

#### <a name="option-1-dedicated-instance-vnet-injection"></a>选项 1：专用实例“VNet 注入”

使用 VNet 注入，客户可以选择性地将给定 Azure 服务的专用实例（例如 HDInsight）部署到其自己的 VNet 中。 服务实例部署在客户 VNet 的专用子网中。 VNet 注入允许通过非 Internet 的可路由地址来访问服务资源。  本地实例可通过 VNet 地址空间直接通过 ExpressRoute 或站点到站点 VPN 访问这些服务实例，而不是向公共 Internet 地址空间打开安全边界网关。 通过将专用实例连接到终结点，可以使用与用于 IaaS TIC 符合性相同的策略，默认路由可确保将 Internet 绑定的流量重定向到绑定到本地的虚拟网关。 可通过给定子网的网络安全组 (NSG) 进一步控制入站和出站访问。

![VNet 注入概览图](media/tic-diagram-f.png)

#### <a name="option-2-vnet-service-endpoints"></a>选项 2：VNet 服务终结点 

越来越多的 Azure 多租户服务提供了“服务终结点”功能，这是集成到 Azure VNet 的另一种方法。 VNet 服务终结点可通过直接连接将 VNet IP 地址空间和 VNet 的标识扩展到服务。  从 VNet 发往 Azure 服务的流量始终保留在 Azure 主干网络中。 为服务启用服务终结点后，可通过由该服务公开的策略将与该服务的连接限制为该 VNet。 Azure 服务在平台中强制执行访问检查，并且仅当请求来自允许的 VNet/子网时，和/或当使用 ExpressRoute 时用于识别本地流量的两个 IP，才授予对锁定资源的访问权限。 这可有效防止入站/出站流量直接离开 PaaS 服务。

![服务终结点概览图](media/tic-diagram-g.png)

## <a name="using-cloud-native-tools-for-network-situational-awareness"></a>使用云本机工具感知网络态势

Azure 提供云本机工具，以帮助确保你具备了解网络流量所需的态势感知功能。 它们无需符合 TIC 策略，但可以极大地提高安全功能。

### <a name="azure-policy"></a>Azure 策略

Azure Policy (https://azure.microsoft.com/services/azure-policy/)) 是一项 Azure 服务，可更好地审核组织并实施符合性计划。  目前在 Azure 商业云中的公共预览版中可用，但在政府的 Microsoft Azure 中尚不可用，具有 TIC 意识的客户可以开始规划和测试其策略规则，以确保未来的符合性。 Azure Policy 以订阅级别为目标，它提供了一个集中的接口，用于管理计划、策略定义、审核和实施结果以及异常管理。 除了许多内置的 Azure Policy 定义之外，管理员还可以通过简单 json 模板定义其自己的自定义定义。对于许多客户，在可能的情况下，Microsoft 建议优先执行审核，而非实施。

以下示例策略可能对 TIC 符合性方案有用：

|策略  |示例方案  |正在启动模板  |
|---------|---------|---------|
|强制执行用户定义的路由表 |     确保所有虚拟网络点上的默认路由指向已批准的虚拟网关，以便路由到本地 | https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table |
|如果未对区域启用网络观察程序，则进行审核  | 确保为所有使用的区域启用了网络观察程序  | https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled |
|每个子网上的 NSG X  | 确保将阻止 Internet 流量的 NSG（或一组已批准的 NSG）应用于每个 VNet 中的所有子网 | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet |
|每个 NIC 上的 NSG X | 确保将阻止 Internet 流量的 NSG 应用于所有 VM 上的所有 NIC。 | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic |
|对 VM 网络接口使用已批准的 VNet  | 确保所有 NIC 都位于已批准的 VNet 上 | https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics |
|允许的位置 | 确保将所有资源部署到符合 VNet 和网络观察程序配置的区域  | https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs |
|不允许的资源类型，如 PublicIP  | 禁止部署没有符合性计划的资源类型。 例如，此策略可用于禁止部署公共 IP 地址资源。 虽然 NSG 规则可用于有效阻止入站 Internet 流量，但阻止使用公共 IP 可进一步减少攻击面。    | https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type  |

### <a name="azure-traffic-analyticshttpsazuremicrosoftcomen-inblogtraffic-analytics-in-preview"></a>Azure [流量分析](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/)

Azure 网络观察程序的流量分析使用流日志数据和其他日志来提供网络流量的高级概述。 这些数据对审核 TIC 符合性并确定故障点非常有用。 高级仪表板可用于快速筛选哪些 VM 正在与 Internet 通信，然后为 TIC 路由提供重点列表。

![流量分析屏幕截图](media/tic-traffic-analytics-1.png)

“异地映射”可用于快速识别 VM 的 Internet 流量的可能的物理目标，使你能够识别和会审可疑位置或模式更改。

![流量分析屏幕截图](media/tic-traffic-analytics-2.png)

拓扑映射可用于快速调查现有虚拟网络：

![流量分析屏幕截图](media/tic-traffic-analytics-3.png)

### <a name="azure-network-watcher-next-hop"></a>Azure 网络观察程序下一跃点

网络观察程序监视的网络流入量区域中可进行“下一跃点”测试，允许基于门户的简单访问来键入示例网络流的源和目标，网络观察程序将为其解析“下一跃点”目标。 这可以用于 VM 和示例 Internet 地址，以确保“下一跃点”确实是网络虚拟网关。

![网络观察程序下一跃点](media/tic-network-watcher.png)

## <a name="conclusions"></a>结论

可以轻松配置 Microsoft Azure、Office 365 和 Dynamics 365 访问，以帮助符合 2018 年 5 月编写和定义的 TIC 2.0 附录 H 指南。  Microsoft 察觉本指南可能会发生更改，并在发布新指南后，致力于帮助客户获取新指南。

## <a name="appendix-tic-patterns-for-common-workloads"></a>附录：常见工作负载的 TIC 模式

| 类别 | 工作负载 | IaaS | 专用 PaaS / VNet 注入  | 服务终结点  |
|---------|---------|---------|---------|--------|
| 计算 | Linux 虚拟机 | 是 | | |
| 计算 | Windows 虚拟机 | 是 | | |
| 计算 | 虚拟机规模集 | 是 | | |
| 计算 | Azure Functions | | 通过应用服务环境 (ASE) | |
| Web 和移动 | 内部 Web 应用程序 | | 通过应用服务环境 (ASE) | |
| Web 和移动 | 内部移动应用程序 | | 通过应用服务环境 (ASE) | |
| Web 和移动 | API 应用 | | 通过应用服务环境 (ASE) | |
| 容器 | Azure 容器服务 (ACS) | | | 是 |
| 容器 | Azure 容器服务 (AKS)* | | | 是 |
| 数据库 | SQL 数据库 | | Azure SQL 数据库托管实例* | Azure SQL |
| 数据库 | Azure Database for MySQL | | | 是 |
| 数据库 | Azure Database for PostgreSQL | | | 是 |
| 数据库 | SQL 数据仓库 | | | 是 |
| 数据库 | Azure Cosmos DB | | | 是 |
| 数据库 | Redis 缓存 | | 是 | |
| 存储 | Blob | 是 | | |
| 存储 | 文件 | 是 | | |
| 存储 | 队列 | 是 | | |
| 存储 | 表 | 是 | | |
| 存储 | 磁盘 | 是 | | |

*：截至 2018 年 5 月的 Azure 政府的公共预览版  
**：截至 2018 年 5 月的 Azure 政府的个人预览版
