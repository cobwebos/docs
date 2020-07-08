---
title: 连接监视器（预览） |Microsoft Docs
description: 了解如何使用连接监视器（预览版）监视分布式环境中的网络通信。
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 52d33e7292ebe7b27eede2b89aa605780f826392
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737608"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>使用连接监视器（预览版）进行网络连接监视

连接监视器（预览版）在 Azure 网络观察程序中提供统一的端到端连接监视。 连接监视器（预览版）功能支持混合部署和 Azure 云部署。 网络观察程序提供的工具可用于监视、诊断和查看针对 Azure 部署的与连接相关的指标。

下面是连接监视器（预览版）的一些用例：

- 前端 Web 服务器 VM 与多层应用程序中的数据库服务器 VM 进行通信。 你希望检查两个 VM 之间的网络连接。
- 希望美国东部区域中的 Vm 对美国中部区域中的 Vm 进行 ping 操作，并且需要比较跨区域网络延迟。
- 你有多个位于华盛顿州西雅图的本地办公场所，在阿什本，弗吉尼亚州。 Office 网站连接到 Office 365 Url。 对于你的 Office 365 Url 用户，比较西雅图与阿什本之间的延迟。
- 混合应用程序需要连接到 Azure 存储终结点。 本地站点和 Azure 应用程序连接到相同的 Azure 存储终结点。 希望比较本地站点的延迟与 Azure 应用程序的延迟。
- 希望检查本地设置与托管云应用程序的 Azure VM 之间的连接。

在其预览阶段，连接监视器组合了两个功能的优点：网络观察程序[连接监视器](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint)功能和网络性能监视器 (NPM) [服务连接监视器](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)功能。

下面是连接监视器（预览版）的一些优点：

* 适用于 Azure 和混合监视需求的直观一致的体验
* 跨区域且跨工作区的连接监视
* 更高的探测频率，更好地了解网络性能
* 为混合部署提供更快的警报
* 支持基于 HTTP、TCP 和 ICMP 的连接检查 
* 同时适用于 Azure 和非 Azure 测试设置的指标和 Log Analytics 支持

![显示连接监视器如何与 Azure VM、非 Azure 主机、终结点和数据存储位置进行交互的示意图](./media/connection-monitor-2-preview/hero-graphic.png)

若要开始使用连接监视器（预览版）进行监视，请执行以下步骤： 

1. 安装监视代理。
1. 在订阅上启用网络观察程序。
1. 创建连接监视器。
1. 设置数据分析和警报。
1. 诊断网络中的问题。

以下部分提供了更多有关这些步骤的详细信息。

## <a name="install-monitoring-agents"></a>安装监视代理

连接监视器依赖轻型的可执行文件来运行连接性检查。  它同时支持来自 Azure 环境和本地环境的连接性检查。 使用的可执行文件取决于 VM 是托管在 Azure 上还是托管在本地。

### <a name="agents-for-azure-virtual-machines"></a>Azure 虚拟机代理

若要使连接监视器将 Azure VM 识别为监视源，请在其上安装网络观察程序代理虚拟机扩展。 此扩展也称为*网络观察程序扩展*。 Azure 虚拟机需要该扩展来触发端对端监控和其他高级功能。 

可以在[创建 VM](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm) 时安装网络观察程序扩展。 还可以为 [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) 和 [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows) 分别安装、配置网络观察程序扩展并对其进行故障排除。

用于网络安全组 (NSG) 或防火墙的规则可以阻止源和目标之间的通信。 连接监视器检测到此问题并将其显示为拓扑中的诊断消息。 若要启用连接监视，请确保 NSG 和防火墙规则允许在源和目标之间使用 TCP 或 ICMP 上的数据包。

### <a name="agents-for-on-premises-machines"></a>本地计算机的代理

若要使连接监视器将本地计算机识别为要监视的源，请在计算机上安装 Log Analytics 代理。 然后启用网络性能监视器解决方案。 这些代理是链接到 Log Analytics 工作区的，因此，需要先设置工作区 ID 和主密钥，然后代理才能开始进行监视。

