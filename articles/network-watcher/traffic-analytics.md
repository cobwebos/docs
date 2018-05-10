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
ms.date: 02/22/2018
ms.author: jdial
ms.openlocfilehash: ba7589b0fcbb987ef4e7225ea5a4cceda9ad1179
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="traffic-analytics"></a>流量分析

流量分析是一种基于云的解决方案，可用于洞察云网络中的用户和应用程序活动。 流量分析可以分析网络观察程序网络安全组 (NSG) 流日志，帮助洞察 Azure 云中的流量流。 使用流量分析可以：

- 直观查看各个 Azure 订阅中的网络活动，以及识别热点。
- 参考有关开放的端口、尝试访问 Internet 的应用程序以及连接到恶意网络的虚拟机 (VM) 的信息，来识别网络安全威胁和保护网络。
- 了解 Azure 区域与 Internet 之间的流量流模式，优化网络部署以提高性能和容量。
- 查明导致网络连接失败的不当网络配置。

## <a name="why-traffic-analytics"></a>为何要使用流量分析？

在监视、管理自己的网络，以及识别网络中是否存在安全性、合规性和性能问题时，流量分析非常关键。 在保护和优化自己的环境之前，了解该环境至关重要。 通常需要知道网络的当前状态、谁正在连接到哪个位置、向 Internet 开放了哪些端口、预期网络行为、异常网络行为，以及流量的突发性增长。

云网络不同于本地企业网络，后者往往部署有支持 Netflow 或类似协议的路由器和交换机，当 IP 网络流量进入或退出网络接口时，它们能够收集这些流量。 通过分析流量流数据，可以生成网络流量流和流量大小的分析数据。

Azure 虚拟网络提供 NSG 流日志，其中提供了传入和传出与单个网络接口、VM 或子网相关联的网络安全组的 IP 流量的信息。 通过分析原始 NSG 流日志并插入安全、拓扑和地理智能功能，流量分析可以提供环境中流量流的深入信息。 流量分析提供的信息包括通信最活跃的主机、通信最活跃的应用程序协议、对话最活跃的主机对、允许/阻止的流量、入站/出站流量、开放的 Internet 端口、最严厉的规则、每个 Azure 数据中心的流量分布、虚拟网络、子网或恶意网络等。

## <a name="key-components"></a>关键组件 

