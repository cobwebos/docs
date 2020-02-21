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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 8f3a6f002fbebe215699c9b97a6dce63177c446f
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77506247"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>用连接监视器进行网络连接监视（预览版）

连接监视器（预览版）在 Azure 网络观察程序中提供统一的端到端连接监视。 连接监视器（预览）功能支持混合部署和 Azure 云部署。 网络观察程序提供的工具可用于监视、诊断和查看 Azure 部署的与连接相关的指标。

下面是连接监视器（预览版）的一些用例：

- 前端 web 服务器 VM 与多层应用程序中的数据库服务器 VM 进行通信。 需要检查两个 Vm 之间的网络连接。
- 希望美国东部区域中的 Vm 对美国中部区域中的 Vm 进行 ping 操作，并且需要比较跨区域网络延迟。
- 你有多个位于华盛顿州西雅图的本地办公场所，在阿什本，弗吉尼亚州。 Office 网站连接到 Office 365 Url。 对于你的 Office 365 Url 用户，比较西雅图与阿什本之间的延迟。
- 混合应用程序需要连接到 Azure 存储终结点。 本地站点和 Azure 应用程序连接到相同的 Azure 存储终结点。 需要将本地站点的延迟与 Azure 应用程序的延迟进行比较。
- 需要检查本地设置与托管云应用程序的 Azure Vm 之间的连接。

在其预览阶段，连接监视器组合了两个功能的最佳功能：网络观察程序[连接监视器](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint)功能和网络性能监视器（NPM）[服务连接监视器](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)功能。

下面是连接监视器（预览版）的一些优点：

* 适用于 Azure 和混合监视需求的统一、直观的体验
* 跨区域跨工作区连接监视
* 更高的探测频率，更好地了解网络性能
* 混合部署的更快警报
* 支持基于 HTTP、TCP 和 ICMP 的连接检查 
* 针对 Azure 和非 Azure 测试设置的指标和 Log Analytics 支持

![显示连接监视器如何与 Azure Vm、非 Azure 主机、终结点和数据存储位置交互的关系图](./media/connection-monitor-2-preview/hero-graphic.png)

若要开始使用连接监视器（预览版）进行监视，请执行以下步骤： 

1. 安装监视代理。
1. 在订阅上启用网络观察程序。
1. 创建连接监视器。
1. 设置数据分析和警报。
1. 诊断网络中的问题。

以下各节提供了有关这些步骤的详细信息。

## <a name="install-monitoring-agents"></a>安装监视代理

连接监视器依赖轻型可执行文件来运行连接性检查。  它支持来自 Azure 环境和本地环境的连接检查。 你使用的可执行文件取决于你的 VM 是托管在 Azure 上还是在本地。

### <a name="agents-for-azure-virtual-machines"></a>Azure 虚拟机代理

要使连接监视器将 Azure Vm 识别为监视源，请在其上安装网络观察程序代理虚拟机扩展。 此扩展也称为*网络观察程序扩展*。 Azure 虚拟机需要扩展来触发端到端的监视和其他高级功能。 

你可以在[创建 VM](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)时安装网络观察程序扩展。 你还可以对[Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux)和[Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)的网络观察程序扩展单独进行安装、配置和故障排除。

网络安全组（NSG）或防火墙的规则可以阻止源和目标之间的通信。 连接监视器检测到此问题并将其显示为拓扑中的诊断消息。 若要启用连接监视，请确保 NSG 和防火墙规则允许源和目标之间的 TCP 或 ICMP 上的数据包。

### <a name="agents-for-on-premises-machines"></a>本地计算机的代理

若要使连接监视器将本地计算机识别为要监视的源，请在计算机上安装 Log Analytics 代理。 然后启用网络性能监视器解决方案。 这些代理链接到 Log Analytics 工作区，因此，需要先设置工作区 ID 和主密钥，然后才能开始监视。

若要为 Windows 计算机安装 Log Analytics 代理，请参阅[适用于 windows 的 Azure Monitor 虚拟机扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)。

