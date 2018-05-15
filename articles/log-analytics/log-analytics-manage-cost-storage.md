---
title: 在 Azure Log Analytics 中管理数据成本 | Microsoft Docs
description: 了解如何在 Azure 中更改定价计划和管理 Log Analytics 工作区的数据量与保留策略。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: magoedte
ms.openlocfilehash: 0e4c4c9e950610526a29e02d70827a1279d9686a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="manage-cost-by-controlling-data-volume-and-retention-in-log-analytics"></a>通过在 Log Analytics 中控制数据量和保留期管理成本
Log Analytics 用于调整和支持来自任何源的巨量数据的每日收集、索引和存储，这些源部署在企业或 Azure 中。  尽管这可能是组织的主要驱动力，但成本效益最终是基本驱动力。 为此，必须了解 Log Analytics 工作区的成本不仅仅是基于收集的数据量，而且也取决于所选的计划，以及连接源生成的数据的存储时间长短。  

本文介绍如何主动监视数据量和存储增长，以及定义限制来控制这些关联的成本。 

根据以下因素，数据成本有时会很大： 

- 系统数量、基础结构组件、云资源等（从中收集数据） 
- 源创建的数据类型（例如，消息队列、日志、事件、安全相关数据或性能指标） 
- 这些源生成的以及引入到工作区的数据量 
- 数据在工作区中保留的时限  
- 启用的管理解决方案数、数据源和收集频率 

> [!NOTE]
> 请参考每个解决方案的文档，这些文档提供了数据收集量的预测值。   

如果是“免费”计划，数据保留期限制为 7 天。 对于“独立”或“付费”层，收集的数据在最后 31 天可用。 “免费”计划的每日引入限制为 500 MB。如果发现一直超出允许的量，可将工作区更改为付费计划，从而收集超出该限制的数据。 

> [!NOTE]
> 如果为该付费层选择更长的保留期，会收取费用。 可随时更改计划类型，关于定价的详细信息，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/log-analytics/)。 

可通过以下两种方法限制数据量和帮助控制成本：每日上限和数据保留。  

## <a name="review-estimated-cost"></a>查看预估成本
使用 Log Analytics 可以轻松了解基于最近使用模式的可能成本。  为此，请执行以下步骤。  

