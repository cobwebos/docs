---
title: 如何从启动/停止 VM 解决方案查询日志
description: 本文介绍如何查询 Azure 监视器中的开始/停止 VM 解决方案生成的日志数据。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 00f6a5f88a66f0a19943ff3a2c722ae1a9938e9e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550391"
---
# <a name="how-to-query-logs-from-startstop-vms-solution"></a>如何从启动/停止 VM 解决方案查询日志

Azure 自动化将两种类型的记录转发到链接的日志分析工作区：作业日志和作业流。 此数据可用于 Azure Monitor 中的[查询](../azure-monitor/log-query/log-query-overview.md)。

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

下表提供了此解决方案收集的作业记录的示例日志搜索。

|查询 | 说明|
|----------|----------|
|查找已成功完成 Runbook ScheduledStartStop_Parent 的作业 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|查找未成功完成的 runbook ScheduledStartStop_Parent的作业 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|查找已成功完成 Runbook SequencedStartStop_Parent 的作业 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|查找未成功完成的 runbook SequencedStartStop_Parent的作业 | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>后续步骤

在非工作时间启动/停止 VM 解决方案不包括预定义的警报集。 使用 Azure 监视器查看[创建日志警报](../azure-monitor/platform/alerts-log.md)，了解如何创建作业失败警报以支持 DevOps 或操作过程和过程。