---
title: Azure 中的监视数据源
description: 了解现可在 Azure 上使用的所有监视数据源。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: c703f735f59a8425c2a14641781f482a6e2d1c78
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58848537"
---
# <a name="consume-monitoring-data-from-azure"></a>使用 Azure 中的监视数据

在 Azure 平台上，我们使用 Azure Monitor 管道将监视数据集中在单个位置，但实际上需承认，目前并非所有监视数据都可在该管道中使用。 本文概述了以编程方式从 Azure 服务访问监视数据的各种方法。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="options-for-data-consumption"></a>数据使用选项

| 数据类型 | 类别 | 支持的服务 | 访问方法 |
| --- | --- | --- | --- |
| Azure Monitor 平台级指标 | 度量值 | [查看此处的列表](metrics-supported.md) | <ul><li>**REST API：**[Azure Monitor 指标 API](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**存储 blob 或事件中心：**[诊断设置](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| 计算来宾 OS 指标（例如 性能计数器） | 度量值 | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) 和 Linux 虚拟机 (v2)、[云服务](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)、[Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**存储表或 blob：**[Windows 或 Linux Azure 诊断](diagnostics-extension-to-storage.md)</li><li>**事件中心：**[Windows Azure 诊断](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| 自定义或应用程序指标 | 度量值 | 使用 Application Insights 检测的任何应用程序 | <ul><li>**REST API：**[Application Insights REST API](https://dev.applicationinsights.io/reference)</li></ul> |
| 存储度量值 | 度量值 | Azure 存储 | <ul><li>**存储表：**[存储分析](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| 账单数据 | 度量值 | 所有 Azure 服务 | <ul><li>**REST API：**[Azure 资源使用状况和 RateCard API](../../billing/billing-usage-rate-card-overview.md)</li></ul> |
| 活动日志 | 活动 | 所有 Azure 服务 | <ul><li>**REST API：**[Azure Monitor 事件 API](https://docs.microsoft.com/rest/api/monitor/eventcategories)</li><li>**存储 blob 或事件中心：**[日志配置文件](activity-logs-overview.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Azure Monitor 诊断日志 | 活动 | [查看此处的列表](diagnostic-logs-schema.md) | <ul><li>**存储 blob 或事件中心：**[诊断设置](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| 计算来宾 OS 日志（例如 IIS、ETW、syslog） | 活动 | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) 和 Linux 虚拟机 (v2)、[云服务](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)、[Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**存储表或 blob：**[Windows 或 Linux Azure 诊断](diagnostics-extension-to-storage.md)</li><li>**事件中心：**[Windows Azure 诊断](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| 应用服务日志 | 事件 | 应用程序服务 | <ul><li>**文件、表或 blob 存储：**[Web 应用诊断](../../app-service/troubleshoot-diagnostic-logs.md)</li></ul> |
| 存储日志 | 活动 | Azure 存储 | <ul><li>**存储表：**[存储分析](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| 安全中心警报 | 活动 | Azure 安全中心 | <ul><li>**REST API：**[安全警报](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Active Directory 报告 | 活动 | Azure Active Directory | <ul><li>**REST API：**[Azure Active Directory 图形 API](../../active-directory/reports-monitoring/concept-reporting-api.md)</li></ul> |
| 安全中心资源状态 | 状态 | [支持的所有资源](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**REST API：**[安全状态](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| 资源运行状况 | 状态 | 支持的服务 | <ul><li>**REST API：**[资源运行状况 REST API](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Azure Monitor 指标警报 | 通知 | [查看此处的列表](metrics-supported.md) | <ul><li>**Webhook：**[Azure 指标警报](alerts-webhooks.md)</li></ul> |
| Azure Monitor 活动日志警报 | 通知 | 所有 Azure 服务 | <ul><li>**Webhook：** Azure 活动日志警报</li></ul> |
| 自动缩放通知 | 通知 | [查看此处的列表](autoscale-overview.md#supported-services-for-autoscale) | <ul><li>**Webhook：**[自动缩放通知 Webhook 有效负载架构](autoscale-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| 日志搜索查询警报 | 通知 | Azure Monitor 日志 | <ul><li>**Webhook：**[日志警报规则的 Webhook 操作](alerts-log-webhook.md)</li></ul> |
| Application Insights 指标警报 | 通知 | Application Insights | <ul><li>**Webhook：**[Application Insights 警报](../../azure-monitor/app/alerts.md)</li></ul> |
| Application Insights Web 测试 | 通知 | Application Insights | <ul><li>**Webhook：**[Application Insights 警报](../../azure-monitor/app/alerts.md)</li></ul> |

## <a name="next-steps"></a>后续步骤

- 深入了解 [Azure Monitor 指标](data-platform.md)
- 深入了解 [Azure 活动日志](activity-logs-overview.md)
- 深入了解 [Azure 诊断日志](diagnostic-logs-overview.md)