若要为 Windows 计算机安装 Log Analytics 代理，请参阅[适用于 Windows 的 Azure Monitor 虚拟机扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)。

如果路径包括防火墙或网络虚拟设备 (NVA)，请确保可访问目标。

## <a name="enable-network-watcher-on-your-subscription"></a>在订阅上启用网络观察程序

使用网络观察程序启用具有虚拟网络的所有订阅。 在订阅中创建虚拟网络时，虚拟网络的区域和订阅中会自动启用网络观察程序。 这种自动启用不会影响资源，也不会产生费用。 确保未在订阅上显式禁用网络观察程序。 

有关详细信息，请参阅[启用网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)。

## <a name="create-a-connection-monitor"></a>创建连接监视器 

连接监视器会定期监视通信。 它会通知你有关可访问性和延迟的更改情况。 还可以检查源代理和目标终结点之间当前的和历史的网络拓扑。

源可以是 Azure VM，源也可以是具有已安装的监视代理的本地计算机。 目标终结点可以是 Office 365 URL、Dynamics 365 URL、自定义 URL、Azure VM 资源 ID、IPv4、IPv6、FQDN 或任何域名。

### <a name="access-connection-monitor-preview"></a>访问连接监视器（预览版）

1. 在 Azure 门户主页上，转到“网络观察程序”。
1. 在左侧的“监视”部分，选择“连接监视器（预览版）” 。
1. 你会看到在连接监视器（预览版）中创建的所有连接监视器。 若要查看在经典连接监视器体验中创建的连接监视器，请转到“连接监视器”选项卡。

    ![显示连接监视器（预览版）中创建的连接监视器的屏幕截图](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>创建连接监视器

在连接监视器（预览版）中创建的连接监视器中，可以将本地计算机和 Azure VM 添加为源。 这些连接监视器还可以监视与终结点的连接。 终结点可以位于 Azure 上，也可以位于任何其他 URL 或 IP 上。

连接监视器（预览版）包含以下实体：

* **连接监视器资源** – 特定于区域的 Azure 资源。 以下所有实体都是连接监视器资源的属性。
* **终结点** – 参与连接检查的源或目标。 终结点的示例包括 Azure VM、本地代理、URL 和 IP。
* **测试配置** – 针对测试的特定于协议的配置。 根据选定协议，可以定义端口、阈值、测试频率和其他参数。
* **测试组** – 包含源终结点、目标终结点和测试配置的组。 连接监视器可包含多个测试组。
* **测试** – 将源终结点、目标终结点和测试配置组合在一起。 测试是可用于监视数据的最精细级别。 监视数据包括检查失败的百分比和往返时间 (RTT)。

 ![显示连接监视器的示意图（该图定义了测试组和测试之间的关系）](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>从 Azure 门户创建连接监视器

若要从 Azure 门户中创建连接监视器，请执行以下步骤：

1. 在“连接监视器（预览版）”仪表板的左上角，选择“创建” 。
1. 在“基本信息”选项卡上，为连接监视器输入信息：
   * **连接监视器名称**–添加连接监视器的名称。 使用针对 Azure 资源的标准命名规则。
   * **订阅**–为连接监视器选择订阅。
   * **区域**–为连接监视器选择一个区域。 只能选择在此区域创建的源 VM。
   * **工作区配置** - 工作区包含监视数据。 可以使用自定义工作区或默认工作区。 
       * 若要使用默认工作区，请选择此复选框。 
       * 若要选择自定义工作区，请清除此复选框。 然后为自定义工作区选择订阅和区域。 
1. 在选项卡底部，选择“下一步：测试组”。

   ![显示连接监视器中“基本信息”选项卡的屏幕截图](./media/connection-monitor-2-preview/create-cm-basics.png)

1. 在“测试组”选项卡上，选择“+ 测试组” 。 若要设置测试组，请参阅[在连接监视器中创建测试组](#create-test-groups-in-a-connection-monitor)。 
1. 在选项卡底部，选择“下一步：查看 + 创建”可查看连接监视器。

   ![显示“测试组”选项卡和窗格（可以在其中添加测试组详细信息）的屏幕截图](./media/connection-monitor-2-preview/create-tg.png)

1. 先在“查看 + 创建”选项卡上查看基本信息和测试组，再创建连接监视器。 如果需要编辑连接监视器：
   * 若要编辑基本详细信息，请选择铅笔图标。
   * 若要编辑测试组，请将其选中。

   > [!NOTE] 
   > “查看 + 创建”选项卡显示连接监视器预览阶段每月的成本。 目前，“当前成本”列显示无需付费。 连接监视器公开上市之后，此列才会显示每月费用。 
   > 
   > 在连接监视器预览阶段，仍需支付 Log Analytics 引入费用。

1. 准备好创建连接监视器后，在“查看 + 创建”选项卡的底部，选择“创建” 。

   ![连接监视器的屏幕截图（显示“查看 + 创建”选项卡）](./media/connection-monitor-2-preview/review-create-cm.png)

连接监视器（预览版）在后台创建连接监视器资源。

#### <a name="create-a-connection-monitor-by-using-armclient"></a>使用 ARMClient 创建连接监视器

使用以下代码通过 ARMClient 创建连接监视器。

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

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

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

下面是部署命令：
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>在连接监视器中创建测试组

每个连接监视器中的测试组都包括按网络参数进行测试的源和目标。 它们测试的是失败检查的百分比和基于测试配置的 RTT。

在 Azure 门户中，若要在连接监视器中创建测试组，请为以下字段指定值：

* **禁用测试组**–可以选择此字段，以对测试组指定的所有源和目标禁用监视。 默认情况下，此选择处于未选中状态。
* **名称**–命名你的测试组。
* **源**–如果已在 Azure vm 和本地计算机上安装代理，则可以将其指定为源。 若要为源安装代理，请参阅[安装监视代理](#install-monitoring-agents)。
   * 若要选择 Azure 代理，请选择“Azure 代理”选项卡。此处只能看到绑定到创建连接监视器时指定的区域的 VM。 默认情况下，会将 VM 分组到它们所属的订阅中。 已折叠这些组。 
   
       可以从订阅级别向下钻取到层次结构中的其他级别：

      订阅 > 资源组 > VNET > 子网 > 具有代理的 VM    

      还可以更改“分组依据”字段的值，从任何其他级别启动树。 例如，如果按虚拟网络分组，则会看到层次结构（VNET > 子网 > 具有代理的 VM）中具有代理的 VM  。

      ![连接监视器的屏幕截图（显示“添加源”面板和“Azure 代理”选项卡）](./media/connection-monitor-2-preview/add-azure-sources.png)

   * 若要选择本地代理，请选择 "**非 Azure 代理**" 选项卡。默认情况下，代理按区域分组到工作区中。 所有这些工作区都配置了网络性能监视器解决方案。 
   
       如果需要将网络性能监视器添加到工作区，请通过 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)获取。 有关如何添加网络性能监视器的信息，请参阅 [Azure Monitor 中的监视解决方案](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)。 
   
       在“创建连接监视器”视图的“基本信息”选项卡上，已选择默认区域 。 如果更改了区域，则可以在新区域的工作区中选择代理。 还可以更改“分组依据”字段的值，以按子网进行分组。

      ![连接监视器的屏幕截图（显示“添加源”面板和“非 Azure 代理”选项卡）](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * 若要查看所选的 Azure 代理和非 Azure 代理，请转到“查看”选项卡。

      ![连接监视器的屏幕截图（显示“添加源”面板和“查看”选项卡）](./media/connection-monitor-2-preview/review-sources.png)

   * 设置好源之后，在“添加源”面板底部选择“完成” 。

* **目标**–可以通过将其指定为目标来监视 Azure vm 或任何终结点（公共 IP、URL 或 FQDN）的连接。 单个测试组中可以添加 Azure VM、Office 365 URL、Dynamics 365 URL 和自定义终结点。

    * 若要选择 Azure VM 作为目标，请选择“Azure VM”选项卡。默认情况下，Azure VM 会分组到同一区域中的订阅层次结构，该区域是在“基本信息”选项卡上的“创建连接监视器”视图中选择的 。可以更改区域，并从新选择的区域中选择“Azure VM”。 然后可以从订阅级别向下钻取到层次结构中的其他级别，比如 Azure 代理级别。

       ![“添加目标”窗格的屏幕截图（显示“Azure VM”选项卡）](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![“添加目标”窗格的屏幕截图（显示订阅级别）](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * 若要选择终结点作为目标，请选择“终结点”选项卡。终结点列表包含 Office 365 测试 URL 和 Dynamics 365 测试 URL（按名称分组）。 除了这些终结点外，还可以选择在同一连接监视器中其他测试组中创建的终结点。 
    
        若要添加新的终结点，请在右上角选择“+ 终结点”。 然后提供终结点名称和 URL、IP 或 FQDN。

       ![显示在连接监视器中将终结点作为目标添加到何处的屏幕截图](./media/connection-monitor-2-preview/add-endpoints.png)

    * 若要查看所选的 Azure VM 和终结点，请选择“查看”选项卡。
    * 选择完目标后，选择“完成”。

* **测试配置**–您可以关联测试组中的测试配置。 Azure 门户仅允许每个测试组有一个测试配置，但可以使用 ARMClient 添加更多测试。

    * **名称**–命名测试配置。
    * **协议**–选择 "TCP"、"ICMP" 或 "HTTP"。 若要将 HTTP 更改为 HTTPS，请选择“HTTP”作为协议，并选择“443”作为端口 。
        * "**创建网络测试配置**"-仅当在 "**协议**" 字段中选择 " **HTTP** " 时才会显示此复选框。 选择此框以创建另一个测试配置，该配置使用你在配置中其他地方指定的相同源和目标。 新创建的测试配置名为 `<the name of your test configuration>_networkTestConfig`。
        * **禁用 traceroute** –此字段适用于其协议为 TCP 或 ICMP 的测试组。 选择此框以阻止源发现拓扑和逐跳 RTT。
    * **目标端口**–您可以使用所选的目标端口自定义此字段。
    * **测试频率**–使用此字段可选择源对指定的协议和端口 ping 目标的频率。 可以选择 30 秒、1 分钟、5 分钟、15 分钟或 30 分钟。 源将根据所选的值来测试与目标的连接。  例如，如果选择 30 秒，则源将在 30 秒的时间段内至少检查一次与目标的连接。
    * **成功阈值**–你可以在以下网络参数上设置阈值：
       * **检查失败**–设置在源使用指定的条件检查与目标的连接时可能失败的检查的百分比。 对于 TCP 或 ICMP 协议，检查失败的百分比可能会与数据包丢失的百分比相同。 对于 HTTP 协议，此字段表示未接收到响应的 HTTP 请求的百分比。
       * **往返时间**–设置通过测试配置连接到目标所需的源时间长度（以毫秒为单位）。
    
       ![显示在连接监视器中设置测试配置的位置的屏幕截图](./media/connection-monitor-2-preview/add-test-config.png)

添加到测试组中的所有源、目标和测试配置将细分为单独的测试。 下面是如何分解源和目标的示例：

* 测试组：TG1
* 源：3 (A, B, C)
* 目标：2 (D, E)
* 测试配置：2 (Config 1, Config 2)
* 已创建的测试总数：12

| 测试编号 | Source | 目标 | 测试配置 |
| --- | --- | --- | --- |
| 1 | A | D | Config 1 |
| 2 | A | D | Config 2 |
| 3 | A | E | Config 1 |
| 4 | A | E | Config 2 |
| 5 | B | D | Config 1 |
| 6 | B | D | Config 2 |
| 7 | B | E | Config 1 |
| 8 | B | E | Config 2 |
| 9 | C | D | Config 1 |
| 10 | C | D | Config 2 |
| 11 | C | E | Config 1 |
| 12 | C | E | Config 2 |

### <a name="scale-limits"></a>规模限制

连接监视器具有以下规模限制：

* 每个区域每个订阅的最大连接监视器数：100
* 每个连接监视器的最大测试组：20 个
* 每个连接监视器的最大源和目标：100
* 每个连接监视器的最大测试组： 
    * 20 个通过 ARMClient
    * 2 个通过 Azure 门户

## <a name="analyze-monitoring-data-and-set-alerts"></a>分析监视数据并设置警报

创建连接监视器后，源会根据测试配置来检查与目标的连接。

### <a name="checks-in-a-test"></a>签入测试

根据你在测试配置中所选的协议，连接监视器（预览版）对源-目标对运行一系列检查。 根据所选测试频率运行检查。

如果使用 HTTP，则服务会计算返回了响应代码的 HTTP 响应数。 结果确定检查失败的百分比。 为了计算 RTT，服务会测量 HTTP 调用和响应之间的时间。

如果使用 TCP 或 ICMP，则服务会计算数据包丢失百分比，以确定检查失败的百分比。 若要计算 RTT，则服务会测量为发送的数据包接收确认 (ACK) 所花费的时间。 如果已为网络测试启用了跟踪路由数据，则可以查看本地网络的逐跳丢失和延迟。

### <a name="states-of-a-test"></a>测试的状态

根据检查返回的数据，测试可以具有以下状态：

* **Pass** –失败检查和 RTT 百分比的实际值在指定的阈值内。
* **Fail** –失败检查或 RTT 百分比的实际值超出了指定的阈值。 如果未指定阈值，则当检查失败的百分比为 100 时，测试进入失败状态。
* **警告**–未指定未通过检查的百分比的标准。 如果没有指定条件，连接监视器（预览版）会自动分配一个阈值。 超过此阈值时，测试状态将更改为“警告”。

### <a name="data-collection-analysis-and-alerts"></a>数据收集、分析和警报

连接监视器（预览版）收集的数据存储在 Log Analytics 工作区中。 创建连接监视器时设置此工作区。 

监视数据也可用于 Azure Monitor 指标中。 可以根据需要使用 Log Analytics 保留监视数据。 默认情况下，Azure Monitor 仅存储 30 天的指标。 

可以[根据数据设置基于指标的警报](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)。

#### <a name="monitoring-dashboards"></a>监视仪表板

在监视仪表板上，可以查看可以访问的订阅、区域、时间戳、源和目标类型的连接监视器列表。

从网络观察程序中转到连接监视器（预览版）时，可以通过以下方式查看数据：

* **连接监视器**–为你的订阅、区域、时间戳、源和目标类型创建的所有连接监视器的列表。 此视图为默认视图。
* **测试组**–为你的订阅、区域、时间戳、源和目标类型创建的所有测试组的列表。 这些测试组不由连接监视器进行筛选。
* **测试**–为你的订阅、区域、时间戳、源和目标类型运行的所有测试的列表。 这些测试不由连接监视器或测试组进行筛选。

在下图中，这三个数据视图由箭头 1 指示。

在仪表板上，可以展开每个连接监视器以查看其测试组。 然后，可以展开每个测试组，以查看在其中运行的测试。 

你可以基于以下内容筛选列表：

* **顶级筛选器**–选择订阅、区域、时间戳源和目标类型。 请查看下列图像中的框 2。
* **基于状态的筛选**器–按连接监视器、测试组或测试的状态进行筛选。 请查看下列图像中的箭头 3。
* **自定义筛选器**–选择 "**全选**" 以执行通用搜索。 若要按特定实体进行搜索，请从下拉列表中选择。 请查看下列图像中的箭头 4。

![显示如何筛选连接监视器（预览版）中的连接监视器、测试组和测试的视图的屏幕截图](./media/connection-monitor-2-preview/cm-view.png)

例如，若要查看其中源 IP 是 10.192.64.56 的连接监视器（预览版）中的所有测试，请执行以下操作：
1. 将视图更改为“测试”。
1. 在搜索字段中键入“10.192.64.56”
1. 在下拉列表中选择“源”。

仅显示其中源 IP 是 10.192.64.56 的连接监视器（预览版）中失败的测试：
1. 将视图更改为“测试”。
1. 对于基于状态的筛选器，请选择“失败”。
1. 在搜索字段中键入“10.192.64.56”
1. 在下拉列表中选择“源”。

仅显示其中目标为 outlook.office365.com 连接监视器（预览版）中失败的测试：
1. 将视图更改为“测试”。
1. 对于基于状态的筛选器，请选择“失败”。
1. 在搜索字段中，输入“outlook.office365.com”
1. 在下拉列表中选择“目标”。

   ![显示筛选为仅显示 Outlook.Office365.com 目标测试失败的视图的屏幕截图](./media/connection-monitor-2-preview/tests-view.png)

若要查看 RTT 的趋势和连接监视器检查失败的百分比，请执行以下操作：
1. 选择要调查的连接监视器。 默认情况下，监视数据按测试组进行组织。

   ![显示连接监视器的指标的屏幕截图（按测试组显示）](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. 选择要调查的测试组。

   ![显示在何处选择测试组的屏幕截图](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    根据 RTT 或检查失败百分比，可以查看测试组的前五个失败的测试。 每个测试都可以看到 RTT 以及检查失败百分比的趋势线。
1. 从列表中选择一个测试，或选择另一个要调查的测试。 在时间间隔和检查失败百分比中可以查看阈值和实际值。 在 RTT 中可以查看阈值、平均值、最小值和最大值。

   ![显示 RTT 测试结果和检查失败百分比测试结果的屏幕截图](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. 更改时间间隔以查看更多数据。
1. 更改视图以查看源、目标或测试配置。 
1. 选择基于失败的测试的源，并调查前五个失败的测试。 例如，选择“按 > 源查看”和“按 > 目的地查看”，从而调查连接监视器中的相关测试   。

   ![显示前五个失败测试的性能指标的屏幕截图](./media/connection-monitor-2-preview/cm-drill-select-source.png)

若要查看 RTT 的趋势和测试组检查失败的百分比，请执行以下操作：

1. 选择要调查的测试组。 

    默认情况下，监视数据按源、目标和测试配置（测试）排列。 稍后，你可以将该视图从测试组更改为源、目标或测试配置。 然后选择一个实体来调查前五个失败的测试。 例如，将视图更改为源和目标，从而在所选连接监视器中调查相关的测试。
1. 选择要调查的测试。

   ![显示在何处选择测试的屏幕截图](./media/connection-monitor-2-preview/tg-drill.png)

    你可以在时间间隔和检查失败百分比中查看阈值和实际值。 在 RTT 中可以查看阈值、平均值、最小值和最大值。 还可以查看为所选测试触发的警报。
1. 更改时间间隔以查看更多数据。

若要查看 RTT 的趋势和测试检查失败的百分比，请执行以下操作：
1. 选择要调查的源、目标和测试配置。

    你可以在时间间隔和检查失败百分比中查看阈值和实际值。 在 RTT 中可以查看阈值、平均值、最小值和最大值。 还可以查看为所选测试触发的警报。

   ![显示测试指标的屏幕截图](./media/connection-monitor-2-preview/test-drill.png)

1. 若要查看网络拓扑，请选择“拓扑”。

   ![显示“网络拓扑”选项卡的屏幕截图](./media/connection-monitor-2-preview/test-topo.png)

1. 若要查看标识的问题，请在拓扑中选择路径中的任何跃点。 （这些跃点都是 Azure 资源。）此功能当前不适用于本地网络。

   ![显示“拓扑”选项卡上所选跃点链接的屏幕截图](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Log Analytics 中的日志查询

使用 Log Analytics 创建监视数据的自定义视图。 UI 显示的所有数据都来自 Log Analytics。 可以以交互方式分析存储库中的数据。 关联代理运行状况中的数据或基于 Log Analytics 的其他解决方案。 将数据导出到 Excel 或 Power BI，或创建可共享的链接。

#### <a name="metrics-in-azure-monitor"></a>Azure Monitor 中的指标

在连接监视器（预览版）体验推出之前创建的连接监视器中，所有四个指标都可用：探测失败、AverageRoundtripMs、ChecksFailedPercent（预览版）和 RoundTripTimeMs（预览版）。 在连接监视器（预览版）体验中创建的连接监视器中，数据仅可用于标记为（预览版）”的指标。

![显示连接监视器（预览版）中指标的屏幕截图](./media/connection-monitor-2-preview/monitor-metrics.png)

使用指标时，将资源类型设置为“Microsoft.Network/networkWatchers/connectionMonitors”

| 指标 | Display name | 计价单位 | 聚合类型 | 说明 | 维度 |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | 失败的探测百分比 | 百分比 | 平均值 | 连接监视探测的失败百分比。 | 无维度 |
| AverageRoundtripMs | 平均值往返时间（毫秒） | 毫秒 | 平均值 | 源和目标之间发送的连接监视探测的平均网络 RTT。 |             无维度 |
| ChecksFailedPercent（预览版） | 失败检查（预览版） | 百分比 | 平均值 | 测试的检查失败百分比。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>协议 <br>目的地地址 <br>DestinationName <br>DestinationResourceId <br>目标类型 <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>区域 |
| RoundTripTimeMs（预览版） | 往返时间（毫秒）（预览版） | 毫秒 | 平均值 | 在源和目标之间发送的用于检查的 RTT。 此值不是平均值。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>协议 <br>目的地地址 <br>DestinationName <br>DestinationResourceId <br>目标类型 <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>区域 |

#### <a name="metric-alerts-in-azure-monitor"></a>Azure Monitor 中的指标警报

若要在 Azure Monitor 中创建警报，请执行以下操作：

1. 选择在连接监视器（预览版）中创建的连接监视器资源。
1. 确保“指标”显示为连接监视器的信号类型。
1. 在“信号名称”的“添加条件”中，选择“ChecksFailedPercent（预览版）”或“RoundTripTimeMs（预览版）”   。
1. “信号类型”选择“指标” 。 例如，选择“ChecksFailedPercent（预览版）”。
1. 将列出指标的所有维度。 选择维度名称和维度值。 例如，选择“源地址”，然后在连接监视器中输入任何源的 IP 地址。
1. 在“警报逻辑”中填写以下详细信息：
   * 条件类型：静态。
   * 条件和阈值 。
   * 计算的聚合粒度和计算频率：连接监视器（预览版）每分钟更新一次数据。
1. 在“操作”中选择操作组。
1. 提供警报详细信息。
1. 创建警报规则。

   ![显示 Azure Monitor 中创建规则区域的屏幕截图；突出显示“源地址”和“源终结点名称”](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>诊断网络中的问题

连接监视器（预览版）可以帮助你诊断连接监视器中的问题和网络中的问题。 混合网络中的问题通过你之前安装的 Log Analytics 代理进行检测。 Azure 中的问题由网络观察程序扩展进行检测。 

可以在网络拓扑的 Azure 网络中查看问题。

对于源为本地 VM 的网络，可以检测到以下问题：

* 请求已超时。
* 终结点未通过 DNS 解析-临时或持久。 URL 无效。
* 无法找到主机。
* 源无法连接到目标。 无法通过 ICMP 访问目标。
* 与证书相关的问题： 
    * 验证代理所需的客户端证书。 
    * 无法访问证书重定位列表。 
    * 终结点的主机名与证书的使用者或使用者备用名称不匹配。 
    * 源的本地计算机受信任的证书颁发机构存储中缺少根证书。 
    * SSL 证书已过期、无效、吊销或不兼容。

针对其源为Azure VM 的网络，可以检测到以下问题：

* 代理问题：
    * 代理已停止。
    * DNS 解析失败。
    * 无应用程序或在目标端口上侦听的侦听器。
    * 无法打开套接字。
* VM 状态问题： 
    * 正在启动
    * 正在停止
    * 已停止
    * 正在解除分配
    * 已解除分配
    * 正在重新启动
    * 未分配
* 缺少 ARP 表条目。
* 由于本地防火墙问题或 NSG 规则，流量遭到阻止。
* 虚拟网络网关问题： 
    * 缺少路由。
    * 两个网关之间的隧道已断开连接或处于缺失状态。
    * 第二个网关没有让隧道发现。
    * 无法找到对等信息。
* Microsoft Edge 中缺少路由。
* 由于系统路由或 UDR 的原因，流量已停止。
* 网关连接上未启用 BGP。
* 已关闭负载均衡器上的 DIP 探测。