- **网络安全组 (NSG)**：包含一系列安全规则，这些规则可以允许或拒绝流向连接到 Azure 虚拟网络的资源的网络流量。 可以将 NSG 关联到子网、单个 VM（经典）或附加到 VM 的单个网络接口 (NIC) (Resource Manager)。 有关详细信息，请参阅[网络安全组概述](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。
- **网络安全组 (NSG) 流日志**：用于查看有关传入和传出网络安全组的 IP 流量的信息。 NSG 流日志以 JSON 格式编写，并基于每个规则显示出站和入站流、流所适用的 NIC、有关流的 5 元组信息（源/目标 IP 地址、源/目标端口和协议），以及是允许还是拒绝流量。 有关 NSG 流日志的详细信息，请参阅 [NSG 流日志](network-watcher-nsg-flow-logging-overview.md)。
- **Log Analytics**：一个 Azure 服务，可以收集监视数据并将数据存储在中心存储库中。 这些数据可能包括事件、性能数据或通过 Azure API 提供的自定义数据。 收集后，可以分析、导出数据或针对它们发出警报。 网络性能监视器和流量分析等监视应用程序是在 Log Analytics 的基础上构建的。 有关详细信息，请参阅 [Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。
- **Log Analytics 工作区**：Log Analytics 的一个实例，用于存储与 Azure 帐户相关的数据。 有关 Log Analytics 工作区的详细信息，请参阅[创建 Log Analytics 工作区](../log-analytics/log-analytics-quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。
- **网络观察程序**：一个区域性服务，用于在 Azure 中监视和诊断网络方案级别的状态。 可以使用网络观察程序启用和禁用 NSG 流日志。 有关详细信息，请参阅[网络观察程序](network-watcher-monitoring-overview.md)。

## <a name="how-traffic-analytics-works"></a>流量分析的工作原理 

流量分析检查原始 NSG 流日志，并通过聚合相同源 IP 地址、目标 IP 地址、目标端口和协议之间的通用流来捕获精简的日志。 例如，假设主机 1（IP 地址：10.10.10.10）与主机 2（IP 地址：10.10.20.10）在 1 小时内使用端口（例如 80）和协议（例如 HTTP）通信 100 次。 精简的日志只包含条目，指出主机 1 与主机 2 在 1 小时内使用端口 *80* 和协议 *HTTP* 通信 100 次；而不是包含 100 个条目。 系统会使用地理、安全和拓扑信息增强精简的日志，然后将其存储在 Log Analytics 工作区中。 下图显示了数据流：

![NSG 流日志处理的数据流](media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>支持的区域

流量分析目前以预览版提供。 预览版功能的可用性和可靠性级别与正式版不同。  在预览版中，可对以下任何区域中的 NSG 使用流量分析：美国中西部、美国东部、美国东部 2 区、美国中北部、美国中南部、美国中部、美国西部、美国西部 2 区、西欧、北欧、英国西部、英国南部、澳大利亚东部和澳大利亚东南部。 Log Analytics 工作区必须位于美国中西部、美国东部、西欧、澳大利亚东南部或英国南部区域。

## <a name="prerequisites"></a>先决条件

### <a name="enable-network-watcher"></a>启用网络观察程序 

若要分析流量，需要提供现有的网络观察程序，或者在要分析其流量的 NSG 所在的每个区域[启用网络观察程序](network-watcher-create.md)。 可对任一[受支持区域](#supported-regions)中托管的 NSG 启用流量分析。

### <a name="re-register-the-network-resource-provider"></a>重新注册网络资源提供程序 

在预览版中使用流量分析之前，必须重新注册网络资源提供程序。 在以下代码框中单击“试用”打开 Azure Cloud Shell。 Cloud Shell 会自动将你登录到你的 Azure 订阅。 打开 Cloud Shell 之后，输入以下命令重新注册网络资源提供程序：

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Network"
```

### <a name="select-a-network-security-group"></a>选择网络安全组 

在启用 NSG 流日志记录之前，必须提供要记录其流的网络安全组。 如果没有网络安全组，请参阅[创建网络安全组](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)来创建一个。

在 Azure 门户左侧选择“监视”，然后依次选择“网络观察程序”、“NSG 流日志”。 选择要为其启用 NSG 流日志的网络安全组，如下图所示：

![选择需要启用 NSG 流日志的 NSG](media/traffic-analytics/selection-of-nsgs-that-require- enablement-of-nsg-flow-logging.png)

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

    ![选择存储帐户和 Log Analytics 工作区并启用流量分析](media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement.png)

针对想要为其启用流量分析的其他任何 NSG 重复前面的步骤。 流日志中的数据将发送到工作区，因此，请确保所在国家/地区的当地法律和法规允许将数据存储在工作区所在的区域。

## <a name="view-traffic-analytics"></a>查看流量分析

在门户左侧选择“所有服务”，并在“筛选器”框中输入“监视”。 当“监视”出现在搜索结果中时，请选择它。 若要开始浏览流量分析及其功能，请依次选择“网络观察程序”、“流量分析(预览版)”。

![访问流量分析仪表板](media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

首次显示仪表板最长可能需要花费 30 分钟时间，因为流量分析必须先聚合足够的数据来派生有意义的见解，然后才能生成任何报告。

## <a name="usage-scenarios"></a>使用方案

完全配置流量分析之后，可以获取的某些见解如下：

### <a name="find-traffic-hotspots"></a>查找流量热点

**查找**

- 哪些主机正在发送或接收大部分流量？
    - 了解哪些主机正在发送或接收大部分流量有助于识别正在处理大部分流量的主机。
    - 然后，可以评估流量大小是否适合让某台主机来处理。 流量行为是否正常，或者是否需要进一步的调查？
- 有多少入站/出站流量？
    -   主机预期收到的入站流量是否多过出站流量，或反之？
- 允许/阻止的流量统计信息。
    - 主机为何允许或阻止大量流量？

    在“处理大部分流量的主机”下面选择“更多详细信息”，如下图所示：

    ![展示处理大部分流量的主机详细信息的仪表板](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- 下图显示了对话最活跃的五台主机的时间趋势，以及 VM 的流相关详细信息（允许 - 入站/出站流，拒绝 - 入站/出站流）：

    ![对话最活跃的五台主机的趋势](media/traffic-analytics/top-five-most-talking-host-trend.png)

**查找**

- 对话最活跃的主机对有哪些？
    - 预期行为（例如前端与后端之间的通信）或异常行为（例如后端与 Internet 之间的流量）。
- 允许/阻止的流量统计信息
    - 主机为何允许或阻止大量的流量？
- 对话最活跃的主机对之间最常用的应用程序协议：
    - 此网络中是否允许这些应用程序？
    - 应用程序的配置是否正确？ 它们是否使用适当的协议进行通信？ 在“最频繁的对话”下面选择“更多详细信息”，如下图所示：

        ![展示最频繁对话的仪表板](media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- 下图显示了最频繁的五个对话的时间趋势，以及流相关的详细信息，例如，某个对话对允许和拒绝的入站与出站流：

    ![最频繁的五个对话的详细信息和趋势](media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**查找**

- 环境中最常使用的应用程序协议是哪个，哪些对话主机对最常使用该应用程序协议？
    - 此网络中是否允许这些应用程序？
    - 应用程序的配置是否正确？ 它们是否使用适当的协议进行通信？ 预期行为是使用常用端口，例如 80 和 443。 如果为标准通信显示了任何非寻常端口，可能需要对端口进行配置更改。 在“最常使用的应用程序协议”下面选择“更多详细信息”，如下图所示：

        ![展示最常使用的应用程序协议的仪表板](media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- 下图显示了最常使用的五个 L7 协议的时间趋势，以及某个 L7 协议的流相关详细信息（例如，允许和拒绝的流）：

    ![最常使用的五个第 7 层协议的详细信息和趋势](media/traffic-analytics/top five-layer-seven-protocols-details-and-trend.png)

    ![日志搜索中应用程序协议的流详细信息](media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**查找**

- 环境中 VPN 网关的容量利用率趋势。
    - 每个 VPN SKU 允许特定的带宽量。 VPN 网关是否利用不足？
    - 网关是否即将达到容量？ 是否应升级到下一个更高的 SKU？
- 哪些主机的对话最活跃，它们通过哪个 VPN 网关和端口对话？
    - 此模式是否正常？ 在“最活跃的 VPN 连接”下面选择“更多详细信息”，如下图所示：

        ![展示最活跃的 VPN 连接的仪表板](media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- 下图显示了某个 Azure VPN 网关的容量利用率时间趋势，以及流相关的详细信息（例如允许的流和端口）：

    ![VPN 网关利用率趋势和流详细信息](media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>按地理位置可视化流量分布

**查找**

- 每个数据中心的流量分布，例如，向数据中心传送流量的最主要来源、与数据中心对话的最主要恶意网络，以及对话最活跃的应用程序协议。
    - 如果发现数据中心的负载增加，可以规划高效的流量分布。
    - 如果恶意网络在数据中心对话，请更正 NSG 规则以阻止这些网络。

    在“各 Azure 数据中心的流量分布”下面选择“单击此处查看实时地图”，如下图所示：

  ![展示流量分布的仪表板](media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- 使用地图顶部的功能区可以选择参数，例如数据中心（已部署/未部署/活动/非活动/已启用流量分析/未启用流量分析），以及向活动部署分配良性/恶意流量的国家/地区：

    ![展示活动部署的地图视图](media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- 地图中显示了不同国家/地区和大洲在与数据中心通信时分配的流量情况，蓝线表示良性流量，红线表示恶意流量。

    ![展示不同国家/地区和大洲流量分布的地图视图](media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![日志搜索中流量分布的流详细信息](media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>按虚拟网络可视化流量分布

**查找**

- 每个虚拟网络的流量分布、拓扑、向虚拟网络传送流量的最主要来源、与虚拟网络对话的最主要恶意网络，以及对话最活跃的应用程序协议。
    - 了解哪两个虚拟网络正在对话。 如果对话不符合预期，可将其更正。
    - 如果恶意网络正在与虚拟网络对话，可以更正 NSG 规则以阻止恶意网络。
 
    在“虚拟网络分布”下面选择“单击此处查看 VNet 流量拓扑”，如下图所示： 

    ![展示虚拟网络分布的仪表板](media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- 使用虚拟网络拓扑顶部的功能区可以选择参数，例如虚拟网络（虚拟网络连接之间/活动/非活动）、外部连接、活动流和虚拟网络的恶意流。
- 虚拟网络拓扑显示虚拟网络的流相关流量分布（允许/阻止/入站/出站/良性/恶意）、应用程序协议和网络安全组，例如：

    ![展示流量分布和流详细信息的虚拟网络拓扑](media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)

    ![日志搜索中虚拟网络流量分布的流详细信息](media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**查找**

- 每个子网的流量分布、拓扑、向子网传送流量的最主要来源、与子网对话的最主要恶意网络，以及对话最活跃的应用程序协议。
    - 了解哪两个子网正在对话。 如果发现意外的对话，可以更正配置。
    - 例如，如果恶意网络正在与子网对话，可以配置 NSG 规则来阻止恶意网络，从而更正此行为。
- 使用子网拓扑顶部的功能区可以选择参数，例如活动/非活动的子网、外部连接、活动流和子网的恶意流。
- 子网拓扑显示虚拟网络的流相关流量分布（允许/阻止/入站/出站/良性/恶意）、应用程序协议和 NSG，例如：

    ![展示虚拟网络子网的流相关流量分布的子网拓扑](media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>查看从 Internet 接收流量的端口和虚拟机

**查找**

- 哪些开放的端口正在通过 Internet 对话？
    - 如果发现打开了意外的端口，可以更正配置：

        ![展示与 Internet 相互接收和发送流量的端口的仪表板](media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

        ![Azure 目标端口和主机的详细信息](media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**查找**

环境中是否存在恶意流量？ 该流量源于何处？ 该流量传往何处？

![日志搜索中的恶意流量流详细信息](media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsg-rule-hits"></a>可视化 NSG 规则的触发趋势

**查找**

- 哪些 NSG/规则的触发次数最高？
- 每个 NSG 的最常见源和目标对话对是什么？

    ![展示 NSG 触发统计信息的仪表板](media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- 下图显示了 NSG 规则的触发时间趋势，以及网络安全组的源-目标流详细信息：

    ![展示 NSG 规则的触发时间趋势，以及触发次数最多的 NSG 规则](media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![日志搜索中触发次数最多的 NSG 规则详细统计信息](media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>常见问题

若要获取常见问题的解答，请参阅[流量分析常见问题解答](traffic-analytics-faq.md)。
