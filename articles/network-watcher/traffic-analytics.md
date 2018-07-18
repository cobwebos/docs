---
title: Azure 流量分析 | Microsoft Docs
description: 了解如何使用流量分析来分析 Azure 网络安全组流日志。
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: yagup;jdial
ms.openlocfilehash: ad26772650cf052926a2534d343f64765f47b78f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333388"
---
# <a name="traffic-analytics"></a>流量分析

流量分析是一种基于云的解决方案，可用于洞察云网络中的用户和应用程序活动。 流量分析可以分析网络观察程序网络安全组 (NSG) 流日志，帮助洞察 Azure 云中的流量流。 使用流量分析可以：

- 直观查看各个 Azure 订阅中的网络活动，以及识别热点。
- 参考有关开放的端口、尝试访问 Internet 的应用程序以及连接到恶意网络的虚拟机 (VM) 的信息，来识别网络安全威胁和保护网络。
- 了解 Azure 区域与 Internet 之间的流量流模式，优化网络部署以提高性能和容量。
- 查明导致网络连接失败的不当网络配置。

## <a name="why-traffic-analytics"></a>为何要使用流量分析？

在监视、管理自己的网络，以及识别网络中是否存在安全性、合规性和性能问题时，流量分析非常关键。 在保护和优化自己的环境之前，了解该环境至关重要。 通常需要知道网络的当前状态、谁正在连接、他们从哪里进行连接、向 Internet 开放了哪些端口、预期网络行为、异常网络行为，以及流量的突发性增长。

云网络不同于本地企业网络，后者往往部署有支持 Netflow 或类似协议的路由器和交换机，当 IP 网络流量进入或退出网络接口时，它们能够收集这些流量。 通过分析流量流数据，可以生成网络流量流和流量大小的分析数据。

Azure 虚拟网络提供 NSG 流日志，其中提供了传入和传出与单个网络接口、VM 或子网相关联的网络安全组的 IP 流量的信息。 通过分析原始 NSG 流日志并插入安全、拓扑和地理智能功能，流量分析可以提供环境中流量流的深入信息。 流量分析提供的信息包括通信最活跃的主机、通信最活跃的应用程序协议、对话最活跃的主机对、允许/阻止的流量、入站/出站流量、开放的 Internet 端口、最严厉的规则、每个 Azure 数据中心的流量分布、虚拟网络、子网或恶意网络等。

## <a name="key-components"></a>关键组件

