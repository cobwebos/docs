---
title: Log Analytics 常见问题解答 | Microsoft Docs
description: 有关 Azure Log Analytics 服务的常见问题解答。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 5e3ef7782f805e07e24783a987f2753e4e889946
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313027"
---
# <a name="log-analytics-faq"></a>Log Analytics 常见问题解答
此 Microsoft 常见问题解答是有关 Microsoft Azure 中 Log Analytics 的常见问题列表。 如果有与 Log Analytics 有关的任何其他问题，请转到[论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)并发布问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。


## <a name="new-logs-experience"></a>新的日志体验

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>问：新的日志体验和 Log Analytics 的区别在哪里？

答：没有区别。 [Log Analytics 是作为一项功能集成到 Azure Monitor 中的](../../azure-monitor/azure-monitor-rebrand.md)，目的是提供更统一的监视体验。 Azure Monitor 中的新的日志体验与许多客户一直在使用的 Log Analytics 查询完全相同。

### <a name="q-can-i-still-use-log-search"></a>问：是否仍然可以使用日志搜索？ 

答：日志搜索目前仍可在 OMS 门户和 Azure 门户中使用，采用的名称是“日志(经典)”。 OMS 门户将在 2019 年 1 月 15 日正式停用。 Azure 门户中的经典日志体验将会逐渐停用并被新的日志体验代替。 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>问： 是否仍然能够使用高级分析门户？ 
Azure 门户中的新的日志体验基于高级分析门户，但是仍然可以在 Azure 门户之外访问。 停用此外部门户的路线图将很快宣布。

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>问： 为何在新的日志体验中看不到“查询资源管理器”按钮和“保存”按钮？

在特定资源的上下文中浏览日志时，“查询资源管理器”、“保存”和“设置警报”按钮不可用。 若要创建警报以及保存或加载查询，日志的范围必须局限于某个工作区。 若要在工作区上下文中打开日志，请选择“所有服务” > “监视” > “日志”。 选择的是上次使用的工作区，但可以选择任何其他的工作区。 有关详细信息，请参阅[查看和分析 Log Analytics 中的数据](../log-query/portals.md)。

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>问： 在新的日志体验中，如何提取自定义字段？ 

答：经典日志体验目前支持自定义字段提取。 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>问： 在新日志中，何处可以找到“列表”视图？ 

答：新日志中没有“列表”视图可用。 在结果表中，每条记录的左侧有一个箭头。 单击此箭头可以打开特定记录的详细信息。 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>问： 运行查询后，可以使用建议的筛选器列表。 如何才能查看筛选器？ 

答：单击左侧平移工具上的“筛选器”可查看新筛选器实现的预览。 它现在是基于你的完整结果集，没有 UI 的 10,000 记录的限制。 它目前列出了最常用的筛选器以及每个筛选器的 10 个最常用值。 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>问： 为什么从 VM 钻取进去后，我会在日志中发现以下错误：“Register resource provider 'Microsoft.Insights' for this subscription to enable this query”（请为此订阅注册资源提供程序“Microsoft.Insights”以启用此查询）？ 

答：默认情况下，会自动注册多个资源提供程序；但是，可能需要手动注册某些资源提供程序。 这样可以配置订阅，使之适合资源提供程序。 注册的作用域始终是订阅。 有关详细信息，请参阅[资源提供程序和类型](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal)。

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>问： 为什么在从 VM 页访问日志时，会出现“无法访问”错误消息？ 

答：若要查看 VM 日志，需获得存储 VM 日志的工作区的读取权限。 在这些示例中，管理员必须在 Azure 中为你授予相关权限。

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>问： 为什么我可以访问 OMS 门户中的工作区，但在 Azure 门户中却获得“你没有访问权限”错误？  

答：若要访问 Azure 中的工作区，必须获得 Azure 权限。 在某些情况下，你可能没有相应的访问权限。 在这些示例中，管理员必须在 Azure 中为你授予相关权限。有关详细信息，请参阅[从 OMS 门户转到 Azure](oms-portal-transition.md)。

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>问： 为什么在日志中看不到“视图设计器”条目？ 
答：视图设计器在日志中仅供分配了“参与者”权限或更高权限的用户使用。

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>问： 我仍可以在 Azure 之外使用分析门户吗？
A. 可以，Azure 中的“日志”页和高级分析门户基于相同的代码。 Log Analytics 作为一项功能集成到 Azure Monitor 中，目的是提供更统一的监视体验。 仍可以使用以下 URL 访问分析门户： https://portal.loganalytics.io/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/workspaces/{workspaceName}。



## <a name="general"></a>常规

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>问： 如何在 Azure 门户中查看视图和解决方案？ 

答：可以在 Azure 门户中查看视图和已安装解决方案的列表。 单击“所有服务”。 在资源列表中，选择“监视”，然后单击“...更多”。 选择的是上次使用的工作区，但可以选择任何其他的工作区。 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>问： 为何无法在“美国中西部”区域创建工作区？ 