如果路径包括防火墙或网络虚拟设备（Nva），请确保目标是可访问的。

## <a name="enable-network-watcher-on-your-subscription"></a>在订阅上启用网络观察程序

使用网络观察程序启用虚拟网络的所有订阅。 在订阅中创建虚拟网络时，将在虚拟网络的区域和订阅中自动启用网络观察程序。 这种自动启用不会影响资源，也不会产生费用。 确保未在订阅上显式禁用网络观察程序。 

有关详细信息，请参阅[启用网络观察](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)程序。

## <a name="create-a-connection-monitor"></a>创建连接监视器 

连接监视器定期监视通信。 它通知你有关可访问性和延迟的更改。 你还可以在源代理和目标终结点之间检查当前和历史网络拓扑。

源可以是 Azure Vm，也可以是具有已安装的监视代理的本地计算机。 目标终结点可以是 Office 365 Url，Dynamics 365 Url，自定义 Url，Azure VM 资源 Id，IPv4，IPv6，FQDN 或任何域名。

### <a name="access-connection-monitor-preview"></a>访问连接监视器（预览）

1. 在 Azure 门户主页上，请参阅**网络观察**程序。
1. 在左侧的 "**监视**" 部分中，选择 "**连接监视器（预览）** "。
1. 你会看到在连接监视器（预览版）中创建的所有连接监视器。 若要查看在经典连接监视器中创建的连接监视器，请访问**连接监视器**选项卡。

    ![显示连接监视器（预览版）中创建的连接监视器的屏幕截图](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>创建连接监视器

在连接监视器（预览版）中创建的连接监视器中，可以将本地计算机和 Azure Vm 添加为源。 这些连接监视器还可以监视与终结点的连接。 终结点可以位于 Azure 上，也可以是任何其他 URL 或 IP。

连接监视器（预览版）包含以下实体：

* **连接监视器资源**–特定于区域的 Azure 资源。 以下所有实体都是连接监视器资源的属性。
* **Endpoint** –参与连接检查的源或目标。 终结点的示例包括 Azure Vm、本地代理、Url 和 Ip。
* **测试配置**–测试的协议特定配置。 根据所选的协议，可以定义端口、阈值、测试频率和其他参数。
* **测试组**–包含源终结点、目标终结点和测试配置的组。 一个连接监视器可以包含多个测试组。
* **测试**–源终结点、目标终结点和测试配置的组合。 测试是提供监视数据的最精细的级别。 监视数据包括失败检查的百分比和往返时间（RTT）。

 ![显示连接监视器的关系图，定义测试组和测试之间的关系](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>从 Azure 门户创建连接监视器

若要从 Azure 门户创建连接监视器，请执行以下步骤：

1. 在**连接监视器（预览）** 仪表板上的左上角，选择 "**创建**"。
1. 在 "**基本**信息" 选项卡上，为连接监视器输入信息：
   * **连接监视器名称**–添加连接监视器的名称。 使用 Azure 资源的标准命名规则。
   * **订阅**–为连接监视器选择订阅。
   * **区域**–为连接监视器选择一个区域。 只能选择在此区域创建的源 Vm。
   * **工作区配置**-你的工作区包含你的监视数据。 你可以使用自定义工作区或默认工作区。 
       * 若要使用默认工作区，请选中该复选框。 
       * 若要选择自定义工作区，请清除该复选框。 然后选择自定义工作区的订阅和区域。 
1. 在选项卡底部，选择 "**下一步：测试组**"。

   ![显示连接监视器中 "基本信息" 选项卡的屏幕截图](./media/connection-monitor-2-preview/create-cm-basics.png)

1. 在 "**测试组**" 选项卡上，选择 " **+ 测试组**"。 若要设置测试组，请参阅[在连接监视器中创建测试组](#create-test-groups-in-a-connection-monitor)。 
1. 在选项卡底部，选择 "**下一步：查看 + 创建**" 以查看连接监视器。

   ![显示 "测试组" 选项卡和窗格的屏幕截图，你可以在其中添加测试组详细信息](./media/connection-monitor-2-preview/create-tg.png)

1. 在 "**查看**" 和 "创建" 选项卡上，在创建连接监视器之前查看基本信息和测试组。 如果需要编辑连接监视器：
   * 若要编辑基本详细信息，请选择铅笔图标。
   * 若要编辑测试组，请选择它。

   > [!NOTE] 
   > "**查看**" 和 "创建" 选项卡显示连接监视器预览阶段每月的费用。 目前，当前的 "**成本**" 列不会收取任何费用。 当连接监视器公开上市时，此列将显示每月费用。 
   > 
   > 即使在连接监视器预览阶段，也会收取 Log Analytics 引入费用。

1. 准备好创建连接监视器后，在 "**查看和创建**" 选项卡的底部，选择 "**创建**"。

   ![连接监视器的屏幕截图，显示 "查看" 和 "创建" 选项卡](./media/connection-monitor-2-preview/review-create-cm.png)

连接监视器（预览）在后台创建连接监视器资源。

#### <a name="create-a-connection-monitor-by-using-armclient"></a>使用 ARMClient 创建连接监视器

使用以下代码通过 ARMClient 创建连接监视器。

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

address: '\&lt;FQDN of your on-premises agent'

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

下面是部署命令：
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>在连接监视器中创建测试组

连接监视器中的每个测试组都包括可在网络参数上进行测试的源和目标。 它们测试失败的检查百分比和测试配置上的 RTT。

在 Azure 门户中，若要在连接监视器中创建测试组，请为以下字段指定值：

* **禁用测试组**–可以选择此字段，以对测试组指定的所有源和目标禁用监视。 默认情况下清除此选项。
* **名称**–命名你的测试组。
* **源**–如果已在 Azure vm 和本地计算机上安装代理，则可以将其指定为源。 若要为源安装代理，请参阅[安装监视代理](#install-monitoring-agents)。
   * 若要选择 Azure 代理，请选择 " **Azure 代理**" 选项卡。在这里，只能看到绑定到创建连接监视器时指定的区域的 Vm。 默认情况下，Vm 会分组到它们所属的订阅。 这些组处于折叠状态。 
   
       您可以向下钻取到层次结构中的其他级别：

      **订阅** > **资源组** > **vnet** > **子网** > **包含代理的 vm**

      还可以更改 "**分组依据**" 字段的值，以从任何其他级别启动树。 例如，如果按虚拟网络分组，则会看到层次结构中具有代理的 Vm **vnet** > **子网** > **包含代理的 vm**。

      ![连接监视器的屏幕截图，显示 "添加源" 面板和 "Azure 代理" 选项卡](./media/connection-monitor-2-preview/add-azure-sources.png)

   * 若要选择本地代理，请选择 "**非 Azure 代理**" 选项卡。默认情况下，代理按区域分组到工作区中。 所有这些工作区都配置了网络性能监视器解决方案。 
   
       如果需要将网络性能监视器添加到工作区，请从[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)获取它。 有关如何添加网络性能监视器的信息，请参阅[Azure Monitor 中的监视解决方案](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)。 
   
       在 "**创建连接监视器**" 视图的 "**基本**信息" 选项卡上，选中 "默认区域"。 如果更改了区域，则可以在新区域的工作区中选择代理。 还可以更改 "**分组依据**" 字段的值以按子网分组。

      ![连接监视器的屏幕截图，显示 "添加源" 面板和 "非 Azure 代理" 选项卡](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * 若要查看所选 Azure 和非 Azure 代理，请参阅 "**查看**" 选项卡。

      ![连接监视器的屏幕截图，显示 "添加源" 面板和 "查看" 选项卡](./media/connection-monitor-2-preview/review-sources.png)

   * 完成设置源后，在 "**添加源**" 面板底部，选择 "**完成**"。

* **目标**–可以通过将其指定为目标来监视 Azure vm 或任何终结点（公共 IP、URL 或 FQDN）的连接。 在单个测试组中，可以添加 Azure Vm、Office 365 Url、Dynamics 365 Url 和自定义终结点。

    * 若要选择 Azure Vm 作为目标，请选择 " **Azure vm** " 选项卡。默认情况下，Azure Vm 将分组到你在 "**创建连接监视器**" 视图中选择的同一区域中的 "**基本**信息" 选项卡上。可以更改区域，并从新选择的区域中选择 "Azure Vm"。 然后，你可以向下钻取到层次结构中的其他级别，如 Azure 代理级别。

       !["添加目标" 窗格的屏幕截图，显示 "Azure Vm" 选项卡](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![显示订阅级别的 "添加目标" 窗格的屏幕截图](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * 若要选择终结点作为目标，请选择 "**终结点**" 选项卡。终结点列表包含 Office 365 测试 Url 和 Dynamics 365 测试 Url，按名称分组。 除了这些终结点外，还可以选择在同一连接监视器中的其他测试组中创建的终结点。 
    
        若要添加新的终结点，请在右上角选择 " **+ 终结点**"。 然后提供终结点名称和 URL、IP 或 FQDN。

       ![显示将终结点添加到连接监视器中的目标位置的屏幕截图](./media/connection-monitor-2-preview/add-endpoints.png)

    * 若要查看所选的 Azure Vm 和终结点，请选择 "**查看**" 选项卡。
    * 选择完目标后，请选择 "**完成**"。

* **测试配置**–您可以关联测试组中的测试配置。 Azure 门户仅允许每个测试组有一个测试配置，但可以使用 ARMClient 添加更多测试。

    * **名称**–命名测试配置。
    * **协议**–选择 "TCP"、"ICMP" 或 "HTTP"。 若要将 HTTP 更改为 HTTPS，请选择**http**作为协议，并选择**443**作为端口。
        * "**创建网络测试配置**"-仅当在 "**协议**" 字段中选择 " **HTTP** " 时才会显示此复选框。 选择此框以创建另一个测试配置，该配置使用您在配置中其他地方指定的相同源和目标。 新创建的测试配置名为 `<the name of your test configuration>_networkTestConfig`。
        * **禁用 traceroute** –此字段适用于其协议为 TCP 或 ICMP 的测试组。 选择此复选框可阻止源发现拓扑和逐跃点的 RTT。
    * **目标端口**–您可以使用所选的目标端口自定义此字段。
    * **测试频率**–使用此字段可选择源对指定的协议和端口 ping 目标的频率。 可以选择30秒、1分钟、5分钟、15分钟或30分钟。 源将根据你选择的值测试与目标的连接。  例如，如果选择30秒，则源将在30秒的时间段内至少检查与目标的连接。
    * **成功阈值**–你可以在以下网络参数上设置阈值：
       * **检查失败**–设置在源使用指定的条件检查与目标的连接时可能失败的检查的百分比。 对于 TCP 或 ICMP 协议，失败检查的百分比可能会等同到数据包丢失的百分比。 对于 HTTP 协议，此字段表示未接收到响应的 HTTP 请求的百分比。
       * **往返时间**–设置通过测试配置连接到目标所需的源时间长度（以毫秒为单位）。
    
       ![显示在连接监视器中设置测试配置的位置的屏幕截图](./media/connection-monitor-2-preview/add-test-config.png)

添加到测试组中的所有源、目标和测试配置将细分为单独的测试。 下面是如何分解源和目标的示例：

* 测试组： TG1
* 源：3（A，B，C）
* 目标：2（D，E）
* 测试配置：2（配置1，配置2）
* 已创建的测试总数：12

| 测试编号 | 源 | 目标 | 测试配置 |
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

连接监视器具有以下比例限制：

* 每个区域每个订阅的最大连接监视器数：100
* 每个连接监视器的最大测试组：20
* 每个连接的最大源和目标监视器：100
* 每个连接监视器的最大测试配置： 
    * 20 via ARMClient
    * 2通过 Azure 门户

## <a name="analyze-monitoring-data-and-set-alerts"></a>分析监视数据并设置警报

创建连接监视器后，源会根据测试配置检查与目标的连接。

### <a name="checks-in-a-test"></a>签入测试

根据你在测试配置中选择的协议，连接监视器（预览）对源-目标对运行一系列检查。 根据所选测试频率运行检查。

如果使用 HTTP，则服务会计算返回响应代码的 HTTP 响应数。 结果确定失败检查的百分比。 为了计算 RTT，服务会测量 HTTP 调用和响应之间的时间。

如果使用 TCP 或 ICMP，则服务会计算数据包丢失百分比，以确定失败检查的百分比。 若要计算 RTT，服务会测量为发送的数据包接收确认（ACK）所花费的时间。 如果为网络测试启用了 traceroute 数据，则可以查看本地网络的跃点丢失和延迟情况。

### <a name="states-of-a-test"></a>测试的状态

根据检查返回的数据，测试可以具有以下状态：

* **Pass** –失败检查和 RTT 百分比的实际值在指定的阈值内。
* **Fail** –失败检查或 RTT 百分比的实际值超出了指定的阈值。 如果未指定阈值，则当失败检查的百分比为100时，测试将进入失败状态。
* **警告**–未指定未通过检查的百分比的标准。 如果没有指定条件，连接监视器（预览）会自动分配一个阈值。 超过此阈值时，测试状态将更改为 "警告"。

### <a name="data-collection-analysis-and-alerts"></a>数据收集、分析和警报

连接监视器（预览版）收集的数据存储在 Log Analytics 工作区中。 创建连接监视器时设置此工作区。 

监视数据在 Azure Monitor 度量值中也可用。 您可以使用 Log Analytics 根据需要保留您的监视数据。 默认情况下，Azure Monitor 仅存储30天的指标。 

您可以[对数据设置基于指标的警报](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)。

#### <a name="monitoring-dashboards"></a>监视仪表板

在监视仪表板上，可以看到可为订阅、区域、时间戳、源和目标类型访问的连接监视器列表。

从网络观察程序中转到连接监视器（预览版）时，可以通过以下方式查看数据：

* **连接监视器**–为你的订阅、区域、时间戳、源和目标类型创建的所有连接监视器的列表。 此视图为默认视图。
* **测试组**–为你的订阅、区域、时间戳、源和目标类型创建的所有测试组的列表。 连接监视器不筛选这些测试组。
* **测试**–为你的订阅、区域、时间戳、源和目标类型运行的所有测试的列表。 这些测试不按连接监视器或测试组筛选。

在下图中，箭头1指示了三个数据视图。

在仪表板上，可以展开每个连接监视器以查看其测试组。 然后，可以展开每个测试组，以查看在其中运行的测试。 

你可以基于以下内容筛选列表：

* **顶级筛选器**–选择订阅、区域、时间戳源和目标类型。 请参阅下图中的框2。
* **基于状态的筛选**器–按连接监视器、测试组或测试的状态进行筛选。 请参阅下图中的箭头3。
* **自定义筛选器**–选择 "**全选**" 以执行通用搜索。 若要按特定实体进行搜索，请从下拉列表中选择。 请参阅下图中的箭头4。

![显示如何筛选连接监视器中的连接监视器、测试组和测试的视图（预览版）的屏幕截图](./media/connection-monitor-2-preview/cm-view.png)

例如，若要查看连接监视器（预览版）中的所有测试（源 IP 是10.192.64.56），请执行以下操作：
1. 将视图更改为 "**测试**"。
1. 在搜索字段中，键入*10.192.64.56*
1. 在下拉列表中，选择 "**源**"。

仅显示在源 IP 为10.192.64.56 的连接监视器（预览版）中失败的测试：
1. 将视图更改为 "**测试**"。
1. 对于基于状态的筛选器，选择 "**失败**"。
1. 在搜索字段中，键入*10.192.64.56*
1. 在下拉列表中，选择 "**源**"。

仅显示在目标为 outlook.office365.com 的连接监视器（预览版）中失败的测试：
1. 更改要**测试**的视图。
1. 对于基于状态的筛选器，选择 "**失败**"。
1. 在搜索字段中，输入*outlook.office365.com*
1. 在下拉列表中，选择 "**目标**"。

   ![显示筛选为仅显示 Outlook.Office365.com 目标失败测试的视图的屏幕截图](./media/connection-monitor-2-preview/tests-view.png)

查看 RTT 的趋势和连接监视器的失败检查百分比：
1. 选择要调查的连接监视器。 默认情况下，监视数据按测试组进行组织。

   ![显示某个连接监视器的指标的屏幕截图，按测试组显示](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. 选择要调查的测试组。

   ![显示在何处选择测试组的屏幕截图](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    根据失败检查的 RTT 或百分比，可以看到测试组的前五个失败的测试。 对于每个测试，将看到有关失败检查百分比的 RTT 和趋势线。
1. 从列表中选择一个测试，或选择另一个要调查的测试。 对于时间间隔和失败检查的百分比，可以看到阈值和实际值。 对于 RTT，可以看到阈值、平均值、最小值和最大值。

   ![显示针对 RTT 和失败检查百分比的测试结果的屏幕截图](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. 更改时间间隔以查看更多数据。
1. 更改视图以查看源、目标或测试配置。 
1. 选择基于失败的测试的源，并调查前5个失败的测试。 例如，通过 > **源**"和"**查看**" > **目标**来选择"**查看**"，以调查连接监视器中的相关测试。

   ![显示前5个失败测试的性能指标的屏幕截图](./media/connection-monitor-2-preview/cm-drill-select-source.png)

查看 RTT 的趋势和测试组的失败检查的百分比：

1. 选择要调查的测试组。 

    默认情况下，监视数据按源、目标和测试配置（测试）排列。 稍后，你可以将该视图从测试组更改为源、目标或测试配置。 然后选择一个实体来调查前5个失败的测试。 例如，将视图更改为 "源" 和 "目标"，以在所选连接监视器中调查相关的测试。
1. 选择要调查的测试。

   ![显示在何处选择测试的屏幕截图](./media/connection-monitor-2-preview/tg-drill.png)

    对于时间间隔和失败检查的百分比，可以看到阈值和实际值。 对于 RTT，可以查看阈值、平均值、最小值和最大值。 还会看到所选测试的已触发警报。
1. 更改时间间隔以查看更多数据。

查看 RTT 的趋势和测试的失败检查的百分比：
1. 选择要调查的源、目标和测试配置。

    对于时间间隔和失败检查的百分比，可以看到阈值和实际值。 对于 RTT，可以查看阈值、平均值、最小值和最大值。 还会看到所选测试的已触发警报。

   ![显示测试指标的屏幕截图](./media/connection-monitor-2-preview/test-drill.png)

1. 若要查看网络拓扑，请选择 "**拓扑**"。

   ![显示 "网络拓扑" 选项卡的屏幕截图](./media/connection-monitor-2-preview/test-topo.png)

1. 若要查看标识的问题，请在拓扑中选择路径中的任何跃点。 （这些跃点是 Azure 资源。）此功能当前不适用于本地网络。

   ![显示 "拓扑" 选项卡上所选 "跃点" 链接的屏幕截图](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>Log Analytics 中的日志查询

使用 Log Analytics 创建监视数据的自定义视图。 UI 显示的所有数据都来自 Log Analytics。 可以通过交互方式分析存储库中的数据。 关联代理运行状况中的数据或基于 Log Analytics 的其他解决方案。 将数据导出到 Excel 或 Power BI，或创建可共享的链接。

#### <a name="metrics-in-azure-monitor"></a>Azure Monitor 中的指标

在连接监视器（预览版）体验之前创建的连接监视器中，所有四个指标都可用：% 探测失败、AverageRoundtripMs、ChecksFailedPercent （预览版）和 RoundTripTimeMs （预览）。 在连接监视器（预览版）体验中创建的连接监视器中，数据仅适用于使用 *（预览）* 标记的指标。

![显示连接监视器中指标的屏幕截图（预览）](./media/connection-monitor-2-preview/monitor-metrics.png)

使用度量值时，将资源类型设置为 "networkWatchers//connectionMonitors"

| 指标 | 显示名称 | 单位 | 聚合类型 | 说明 | 维度 |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | 失败的探测百分比 | 百分比 | 平均值 | 连接监视探测失败的百分比。 | 无维度 |
| AverageRoundtripMs | 平均往返时间（毫秒） | 毫秒 | 平均值 | 在源和目标之间发送的连接监视探测的平均网络 RTT。 |             无维度 |
| ChecksFailedPercent （预览版） | % 检查失败（预览） | 百分比 | 平均值 | 测试的失败检查的百分比。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>协议 <br>目的地地址 <br>DestinationName <br>DestinationResourceId <br>目标类型 <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>区域 |
| RoundTripTimeMs （预览版） | 往返时间（毫秒）（预览版） | 毫秒 | 平均值 | 在源和目标之间发送的检查的 RTT。 不计算此值的平均值。 | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>协议 <br>目的地地址 <br>DestinationName <br>DestinationResourceId <br>目标类型 <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>区域 |

#### <a name="metric-alerts-in-azure-monitor"></a>Azure Monitor 中的指标警报

若要在 Azure Monitor 中创建警报：

1. 选择在连接监视器（预览版）中创建的连接监视器资源。
1. 确保**指标**显示为连接监视器的信号类型。
1. 在 "**添加条件**" 中 **，选择**" **ChecksFailedPercent （预览版）** " 或 " **RoundTripTimeMs （预览）** "。
1. 对于 "**信号类型**"，请选择 "**指标**"。 例如，选择 " **ChecksFailedPercent （预览版）** "。
1. 将列出度量值的所有维度。 选择维度名称和维度值。 例如，选择 "**源地址**"，然后在连接监视器中输入任何源的 IP 地址。
1. 在 "**警报逻辑**" 中，填写以下详细信息：
   * **条件类型**：**静态**。
   * **条件**和**阈值**。
   * **聚合粒度和计算频率**：连接监视器（预览）每分钟更新一次数据。
1. 在 "**操作**" 中，选择操作组。
1. 提供警报详细信息。
1. 创建警报规则。

   ![显示 Azure Monitor 中创建规则区域的屏幕截图;突出显示 "源地址" 和 "源终结点名称"](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>诊断网络中的问题

连接监视器（预览版）可帮助你诊断连接监视器和网络中的问题。 你之前安装的 Log Analytics 代理检测到你的混合网络中的问题。 网络观察程序扩展检测到 Azure 中的问题。 

可以在 Azure 网络的网络拓扑中查看问题。

对于其源为本地 Vm 的网络，可以检测到以下问题：

* 请求已超时。
* 终结点未通过 DNS 解析-临时或持久。 URL 无效。
* 找不到主机。
* 源无法连接到目标。 无法通过 ICMP 访问目标。
* 与证书相关的问题： 
    * 对代理进行身份验证所需的客户端证书。 
    * 无法访问证书重定位列表。 
    * 终结点的主机名与证书的使用者或使用者备用名称不匹配。 
    * 源的本地计算机受信任的证书颁发机构存储中缺少根证书。 
    * SSL 证书已过期、无效、被吊销或不兼容。

对于源为 Azure Vm 的网络，可以检测到以下问题：

* 代理问题：
    * 代理已停止。
    * DNS 解析失败。
    * 没有在目标端口上侦听的应用程序或侦听器。
    * 无法打开套接字。
* VM 状态问题： 
    * 正在启动
    * 正在停止
    * 已停止
    * 正在解除分配
    * 已解除分配
    * 重新
    * 未分配
* 缺少 ARP 表条目。
* 由于本地防火墙问题或 NSG 规则，流量被阻止。
* 虚拟网络网关问题： 
    * 缺少路由。
    * 两个网关之间的隧道已断开连接或丢失。
    * 隧道找不到第二个网关。
    * 找不到对等信息。
* Microsoft Edge 中缺少路由。
* 由于系统路由或 UDR，流量已停止。
* 未在网关连接上启用 BGP。
* 负载均衡器的 DIP 探测已关闭。
