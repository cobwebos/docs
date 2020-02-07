---
title: 连接监视器（预览） |Microsoft Docs
description: 了解如何使用连接监视器（预览版）监视分布式环境中的网络通信
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 5dc705fbd17a12ee001e1e8de15b49e841f08b81
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049187"
---
# <a name="unified-connectivity-monitoring-with-connection-monitor-preview"></a>统一连接监视与连接监视器（预览）

连接监视器（预览版）在 Azure 网络观察程序中提供统一的端到端连接监视功能，用于混合和 Azure 云部署。 Azure 网络观察程序提供的工具可用于监视、诊断和查看适用于 Azure 部署的与连接相关的指标。

关键用例：

- 你具有与多层应用程序中的数据库服务器 VM 通信的前端 web 服务器 VM。 需要检查两个 Vm 之间的网络连接。
- 你希望美国东部地区的 Vm ping 美国中北部区域的 Vm，并比较跨区域网络延迟
- 你的城市（如西雅图）中有多个本地办公场所。 连接到 Office 365 Url。 你需要比较用户使用西雅图和阿什本的 Office 365 Url 时遇到的延迟。
- 已设置混合应用程序，需要连接到 Azure 存储终结点。 你需要比较本地站点和 Azure 应用程序之间的延迟，同时连接到同一 Azure 存储终结点。
- 需要检查从托管云应用程序的 Azure Vm 到本地设置的连接。

在此预览阶段，该解决方案综合了两个关键功能-网络观察程序的[连接监视器](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint)和网络性能监视器（NPM）的[服务连接监视器](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)。

介绍

* 适用于 Azure 和混合监视需求的统一、直观的体验
* 跨区域，跨工作区连接监视
* 更高的探测频率，更好地了解网络性能
* 混合部署的更快警报
* 支持基于 HTTP、TCP 和 ICMP 的连接检查
* 针对 Azure 和非 Azure 测试设置的指标和 Log Analytics 支持

![连接监视器](./media/connection-monitor-2-preview/hero-graphic.png)

按照下面所述的步骤使用连接监视器（预览版）开始监视

## <a name="step-1-install-monitoring-agents"></a>步骤1：安装监视代理

连接监视器依赖轻型可执行文件来运行连接性检查。  支持从 Azure 和本地环境进行连接检查。 要使用的特定可执行文件取决于 VM 是托管在 Azure 上还是托管在本地。

### <a name="agents-for-azure-virtual-machines"></a>Azure 虚拟机代理

要使连接监视器能够将 Azure Vm 识别为监视源，需要在其上安装网络观察程序代理虚拟机扩展（也称为网络观察程序扩展）。 网络观察程序代理扩展是在 Azure 虚拟机上触发端到端监视和其他高级功能的要求。 你可以[创建 VM 并](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)[在其上](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)安装网络观察程序扩展。  你还可以单独安装、配置和排查适用于[Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux)和[Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)的网络观察程序扩展。

如果 NSG 或防火墙规则阻止源和目标之间的通信，连接监视器将检测该问题并将其显示为拓扑中的诊断消息。 若要启用连接监视，请确保 NSG 和防火墙规则允许源和目标之间的 TCP 或 ICMP 上的数据包。

### <a name="agents-for-on-premise-machines"></a>本地计算机的代理

为了让连接监视器将本地计算机识别为监视源，需要在计算机上安装 Log Analytics 代理，并启用网络性能监视解决方案。 这些代理链接到 Log Analytics 工作区，并需要设置工作区 ID 和主密钥，然后才能开始监视。

若要为 Windows 计算机安装 Log Analytics 代理，请按照[此链接](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)中所述的说明进行操作

如果路径中有防火墙或虚拟网络设备（NVA），请确保可以访问目标。

## <a name="step-2-enable-network-watcher-on-your-subscription"></a>步骤2：在订阅上启用网络观察程序

使用网络观察程序启用 VNET 的所有订阅。 在订阅中创建虚拟网络时，将在该虚拟网络的区域和订阅中自动启用网络观察程序。 对于自动启用网络观察程序的资源或相关费用不会有任何影响。 确保未在订阅上显式禁用网络观察程序。 有关详细信息，请参阅[启用网络观察](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)程序。

