---
title: 连接监视器（预览） |微软文档
description: 了解如何使用连接监视器（预览）监视分布式环境中的网络通信。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599322"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>带连接监视器的网络连接监控（预览）

连接监视器（预览）在 Azure 网络观察程序中提供统一的端到端连接监视。 连接监视器（预览）功能支持混合部署和 Azure 云部署。 网络观察程序提供了用于监视、诊断和查看 Azure 部署的连接相关指标的工具。

以下是连接监视器（预览）的一些用例：

- 前端 Web 服务器 VM 与多层应用程序中的数据库服务器 VM 通信。 您希望检查两个 VM 之间的网络连接。
- 您希望美国东部区域的 VM ping 美国中部区域的 VM，并且希望比较跨区域网络延迟。
- 您在华盛顿西雅图和弗吉尼亚州阿什本有多个本地办公地点。 您的办公室站点连接到 Office 365 URL。 对于 Office 365 URL 的用户，请比较西雅图和阿什本之间的延迟。
- 混合应用程序需要连接到 Azure 存储终结点。 本地站点和 Azure 应用程序连接到相同的 Azure 存储终结点。 您希望将本地站点的延迟与 Azure 应用程序的延迟进行比较。
- 您希望检查本地设置与托管云应用程序的 Azure VM 之间的连接。

在预览阶段，连接监视器结合了两个功能中最好的功能：网络观察程序[连接监视器](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint)功能和网络性能监视器 （NPM）[服务连接监视器](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)功能。

以下是连接监视器（预览）的一些优点：

* 满足 Azure 和混合监视需求的统一直观体验
* 跨区域跨工作区连接监控
* 更高的探测频率和更好的网络性能可见性
* 加快混合部署警报
* 支持基于 HTTP、TCP 和 ICMP 的连接检查 
* 针对 Azure 和非 Azure 测试设置的指标和日志分析支持

![显示连接监视器如何与 Azure VM、非 Azure 主机、终结点和数据存储位置进行交互的图表](./media/connection-monitor-2-preview/hero-graphic.png)

要开始使用连接监视器（预览）进行监视，请按照以下步骤操作： 

1. 安装监视代理。
1. 在订阅中启用网络观察程序。
1. 创建连接监视器。
1. 设置数据分析和警报。
1. 诊断网络中的问题。

以下各节详细介绍了这些步骤。

## <a name="install-monitoring-agents"></a>安装监控代理

连接监视器依赖于轻量级可执行文件来运行连接检查。  它支持来自 Azure 环境和本地环境的连接检查。 您使用的可执行文件取决于 VM 是托管在 Azure 上还是本地。

### <a name="agents-for-azure-virtual-machines"></a>Azure 虚拟机的代理

要使连接监视器将 Azure VM 识别为监视源，请在它们上安装网络观察程序代理虚拟机扩展。 此扩展也称为*网络观察程序扩展*。 Azure 虚拟机需要扩展来触发端到端监视和其他高级功能。 

创建[VM](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)时，可以安装网络观察程序扩展。 您还可以单独安装、配置和排除[Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux)和[Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)的网络观察程序扩展。

网络安全组 （NSG） 或防火墙的规则可能会阻止源和目标之间的通信。 连接监视器检测到此问题，并将其作为拓扑中的诊断消息显示。 要启用连接监视，请确保 NSG 和防火墙规则允许在源和目标之间通过 TCP 或 ICMP 数据包。

### <a name="agents-for-on-premises-machines"></a>本地计算机的代理

要使连接监视器将本地计算机识别为监视源，请在计算机上安装日志分析代理。 然后启用网络性能监视器解决方案。 这些代理链接到日志分析工作区，因此您需要在代理开始监视之前设置工作区 ID 和主密钥。

要安装 Windows 计算机的日志分析代理，请参阅[Windows 的 Azure 监视器虚拟机扩展。](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)

如果路径包括防火墙或网络虚拟设备 （NVA），则请确保目标是可到达的。

## <a name="enable-network-watcher-on-your-subscription"></a>在订阅中启用网络观察程序

