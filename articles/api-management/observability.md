---
title: Azure API 管理中的可观察性 |Microsoft Docs
description: Azure API 管理中的所有可观察性选项的概述。
services: api-management
documentationcenter: ''
author: begim
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2020
ms.author: apimpm
ms.openlocfilehash: 1ebebed465952bbb5d3e8f82ae1c7776c441c6b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095907"
---
# <a name="observability-in-azure-api-management"></a>Azure API 管理中的可观察性

可观察性是从系统生成的数据中了解系统的内部状态，并能够浏览该数据来回答有关发生的问题和原因的能力。 

Azure API 管理可帮助组织集中管理所有 Api。 由于它作为所有 API 流量的单一入口点，因此它是观察 Api 的理想位置。 

## <a name="observability-tools"></a>可观察性工具

下表汇总了 API 管理支持的所有工具，这些工具可用于观察 Api，每个工具可用于一个或多个方案：

| 工具        | 有助于    | 数据滞后时间 | 保留 | 采样 | 数据类型 | 已启用|
|:------------- |:-------------|:---- |:----|:---- |:--- |:---- 
| **[API 检查器](api-management-howto-api-inspector.md)** | 测试和调试 | 即时 | 最后100跟踪 | 启用每个请求 | 请求跟踪 | 始终
| **内置分析** | 报告和监视 | 分钟数 | 生存期 | 100% | 报表和日志 | 始终 |
| **[Azure Monitor 指标](api-management-howto-use-azure-monitor.md)** | 报告和监视 | 分钟数 | 93天内 (升级才能扩展)  | 100% | 指标 | 始终 |
| **[Azure Monitor 日志](api-management-howto-use-azure-monitor.md)** | 报告、监视和调试 | 分钟数 | 31天/5GB (升级以扩展)  | 100% (可调)  | 日志 | 可选 |
| **[Azure Application Insights](api-management-howto-app-insights.md)** | 报告、监视和调试 | 秒 | 90天/5GB (升级以扩展)  | “自定义” | 日志，指标 | 可选 |
| **[通过 Azure 事件中心进行日志记录](api-management-howto-log-event-hubs.md)** | 自定义方案 | 秒 | 用户管理 | “自定义” | “自定义” | 可选 |

### <a name="self-hosted-gateway"></a>自承载网关

云中的托管网关支持上述所有工具。 [自承载的网关](self-hosted-gateway-overview.md)当前不会将诊断日志发送到 Azure Monitor。 但是，可以在部署自承载网关本地配置和保存日志。 有关详细信息，请参阅 [配置独立网关的云指标和日志](how-to-configure-cloud-metrics-logs.md) 和 [配置自承载网关的本地指标和日志](how-to-configure-local-metrics-logs.md)。

## <a name="next-steps"></a>后续步骤

* [请按照教程了解有关 API 管理的详细信息](import-and-publish.md)