## <a name="step-3-create-connection-monitor"></a>步骤3：创建连接监视器 

_连接监视器_定期监视通信，并通知源代理和目标终结点之间的可访问性、延迟和网络拓扑更改。 源可以是 Azure Vm，也可以是安装了监视代理的本地计算机。 目标终结点可以是 Office 365 Url，Dynamics 365 Url，自定义 Url，Azure VM 资源 Id，IPv4，IPv6，FQDN 或任何域名。

### <a name="accessing-connection-monitor-preview"></a>访问连接监视器（预览）

1. 在 Azure 门户主页上，访问网络观察程序
2. 在网络观察程序的左窗格的 "监视" 部分中，单击 "连接监视器（预览）" 选项卡。
3. 你可以看到使用连接监视器（预览）体验创建的所有连接监视器。 使用 "连接监视器" 选项卡的 "经典体验" 创建的所有连接监视器都将显示在 "连接监视器" 选项卡中。

    ![创建连接监视器](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="creating-a-connection-monitor"></a>创建连接监视器

使用连接监视器（预览版）创建的连接监视器使你能够同时将本地和 Azure Vm 添加为源，并监视与终结点（可跨 Azure 或任何其他 URL/IP）的连接。

下面是连接监视器中的实体：

* 连接监视器资源–特定于区域的 Azure 资源。 下面提到的所有实体都是连接监视器资源的属性。
* 终结点–参与连接检查的所有源和目标都作为终结点进行调用。 终结点的示例-Azure Vm、本地代理、Url、Ip
* 测试配置–每个测试配置都是特定于协议的。 根据所选协议，可以定义端口、阈值、测试频率和其他参数
* 测试组–每个测试组包含源终结点、目标终结点和测试配置。 每个连接监视器可包含多个测试组
* 测试–源终结点、目标终结点和测试配置的组合进行一项测试。 测试是监视数据（检查失败% 和 RTT）可用的最低级别

 ![创建连接监视器](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="from-portal"></a>从门户

若要创建连接监视器，请执行以下所述步骤：

1. 在 "连接监视器（预览）" 仪表板中，单击左上角的 "创建"。
2. 在 "基本" 选项卡中，输入连接监视器的信息
   1. 连接监视器名称–连接监视器的名称。 此处适用于 Azure 资源的标准命名规则。
   2. 订阅–为连接监视器选择订阅。
   3. 区域–为连接监视器资源选择一个区域。 只能选择在此区域创建的源 Vm。
   4. 工作区配置-可以使用连接监视器创建的默认工作区来存储监视数据，方法是单击 "默认" 复选框。 若要选择自定义工作区，请取消选中此框。 选择 "订阅" 和 "区域" 以选择将保存监视数据的工作区。
   5. 单击 "下一步：测试组" 以添加测试组

      ![创建连接监视器](./media/connection-monitor-2-preview/create-cm-basics.png)

3. 在 "测试组" 选项卡中，单击 "+ 测试组" 以添加测试组。 使用_在连接监视器中创建测试组_来添加测试组。 单击 "查看 + 创建" 查看连接监视器。

   ![创建连接监视器](./media/connection-monitor-2-preview/create-tg.png)

4. 在 "查看和创建" 选项卡中，查看基本信息和测试组，然后再创建连接监视器。 若要从 "查看 + 创建" 视图编辑连接监视器，请执行以下操作：
   1. 若要编辑基本详细信息，请使用图2中的框1指定的铅笔图标
   2. 若要编辑单个测试组，请单击要编辑的测试组以在编辑模式下打开该测试组。
   3. 当前成本/月份表明预览期间的成本。 目前使用连接监视器不会有任何费用，因此此列将显示为零。 实际成本/月份表示公开上市后将收取的价格。 请注意，在预览期间，log analytics 引入费用也将适用。

5. 在 "查看和创建" 选项卡中，单击 "创建" 按钮创建连接监视器。

   ![创建连接监视器](./media/connection-monitor-2-preview/review-create-cm.png)

6.  连接监视器（预览版）将在后台创建连接监视器资源。

#### <a name="from-armclient"></a>From Armclient

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "[https://](https://apac01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fbrazilus.management.azure.com&amp;data=02%7C01%7CManasi.Sant%40microsoft.com%7Cd900da4ed7f24366842108d68022159b%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636837281231186904&amp;sdata=qHL8zWjkobY9MatRpAVbODwboKSQAqqEFOMnjmfyOnU%3D&amp;reserved=0)management.azure.com"

$SUB = "subscriptions/\&lt;subscription id 1\&gt;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_centraluseuap"

$body =

"{

location: 'eastus',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourcegroups/\&lt;resource group\&gt;/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '\&lt;FQDN of your on-premise agent'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

},

 {

name: 'vm2',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

   },

{

name: 'azure portal'

address: '\&lt;URL\&gt;'

   },

 {

    name: 'ip',

     address: '\&lt;IP\&gt;'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: 60,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'https',

    testFrequencySec: 60,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: 30,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: 90,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: 120,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: 45,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }

  ]

 }

} "
```

部署命令：
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="creating-test-groups-in-connection-monitor"></a>在连接监视器中创建测试组

连接监视器中的每个测试组都包含源和目标，可在检查失败的网络参数和测试配置的 RTT 上进行测试。

#### <a name="from-portal"></a>从门户

若要在连接监视器中创建测试组，请为以下提到的字段指定值：

1. 禁用测试组–选中此字段将对测试组中指定的所有源和目标禁用监视。 默认情况下，此选项处于未选中状态。
2. 名称–测试组的名称
3. 源-如果安装了代理，则可以将 Azure Vm 和本地计算机指定为源。 请参阅步骤1，安装特定于你的源的代理。
   1. 单击 "Azure Agent" 选项卡以选择 "Azure 代理"。 你将仅看到列出的 Vm，这些 Vm 绑定到创建连接监视器时指定的区域。 默认情况下，Vm 按其所属的订阅分组，并折叠这些组。 您可以从 "订阅" 级别向下钻取到层次结构中的其他级别：

      ```Subscription -\&gt; resource groups -\&gt; VNETs -\&gt; Subnets -\&gt; VMs with agents Y```

      还可以更改 "分组依据" 字段的值，以从任何其他级别启动树。 例如： Group by – VNET 会在层次结构中显示包含代理的 Vm\&Vnet gt;子网-\&g t;具有代理的 Vm。

      ![添加源](./media/connection-monitor-2-preview/add-azure-sources.png)

   2. 单击 "非 Azure 代理" 选项卡，选择 "本地代理"。 默认情况下，你会看到代理分组到一个区域中的工作区。仅会列出已配置网络性能监视器解决方案的工作区。 将网络性能监视器解决方案从 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)添加到工作区。 你还可以使用[从解决方案库中添加 Azure Monitor 解决方案](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)中所述的过程。默认情况下，你会在 "创建连接监视器" 视图的 "基本信息" 选项卡中看到所选区域。 可以更改区域，并从新选择的区域中选择工作区中的代理。 你还可以将 "分组依据" 字段的值更改为 "分组依据" 子网。

      ![非 Azure 源](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   3. 单击 "查看" 以查看已选择的 Azure 和非 Azure 代理。

      ![查看源](./media/connection-monitor-2-preview/review-sources.png)

   4. 选择源后，单击 "完成"。

4. 目标–可以通过将其指定为目标来监视 Azure Vm 或任何终结点（公共 IP、URL、FQDN）的连接。 在单个测试组中，可以添加 Azure Vm、O365 Url、D365 Url 或自定义终结点。

   1. 单击 "Azure Vm" 选项卡，选择 "Azure vm" 作为目标。 默认情况下，将在 "创建连接监视器" 视图的 "基本信息" 选项卡中所选的同一区域中看到 Azure Vm 分组到订阅层次结构。 可以更改区域，并从新选择的区域中选择 "Azure Vm"。 你可以从 "订阅" 级别深化到层次结构中的其他级别，如 Azure 代理。

      ![添加目标](./media/connection-monitor-2-preview/add-azure-dests1.png)<br>

      ![添加目标2](./media/connection-monitor-2-preview/add-azure-dests2.png)

   2. 单击 "终结点" 选项卡，选择 "作为目标的 Azure Vm"。 将使用 O365 和 D365 测试 Url 填充 Endpoint 列表，并按名称分组。  你还可以选择在同一连接监视器中的其他测试组中创建的终结点。 若要添加新的终结点，请单击屏幕右上角的 "+ 终结点"，并提供终结点 URL/IP/FQDN 和名称

      ![添加终结点](./media/connection-monitor-2-preview/add-endpoints.png)

   3. 单击 "查看" 以查看已选择的 Azure 和非 Azure 代理。
   4. 选择源后，单击 "完成"。

5. 测试配置–可以将任意数量的测试配置关联到给定的测试组。 门户会将其限制为每个测试组一个测试配置，但使用 Armclient 添加更多。
   1. 名称-测试配置的名称
   2. 协议–可以在 TCP、ICMP 或 HTTP 之间进行选择。 若要将 HTTP 更改为 HTTPS，请选择 HTTP as 协议，并选择443作为端口
   3. 创建网络测试配置-仅当在 "协议" 字段中选择 "HTTP" 时才会显示此复选框。 启用此字段可使用在步骤3和步骤4中通过 TCP/ICMP 协议指定的相同源和目标来创建另一个测试配置。 新创建的测试配置的名称为 "\&lt; 在5中指定的名称。\&g t;\_networkTestConfig "
   4. 禁用 Traceroute –此字段适用于将 TCP 或 ICMP 作为协议的测试组。  检查此字段，以防止源发现拓扑和逐跃点往返时间。
   5. 目标端口–可以自定义此字段，将其放入所选的目标端口。
   6. 测试频率–此字段决定源将对上面指定的协议和端口 ping 目标的频率。 你可以选择30秒、1分钟、5分钟、15分钟和30分钟。 源将根据你选择的值测试与目标的连接。  例如，如果选择30秒，在30秒内，源将检查与目标的连接至少一次。
   7. 运行状况阈值–可以在下面提到的网络参数上设置阈值
      1. 检查失败百分比-当源检查与以上指定的条件的目标的连接时，检查失败百分比。 对于 TCP/ICMP 协议，% 中的检查失败可能会等同到数据包丢失%。 对于 HTTP 协议，此字段表示未收到响应的 http 请求数。
      2. RTT （以毫秒为单位）-当源通过上面指定的测试配置连接到目标时的往返时间（以毫秒为单位）。

      ![添加 TG](./media/connection-monitor-2-preview/add-test-config.png)

添加到测试组并指定测试配置的所有源和目标都将分解为单独的测试。 例如：

* 测试组： TG1
* 源：3（A，B，C）
* 目标：2（D，E）
* 测试配置：2（配置1，配置2）
* 已创建测试： Total = 12

| **测试编号** | **数据源** | **目标** | **测试配置名称** |
| --- | --- | --- | --- |
| 1 | A | D | 配置1 |
| 2 | A | D | 配置2 |
| 3 | A | E | 配置1 |
| 4 | A | E | 配置2 |
| 5 | B | D | 配置1 |
| 6 | B | D | 配置2 |
| 7 | B | E | 配置1 |
| 8 | B | E | 配置2 |
| 9 | C | D | 配置1 |
| 10 | C | D | 配置2 |
| 11 | C | E | 配置1 |
| 12 | C | E | 配置2 |

### <a name="scale-limits"></a>规模限制

* 每个区域每个订阅的最大连接监视器数–100
* 每个连接监视器的最大测试组数-20
* 最大 # 源 + 每个连接监视器的目标–100
* 每个连接监视器的最大测试配置数–20（通过 Armclient）。 2 via 门户。

## <a name="step-4--data-analysis-and-alerts"></a>步骤4：数据分析和警报

创建连接监视器后，源会根据指定的测试配置检查与目标的连接。

### <a name="checks-in-a-test"></a>签入测试

根据用户在测试配置中选择的协议，连接监视器（预览）会针对所选测试频率运行一系列的源目标对检查。

如果选择了 "HTTP"，则服务将计算返回响应代码以确定检查失败% 的 HTTP 响应数。  若要计算 RTT，我们需要度量接收 HTTP 调用响应所用的时间。

如果选择了 "TCP" 或 "ICMP"，则服务将计算数据包% 以确定检查失败%。 若要计算 RTT，我们需要测量为发送的数据包接收确认所花费的时间。 如果已为网络测试启用了 traceroute 数据，则可以查看本地网络的跃点丢失和延迟情况。

### <a name="states-of-a-test"></a>测试的状态

基于测试中的检查返回的数据，每个测试可以具有以下状态：

* Pass = 当检查的实际值失败%，并且 RTT 在指定阈值内
* Fail = 当检查的实际值失败% 或 RTT 超出指定阈值时。 如果未指定阈值，则测试在检查失败时标记为失败% = 100%
* 警告–未指定检查失败的条件%。 在这种情况下，连接监视器（预览）使用自动设置的条件作为阈值，并且当该阈值被破坏时，该测试的状态将设置为 "警告"

### <a name="data-collection-analysis-and-alerts"></a>数据收集、分析和警报

连接监视器（预览版）收集的所有数据都存储在创建连接监视器时配置的 Log Analytics 工作区中。 监视数据在 Azure Monitor 度量值中也可用。 您可以使用 Log Analytics 根据您的需要保留监视数据，Azure Monitor 默认情况下将指标存储30天 **。** 然后，您可以[对数据设置基于指标的警报](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)。

#### <a name="monitoring-dashboards-in-connection-monitor-solution"></a>监视连接监视器解决方案中的仪表板

对于所选的订阅、区域、时间戳、源和目标类型，你将看到你有权访问的连接监视器列表。

导航到网络观察程序服务中的连接监视器（预览版）时，可以选择**查看方式**：

* 连接监视器（默认值）–为选择的订阅、区域、时间戳、源和目标类型创建的所有连接监视器的列表
* 测试组–为选择的订阅、区域、时间戳、源和目标类型创建的所有测试组的列表。 这些测试组未在连接监视器上进行筛选
* 测试–为所选订阅、区域、时间戳、源和目标类型运行的所有测试的列表。 不会对连接监视器或测试组筛选这些测试。

可以将每个连接监视器展开为测试组，并将每个测试组展开到仪表板中正在其上运行的各个测试。 在下图中标记为 [1]。

你可以基于以下内容筛选此列表：

* 顶级筛选器-订阅、区域、时间戳源和目标类型。 在下图中标记为 [2]。
* 基于状态的筛选器-第二级筛选连接监视器/测试组/测试的状态。 在下图中标记为 [3]。
* 搜索字段–选择 "全部" 以执行通用搜索。 若要搜索任何特定实体，请使用下拉列表缩小搜索结果的范围。 在下图中标记为 [4]。

![筛选测试](./media/connection-monitor-2-preview/cm-view.png)

例如：

1. 若要查看跨所有连接监视器（预览版）的所有测试，其中源 IP = 10.192.64.56：
   1. 将视图更改为 "测试"
   2. 搜索字段 = 10.192.64.56
   3. 使用 "值" 旁边的下拉列表选择 "源"
2. 仅筛选所有连接监视器（预览版）中未通过的测试，源 IP = 10.192.64.56
   1. 将视图更改为 "测试"
   2. 从基于状态的筛选器中选择 "失败"。
   3. 搜索字段 = 10.192.64.56
   4. 使用 "值" 旁边的下拉列表选择 "源"
3. 仅筛选出在目标为 outlook.office365.com 的所有连接监视器（预览版）中未通过的测试
   1. 将视图更改为 "测试"
   2. 从基于状态的筛选器中选择 "失败"。
   3. 搜索字段 = outlook.office365.com
   4. 使用 "值" 旁边的下拉列表选择 "目标"

   ![已失败的测试](./media/connection-monitor-2-preview/tests-view.png)

若要查看失败的检查的趋势% 和 RTT：

1. 连接监视器
   1. 单击要详细调查的连接监视器
   2. 默认情况下，你将使用 "测试组" 查看监视数据

      ![查看度量值](./media/connection-monitor-2-preview/cm-drill-landing.png)

   3. 选择要详细调查的测试组

      ![指标（按 TG）](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

   4. 对于你在上一步中选择的测试组，你将看到针对检查失败的前5个失败的测试% 或 RTT 毫秒。 对于每个测试，你都将看到用于检查失败百分比和 RTT 毫秒的趋势线
   5. 从上述列表中选择一个测试，或选择另一个测试进行详细调查。
   6. 对于所选时间间隔，如果检查失败，则会看到 "阈值" 和 "实际" 值。 对于 RTT msec，你将看到阈值、平均值、最小值和最大值。

      ![RTT](./media/connection-monitor-2-preview/cm-drill-charts.png)

  7. 更改时间间隔以查看更多数据
  8. 可以在步骤 b 中更改视图，并选择按源、目标或测试配置查看。 然后根据失败的测试选择源，并调查前5个失败的测试。  例如：选择 "查看方式：源" 和 "目标"，调查在所选连接监视器中该组合之间运行的所有测试。

      ![RTT2](./media/connection-monitor-2-preview/cm-drill-select-source.png)

2. 测试组
   1. 单击要详细调查的测试组
   2. 默认情况下，你将通过 "源 + 目标 + 测试配置（测试）" 查看监视数据

      ![RTT3](./media/connection-monitor-2-preview/tg-drill.png)

   3. 选择要详细调查的测试
   4. 对于所选时间间隔，如果检查失败，则会看到 "阈值" 和 "实际" 值。 对于 RTT msec，你将看到阈值、平均值、最小值和最大值。 你还将看到特定于所选测试的已触发警报。
   5. 更改时间间隔以查看更多数据
   6. 可以在步骤 b 中更改视图，并选择按源、目标或测试配置查看。 然后选择一个实体来调查前5个失败的测试。  例如：选择 "查看方式：源" 和 "目标"，调查在所选连接监视器中该组合之间运行的所有测试。

3. 测试
   1. 单击要详细调查的 "源 + 目标 + 测试" 配置
   2. 对于所选时间间隔，如果检查失败，则会看到 "阈值" 和 "实际" 值。 对于 RTT msec，你将看到阈值、平均值、最小值和最大值。 你还将看到特定于所选测试的已触发警报。

      ![Test1](./media/connection-monitor-2-preview/test-drill.png)

   3. 你还可以单击 "拓扑" 以在任何时间点查看网络拓扑。

      ![Test2](./media/connection-monitor-2-preview/test-topo.png)

   4. 你可以单击 "Azure 网络" 的任何链接，以查看连接监视器标识的问题。 此功能目前不适用于本地网络。

       ![Test3](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics 中的日志查询

使用 Log Analytics 创建监视数据的自定义视图。 UI 中显示的所有数据都将从 Log Analytics 中填充。 你可以对存储库中的数据执行交互式分析，并将来自不同源（如代理运行状况和其他基于 Log Analytics 的应用程序）的数据关联起来。 还可以将数据导出到 Excel、Power BI 或可共享的链接。

#### <a name="metrics-in-azure-monitor"></a>Azure Monitor 中的指标

对于在连接监视器（预览版）体验之前创建的连接监视器，将提供所有4个度量值。 对于通过连接监视器（预览版）体验创建的连接监视器，数据仅可用于标记为 "（预览版）" 的指标。

资源类型-networkWatchers/connectionMonitors/

| 跃点数 | 指标显示名称 | 单位 | 聚合类型 | 说明 | Dimensions |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | 失败的探测百分比 | 百分比 | 平均值 | 失败的连接监视探测百分比 | 无维度 |
| AverageRoundtripMs | 平均往返时间（毫秒） | 毫秒 | 平均值 | 源和目标之间发送的连接监视探测的平均网络往返时间（毫秒） |             无维度 |
| ChecksFailedPercent （预览版） | % 检查失败（预览） | 百分比 | 平均值 | 测试失败的检查百分比 | * ConnectionMonitorResourceId <br> * SourceAddress <br> * 未用 <br> * SourceResourceId <br> * SourceType <br> * 协议 <br> * DestinationAddress <br> * DestinationName <br> * DestinationResourceId <br> * DestinationType <br> * DestinationPort <br> * TestGroupName <br> * TestConfigurationName <br> * 区域 |
| RoundTripTimeMs （预览版） | 往返时间（毫秒）（预览版） | 毫秒 | 平均值 | 在源和目标之间发送的检查的往返时间（毫秒）。 此值不是平均值 | * ConnectionMonitorResourceId <br> * SourceAddress <br> * 未用 <br> * SourceResourceId <br> * SourceType <br> * 协议 <br> * DestinationAddress <br> * DestinationName <br> * DestinationResourceId <br> * DestinationType <br> * DestinationPort <br> * TestGroupName <br> * TestConfigurationName <br> * 区域 |

 ![监视指标](./media/connection-monitor-2-preview/monitor-metrics.png)

#### <a name="metric-alerts-in-azure-monitor"></a>Azure Monitor 中的指标警报

创建警报：

1. 选择使用连接监视器创建的连接监视器资源（预览版）
2. 确保 "指标" 显示为上一步中所选资源的信号类型
3. 在 "添加条件" 中，选择 "信号名称" 作为 ChecksFailedPercent （预览版）或 "RoundTripTimeMs （预览版）"，并选择 "信号" 作为指标。 例如：选择 ChecksFailedPercent （预览版）
4. 将列出所有适用于每个度量值的维度。  选择维度名称和维度值。 例如：选择源地址并提供在步骤1中选择的连接监视器资源中所涉及的任何源的 IP 地址
5. 在 "警报逻辑" 中，选择：
   1. 条件类型-静态
   2. 条件和阈值
   3. 聚合粒度和评估频率–连接监视器（预览）每隔1分钟更新一次数据。
6.  在 "操作" 中，选择操作组
7. 提供警报详细信息
8. 创建警报规则

   ![警报](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="step-5-diagnose-issues-in-your-network"></a>步骤5：诊断网络中的问题

连接监视器将帮助你诊断与网络中的连接监视器资源和网络相关的问题。 你在步骤1中安装的 Log Analytics 代理将检测混合网络中的问题，网络观察程序扩展将检测到 Azure 中的问题。  混合网络中的问题将显示在 "诊断" 页中，Azure 网络中的问题将显示在网络拓扑中。

对于具有作为源的本地 Vm 的网络，我们检测到：

* 请求超时
* 终结点未通过 DNS 解析-临时或持久。 URL 无效。
* 找不到主机。
* 源无法连接到目标。 无法通过 ICMP 访问目标。
* 与证书相关的问题-对代理进行身份验证所需的客户端证书，证书重定位列表不可访问，终结点的主机名与证书的使用者或使用者备用名称不匹配，源的本地计算机受信任的证书颁发机构存储中缺少根证书，SSL 证书已过期/无效/已吊销，不兼容

对于 Azure Vm 为源的网络，我们检测到：

* 代理问题–代理已停止，无法进行 DNS 解析，没有在目标端口上侦听的应用程序/侦听器，无法打开套接字
* VM 状态问题-启动、停止、停止、解除分配、解除分配、重新启动、未分配
* 缺少 ARP 表项
* 由于本地防火墙问题，流量被阻止，NSG 规则
* VNET 网关–缺少路由，两个网关之间的隧道已断开连接或缺失，或者隧道找不到第二个网关，未找到对等信息
* MS Edge 缺少路由。
* 由于系统路由或 UDR，流量已停止
* 未在网关连接上启用 BGP
* 负载均衡器的 DIP 探测
