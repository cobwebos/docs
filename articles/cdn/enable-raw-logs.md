---
title: 监视指标和 Microsoft Azure CDN 的原始日志
description: 本文介绍了 Microsoft 监视指标和原始日志中的 Azure CDN。
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/25/2020
ms.author: allensu
ms.openlocfilehash: c41bf8bc6e5aa3749786bc1189343dfdebdc1508
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321108"
---
# <a name="monitoring-metrics-and-raw-logs-for-azure-cdn-from-microsoft"></a>监视指标和 Microsoft Azure CDN 的原始日志
利用 Microsoft 提供的 Azure CDN，你可以通过以下方式监视资源，以帮助你排查、跟踪和调试问题。 

* 原始日志提供有关 CDN 接收的每个请求的丰富信息。 原始日志不同于活动日志。 活动日志让你能够了解对 Azure 资源执行的操作。
* 度量值，它在 CDN 上显示四个关键指标，包括字节命中率、请求计数、响应大小和总滞后时间。 它还提供不同的维度来细分指标。
* 警报，允许客户设置关键指标的警报
* 其他指标，可让客户使用 Azure Log Analytics 来启用其他指标值。 我们还提供了 Azure Log Analytics 下几个其他指标的查询示例。

> [!IMPORTANT]
> HTTP 原始日志功能适用于 Microsoft Azure CDN。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="configuration---azure-portal"></a>配置-Azure 门户

为 Microsoft 配置文件中的 Azure CDN 配置原始日志： 

1. 从 "Azure 门户" 菜单中，选择 "**所有资源**"  >>  **\<your-CDN-profile>** 。

2. 在“监视”下，选择“诊断设置” 。

3. 选择“+ 添加诊断设置”。

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="添加 CDN 配置文件的诊断设置。" border="true":::
    
    > [!IMPORTANT]
    > 原始日志仅在配置文件级别提供，而聚合的 http 状态代码日志在终结点级别提供。

4. 在“诊断设置”下，在“诊断设置名称”下输入诊断设置的名称 。

5. 选择 **AzureCdnAccessLog** 并设置保留天数。

6. 选择“目标详细信息”。 目标选项有：
    * 发送到 Log Analytics
        * 选择“订阅”和“Log Analytics 工作区” 。
    * 存档到存储帐户
        * 选择“订阅”和“存储帐户” 。
    * 流式传输到事件中心
        * 选择“订阅”、“事件中心命名空间”、“事件中心名称（可选）”和“事件中心策略名称”   。

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="添加 CDN 配置文件的诊断设置。" border="true":::

7. 选择“保存”。

## <a name="configuration---azure-powershell"></a>配置-Azure PowerShell

使用 [AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) 配置原始日志的诊断设置。

保留数据由命令中的 **-RetentionInDays** 选项定义。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>在存储帐户中启用诊断日志

1. 登录到 Azure PowerShell：

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. 若要在存储帐户中启用诊断日志，请输入以下命令。 将变量替换为值：

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>为 Log Analytics 工作区启用诊断日志

1. 登录到 Azure PowerShell：

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. 若要为 Log Analytics 工作区启用诊断日志，请输入以下命令。 将变量替换为值：

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>为事件中心命名空间启用诊断日志

1. 登录到 Azure PowerShell：

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. 若要为事件中心命名空间启用诊断日志，请输入以下命令。 将变量替换为值：

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>原始日志属性

Microsoft 服务中的 Azure CDN 当前提供原始日志。 原始日志提供单个 API 请求，其中每个条目具有以下架构： 