答：此区域暂时有容量限制。 计划在 2019 年上半年解决此限制问题。


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>问： Log Analytics 是否与 Azure 安全中心使用同一代理？

答：2017 年 6 月初，Azure 安全中心开始使用 Microsoft Monitoring Agent 收集和存储数据。 要了解详细信息，请参阅 [Azure 安全中心平台迁移常见问题解答](../../security-center/security-center-enable-data-collection.md)。

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>问： AD 和 SQL 评估解决方案会执行哪些检查？

答：以下查询显示了当前执行的所有检查的说明：

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

可以将结果导出到 Excel，以便深入查看。

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>问： 为何我在 System Center Operations Manager 控制台中看到了不同于 OMS 的内容？

答：你可能会看到*系统中心顾问*、*操作见解*或 *Log Analytics* 节点，具体取决于所使用的 Operations Manager 更新汇总。

对 *OMS* 进行的文本字符串更新包含在一个管理包中，需要你手动导入。 若想看到最新文本和功能，请按照最新 System Center Operations Manager 更新汇总知识库文章中的说明进行操作并刷新控制台。

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>问：Log Analytics 是否有本地版本？

答：不是。 Log Analytics 是处理和存储大量数据的可缩放云服务。 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>问： 如何排除 Log Analytics 不再收集数据这一故障？

答：对于在 2018 年 4 月 2 日之前在“免费”定价层上创建的订阅和工作区，如果在一天中发送的数据超过 500 MB，则在当天的剩余时间内会停止数据收集。 达到每日限制是 Log Analytics 停止数据收集或者看起来缺少数据的常见原因。  

Log Analytics 创建类型为“Heartbeat”的事件，并可用于确定数据收集是否停止。 

请在搜索中运行以下查询来检查是否已达到每日限制并缺少数据：`Heartbeat | summarize max(TimeGenerated)`

若要检查特定计算机，可运行以下查询：`Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`

当数据收集停止时，根据所选的时间范围，将看不到任何返回的记录。   

下表描述了数据收集停止的原因以及用于恢复数据收集的建议操作：

| 数据收集停止的原因                       | 恢复数据收集需执行的操作 |
| -------------------------------------------------- | ----------------  |
| 达到免费数据限制<sup>1</sup>       | 等到下一月收集自动重新启动，或者<br> 更改为付费定价层 |
| Azure 订阅由于以下原因处于挂起状态： <br> 免费试用已结束 <br> Azure 许可已过期 <br> 已达到每月支出限制（例如，在 MSDN 或 Visual Studio 订阅上）                          | 转换为付费订阅 <br> 转换为付费订阅 <br> 删除限制，或者等到限制重置 |

<sup>1</sup> 如果工作区位于*免费*定价层，则限制为每天向服务发送 500 MB 数据。 达到每日限制时，数据收集将停止到下一天。 不会为在数据收集停止期间发送的数据编制索引并且该数据不可搜索。 当数据收集恢复后，将仅对发送的新数据进行处理。 

Log Analytics 使用 UTC 时间并且每天从 UTC 午夜时间开始。 如果工作区达到每日限制，则处理将在采用 UTC 时间的下一天的第一个小时恢复。

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>问： 当数据收集停止时如何可以收到通知？

答：当数据收集停止时，可以使用[新建日志警报](../../azure-monitor/platform/alerts-metric.md)中所述的步骤收到通知。

针对数据收集停止时创建警报时，请设置以下各项：

