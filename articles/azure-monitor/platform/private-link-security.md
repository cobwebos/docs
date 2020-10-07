---
title: 使用 Azure 专用链接将网络安全地连接到 Azure Monitor
description: 使用 Azure 专用链接将网络安全地连接到 Azure Monitor
author: nkiest
ms.author: nikiest
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: 0c7838b291ca5ba1747b08d7e8fcc6d17cc35f7d
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802219"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>使用 Azure 专用链接将网络安全地连接到 Azure Monitor

通过 [Azure 专用链接](../../private-link/private-link-overview.md)，可使用专用终结点将 Azure PaaS 服务安全地链接到你的虚拟网络。 对于很多服务，一个资源只需设置一个终结点即可。 不过，Azure Monitor 是不同互连服务的集合，它们相互协同，共同监视你的工作负载。 因此，我们构建了一个名为 Azure Monitor 专用链接范围 (AMPLS) 的资源，你可用它来定义你的监视网络的边界并连接到你的虚拟网络。 本文介绍何时使用以及如何设置 Azure Monitor 专用链接范围。

## <a name="advantages"></a>优势

借助专用链接，你可以：

- 以专用方式连接到 Azure Monitor，无需开放任何公共网络访问权限
- 确保仅可通过授权的专用网络访问你正在监视的数据
- 定义通过你的专用终结点连接的特定 Azure Monitor 资源，从而防止你的专用网络出现数据泄露的情况
- 使用 ExpressRoute 和专用链接将你的专用本地网络安全地连接到 Azure Monitor
- 将所有流量保留在 Microsoft Azure 主干网络中