1. 登录到 [Azure 门户](http://portal.azure.com)。 
2. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。<br><br> ![Azure 门户](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
3. 在 Log Analytics 订阅窗格中选择自己的工作区，然后单击左侧窗格中的“使用情况和预估成本”。<br><br> ![使用情况和预估成本页](media/log-analytics-manage-cost-storage/usage-estimated-cost-dashboard-01.png)<br>

在此处可以查看当月数据量。 这包括 Log Analytics 工作区中收到和保留的所有数据。  单击页面顶部的“使用情况详细信息”查看使用情况仪表板，其中按源、计算机和产品/服务显示了有关数据量趋势的信息。 若要查看和设置每日上限或修改保留期，请单击“数据量管理”。
 
Log Analytics 费用将添加到 Azure 帐单。 可以在 Azure 门户的“计费”部分或在 [Azure 计费门户](https://account.windowsazure.com/Subscriptions)中查看 Azure 账单详细信息。  

## <a name="daily-cap"></a>每日上限
从 Azure 门户创建 Log Analytics 工作区时，如果选择“免费”计划，该工作区的限制将设置为每日 500 MB。 其他定价计划没有限制。 可以配置工作区的每日上限并限制每日引入量，但请谨慎设置，因为目标是避免达到每日限制。  否则，会丢失该天剩余时间的数据，这可能会影响其功能依赖于工作区中提供的最新数据的其他 Azure 服务和解决方案。  因此，需要具有在支持 IT 服务的资源的运行状况受到影响时监视和接收警报的能力。  每日上限旨在用作一种调控受管理资源数据量意外增长并使其保留在限制范围内，或者只是限制工作区产生计划外费用的方式。  

达到每日限制后，在当天的剩余时间，应计费数据类型的收集将会停止。 选定 Log Analytics 工作区的页面顶部会显示警告横幅，同时会将一个操作事件发送到“LogManagement”类别下的“操作”表。 在“每日限制设置时间”定义的重置时间过后，数据收集将会恢复。 我们建议基于此操作事件定义一个警报规则，并将其配置为在达到每日数据限制时发出通知。 

### <a name="identify-what-daily-data-limit-to-define"></a>确定要定义的每日数据限制 
查看 [Log Analytics 使用情况和预估成本](log-analytics-usage.md)，了解数据引入趋势，以及要定义的每日数据量上限。 应该慎重考虑上限，因为在达到限制后，将无法监视资源。 

### <a name="manage-the-maximum-daily-data-volume"></a>管理每日最大数据量 
以下步骤说明如何配置一个限制来管理 Log Analytics 每日引入的数据量。  

1. 在工作区的左窗格中，选择“使用情况和预估成本”。
2. 在所选工作区的“使用情况和预估成本”页面顶部，单击“数据量管理”。 
5. 每日上限默认为“关闭”– 单击“打开”将其启用，然后设置数据量限制（以 GB/天为单位）。<br><br> ![Log Analytics 配置数据限制](media/log-analytics-manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-limit-reached"></a>达到限制时发出警报
尽管在达到数据限制阈值时，Azure 门户中会显示视觉提示，但此行为不一定符合需要立即关注的操作问题的处理方式。  若要接收警报通知，可以在 Azure Monitor 中创建一个新的警报规则。  有关详细信息，请参阅[如何创建、查看和管理警报](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)。      

若要开始操作，请参考下面提供的建议警报设置：

* 目标：选择 Log Analytics 资源
* 条件： 
   * 信号名称：自定义日志搜索
   * 搜索查询：Operation | where Detail has 'OverQuota'
   * 基于：结果数
   * 条件：大于
   * 阈值：0
   * 期限：5（分钟）
   * 频率：5（分钟）
* 警报规则名称：达到每日数据限制
* 严重性：警告（严重性 1）

定义警报并达到限制后，警报将会触发，并执行操作组中定义的响应。 该警报可通过电子邮件和短信通知团队，或者使用 Webhook、自动化 Runbook 或[与外部 ITSM 解决方案的集成](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts)来自动执行操作。 

## <a name="change-the-data-retention-period"></a>更改数据保留期 
以下步骤说明如何配置日志数据在工作区中的保留期限。
 
1. 在工作区的左窗格中，选择“使用情况和预估成本”。
2. 在“使用情况和预估成本”页面顶部，单击“数据量管理”。
5. 在窗格中，移动滑块以增加或减少天数，然后单击“确定”。  如果位于“免费”层，则不能修改数据保留期，需要升级到付费层才能控制这一项设置。<br><br> ![更改工作区数据保留设置](media/log-analytics-manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="troubleshooting"></a>故障排除
**问**：如果 Log Analytics 不再收集数据，应如何排查问题？ 
**答**：如果采用的是免费定价层并且某天已发送的数据超过 500 MB，则该天的剩余时间内会停止数据收集。 达到每日限制是 Log Analytics 停止数据收集或者看起来缺少数据的常见原因。  
在数据收集启动和停止时，Log Analytics 会创建一个类型为“操作”的事件。  
请在搜索中运行以下查询来检查是否已达到每日限制并缺少数据：Operation | where OperationCategory == 'Data Collection Status'   
当数据收集停止时，OperationStatus 为 Warning。 当数据收集启动时，OperationStatus 为 Succeeded。  
下表描述了数据收集停止的原因以及用于恢复数据收集的建议操作：  

|停止收集的原因| 解决方案| 
|-----------------------|---------|
|达到了免费数据的每日限制<sup>1</sup>|等到下一天收集自动重启，或者更改为付费定价层。|
|达到了“数据量管理”中定义的每日限制|等到下一天收集自动重启，或者根据[管理每日最大数据量](#manage-the-maximum-daily-volume)中所述增大每日数据量限制|
|Azure 订阅由于以下原因处于挂起状态：<br> 免费试用已结束<br> Azure 许可已过期<br> 已达到每月支出限制（例如，在 MSDN 或 Visual Studio 订阅上）|转换为付费订阅<br> 删除限制，或者等到限制重置|

<sup>1</sup> 如果工作区位于免费定价层，则限制为每天向服务发送 500 MB 数据。 达到每日限制时，数据收集将停止到下一天。 不会为在数据收集停止期间发送的数据编制索引并且该数据不可搜索。 当数据收集恢复后，将仅对发送的新数据进行处理。 

Log Analytics 使用 UTC 时间。 重置时间根据工作区而异，可防止所有受限工作区同时开始引入数据。 如果工作区达到每日限制，在“每日限制设置时间”中定义的重置时间过后，处理工作将会恢复。<br><br> ![Log Analytics 限制 UTC 时区](media/log-analytics-manage-cost-storage/data-volume-mgmt-limit-utc.png)

**问**：当数据收集停止时如何收到通知？ 
**答**：执行“创建每日数据上限”中所述的步骤，在数据收集停止时接收警报通知；遵循“将操作添加到警报规则”中所述的步骤，为警报规则配置电子邮件、Webhook 或 Runbook 操作。 

## <a name="next-steps"></a>后续步骤  

若要确定收集的数据量、发送源和发送的各种不同数据类型，以帮助管理使用量和成本，请参阅[在 Log Analytics 中分析数据使用情况](log-analytics-usage.md)。