| properties              | 说明                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | 标识由 Front Door 提供的请求的唯一引用字符串，该请求还会以 X-Azure-Ref 标头的形式发送到客户端。 是搜索特定请求访问日志中的详细信息必需的。 |
| HttpMethod            | 请求使用的 HTTP 方法。                                                                                                                                                                     |
| HttpVersion           | 请求或连接的类型。                                                                                                                                                                   |
| RequestUri            | 已收到请求的 URI。                                                                                                                                                                         |
| RequestBytes          | HTTP 请求消息的大小（以字节为单位），包括请求标头和请求正文。                                                                                                   |
| ResponseBytes         | 后端服务器作为响应发送的字节数。                                                                                                                                                    |
| UserAgent             | 客户端使用的浏览器类型。                                                                                                                                                               |
| ClientIp              | 发出请求的客户端的 IP 地址。                                                                                                                                                  |
| TimeTaken             | 操作所用的时间长度（以毫秒为单位）。                                                                                                                                            |
| SecurityProtocol      | 请求所使用的 TLS/SSL 协议版本，如果没有加密，则为 null。                                                                                                                           |
| 端点              | CDN 终结点主机已在父 CDN 配置文件下进行配置。                                                                                                                                   |
| 后端主机名称     | 发送请求的后端主机或源的名称。                                                                                                                                |
| 发送到源防护 </br>  (弃用) * **参阅下面的弃用说明。** | 如果为 true，则表示请求是从源防护缓存（而不是边缘 pop）响应的。 源防护是用于提高缓存命中率的父缓存。                                       |
| isReceivedFromClient | 如果为 true，则表示请求来自客户端。 如果为 false，则请求在边缘 (子 POP) ，并从源盾牌 (父 POP) 中进行响应。 
| HttpStatusCode        | 从代理返回的 HTTP 状态代码。                                                                                                                                                        |
| HttpStatusDetails     | 请求的结果状态。 此字符串值的含义可以在状态引用表中找到。                                                                                              |
| Pop                   | 响应用户请求的边缘 pop。 POP 的缩写是其各自大城市的机场代码。                                                                                   |
| 缓存状态          | 指示对象是从缓存中返回还是来自源。                                                                                                             |
> [!NOTE]
> 可以通过运行查询来查看 Log Analytics 配置文件下的日志。 示例查询如下所示：
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>已发送到源防护板弃用
原始日志属性 **isSentToOriginShield** 已弃用，并已替换为新的字段 **isReceivedFromClient**。 如果已在使用已弃用的字段，请使用新字段。 

原始日志包括从 CDN 边缘 (子 POP) 和源盾牌生成的日志。 源盾牌是指在全球上战略定位的父节点。 这些节点与源服务器通信，并减少源上的流量负载。 

对于进入源盾牌的每个请求，都有2个日志条目：

* 一个用于边缘节点
* 一个用于源盾牌。 

若要区分来自边缘节点与源盾牌的出口或响应，可以使用字段 **isReceivedFromClient** 来获取正确的数据。 

如果该值为 false，则表示该请求将从源盾牌响应到边缘节点。 此方法可用于将原始日志与计费数据进行比较。 从来源盾牌出口到边缘节点不会产生费用。 从边缘节点向客户端出口会产生费用。 

**Kusto 查询示例，排除在 Log Analytics 中的源盾牌上生成的日志。**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> 2020 年 2 月 25 日之后创建或更新的任何配置文件可自动使用 HTTP 原始日志功能。 对于之前创建的 CDN 配置文件，应在设置日志记录后更新 CDN 终结点。 例如，可以导航到 CDN 终结点下的地区筛选，并阻止任何与工作负荷无关的国家/地区，并按保存。


## <a name="metrics"></a>指标
Microsoft Azure CDN 与 Azure Monitor 集成，并发布四个 CDN 指标来帮助跟踪、排查和调试问题。 

度量值显示在图表中，并可通过 PowerShell、CLI 和 API 进行访问。 CDN 指标是免费的。

从 Microsoft 度量值 Azure CDN，并以60秒为间隔发送指标。 此指标最多可能需要3分钟才能在门户中显示。 

有关详细信息，请参阅 [Azure Monitor 指标](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)。

