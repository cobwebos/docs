---
title: "Log Analytics 常见问题解答 | Microsoft Docs"
description: "有关 Azure Log Analytics 服务的常见问题解答。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 0b27386cd0f9f3ae50314b8c5d7708aea3e3d028
ms.contentlocale: zh-cn
ms.lasthandoff: 09/27/2017

---
# <a name="log-analytics-faq"></a>Log Analytics 常见问题解答
此 Microsoft 常见问题解答是有关 Microsoft Azure 中 Log Analytics 的常见问题列表。 如果有与 Log Analytics 有关的任何其他问题，请转到[论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)并发布问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

## <a name="general"></a>常规

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>问： Log Analytics 是否与 Azure 安全中心使用同一代理？

A. 2017 年 6 月初，Azure 安全中心开始使用 Microsoft Monitoring Agent 收集和存储数据。 要了解详细信息，请参阅 [Azure 安全中心平台迁移常见问题解答](../security-center/security-center-platform-migration-faq.md)。

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>问： AD 和 SQL 评估解决方案会执行哪些检查？

答： 以下查询显示了当前执行的所有检查的说明：

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

可以将结果导出到 Excel，以便深入查看。

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>问：为何我在 System Center Operations Manager 控制台中看到了不同于 OMS 的内容？

答：你可能会看到*系统中心顾问*、*操作见解*或 *Log Analytics* 节点，具体取决于所使用的 Operations Manager 更新汇总。

对 *OMS* 进行的文本字符串更新包含在一个管理包中，需要你手动导入。 若想看到最新文本和功能，请按照最新 System Center Operations Manager 更新汇总知识库文章中的说明进行操作并刷新控制台。

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>问：Log Analytics 是否有“本地”版本？

答：没有。 Log Analytics 是处理和存储大量数据的可缩放云服务。 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>问： 如何排除 Log Analytics 不再收集数据这一故障？

答：如果采用的是免费定价层并且某天已发送的数据超过 500 MB，则该天的剩余时间内会停止数据收集。 达到每日限制是 Log Analytics 停止数据收集或者看起来缺少数据的常见原因。

在数据收集启动和停止时，Log Analytics 会创建一个类型为 *Operation* 的事件。 

请在搜索中运行以下查询来检查是否已达到每日限制并缺少数据：`Type=Operation OperationCategory="Data Collection Status"`

当数据收集停止时，*OperationStatus* 为 **Warning**。 当数据收集启动时，*OperationStatus* 为 **Succeeded**。 

下表描述了数据收集停止的原因以及用于恢复数据收集的建议操作：

| 数据收集停止的原因                       | 恢复数据收集需执行的操作 |
| -------------------------------------------------- | ----------------  |
| 达到了免费数据的每日限制<sup>1</sup>       | 等到下一天收集自动重新启动，或者<br> 更改为付费定价层 |
| Azure 订阅由于以下原因处于挂起状态： <br> 免费试用已结束 <br> Azure 许可已过期 <br> 已达到每月支出限制（例如，在 MSDN 或 Visual Studio 订阅上）                          | 转换为付费订阅 <br> 转换为付费订阅 <br> 删除限制，或者等到限制重置 |

<sup>1</sup> 如果工作区位于免费定价层，则限制为每天向服务发送 500 MB 数据。 达到每日限制时，数据收集将停止到下一天。 不会为在数据收集停止期间发送的数据编制索引并且该数据不可搜索。 当数据收集恢复后，将仅对发送的新数据进行处理。 

Log Analytics 使用 UTC 时间并且每天从 UTC 午夜时间开始。 如果工作区达到每日限制，则处理将在采用 UTC 时间的下一天的第一个小时恢复。

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>问： 当数据收集停止时如何可以收到通知？

