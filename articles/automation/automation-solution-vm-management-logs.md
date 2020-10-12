---
title: Azure 自动化“在空闲时间启动/停止 VM”的查询记录
description: 本文介绍了如何使用 Azure Monitor 查询由“在空闲时间启动/停止 VM”生成的记录数据。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: de013b6ccd924f50ffe12fcba1285b121eece5f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83827550"
---
# <a name="query-logs-from-startstop-vms-during-off-hours"></a>从“在空闲时间启动/停止 VM”查询日志

Azure 自动化将两种类型的记录转发到链接的 Log Analytics 工作区：作业日志和作业流。 本文介绍了适用于 Azure Monitor 中的[查询](../azure-monitor/log-query/log-query-overview.md)的数据。

## <a name="job-logs"></a>作业日志

|properties | 说明|
|----------|----------|
|调用方 |  谁启动了该操作。 可能的值为电子邮件地址或计划作业的系统。|
|类别 | 数据类型的分类。 对于自动化，该值为 JobLogs。|
|CorrelationId | 用作 Runbook 作业相关性 ID 的 GUID。|
|JobId | 用作 Runbook 作业 ID 的 GUID。|
|operationName | 指定在 Azure 中执行的操作类型。 对于自动化，该值为 Job。|
|resourceId | 指定 Azure 中的资源类型。 对于自动化，该值是与 Runbook 关联的自动化帐户。|
|ResourceGroup | 指定 Runbook 作业的资源组名称。|
|ResourceProvider | 指定  Azure 服务，它提供可部署和管理的资源。 对于自动化，该值为 Azure Automation。|
|ResourceType | 指定 Azure 中的资源类型。 对于自动化，该值是与 Runbook 关联的自动化帐户。|
|resultType | Runbook 作业的状态。 可能的值包括：<br>- Started（已启动）<br>- Stopped（已停止）<br>- Suspended（已暂停）<br>- Failed（失败）<br>- Succeeded（成功）|
|resultDescription | 描述 Runbook 作业结果状态。 可能的值包括：<br>- 作业已启动<br>- 作业失败<br>- 作业已完成|
|RunbookName | 指定 Runbook 的名称。|
|SourceSystem | 指定所提交数据的源系统。 对于自动化，值为 OpsManager|
|StreamType | 指定事件的类型。 可能的值包括：<br>- Verbose（详细）<br>- Output（输出）<br>- Error（错误）<br>- Warning（警告）|
|SubscriptionId | 指定作业的订阅 ID。
|时间 | 执行 Runbook 作业的日期和时间。|

## <a name="job-streams"></a>作业流

|properties | 说明|
|----------|----------|
|调用方 |  谁启动了该操作。 可能的值为电子邮件地址或计划作业的系统。|
|类别 | 数据类型的分类。 对于自动化，该值为 JobStreams。|
|JobId | 用作 Runbook 作业 ID 的 GUID。|
|operationName | 指定在 Azure 中执行的操作类型。 对于自动化，该值为 Job。|
|ResourceGroup | 指定 Runbook 作业的资源组名称。|
|resourceId | 指定 Azure 中的资源 ID。 对于自动化，该值是与 Runbook 关联的自动化帐户。|
|ResourceProvider | 指定  Azure 服务，它提供可部署和管理的资源。 对于自动化，该值为 Azure Automation。|
|ResourceType | 指定 Azure 中的资源类型。 对于自动化，该值是与 Runbook 关联的自动化帐户。|
|resultType | 生成事件时 Runbook 作业的结果。 可能的值为：<br>- InProgress|
|resultDescription | 包括来自 Runbook 的输出流。|
|RunbookName | Runbook 的名称。|
|SourceSystem | 指定所提交数据的源系统。 对于自动化，值为 OpsManager。|
|StreamType | 作业流的类型。 可能的值包括：<br>- Progress（进度）<br>- Output（输出）<br>- Warning（警告）<br>- Error（错误）<br>- Debug（调试）<br>- Verbose（详细）|
|时间 | 执行 Runbook 作业的日期和时间。|

如果执行的日志搜索返回 **JobLogs** 或 **JobStreams** 类别的记录时，可以选择 **JobLogs** 或 **JobStreams** 视图，其中显示了一组汇总搜索所返回的更新的磁贴。

## <a name="sample-log-searches"></a>示例日志搜索

下表提供了“在空闲时间启动/停止 VM”收集的作业记录的示例日志搜索。

|查询 | 说明|
|----------|----------|
|查找已成功完成 Runbook ScheduledStartStop_Parent 的作业 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|查找未成功完成 runbook ScheduledStartStop_Parent 的作业 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|查找已成功完成 Runbook SequencedStartStop_Parent 的作业 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|查找未成功完成 runbook SequencedStartStop_Parent 的作业 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>后续步骤

* 要设置该功能，请参阅[配置“在空闲时间启动/停止 VM”](automation-solution-vm-management-config.md)。
* 有关功能部署中记录警报的信息，请参阅[通过 Azure Monitor 创建记录警报](../azure-monitor/platform/alerts-log.md)。
* 若要解决功能问题，请参阅[排除故障“在空闲时间启动/停止 VM”](troubleshoot/start-stop-vm.md)。