有关详细信息，请参阅[专用链接的主要优势](../../private-link/private-link-overview.md#key-benefits)。

## <a name="how-it-works"></a>工作原理

Azure Monitor 专用链接范围是一项分组资源，它将一个或多个专用终结点（因此还有包含它们的虚拟网络）连接到一个或多个 Azure Monitor 资源。 这些资源包含 Log Analytics 工作区和 Application Insights 组件。

![资源拓扑关系图](./media/private-link-security/private-link-topology-1.png)

> [!NOTE]
> 一个 Azure Monitor 资源可属于多个 AMPLS，但无法将一个 VNet 连接到多个 AMPLS。 

## <a name="planning-based-on-your-network"></a>根据自身网络进行规划

设置 AMPLS 资源之前，请考虑自身的网络隔离要求。 评估你的虚拟网络对公共 Internet 的访问权限，以及每项 Azure Monitor 资源（即 Application Insights 组件和 Log Analytics 工作区）的访问限制。

### <a name="evaluate-which-virtual-networks-should-connect-to-a-private-link"></a>评估哪些虚拟网络应连接到专用链接

首先评估你的哪些虚拟网络 (VNet) 对 Internet 的访问受到限制。 具有免费 Internet 的 VNet 可能不需要专用链接即可访问你的 Azure Monitor 资源。 VNet 连接到的监视资源可能会显示传入流量并要求专用链接连接（用于日志引入或查询）。 在这种情况下，即使是有权访问公共 Internet 的 VNet 也需要通过专用链接和 AMPLS 连接到这些资源。

### <a name="evaluate-which-azure-monitor-resources-should-have-a-private-link"></a>评估哪些 Azure Monitor 资源应具有专用链接

评审每项 Azure Monitor 资源：

- 资源是否应仅允许从特定 VNet 上的资源引入日志？
- 是否应仅由特定 VNet 上的客户端查询资源？

如果上述任一问题的答案为“是”，那么请根据[配置 Log Analytics](#configure-log-analytics) 工作区和[配置 Application Insights 组件](#configure-application-insights)中的说明设置限制，并将这些资源关联到一个或多个 AMPLS。 应访问这些监视资源的虚拟网络需要具有连接到相关 AMPLS 的专用终结点。
请记住，可将相同的工作区或应用程序连接到多个 AMPLS，让它们可通过不同的网络进行访问。

### <a name="group-together-monitoring-resources-by-network-accessibility"></a>按网络可访问性将监视资源组合在一起

由于每个 VNet 只能连接到一个 AMPLS 资源，因此你必须将应可由相同网络访问的监视资源组合在一起。 要管理该分组，最简单的方式是对每个 VNet 创建一个 AMPLS，并选择要连接到该网络的资源。 然而，为了减少资源和提高可管理性，你可能需要在不同网络中重复使用 AMPLS。

例如，如果内部虚拟网络 VNet1 和 VNet2 应连接到工作区 Workspace1 和 Workspace2，还应连接到 Application Insights 组件“Application Insights 3”，则需将这三个资源关联到同一个 AMPLS。 如果 VNet3 只应访问 Workspace1，需再创建一个 AMPLS 资源，将 Workspace1 关联给它，并按下图所示连接 VNet3：

![AMPLS A 拓扑关系图](./media/private-link-security/ampls-topology-a-1.png)

![AMPLS B 拓扑关系图](./media/private-link-security/ampls-topology-b-1.png)

### <a name="consider-limits"></a>考虑限制

在规划专用链接设置时，应该考虑以下几个限制：

* VNet 只能连接到1个 AMPLS 对象。 这意味着，AMPLS 对象必须提供对 VNet 应有权访问的所有 Azure Monitor 资源的访问权限。
* Azure Monitor 资源 (工作区或 Application Insights 组件) 最多可以连接到5个 AMPLSs。
* AMPLS 对象最多可连接到 50 Azure Monitor 资源。
* AMPLS 对象最多可连接到10个私有终结点。

在下面的拓扑中：
* 每个 VNet 连接到1个 AMPLS 对象，因此它无法连接到其他 AMPLSs。
* AMPLS B 连接到 2 Vnet：使用2/10 的可能的专用终结点连接。
* AMPLS A 连接到2个工作区和1个 Application insights 组件：使用3/50 的可能 Azure Monitor 资源。
* 工作区2连接到 AMPLS A 和 AMPLS B：使用2/5 的可能的 AMPLS 连接。

![AMPLS 限制关系图](./media/private-link-security/ampls-limits.png)

## <a name="example-connection"></a>示例连接

首先创建一个 Azure Monitor 专用链接范围资源。

1. 转到 Azure 门户中的“创建资源”，并搜索“Azure Monitor 专用链接范围” 。

   ![查找 Azure Monitor 专用链接范围](./media/private-link-security/ampls-find-1c.png)

2. 单击“创建”。
3. 选择一个订阅和资源组。
4. 为 AMPLS 指定名称。 最好使用明确指出该范围将用于哪个用途和安全边界的名称，防止有人意外中断网络安全边界。 例如，使用名称“AppServerProdTelem”。
5. 单击“查看 + 创建”。 

   ![创建 Azure Monitor 专用链接范围](./media/private-link-security/ampls-create-1d.png)

6. 让验证通过，然后单击“创建”。

## <a name="connect-azure-monitor-resources"></a>连接 Azure Monitor 资源

可先将 AMPLS 连接到专用终结点，然后连接到 Azure Monitor 资源（或者反向操作），但如果先连接 Azure Monitor 资源，连接过程会更快完成。 下面介绍了如何将 Azure Monitor Log Analytics 工作区和 Application Insights 组件连接到 AMPLS

1. 在 Azure Monitor 专用链接范围中，单击左侧菜单中的“Azure Monitor 资源”。 单击“添加”按钮。
2. 添加工作区或组件。 单击“添加”按钮将调出一个对话框，你可在这里选择 Azure Monitor 资源。 你可浏览订阅和资源组，也可键入其名称通过筛选找到它们。 选择工作区或组件，然后单击“应用”，让它们添加到范围中。

    ![显示选择范围 UX 的屏幕截图](./media/private-link-security/ampls-select-2.png)

### <a name="connect-to-a-private-endpoint"></a>连接到专用终结点

你现已将资源连接到 AMPLS，接下来可创建一个专用终结点来连接我们的网络。 可在 [Azure 门户专用链接中心](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)或 Azure Monitor 专用链接范围中执行此任务，如下例所示。

1. 在范围资源中，单击左侧资源菜单中的“专用终结点连接”。 单击“专用终结点”，启动终结点创建进程。 你还可批准在专用链接中心启动的连接，方式是将其选择并单击“批准”。

    ![专用终结点连接 UX 的屏幕截图](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. 选择订阅、资源组、终结点的名称及其应位于的区域。 该区域必须是你要连接到的虚拟网络所在的区域。

3. 单击“下一步:资源”。 

4. 在“资源”屏幕中，

   a. 选择包含你的 Azure Monitor 专用范围资源的订阅。 

   b. 对于“资源类型”，选择“Microsoft.insights/privateLinkScopes” 。 

   c. 从“资源”下拉列表中，选择你之前创建的专用连接范围。 

   d. 单击“下一步:配置 >”。
      ![显示选择“创建专用终结点”的屏幕截图](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. 在“配置”窗格中，

   a.    选择要连接到 Azure Monitor 资源的虚拟网络和子网 。 
 
   b.    针对“与专用 DNS 区域集成”选择“是”，让它自动新建一个专用 DNS 区域 。 实际 DNS 区域可能不同于下面屏幕截图中显示的内容。 
 
   c.    单击“查看 + 创建”。
 
   d.    让验证通过。 
 
   e.    单击“创建”。 

    ![显示选择“创建专用终结点 2”的屏幕截图](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

你现创建了一个新的专用终结点，它与这个 Azure Monitor 专用链接范围相连。

## <a name="configure-log-analytics"></a>配置 Log Analytics

转到 Azure 门户。 在 Log Analytics 工作区资源的左侧有一个菜单项 " **网络隔离** "。 你可通过此菜单控制两种不同的状态。 

![LA 网络隔离](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

首先，可将该 Log Analytics 资源连接到你有权访问的任意 Azure Monitor 专用链接范围。 单击“添加”并选择 Azure Monitor 专用链接范围。  单击“应用”进行连接。 所有已连接的范围都显示在此屏幕中。 建立此连接后，已连接的虚拟网络中的网络流量可到达此工作区。 建立此连接的效果与我们在[连接 Azure Monitor 资源](#connect-azure-monitor-resources)中所做的从范围连接它的效果一样。  

其次，你能控制可如何从上述专用链接范围外部访问该资源。 如果将“允许公用网络访问以便执行引入”设置为“否”，则已连接的范围之外的计算机无法将数据上传到此工作区中 。 如果将“允许公用网络访问以便执行查询”设置为“否”，则范围之外的计算机无法访问此工作区中的数据 。 该数据包括访问工作簿、仪表板、基于查询 API 的客户端体验、Azure 门户中的见解等等。 在 Azure 门户外运行的体验，还必须在专用链接的 VNET 中运行查询 Log Analytics 数据。

以这种方式限制访问权限不适用于 Azure 资源管理器，因此具有以下限制：
* 对数据的访问-阻止来自公共网络的查询适用于大多数 Log Analytics 体验，一些经验通过 Azure 资源管理器查询数据，因此将无法查询数据，除非将专用链接设置应用到 (资源管理器，不久就会) 该功能。 例如，Azure Monitor 解决方案、工作簿和见解以及逻辑应用连接器。
* 工作区管理-工作区设置和配置更改 (包括打开或关闭这些访问设置) 由 Azure 资源管理器管理。 使用适当的角色、权限、网络控制和审核限制对工作区管理的访问。 有关详细信息，请参阅 [Azure Monitor 角色、权限和安全性](roles-permissions-security.md)。

> [!NOTE]
> 通过[诊断设置](diagnostic-settings.md)上传到工作区的日志和指标会通过安全的专用 Microsoft 通道，且不受这些设置控制。

### <a name="log-analytics-solution-packs-download"></a>Log Analytics 解决方案包下载

若要使 Log Analytics 代理能够下载解决方案包，请在防火墙允许列表中添加适当的完全限定的域名。 


| 云环境 | 代理资源 | 端口 | 方向 |
|:--|:--|:--|:--|
|Azure Public     | scadvisorcontent.blob.core.windows.net         | 443 | 出站
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  出站
|Azure 中国世纪互联      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | 出站

## <a name="configure-application-insights"></a>配置 Application Insights

转到 Azure 门户。 在 Azure Monitor 中，Application Insights 组件资源是左侧的“网络隔离”菜单项。 你可通过此菜单控制两种不同的状态。

![AI 网络隔离](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

首先，可将该 Application Insights 资源连接到你有权访问的 Azure Monitor 专用链接范围。 单击“添加”并选择 Azure Monitor 专用链接范围 。 单击“应用”进行连接。 所有已连接的范围都显示在此屏幕中。 建立此连接后，已连接的虚拟网络中的网络流量可到达此组件。 建立此连接的效果与我们在[连接 Azure Monitor 资源](#connect-azure-monitor-resources)中所做的从范围连接它的效果一样。 

其次，你能控制可如何从之前列出的专用链接范围外部访问该资源。 如果将“允许公用网络访问以便执行引入”设置为“否”，则已连接的范围之外的计算机或 SDK 无法将数据上传到此组件中 。 如果将“允许公用网络访问以便执行查询”设置为“否”，则范围之外的计算机无法访问该 Application Insights 资源中的数据 。 该数据包括访问 APM 日志、指标和实时指标流、以及根据工作簿、仪表板、基于查询 API 的客户端体验和 Azure 门户中的见解等构建的体验等等。 

请注意，不使用门户的体验也必须在包含受监视工作负载的专用链接的 VNET 中运行。 

需要将托管受监视工作负载的资源添加到专用链接中。 可在此[文档](../../app-service/networking/private-endpoint.md)中了解如何对应用服务执行此操作。

仅可以此方式限制对 Application Insights 资源中数据的访问。 配置更改（例如打开或关闭这些访问设置）由 Azure 资源管理器进行管理。 转而使用适当的角色、权限、网络控件和审核来限制对资源管理器的访问。 有关详细信息，请参阅 [Azure Monitor 角色、权限和安全性](roles-permissions-security.md)。

> [!NOTE]
> 若要完全保护基于工作区的 Application Insights，需要锁定对 Application Insights 资源和基础 Log Analytics 工作区的访问。
>
> 代码级诊断 (探查器/调试器) 需要你提供自己的存储帐户以支持专用链接。 下面是有关如何执行此操作的 [文档](../app/profiler-bring-your-own-storage.md) 。

## <a name="use-apis-and-command-line"></a>使用 API 和命令行

可使用 Azure 资源管理器模板和命令行接口将前面描述的过程自动化。

若要创建和管理专用链接范围，请使用 [az monitor private-link-scope](/cli/azure/monitor/private-link-scope?view=azure-cli-latest)。 通过该命令可创建范围、关联 Log Analytics 工作区和 Application Insights 组件，还可添加/删除/批准专用终结点。

要管理网络访问，请在 [Log Analytics 工作区](/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest)或 [Application Insights 组件](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest)上使用 `[--ingestion-access {Disabled, Enabled}]` 和 `[--query-access {Disabled, Enabled}]` 标志。

## <a name="collect-custom-logs-over-private-link"></a>通过专用链接收集自定义日志

在自定义日志的引入过程中会使用存储帐户。 默认使用服务托管的存储帐户。 但若要通过专用链接引入自定义日志，必须使用你自己的存储帐户并将其与 Log Analytics 工作区关联。 请查看更多详细信息，了解如何使用[命令行](/cli/azure/monitor/log-analytics/workspace/linked-storage?view=azure-cli-latest)设置此类帐户。

要详细了解如何使用你自己的存储帐户，请参阅[使用客户拥有的存储帐户引入日志](private-storage.md)

## <a name="restrictions-and-limitations"></a>限制和局限

### <a name="agents"></a>代理

必须在专用网络上使用最新版本的 Windows 和 Linux 代理，才能启用到 Log Analytics 工作区的安全引入。 旧版本无法在专用网络上加载监视数据。

**Log Analytics Windows 代理**

使用 Log Analytics 代理版本10.20.18038.0 或更高版本。

**Log Analytics Linux 代理**

使用代理版本 1.12.25 或更高版本。 如果没法做法，请在 VM 上运行以下命令。

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure 门户

要使用 Azure Monitor 门户体验（例如 Application Insights 和 Log Analytics），你需要使 Azure 门户和 Azure Monitor 扩展能在专用网络上进行访问。 将 **AzureActiveDirectory**、 **AzureResourceManager**、 **AzureFrontDoor**和 **AzureFrontDoor** [服务标记](../../firewall/service-tags.md) 添加到防火墙。

### <a name="programmatic-access"></a>以编程方式访问

要在专用网络上将 REST API、[CLI](/cli/azure/monitor?view=azure-cli-latest) 或 PowerShell 与 Azure Monitor 结合使用，请在防火墙中添加 AzureActiveDirectory 和 AzureResourceManager [服务标记](../../virtual-network/service-tags-overview.md) 。

通过添加这些标记，可执行查询日志数据、创建和管理 Log Analytics 工作区及 AI 组件等操作。

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>从内容分发网络下载 Application Insights SDK

在脚本中捆绑 JavaScript 代码，让浏览器不尝试从 CDN 中下载代码。 有关示例，可查看 [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>浏览器 DNS 设置

如果要通过专用链接连接到 Azure Monitor 资源，则必须通过在网络上配置的专用终结点完成到这些资源的通信。 若要启用专用终结点，请根据 [连接到专用终结点](#connect-to-a-private-endpoint)中所述更新 DNS 设置。 某些浏览器使用自己的 DNS 设置，而不是设置的设置。 浏览器可能会尝试连接到 Azure Monitor 公用终结点，并完全绕过专用链接。 验证你的浏览器设置不会替代或缓存旧的 DNS 设置。 

## <a name="next-steps"></a>后续步骤

- 了解 [专用存储](private-storage.md)