答：当数据收集停止时，可以使用[创建警报规则](log-analytics-alerts-creating.md#create-an-alert-rule)中所述的步骤收到通知。

针对数据收集停止时创建警报时，请设置以下各项：
- 将“名称”设置为“数据收集已停止”
- 将“严重性”设置为“警告”
- 将“搜索查询”设置为 `Type=Operation OperationCategory="Data Collection Status" OperationStatus=Warning`
- 将“时间窗口”设置为“2 小时”。
- 将“警报频率”设置为“一小时”，因为使用情况数据一小时才更新一次。
- 将“生成警报的基础”设置为“结果数”
- 将“结果数”设置为“大于 0”

执行[将操作添加到警报规则](log-analytics-alerts-actions.md)中介绍的步骤，为警报规则配置电子邮件、Webhook 或 Runbook 操作。


## <a name="configuration"></a>配置
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>问： 是否可以更改用于从 Azure 诊断 (WAD) 读取数据的表/blob 容器的名称？

答： 不可以，当前还不能从 Azure 存储中的任意表或容器进行读取。

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>问： Log Analytics 服务使用什么 IP 地址？ 如何确保我的防火墙仅允许与 Log Analytics 服务通信？

答： Log Analytics 服务是在 Azure 的基础上构建的。 Log Analytics IP 地址在 [Microsoft Azure 数据中心 IP 范围](http://www.microsoft.com/download/details.aspx?id=41653)内。

当进行服务部署时，Log Analytics 服务的实际 IP 地址会发生变化。 [在 Log Analytics 中配置代理和防火墙设置](log-analytics-proxy-firewall.md)中记录了允许通过防火墙的 DNS 名称。

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>问： 我使用 ExpressRoute 连接到 Azure。 我的 Log Analytics 流量是否使用我的 ExpressRoute 连接？

答： 不同类型的 ExpressRoute 通信流量记录在 [ExpressRoute 文档](../expressroute/expressroute-faqs.md#supported-services)中。

Log Analytics 通信流量使用的是公共对等 ExpressRoute 线路。

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>问： 有没有简单易用的方法将现有的 Log Analytics 工作区移到另一个 Log Analytics 工作区/Azure 订阅？

答： `Move-AzureRmResource` Cmdlet 可以用来将 Log Analytics 工作区以及自动化帐户从一个 Azure 订阅移到另一个订阅。 有关详细信息，请参阅[移动 - AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx)。

也可在 Azure 门户中进行此更改。

不能将数据从一个 Log Analytics 工作区移到另一个工作区，或更改 Log Analytics 数据所存储的区域。

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>问：如何将 Log Analytics 添加到 System Center Operations Manager？

答：更新到最新的更新汇总并导入管理包后，便可以将 Operations Manager 连接到 Log Analytics。

>[!NOTE]
>只有 System Center Operations Manager 2012 SP1 和更高版本中才提供了到 Log Analytics 的 Operations Manager 连接。

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>问：如何确认代理能够与 Log Analytics 通信？

答：要确保代理能够与 OMS 通信，请转到：控制面板、安全性和设置、**Microsoft 监视代理**。

在“**Azure Log Analytics (OMS)**”选项卡下，查找一个绿色复选标记。 绿色复选标记图标确认代理能够与 Azure 服务进行通信。

黄色警告图标意味着代理与 Log Analytics 存在通信问题。 一个常见原因是 Microsoft 监视代理服务已停止。 请使用服务控制管理器重启该服务。

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>问：如何使某个代理停止与 Log Analytics 进行通信？

答：在 System Center Operations Manager 中，从 OMS 托管的计算机列表中删除该计算机。 Operations Manager 会更新该代理的配置以便不再向 Log Analytics 进行报告。 对于直接连接到 Log Analytics 的代理，可以通过以下路径使其停止与 Log Analytics 进行通信：控制面板、安全性和设置、**Microsoft 监视代理**。
在“**Azure Log Analytics (OMS)**”选项卡下，删除列出的所有工作区。

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>问：为什么我在尝试将工作区从一个 Azure 订阅移动到另一个订阅时收到错误？

答：如果使用的是 Azure 门户，请确保仅选择工作区进行移动。 不要选择解决方案 -- 它们会在工作区移动后自动移动。 

请确保在两个 Azure 订阅中都有权限。

## <a name="agent-data"></a>代理数据
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>问： 可以通过代理向 Log Analytics 发送多少数据？ 每个客户的数据量是否有最大限制？
答： 免费套餐设置的上限是每个工作区每天 500 MB。 标准和高级套餐对上传的数据量没有限制。 作为云服务，Log Analytics 设计为自动纵向扩展以处理来自客户的数据 – 即使每天数据量达到数万亿字节 (TB)。

Log Analytics 代理设计为确保占用较小的数据空间。 我们的一位客户针对他们对我们的代理执行的测试写了一篇博客，测试结果令人印象深刻。 数据量因启用的解决方案而异。 在[“使用情况”](log-analytics-usage.md)页面中可以找到有关数据量的详细信息以及按解决方案列出的细目。

有关更多信息，可以阅读[客户博客](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html)以了解 OMS 代理的低数据占用空间。

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>问： 将数据发送到 Log Analytics 时，Microsoft 管理代理 (MMA) 使用多少网络带宽？

答： 带宽是已发送数据量的一个函数。 数据通过网络发送时会被压缩。

### <a name="q-how-much-data-is-sent-per-agent"></a>问： 每个代理发送多少数据？

答： 每个代理发送的数据量取决于：

* 已启用的解决方案
* 所收集的日志和性能计数器的数量
* 日志中的数据量

一个好的方法就是利用免费定价层来搭载数台服务器并测量典型数据量。 总体使用情况显示在 [使用情况](log-analytics-usage.md) 页。

对于能够运行 WireData 代理的计算机，可以使用以下查询了解正在发送的数据量：

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>后续步骤
* [开始使用 Log Analytics](log-analytics-get-started.md)，以快速了解有关 Log Analytics 的更多信息并使 Log Analytics 开始运行。

