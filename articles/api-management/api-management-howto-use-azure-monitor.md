---
title: "使用 Azure Monitor 监视 API 管理 | Microsoft Docs"
description: "了解如何使用 Azure Monitor 监视 Azure API 管理服务。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 717e033aa4bbd4dd8ebcc727c3f551aee81770dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-api-management-with-azure-monitor"></a>使用 Azure Monitor 监视 API 管理
Azure Monitor 作为一项 Azure 服务，提供单一资源来监视所有 Azure 资源。 通过 Azure Monitor，可对来自 Azure 资源（例如 API 管理）的指标和日志进行可视化、查询、路由、存档和执行操作。 

下方视频介绍如何使用 Azure Monitor 监视 API 管理。 有关 Azure Monitor 的详细信息，请参阅 [Azure Monitor 入门]。 


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>
 
## <a name="metrics"></a>度量值
API 管理当前发出 5 个指标，我们计划在将来添加更多指标。 每分钟发出一次这些指标，几乎可让你实时了解 API 的状态和运行状况。 以下为指标摘要：
* 网关请求总数：期间内的 API 请求数。 
* 成功的网关请求数：接收成功的 HTTP 响应代码（包括 304、307 以及任何小于 301 的代码，例如 200）的 API 请求数。 
* 失败的网关请求数：接收错误的 HTTP 响应代码（包括 400 以及任何大于 500 的代码）的 API 请求数。
* 未经授权的网关请求数：接收包括 401、403 和 429的 HTTP 响应代码的 API 请求数。 
* 其他网关请求数：接收不属于上述任何类别的 HTTP 响应代码（例如 418）的 API 请求数。

可在 API 管理服务中访问指标，或在 Azure Monitor 中访问所有 Azure 资源的指标。 查看 API 管理服务中的指标：
1. 打开 Azure 门户。
2. 转到 API 管理服务。
3. 单击“指标”。

![“指标”边栏选项卡][metrics-blade]

有关如何使用指标的详细信息，请参阅[指标概述]。

## <a name="activity-logs"></a>活动日志
活动日志提供针对 API 管理服务执行的操作的详细信息。 活动日志以前称为“审核日志”或“操作日志”。 通过活动日志，可确定对 API 管理服务执行的任何写入操作 (PUT、POST、DELETE) 的“操作内容、操作人员和操作时间”。 

> [!NOTE]
> 活动日志不包括读取 (GET) 操作，或者通过经典发布服务器门户或使用原始管理 API 执行的操作。

可在 API 管理服务中访问活动日志，或在 Azure Monitor 中访问所有 Azure 资源的日志。 查看 API 管理服务中的活动日志：
1. 打开 Azure 门户。
2. 转到 API 管理服务。
3. 单击“活动日志”。

![“活动日志”边栏选项卡][activity-logs-blade]

有关如何使用指标的详细信息，请参阅[活动日志概述]。

## <a name="alerts"></a>警报
可配置为基于指标和活动日志接收警报。 通过 Azure Monitor 可配置警报，使警报触发时执行以下操作：

* 发送电子邮件通知
* 调用 Webhook
* 调用 Azure 逻辑应用

可在 API 管理服务或在 Azure Monitor 中配置警报规则。 在 API 管理中配置警报规则： 
1. 打开 Azure 门户。
2. 转到 API 管理服务。
3. 单击“警报规则”。

![“警报规则”边栏选项卡][alert-rules-blade]

有关使用警报的详细信息，请参阅[警报概述]。

## <a name="diagnostic-logs"></a>诊断日志
诊断日志提供大量有关操作和错误的信息，这些信息对审核和故障排除非常重要。 诊断日志不同于活动日志。 活动日志提供针对 Azure 资源执行的操作的详细信息。 诊断日志提供资源本身执行的操作的深入信息。

API 管理当前提供有关单个 API 请求的诊断日志（每小时进行批处理），其中每个条目具有以下结构：

```
{
    "Tenant": "",
      "DeploymentName": "",
      "time": "",
      "resourceId": "",
      "category": "GatewayLogs",
      "operationName": "Microsoft.ApiManagement/GatewayLogs",
      "durationMs": ,
      "Level": ,
      "properties": "{
          "ApiId": "",
          "OperationId": "",
          "ProductId": "",
          "SubscriptionId": "",
          "Method": "",
          "Url": "",
          "RequestSize": ,
          "ServiceTime": "",
          "BackendMethod": "",
          "BackendUrl": "",
          "BackendResponseCode": ,
          "ResponseCode": ,
          "ResponseSize": ,
          "Cache": "",
          "UserId"
      }"
 }
```

可在 API 管理服务中访问诊断日志，或在 Azure Monitor 中访问所有 Azure 资源的日志。 查看 API 管理服务中的诊断日志：
1. 打开 Azure 门户。
2. 转到 API 管理服务。
3. 单击“诊断日志”。

![诊断日志边栏选项卡][diagnostic-logs-blade]

有关如何使用指标的详细信息，请参阅[诊断日志概述]。

## <a name="next-step"></a>后续步骤

* [Azure Monitor 入门]
* [指标概述]
* [活动日志概述]
* [诊断日志概述]
* [警报概述]

[Azure Monitor 入门]: ../monitoring-and-diagnostics/monitoring-get-started.md
[指标概述]: ../monitoring-and-diagnostics/monitoring-overview-metrics.md
[活动日志概述]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[诊断日志概述]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[警报概述]: ../monitoring-and-diagnostics/insights-alerts-portal.md



[metrics-blade]: ./media/api-management-azure-monitor/api-management-metrics-blade.png
[activity-logs-blade]: ./media/api-management-azure-monitor/api-management-activity-logs-blade.png
[alert-rules-blade]: ./media/api-management-azure-monitor/api-management-alert-rules-blade.png
[diagnostic-logs-blade]: ./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png