- **定义警报条件**将 Log Analytics 工作区指定为资源目标。
- **警报条件**指定下列项：
   - **信号名称**选择“自定义日志搜索”。
   - 将“搜索查询”设置为 `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **警报逻辑** **基于** *结果数*，**条件** *大于* **阈值** *0*
   - **时间段**为 *30* 分钟，并将**警报频率**设为每 *10* 分钟
- **定义警报详细信息**指定以下项：
   - 将“名称”设置为“数据收集已停止”
   - 将“严重性”设置为“警告”

指定现有的操作组或创建一个新[操作组](../../azure-monitor/platform/action-groups.md)，以便当日志警报匹配条件时，如果检测信号丢失超过 15 分钟，你将收到通知。

## <a name="configuration"></a>配置
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>问： 是否可以更改用于从 Azure 诊断 (WAD) 读取数据的表/blob 容器的名称？

A. 不可以，当前还不能从 Azure 存储中的任意表或容器进行读取。

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>问： Log Analytics 服务使用什么 IP 地址？ 如何确保我的防火墙仅允许与 Log Analytics 服务通信？

A. Log Analytics 服务是在 Azure 的基础上构建的。 Log Analytics IP 地址在 [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)内。

当进行服务部署时，Log Analytics 服务的实际 IP 地址会发生变化。 [网络要求](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements)中记录了允许穿过防火墙的 DNS 名称。

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>问： 我使用 ExpressRoute 连接到 Azure。 我的 Log Analytics 流量是否使用我的 ExpressRoute 连接？

A. 不同类型的 ExpressRoute 通信流量记录在 [ExpressRoute 文档](../../expressroute/expressroute-faqs.md#supported-services)中。

Log Analytics 通信流量使用的是公共对等 ExpressRoute 线路。

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>问： 有没有简单易用的方法将现有的 Log Analytics 工作区移到另一个 Log Analytics 工作区/Azure 订阅？

A. `Move-AzureRmResource` Cmdlet 可以用来将 Log Analytics 工作区以及自动化帐户从一个 Azure 订阅移到另一个订阅。 有关详细信息，请参阅[移动 - AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx)。

也可在 Azure 门户中进行此更改。

不能将数据从一个 Log Analytics 工作区移到另一个工作区，或更改 Log Analytics 数据所存储的区域。

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>问：如何将 Log Analytics 添加到 System Center Operations Manager？

答：更新到最新的更新汇总并导入管理包后，便可以将 Operations Manager 连接到 Log Analytics。

>[!NOTE]
>只有 System Center Operations Manager 2012 SP1 和更高版本中才提供了到 Log Analytics 的 Operations Manager 连接。

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>问：如何确认代理能够与 Log Analytics 通信？

答：若要确保代理可与 OMS 通信，请转到：“控制面板”>“安全和设置”>“Microsoft Monitoring Agent”。

在“**Azure Log Analytics (OMS)**”选项卡下，查找一个绿色复选标记。 绿色复选标记图标确认代理能够与 Azure 服务进行通信。

黄色警告图标意味着代理与 Log Analytics 存在通信问题。 一个常见原因是 Microsoft 监视代理服务已停止。 请使用服务控制管理器重启该服务。

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>问：如何使某个代理停止与 Log Analytics 通信？

答：在 System Center Operations Manager 中，从 OMS 托管的计算机列表中删除该计算机。 Operations Manager 会更新该代理的配置以便不再向 Log Analytics 进行报告。 对于直接连接到 Log Analytics 的代理，可以通过以下路径使其停止与 Log Analytics 通信：“控制面板”>“安全和设置”>“Microsoft Monitoring Agent”。
在“**Azure Log Analytics (OMS)**”选项卡下，删除列出的所有工作区。

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>问：为什么我在尝试将工作区从一个 Azure 订阅移动到另一个订阅时收到错误？

答：若要将工作区移到其他订阅或资源组，必须先取消工作区中自动化帐户的链接。 如果以下解决方案安装在工作区中，则取消自动化帐户的链接需要删除这些解决方案：删除“更新管理”、“更改跟踪”或“在空闲时间启动/停止 VM”。 删除这些解决方案后，请取消自动化帐户的链接，方法是：在自动化帐户资源的左窗格中选择“链接工作区”，然后单击功能区中的“取消链接工作区”。
 > 删除的解决方案需重新安装在工作区中，自动化帐户到工作区的链接需在移动后重新声明。

请确保在两个 Azure 订阅中都有权限。

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>问：为何我在尝试更新已保存的搜索时收到错误？

答：需要在 API 的正文中或者在 Azure 资源管理器模板属性中添加“etag”：
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>代理数据
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>问： 可以通过代理向 Log Analytics 发送多少数据？ 每个客户的数据量是否有最大限制？
A. 免费套餐设置的上限是每个工作区每天 500 MB。 标准和高级套餐对上传的数据量没有限制。 作为云服务，Log Analytics 设计为自动纵向扩展以处理来自客户的数据 – 即使每天数据量达到数万亿字节 (TB)。

Log Analytics 代理设计为确保占用较小的数据空间。 数据量因启用的解决方案而异。 在[“使用情况”](../../azure-monitor/platform/data-usage.md)页面中可以找到有关数据量的详细信息以及按解决方案列出的故障。

有关详细信息，可以阅读[客户博客](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html)，其中显示了他们在评估 OMS 代理的资源利用率后的结果。

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>问： 将数据发送到 Log Analytics 时，Microsoft 管理代理 (MMA) 使用多少网络带宽？

A. 带宽是已发送数据量的一个函数。 数据通过网络发送时会被压缩。

### <a name="q-how-much-data-is-sent-per-agent"></a>问： 每个代理发送多少数据？

A. 每个代理发送的数据量取决于：

* 已启用的解决方案
* 所收集的日志和性能计数器的数量
* 日志中的数据量

一个好的方法就是利用免费定价层来搭载数台服务器并测量典型数据量。 总体使用情况显示在 [使用情况](../../azure-monitor/platform/data-usage.md) 页。

对于能够运行 WireData 代理的计算机，可以使用以下查询了解正在发送的数据量：

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>后续步骤
* [开始使用 Log Analytics](../../azure-monitor/overview.md)，以快速了解有关 Log Analytics 的更多信息并使 Log Analytics 开始运行。