使用网络观察程序启用具有虚拟网络的所有订阅。 在订阅中创建虚拟网络时，将在虚拟网络的区域和订阅中自动启用网络观察程序。 此自动启用不会影响您的资源或产生费用。 确保订阅上未显式禁用网络观察程序。 

有关详细信息，请参阅[启用网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)。

## <a name="create-a-connection-monitor"></a>创建连接监视器 

连接监视器定期监控通信。 它会通知您可伸易道性和延迟的变化。 您还可以检查源代理和目标终结点之间的当前和历史网络拓扑。

源可以是 Azure VM 或具有已安装监视代理的本地计算机。 目标终结点可以是 Office 365 URL、动态 365 URL、自定义 URL、Azure VM 资源标识、IPv4、IPv6、FQDN 或任何域名。

### <a name="access-connection-monitor-preview"></a>访问连接监视器（预览）

1. 在 Azure 门户主页上，转到**网络观察程序**。
1. 在左侧，在 **"监视**"部分中，选择**连接监视器（预览）。**
1. 您将看到在连接监视器（预览）中创建的所有连接监视器。 要查看在连接监视器的经典体验中创建的连接监视器，请访问**连接监视器**选项卡。

    ![显示在连接监视器中创建的连接监视器的屏幕截图（预览）](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>创建连接监视器

在连接监视器（预览）中创建的连接监视器中，可以将本地计算机和 Azure VM 添加为源。 这些连接监视器还可以监视与端点的连接。 终结点可以位于 Azure 或任何其他 URL 或 IP 上。

连接监视器（预览）包括以下实体：

* **连接监视器资源**= 特定于区域的 Azure 资源。 以下所有实体都是连接监视器资源的属性。
* **终结点**= 参与连接检查的源或目标。 终结点的示例包括 Azure VM、本地代理、URL 和 IP。
* **测试配置**– 测试特定于协议的配置。 根据您选择的协议，您可以定义端口、阈值、测试频率和其他参数。
* **测试组**– 包含源终结点、目标终结点和测试配置的组。 连接监视器可以包含多个测试组。
* **测试**– 源终结点、目标终结点和测试配置的组合。 测试是监视数据可用的最精细级别。 监视数据包括失败的检查百分比和往返时间 （RTT）。

 ![显示连接监视器的图表，定义测试组和测试之间的关系](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>从 Azure 门户创建连接监视器

要从 Azure 门户创建连接监视器，请按照以下步骤操作：

1. 在左上角的连接**监视器（预览）** 仪表板上，选择 **"创建**"。
1. 在 **"基本"** 选项卡上，输入连接监视器的信息：
   * **连接监视器名称**= 添加连接监视器的名称。 使用 Azure 资源的标准命名规则。
   * **订阅**• 为连接监视器选择订阅。
   * **区域**= 为连接监视器选择区域。 只能选择在此区域中创建的源 VM。
   * **工作区配置**- 工作区保存监视数据。 您可以使用自定义工作区或默认工作区。 
       * 要使用默认工作区，请选择"复选框"。 
       * 要选择自定义工作区，请清除该复选框。 然后，选择自定义工作区的订阅和区域。 
1. 在选项卡的底部，选择 **"下一步：测试组**"。

   ![显示连接监视器中基础知识选项卡的屏幕截图](./media/connection-monitor-2-preview/create-cm-basics.png)

1. 在 **"测试组**"选项卡上，选择 **" 测试组**"。 要设置测试组，请参阅[在连接监视器中创建测试组](#create-test-groups-in-a-connection-monitor)。 
1. 在选项卡底部，选择 **"下一步：查看 + 创建**"以查看连接监视器。

   ![显示"测试组"选项卡和添加测试组详细信息的窗格的屏幕截图](./media/connection-monitor-2-preview/create-tg.png)

1. 在 **"审阅 + 创建**"选项卡上，在创建连接监视器之前查看基本信息和测试组。 如果需要编辑连接监视器：
   * 要编辑基本详细信息，请选择铅笔图标。
   * 要编辑测试组，请选择它。

   > [!NOTE] 
   > "**审核 + 创建**"选项卡显示连接监视器预览阶段每月的成本。 目前，"**当前成本"** 列不显示任何费用。 当连接监视器普遍可用时，此列将显示每月费用。 
   > 
   > 即使在连接监视器预览阶段，日志分析引入费用也会增加。

1. 准备好创建连接监视器时，在 **"审阅 + 创建**"选项卡的底部，选择"**创建**"。

   ![连接监视器的屏幕截图，显示"审阅 + 创建"选项卡](./media/connection-monitor-2-preview/review-create-cm.png)

连接监视器（预览）在后台创建连接监视器资源。

#### <a name="create-a-connection-monitor-by-using-armclient"></a>使用 ARMClient 创建连接监视器

使用以下代码使用 ARMClient 创建连接监视器。

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

连接监视器中的每个测试组都包括通过网络参数进行测试的源和目标。 测试检查失败百分比和 RTT 测试配置的百分比。

在 Azure 门户中，要在连接监视器中创建测试组，请为以下字段指定值：

* **禁用测试组**– 您可以选择此字段以禁用测试组指定的所有源和目标的监视。 默认情况下，将清除此选项。
* **名称**= 命名测试组。
* **源**– 如果在其上安装了代理，则可以将 Azure VM 和本地计算机指定为源。 要为源安装代理，请参阅[安装监视代理](#install-monitoring-agents)。
   * 要选择 Azure 代理，请选择 **"Azure 代理"** 选项卡。在这里，您只看到绑定到创建连接监视器时指定的区域的 VM。 默认情况下，VM 被分组到它们所属的订阅中。 这些组已折叠。 
   
       可以从订阅级别向下钻取到层次结构中的其他级别：

      **订阅** > **资源组** > **VNETs** > **子网** > VM**与代理**

      您还可以按字段更改 **"组"** 的值，以便从任何其他级别启动树。 例如，如果按虚拟网络分组，您将看到层次结构中具有代理**的** > **Subnets** > VM**与代理**。

      ![连接监视器的屏幕截图，显示"添加源"面板和 Azure 代理选项卡](./media/connection-monitor-2-preview/add-azure-sources.png)

   * 要选择本地代理，请选择 **"非 Azure 代理"** 选项卡。默认情况下，按区域将代理分组到工作区中。 所有这些工作区都配置了网络性能监视器解决方案。 
   
       如果需要将网络性能监视器添加到工作区，请从[Azure 应用商店](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)获取它。 有关如何添加网络性能监视器的信息，请参阅 Azure[监视器 中的监视解决方案](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)。 
   
       在 **"创建连接监视器"** 视图中，在"**基本"** 选项卡上，选择了默认区域。 如果更改区域，则可以从新区域中的工作区中选择代理。 您还可以**将"分组"的值（按**字段）更改为按子网分组。

      ![连接监视器的屏幕截图，显示"添加源"面板和非 Azure 代理选项卡](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * 要查看所选的 Azure 和非 Azure 代理，请访问 **"审阅**"选项卡。

      ![连接监视器的屏幕截图，显示"添加源"面板和"查看"选项卡](./media/connection-monitor-2-preview/review-sources.png)

   * 完成源设置后，在"**添加源**"面板的底部，选择 **"完成**"。

* **目标**– 可以通过将其指定为目标来监视与 Azure VM 或任何终结点（公共 IP、URL 或 FQDN）的连接。 在单个测试组中，可以添加 Azure VM、Office 365 URL、动态 365 URL 和自定义终结点。

    * 要选择 Azure VM 作为目标，请选择**Azure VM**选项卡。默认情况下，Azure VM 被分组到订阅层次结构中，该层次结构位于"**创建连接监视器"选项卡上"创建连接监视器"** 视图中选择的同一区域**Basics**中。您可以更改区域并从新选择的区域中选择 Azure VM。 然后，可以从订阅级别向下钻取到层次结构中的其他级别，如 Azure 代理级别。

       !["添加目标"窗格的屏幕截图，显示 Azure VM 选项卡](./media/connection-monitor-2-preview/add-azure-dests1.png)

       !["添加目标"窗格的屏幕截图，显示订阅级别](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * 要选择终结点作为目标，请选择 **"终结点**"选项卡。终结点列表包括 Office 365 测试 URL 和 Dynamics 365 测试 URL，按名称分组。 除了这些终结点之外，您还可以选择在同一连接监视器中的其他测试组中创建的终结点。 
    
        要添加新终结点，请在右上角选择 **+ 终结点**。 然后提供终结点名称和 URL、IP 或 FQDN。

       ![显示在连接监视器中将终结点作为目标添加位置的屏幕截图](./media/connection-monitor-2-preview/add-endpoints.png)

    * 要查看您选择的 Azure VM 和终结点，请选择"**审阅**"选项卡。
    * 完成选择目的地后，选择 **"完成**"。

* **测试配置**– 您可以在测试组中关联测试配置。 Azure 门户仅允许每个测试组一个测试配置，但可以使用 ARMClient 添加更多配置。

    * **名称**= 命名测试配置。
    * **协议**= 选择 TCP、ICMP 或 HTTP。 要将 HTTP 更改为 HTTPS，请选择**HTTP**作为协议，然后选择**443**作为端口。
        * **创建网络测试配置**— 仅当在 **"协议"** 字段中选择**HTTP**时，才会显示此复选框。 选择此框可创建另一个测试配置，该配置使用与配置中其他位置指定的源和目标相同的源和目标。 新创建的测试配置名为`<the name of your test configuration>_networkTestConfig`。
        * **禁用跟踪路由**- 此字段适用于协议为 TCP 或 ICMP 的测试组。 选择此框可阻止源发现拓扑和逐跳 RTT。
    * **目标端口**– 您可以使用您选择的目标端口自定义此字段。
    * **测试频率**– 使用此字段选择源在指定的协议和端口上 ping 目标的频率。 您可以选择 30 秒、1 分钟、5 分钟、15 分钟或 30 分钟。 源将根据您选择的值测试到目标的连接。  例如，如果选择 30 秒，源将在 30 秒内至少检查一次与目标的连接。
    * **成功阈值**– 您可以设置以下网络参数的阈值：
       * **检查失败**– 使用指定的条件设置源检查到目标连接时可能失败的检查百分比。 对于 TCP 或 ICMP 协议，失败的检查百分比可以等同于数据包丢失百分比。 对于 HTTP 协议，此字段表示未收到响应的 HTTP 请求的百分比。
       * **往返时间**– 将 RTT 设置为毫秒，了解源通过测试配置连接到目标所花的时间。
    
       ![显示在连接监视器中设置测试配置的位置的屏幕截图](./media/connection-monitor-2-preview/add-test-config.png)

添加到测试组的所有源、目标和测试配置都细分为单个测试。 下面是如何分解源和目标的示例：

* 测试组：TG1
* 资料来源：3（A、B、C）
* 目的地： 2 （D， E）
* 测试配置： 2 （配置 1， 配置 2）
* 创建的测试总数： 12

| 测试编号 | 源 | 目标 | 测试配置 |
| --- | --- | --- | --- |
| 1 | A | D | 配置 1 |
| 2 | A | D | 配置 2 |
| 3 | A | E | 配置 1 |
| 4 | A | E | 配置 2 |
| 5 | B | D | 配置 1 |
| 6 | B | D | 配置 2 |
| 7 | B | E | 配置 1 |
| 8 | B | E | 配置 2 |
| 9 | C | D | 配置 1 |
| 10 | C | D | 配置 2 |
| 11 | C | E | 配置 1 |
| 12 | C | E | 配置 2 |

### <a name="scale-limits"></a>缩放限制

连接监视器具有以下比例限制：

* 每个区域每个订阅的最大连接监视器：100
* 每个连接监视器的最大测试组：20
* 每个连接监视器的最大源和目标：100
* 每个连接监视器的最大测试配置： 
    * 20 通过 ARMClient
    * 2 通过 Azure 门户

## <a name="analyze-monitoring-data-and-set-alerts"></a>分析监视数据并设置警报

创建连接监视器后，源会根据测试配置检查与目标的连接。

### <a name="checks-in-a-test"></a>签入测试

根据您在测试配置中选择的协议，连接监视器（预览）对源目标对运行一系列检查。 检查根据您选择的测试频率运行。

如果使用 HTTP，则服务将计算返回响应代码的 HTTP 响应数。 结果确定失败的检查的百分比。 要计算 RTT，服务测量 HTTP 调用和响应之间的时间。

如果使用 TCP 或 ICMP，该服务将计算数据包丢失百分比以确定失败检查的百分比。 要计算 RTT，服务测量接收所发送数据包的确认 （ACK） 所占用的时间。 如果为网络测试启用了跟踪路由数据，则可以看到本地网络的逐跳丢失和延迟。

### <a name="states-of-a-test"></a>测试状态

根据检查返回的数据，测试可以具有以下状态：

* **通过**= 失败检查和 RTT 的百分比的实际值在指定的阈值内。
* **失败**= 失败检查或 RTT 超出指定阈值的百分比的实际值。 如果未指定阈值，则当失败检查的百分比为 100 时，测试将达到"失败"状态。
* **警告**– 未为失败检查的百分比指定条件。 如果没有指定的条件，连接监视器（预览）将自动分配阈值。 超过该阈值时，测试状态将更改为警告。

### <a name="data-collection-analysis-and-alerts"></a>数据收集、分析和警报

连接监视器（预览）收集的数据存储在日志分析工作区中。 创建连接监视器时设置此工作区。 

监视数据在 Azure 监视器指标中也可用。 您可以使用日志分析根据需要保留监视数据。 默认情况下，Azure 监视器仅存储 30 天的指标。 

您可以[对数据设置基于指标的警报](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)。

#### <a name="monitoring-dashboards"></a>监视仪表板

在监视仪表板上，您将看到一个连接监视器的列表，您可以访问订阅、区域、时间戳、源和目标类型。

当您从网络观察程序转到连接监视器（预览）时，您可以通过：

* **连接监视器**– 为订阅、区域、时间戳、源和目标类型创建的所有连接监视器的列表。 此为默认视图。
* **测试组**– 为订阅、区域、时间戳、源和目标类型创建的所有测试组的列表。 连接监视器不会筛选这些测试组。
* **测试**= 为订阅、区域、时间戳、源和目标类型运行的所有测试的列表。 这些测试不会由连接监视器或测试组筛选。

在下图中，三个数据视图由箭头 1 指示。

在仪表板上，可以展开每个连接监视器以查看其测试组。 然后，您可以展开每个测试组以查看在其中运行的测试。 

您可以基于：

* **顶级筛选器**– 选择订阅、区域、时间戳源和目标类型。 请参阅下图中的框 2。
* **基于状态的筛选器**– 按连接监视器、测试组或测试的状态进行筛选。 请参阅下图中的箭头 3。
* **自定义筛选器**-**选择"全部"** 以执行通用搜索。 要按特定实体进行搜索，请从下拉列表中选择。 请参阅下图中的箭头 4。

![显示如何筛选连接监视器、测试组和连接监视器中的测试视图的屏幕截图（预览版）](./media/connection-monitor-2-preview/cm-view.png)

例如，查看源 IP 为 10.192.64.56 的连接监视器（预览）中的所有测试：
1. 将视图更改为 **"测试**"。
1. 在搜索字段中，键入*10.192.64.56*
1. 在下拉列表中，选择 **"源**"。

要在源 IP 为 10.192.64.56 的连接监视器（预览）中仅显示失败的测试：
1. 将视图更改为 **"测试**"。
1. 对于基于状态的筛选器，选择 **"失败**"。
1. 在搜索字段中，键入*10.192.64.56*
1. 在下拉列表中，选择 **"源**"。

要在目标outlook.office365.com的连接监视器（预览）中仅显示失败的测试：
1. 将视图更改为 **"测试**"。
1. 对于基于状态的筛选器，选择 **"失败**"。
1. 在搜索字段中，输入*outlook.office365.com*
1. 在下拉列表中，选择 **"目的地**"。

   ![显示经过筛选以仅显示Outlook.Office365.com目标的失败的测试的视图的屏幕截图](./media/connection-monitor-2-preview/tests-view.png)

要查看 RTT 中的趋势以及连接监视器的失败检查百分比：
1. 选择要调查的连接监视器。 默认情况下，监视数据按测试组组织。

   ![显示连接监视器指标的屏幕截图，由测试组显示](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. 选择要调查的测试组。

   ![显示选择测试组的位置的屏幕截图](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    根据 RTT 或失败检查的百分比，您将看到测试组的五大失败测试。 对于每个测试，您都会看到未通过检查百分比的 RTT 和趋势线。
1. 从列表中选择一个测试，或选择另一个测试进行调查。 对于时间间隔和失败检查的百分比，您将看到阈值和实际值。 对于 RTT，您将看到阈值、平均值、最小值和最大值的值。

   ![显示 RTT 测试结果和失败检查百分比的屏幕截图](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. 更改时间间隔以查看更多数据。
1. 更改视图以查看源、目标或测试配置。 
1. 根据失败的测试选择源，并调查前五个失败的测试。 例如，选择 **"按** > **源**进行查看"和 **"按目标查看"** > **Destinations**以调查连接监视器中的相关测试。

   ![显示前五个失败测试的性能指标的屏幕截图](./media/connection-monitor-2-preview/cm-drill-select-source.png)

要查看 RTT 中的趋势以及测试组的未通过检查的百分比：

1. 选择要调查的测试组。 

    默认情况下，监视数据由源、目的地和测试配置（测试）排列。 稍后，您可以将视图从测试组更改为源、目标或测试配置。 然后选择一个实体来调查前五个失败的测试。 例如，将视图更改为源和目标以调查所选连接监视器中的相关测试。
1. 选择要调查的测试。

   ![显示选择测试位置的屏幕截图](./media/connection-monitor-2-preview/tg-drill.png)

    对于时间间隔和未通过检查的百分比，您将看到阈值和实际值。 对于 RTT，您将看到阈值、平均值、最小值和最大值的值。 您还会看到所选测试的已触发警报。
1. 更改时间间隔以查看更多数据。

要查看 RTT 中的趋势以及测试的失败检查百分比：
1. 选择要调查的源、目标和测试配置。

    对于时间间隔和失败检查的百分比，您将看到阈值和实际值。 对于 RTT，您将看到阈值、平均值、最小值和最大值的值。 您还会看到所选测试的已触发警报。

   ![显示测试指标的屏幕截图](./media/connection-monitor-2-preview/test-drill.png)

1. 要查看网络拓扑，请选择**拓扑**。

   ![显示网络拓扑选项卡的屏幕截图](./media/connection-monitor-2-preview/test-topo.png)

1. 要查看已识别的问题，请在拓扑中选择路径中的任何跃点。 （这些跃点是 Azure 资源。此功能当前不适用于本地网络。

   !["拓扑"选项卡上显示选定跃点链接的屏幕截图](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>日志分析中的日志查询

使用日志分析创建监视数据的自定义视图。 UI 显示的所有数据都来自日志分析。 您可以以交互方式分析存储库中的数据。 关联来自代理运行状况的数据或日志分析中基于的其他解决方案。 将数据导出到 Excel 或 Power BI，或创建可共享的链接。

#### <a name="metrics-in-azure-monitor"></a>Azure Monitor 中的指标

在连接监视器（预览）体验之前创建的连接监视器中，所有四个指标都可用：百分比探测失败、平均往返时间、检查失败百分比（预览）和往返时间（预览）。 在连接监视器（预览）体验中创建的连接监视器中，数据仅适用于标记为 *（预览）的*指标。

![显示连接监视器中指标的屏幕截图（预览）](./media/connection-monitor-2-preview/monitor-metrics.png)

使用指标时，将资源类型设置为 Microsoft.网络/网络观察器/连接监视器

| 指标 | 显示名称 | 单位 | 聚合类型 | 描述 | 维度 |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | 失败的探测百分比 | 百分比 | 平均值 | 连接监视探测器的百分比失败。 | 无维度 |
| AverageRoundtripMs | 平均往返时间（毫秒） | 毫秒 | 平均值 | 用于在源和目标之间发送的连接监视探测器的平均网络 RTT。 |             无维度 |
| 检查失败百分比（预览） | 检查失败百分比（预览） | 百分比 | 平均值 | 测试的失败检查的百分比。 | 连接监视器资源 Id <br>源地址 <br>SourceName <br>源资源 Id <br>SourceType <br>协议 <br>目的地地址 <br>DestinationName <br>目标资源Id <br>目标类型 <br>DestinationPort <br>测试组名称 <br>测试配置名称 <br>区域 |
| 往返时间（预览） | 往返时间 （ms） （预览） | 毫秒 | 平均值 | RTT 用于在源和目标之间发送的检查。 此值不为平均值。 | 连接监视器资源 Id <br>源地址 <br>SourceName <br>源资源 Id <br>SourceType <br>协议 <br>目的地地址 <br>DestinationName <br>目标资源Id <br>目标类型 <br>DestinationPort <br>测试组名称 <br>测试配置名称 <br>区域 |

#### <a name="metric-alerts-in-azure-monitor"></a>Azure 监视器中的指标警报

要在 Azure 监视器中创建警报，请执行以下计划：

1. 选择您在连接监视器（预览）中创建的连接监视器资源。
1. 确保**指标**显示为连接监视器的信号类型。
1. 在 **"添加条件**"中 **，选择****"检查失败百分比（预览）"** 或 **"往返时间"（预览）。**
1. 对于**信号类型**，请选择**指标**。 例如，选择 **"检查失败百分比"（预览）。**
1. 列出了指标的所有维度。 选择维度名称和维度值。 例如，选择 **"源地址**"，然后在连接监视器中输入任何源的 IP 地址。
1. 在**警报逻辑**中，填写以下详细信息：
   * **条件类型**：**静态**。
   * **条件和****阈值**。
   * **聚合粒度和评估频率**：连接监视器（预览）每分钟更新数据。
1. 在 **"操作"** 中，选择您的行动组。
1. 提供警报详细信息。
1. 创建警报规则。

   ![显示 Azure 监视器中的"创建规则区域"的屏幕截图;突出显示"源地址"和"源终结点名称"](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>诊断网络中的问题

连接监视器（预览）可帮助您诊断连接监视器和网络中的问题。 您之前安装的日志分析代理检测到混合网络中的问题。 网络观察程序扩展检测到 Azure 中的问题。 

您可以在网络拓扑中查看 Azure 网络中的问题。

对于源为本地 VM 的网络，可以检测到以下问题：

* 请求已超时。
* 未通过 DNS 解析的终结点 = 临时或持久性。 URL 无效。
* 未找到主机。
* 源无法连接到目标。 目标无法通过ICMP到达。
* 证书相关问题： 
    * 验证代理所需的客户端证书。 
    * 无法访问证书重定位列表。 
    * 终结点的主机名与证书的主题或主题备用名称不匹配。 
    * 根证书在源的本地计算机受信任的证书颁发机构存储中丢失。 
    * SSL 证书已过期、无效、吊销或不兼容。

对于源为 Azure VM 的网络，可以检测到以下问题：

* 代理问题：
    * 代理已停止。
    * DNS 解析失败。
    * 在目标端口上没有应用程序或侦听器。
    * 无法打开套接字。
* VM 状态问题： 
    * 正在启动
    * 正在停止
    * 已停止
    * 正在解除分配
    * 已解除分配
    * 重 启
    * 未分配
* 缺少 ARP 表条目。
* 由于本地防火墙问题或 NSG 规则，流量被阻止。
* 虚拟网络网关问题： 
    * 缺少路由。
    * 两个网关之间的隧道已断开连接或丢失。
    * 隧道没有找到第二个网关。
    * 未找到对等信息。
* 在微软边缘中缺少路由。
* 由于系统路由或 UDR 而停止流量。
* 网关连接上未启用 BGP。
* DIP 探头位于负载平衡器处。