**Microsoft Azure CDN 支持的指标**

| 指标         | 说明                                                                                                      | 维度                                                                                   |
|-----------------|------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| 字节命中率 * | 针对传出总数计算的 CDN 缓存中的出口百分比。                                      | 端点                                                                                    |
| RequestCount    | CDN 提供的客户端请求数。                                                                     | 端点 </br> 客户端国家/地区。 </br> 客户端区域。 </br> HTTP 状态。 </br> HTTP 状态组。 |
| ResponseSize    | 从 CDN 边缘到客户端的响应发送的字节数。                                                  |端点 </br> 客户端国家/地区。 </br> 客户端区域。 </br> HTTP 状态。 </br> HTTP 状态组。                                                                                          |
| TotalLatency    | CDN 从 **cdn 发送到客户端之前**，CDN 收到的客户端请求的总时间。 |端点 </br> 客户端国家/地区。 </br> 客户端区域。 </br> HTTP 状态。 </br> HTTP 状态组。                                                                                             |

***Bytes r = 从起源) /egress 的边缘出口 (出口**

排除的方案数（按字节数计算）：

* 不通过规则引擎或查询字符串缓存行为显式配置缓存。
* 显式配置缓存控制指令和无存储或专用缓存。

### <a name="metrics-configuration"></a>指标配置

1. 从 "Azure 门户" 菜单中，选择 "**所有资源**"  >>  **\<your-CDN-profile>** 。

2. 在 " **监视**" 下，选择 " **指标**"：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="添加 CDN 配置文件的诊断设置。" border="true":::

3. 选择 " **添加度量**值"，然后选择要添加的度量值：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="添加 CDN 配置文件的诊断设置。" border="true":::

4. 选择 " **添加筛选** 器" 以添加筛选器：
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="添加 CDN 配置文件的诊断设置。" border="true":::

5. 选择 " **应用** 拆分" 以查看不同维度的趋势：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="添加 CDN 配置文件的诊断设置。" border="true":::

6. 选择 " **新建图表** " 以添加新图表：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="添加 CDN 配置文件的诊断设置。" border="true":::

### <a name="alerts"></a>警报

可以通过选择 "**监视**警报" 在 Microsoft CDN 上设置警报  >>  **Alerts**。

为 "度量值" 部分中列出的指标选择 **新的警报规则** ：

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="添加 CDN 配置文件的诊断设置。" border="true":::

将根据 Azure Monitor 对警报进行收费。 有关警报的详细信息，请参阅 [Azure Monitor 警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)。

### <a name="additional-metrics"></a>其他指标
可以使用 Azure Log Analytics 和原始日志启用其他度量值，以实现额外的费用。

1. 按照上述步骤启用诊断，将原始日志发送到 log analytics。

2. 选择创建的 Log Analytics 工作区：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="添加 CDN 配置文件的诊断设置。" border="true":::   

3. 在 log analytics 工作区中，选择 "**常规**" 下的 "**日志**"。  然后选择 " **开始**"：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="添加 CDN 配置文件的诊断设置。" border="true":::   
 
4. 选择 " **CDN 配置文件**"。  选择一个示例查询以运行或关闭示例屏幕以输入自定义查询：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="添加 CDN 配置文件的诊断设置。" border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="添加 CDN 配置文件的诊断设置。" border="true":::   

4. 若要按图表查看数据，请选择 " **图表**"。  选择 " **固定到仪表板** "，将图表固定到 Azure 仪表板：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="添加 CDN 配置文件的诊断设置。" border="true"::: 

## <a name="next-steps"></a>后续步骤
本文介绍了如何为 Microsoft CDN 服务启用 HTTP 原始日志。

有关 Azure CDN 和本文中所述的其他 Azure 服务的详细信息，请参阅：

* [分析](cdn-log-analysis.md) Azure CDN 使用模式。

* 详细了解 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)。

* 配置 [Azure Monitor 中的 Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。