- **网络安全组 (NSG)**：包含一系列安全规则，这些规则可以允许或拒绝流向连接到 Azure 虚拟网络的资源的网络流量。 可以将 NSG 关联到子网、单个 VM（经典）或附加到 VM 的单个网络接口 (NIC) (Resource Manager)。 有关详细信息，请参阅[网络安全组概述](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。
- **网络安全组 (NSG) 流日志**：用于查看有关传入和传出网络安全组的 IP 流量的信息。 NSG 流日志以 JSON 格式编写，并基于每个规则显示出站和入站流、流所适用的 NIC、有关流的五元组信息（源/目标 IP 地址、源/目标端口和协议），以及是允许还是拒绝流量。 有关 NSG 流日志的详细信息，请参阅 [NSG 流日志](network-watcher-nsg-flow-logging-overview.md)。
- **Log Analytics**：一个 Azure 服务，可以收集监视数据并将数据存储在中心存储库中。 这些数据可能包括事件、性能数据或通过 Azure API 提供的自定义数据。 收集后，可以分析、导出数据或针对它们发出警报。 网络性能监视器和流量分析等监视应用程序是在 Log Analytics 的基础上构建的。 有关详细信息，请参阅 [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。
- **Log Analytics 工作区**：Log Analytics 的一个实例，用于存储与 Azure 帐户相关的数据。 有关 Log Analytics 工作区的详细信息，请参阅[创建 Log Analytics 工作区](../log-analytics/log-analytics-quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。
- **网络观察程序**：一个区域性服务，用于在 Azure 中监视和诊断网络方案级别的状态。 可以使用网络观察程序启用和禁用 NSG 流日志。 有关详细信息，请参阅[网络观察程序](network-watcher-monitoring-overview.md)。

## <a name="how-traffic-analytics-works"></a>流量分析的工作原理

流量分析检查原始 NSG 流日志，并通过聚合相同源 IP 地址、目标 IP 地址、目标端口和协议之间的通用流来捕获精简的日志。 例如，假设主机 1（IP 地址：10.10.10.10）与主机 2（IP 地址：10.10.20.10）在 1 小时内使用端口（例如 80）和协议（例如 HTTP）通信 100 次。 精简的日志只包含条目，指出主机 1 与主机 2 在 1 小时内使用端口 *80* 和协议 *HTTP* 通信 100 次；而不是包含 100 个条目。 系统会使用地理、安全和拓扑信息增强精简的日志，然后将其存储在 Log Analytics 工作区中。 下图显示了数据流：

![NSG 流日志处理的数据流](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>支持的区域

可以对以下任何区域中的 NSG 使用流量分析：美国中西部、美国东部、美国东部 2 区、美国中北部、美国中南部、美国中部、美国西部、美国西部 2 区、西欧、北欧、英国西部、英国南部、澳大利亚东部、澳大利亚东南部和东南亚。 Log Analytics 工作区必须位于美国中西部、美国东部、西欧、英国南部、澳大利亚东南部或东南亚区域。

## <a name="prerequisites"></a>先决条件

### <a name="user-access-requirements"></a>用户访问要求

帐户必须是以下 Azure [内置角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)之一的成员：

|部署模型   | 角色                   |
|---------          |---------               |
|资源管理器   | 所有者                  |
|                   | 参与者            |
|                   | 读取器                 |
|                   | 网络参与者    |
|经典            | 帐户管理员  |
|                   | 服务管理员  |
|                   | 共同管理员       |

如果未将帐户分配给内置角色之一，则必须在订阅级别将其分配给分配有以下操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)：

- "Microsoft.Network/applicationGateways/read"
- "Microsoft.Network/connections/read"
- "Microsoft.Network/loadBalancers/read"
- "Microsoft.Network/localNetworkGateways/read"
- "Microsoft.Network/networkInterfaces/read"
- "Microsoft.Network/networkSecurityGroups/read"
- "Microsoft.Network/publicIPAddresses/read"
- "Microsoft.Network/routeTables/read"
- "Microsoft.Network/virtualNetworkGateways/read"
- "Microsoft.Network/virtualNetworks/read"

有关如何检查用户访问权限的信息，请参阅[流量分析常见问题解答](traffic-analytics-faq.md)。

### <a name="enable-network-watcher"></a>启用网络观察程序

若要分析流量，需要提供现有的网络观察程序，或者在要分析其流量的 NSG 所在的每个区域[启用网络观察程序](network-watcher-create.md)。 可对任一[受支持区域](#supported-regions)中托管的 NSG 启用流量分析。

### <a name="re-register-the-network-resource-provider"></a>重新注册网络资源提供程序

在使用流量分析之前，必须重新注册网络资源提供程序。 在以下代码框中单击“试用”打开 Azure Cloud Shell。 Cloud Shell 会自动将你登录到你的 Azure 订阅。 打开 Cloud Shell 之后，输入以下命令重新注册网络资源提供程序：

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Network"
```

### <a name="select-a-network-security-group"></a>选择网络安全组

在启用 NSG 流日志记录之前，必须提供要记录其流的网络安全组。 如果没有网络安全组，请参阅[创建网络安全组](../virtual-network/manage-network-security-group.md#create-a-network-security-group)来创建一个。

在 Azure 门户左侧选择“监视”，然后依次选择“网络观察程序”、“NSG 流日志”。 选择要为其启用 NSG 流日志的网络安全组，如下图所示：

![选择需要启用 NSG 流日志的 NSG](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

尝试为[受支持区域](#supported-regions)以外的任何区域中托管的 NSG 启用流量分析时，会收到“未找到”错误。

## <a name="enable-flow-log-settings"></a>启用流日志设置

启用流日志设置之前，必须完成以下任务：

如果尚未为订阅注册 Azure Insights 提供程序，请注册该提供程序：

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

如果尚未创建一个用于存储 NSG 流日志的 Azure 存储帐户，则必须创建一个存储帐户。 可以使用以下命令创建存储帐户。 运行该命令之前，请将 `<replace-with-your-unique-storage-account-name>` 替换为在所有 Azure 位置中唯一的、长度为 3-24 个字符且仅使用数字和小写字母的名称。 还可以根据需要更改资源组名称。

```azurepowershell-interactive
New-AzureRmStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

如图所示选择以下选项：

1. 为“状态”选择“打开”
2. 选择用于存储流日志的现有存储帐户。 若要永久存储数据，请将值设置为 *0*。 存储帐户会产生 Azure 存储费用。
3. 将“保留期”设置为存储数据的天数。
4. 为“流量分析状态”选择“打开”。
5. 选择现有的 Log Analytics (OMS) 工作区，或选择“创建新工作区”来创建一个新工作区。 流量分析使用 Log Analytics 工作区来存储聚合数据和索引数据，然后，这些数据用于生成分析。 如果选择现有的工作区，该工作区必须位于某个[受支持区域](#traffic-analytics-supported-regions)，并且已升级为新查询语言。 如果不希望升级现有工作区，或者受支持区域中没有工作区，请创建一个新工作区。 有关查询语言的详细信息，请参阅[将 Azure Log Analytics 升级到新的日志搜索](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。

    托管流量分析解决方案和 NSG 的 Log Analytics 工作区不一定要位于同一个区域。 例如，可将流量分析部署在西欧区域的某个工作区中，同时将 NSG 部署在美国东部和美国西部。 可在同一工作区中配置多个 NSG。
6. 选择“保存”。

    ![选择存储帐户和 Log Analytics 工作区并启用流量分析](./media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement.png)

针对想要为其启用流量分析的其他任何 NSG 重复前面的步骤。 流日志中的数据将发送到工作区，因此，请确保所在国家/地区的当地法律和法规允许将数据存储在工作区所在的区域。

还可以使用 AzureRm PowerShell 模块 6.2.1 版或更高版本中的 [Set-AzureRmNetworkWatcherConfigFlowLog](/powershell/module/azurerm.network/set-azurermnetworkwatcherconfigflowlog) PowerShell cmdlet 来配置流量分析。 运行 `Get-Module -ListAvailable AzureRM` 来查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

## <a name="view-traffic-analytics"></a>查看流量分析

在门户左侧选择“所有服务”，并在“筛选器”框中输入“监视”。 当“监视”出现在搜索结果中时，请选择它。 若要开始浏览流量分析及其功能，请依次选择“网络观察程序”、“流量分析”。

![访问流量分析仪表板](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

首次显示仪表板最长可能需要花费 30 分钟时间，因为流量分析必须先聚合足够的数据来派生有意义的见解，然后才能生成任何报告。

## <a name="usage-scenarios"></a>使用方案

完全配置流量分析之后，可以获取的某些见解如下：

### <a name="find-traffic-hotspots"></a>查找流量热点

**查找**

- 哪些主机、子网和虚拟网络正在发送或接收最多的流量、有最大恶意流量流过以及阻止重要流？
    - 检查主机、子网和虚拟网络的比较图表。 了解哪些主机、子网和虚拟网络正在发送或接收大部分流量有助于识别正在处理大部分流量的主机，以及是否正确进行了流量分发。
    - 然后，可以评估流量大小是否适合让某台主机来处理。 流量行为是否正常，或者是否需要进一步的调查？
- 有多少入站/出站流量？
    -   主机预期收到的入站流量是否多过出站流量，或反之？
- 阻止的流量统计信息。
    - 主机为何阻止大量良性流量？ 对于此行为，需要进一步进行调查，并且可能需要对配置进行优化。
- 允许/阻止的恶意流量统计信息
    - 主机为何在接收恶意流量以及为何允许来自恶意源的流？ 对于此行为，需要进一步进行调查，并且可能需要对配置进行优化。

    在“主机”下选择“查看全部”，如下图所示：

    ![展示处理大部分流量的主机详细信息的仪表板](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- 下图显示了对话最活跃的五台主机的时间趋势，以及主机的流相关详细信息（允许 - 入站/出站流，拒绝 - 入站/出站流）：

    ![对话最活跃的五台主机的趋势](media/traffic-analytics/top-five-most-talking-host-trend.png)

**查找**

- 对话最活跃的主机对有哪些？
    - 预期行为（例如前端与后端之间的通信）或异常行为（例如后端与 Internet 之间的流量）。
- 允许/阻止的流量统计信息
    - 主机为何允许或阻止大量的流量？
- 对话最活跃的主机对之间最常用的应用程序协议：
    - 此网络中是否允许这些应用程序？
    - 应用程序的配置是否正确？ 它们是否使用适当的协议进行通信？ 在“频繁的对话”下选择“查看全部”，如下图所示：

        ![展示最频繁对话的仪表板](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- 下图显示了最频繁的五个对话的时间趋势，以及流相关的详细信息，例如，某个对话对允许和拒绝的入站与出站流：

    ![最频繁的五个对话的详细信息和趋势](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**查找**

- 环境中最常使用的应用程序协议是哪个，哪些对话主机对最常使用该应用程序协议？
    - 此网络中是否允许这些应用程序？
    - 应用程序的配置是否正确？ 它们是否使用适当的协议进行通信？ 预期行为是使用常用端口，例如 80 和 443。 如果为标准通信显示了任何非寻常端口，可能需要对端口进行配置更改。 在“应用程序端口”下选择“查看全部”，如下图所示：

        ![展示最常使用的应用程序协议的仪表板](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- 下图显示了最常使用的五个 L7 协议的时间趋势，以及某个 L7 协议的流相关详细信息（例如，允许和拒绝的流）：

    ![最常使用的五个第 7 层协议的详细信息和趋势](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![日志搜索中应用程序协议的流详细信息](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**查找**

- 环境中 VPN 网关的容量利用率趋势。
    - 每个 VPN SKU 允许特定的带宽量。 VPN 网关是否利用不足？
    - 网关是否即将达到容量？ 是否应升级到下一个更高的 SKU？
- 哪些主机的对话最活跃，它们通过哪个 VPN 网关和端口对话？
    - 此模式是否正常？ 在“VPN 网关”下选择“查看全部”，如下图所示：

        ![展示最活跃的 VPN 连接的仪表板](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- 下图显示了某个 Azure VPN 网关的容量利用率时间趋势，以及流相关的详细信息（例如允许的流和端口）：

    ![VPN 网关利用率趋势和流详细信息](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>按地理位置可视化流量分布

**查找**

- 每个数据中心的流量分布，例如，向数据中心传送流量的最主要来源、与数据中心对话的最主要恶意网络，以及对话最活跃的应用程序协议。
    - 如果发现数据中心的负载增加，可以规划高效的流量分布。
    - 如果恶意网络在数据中心对话，请更正 NSG 规则以阻止这些网络。

    在“你的环境”下选择“查看地图”，如下图所示：

    ![展示流量分布的仪表板](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- 使用地图顶部的功能区可以选择参数，例如数据中心（已部署/未部署/活动/非活动/已启用流量分析/未启用流量分析），以及向活动部署分配良性/恶意流量的国家/地区：

    ![展示活动部署的地图视图](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- 地图中显示了不同国家/地区和大洲在与数据中心通信时分配的流量情况，蓝线表示良性流量，红线表示恶意流量。

    ![展示不同国家/地区和大洲流量分布的地图视图](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![日志搜索中流量分布的流详细信息](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>按虚拟网络可视化流量分布

**查找**

- 每个虚拟网络的流量分布、拓扑、向虚拟网络传送流量的最主要来源、与虚拟网络对话的最主要恶意网络，以及对话最活跃的应用程序协议。
    - 了解哪两个虚拟网络正在对话。 如果对话不符合预期，可将其更正。
    - 如果恶意网络正在与虚拟网络对话，可以更正 NSG 规则以阻止恶意网络。
 
    在“你的环境”下选择“查看 VNet”，如下图所示：

    ![展示虚拟网络分布的仪表板](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- 使用虚拟网络拓扑顶部的功能区可以选择参数，例如虚拟网络（虚拟网络连接之间/活动/非活动）、外部连接、活动流和虚拟网络的恶意流。
- 虚拟网络拓扑显示虚拟网络的流相关流量分布（允许/阻止/入站/出站/良性/恶意）、应用程序协议和网络安全组，例如：

    ![展示流量分布和流详细信息的虚拟网络拓扑](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)

    ![日志搜索中虚拟网络流量分布的流详细信息](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**查找**

- 每个子网的流量分布、拓扑、向子网传送流量的最主要来源、与子网对话的最主要未授权网络，以及对话最活跃的应用程序协议。
    - 了解哪两个子网正在对话。 如果发现意外的对话，可以更正配置。
    - 如果未授权网络正在与子网对话，可以配置 NSG 规则来阻止未授权网络，从而更正此行为。
- 使用子网拓扑顶部的功能区可以选择参数，例如活动/非活动的子网、外部连接、活动流和子网的恶意流。
- 子网拓扑显示虚拟网络的流相关流量分布（允许/阻止/入站/出站/良性/恶意）、应用程序协议和 NSG，例如：

    ![展示虚拟网络子网的流相关流量分布的子网拓扑](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**查找**

每个应用程序网关和负载均衡器的流量分布、拓扑、最主要的流量来源、与应用程序网关和负载均衡器对话的最主要未授权网络，以及对话最活跃的应用程序协议。 
    
 - 了解哪个子网正在与哪个应用程序网关或负载均衡器对话。 如果观察到意外的对话，可以更正配置。
 - 如果未授权网络正在与应用程序网关或负载均衡器对话，可以配置 NSG 规则来阻止未授权网络，从而更正此行为。 

    ![subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>查看从 Internet 接收流量的端口和虚拟机

**查找**

- 哪些开放的端口正在通过 Internet 对话？
    - 如果发现打开了意外的端口，可以更正配置：

    ![展示与 Internet 相互接收和发送流量的端口的仪表板](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Azure 目标端口和主机的详细信息](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**查找**

环境中是否存在恶意流量？ 该流量源于何处？ 该流量传往何处？

![日志搜索中的恶意流量流详细信息](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>可视化 NSG/NSG 规则的触发趋势

**查找**

- 哪些 NSG/NSG 规则在比较图表中具有与流分布最多的命中数？
- 每个 NSG/NSG 规则的最常见源和目标对话对是什么？

    ![展示 NSG 触发统计信息的仪表板](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- 下图显示了 NSG 规则的触发时间趋势，以及网络安全组的源-目标流详细信息：

    - 快速检测哪些 NSG 和 NSG 规则在遍历恶意流，以及哪些是访问你的云环境的主要恶意 IP 地址。
    - 查明哪些 NSG/NSG 规则在允许/阻止大量的网络流量
    - 选择顶部的用于对 NSG 或 NSG 规则进行精细检查的筛选器

    ![展示 NSG 规则的触发时间趋势，以及触发次数最多的 NSG 规则](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![日志搜索中触发次数最多的 NSG 规则详细统计信息](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>常见问题

若要获取常见问题的解答，请参阅[流量分析常见问题解答](traffic-analytics-faq.md)。
