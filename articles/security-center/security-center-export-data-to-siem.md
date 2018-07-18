---
title: 将 Azure 安全数据导出到 SIEM - 管道配置 [预览版] | Microsoft Docs
description: 本文介绍将 Azure 安全中心日志传送至 SIEM 的过程
services: security-center
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2018
ms.author: barclayn
ms.openlocfilehash: 7a0a72a25010952f13eb190f0e0a1a65cc6d42d3
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
ms.locfileid: "29124827"
---
# <a name="azure-security-data-export-to-siem--pipeline-configuration-preview"></a>将 Azure 安全数据导出到 SIEM - 管道配置 [预览版]

本文详细介绍了将 Azure 安全中心安全数据导出到 SIEM 的过程。

Azure 安全中心生成的事件经过处理后，将被发布到 Azure [活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)，这是 Azure Monitor 提供的可用日志类型之一。 Azure Monitor 提供了一个综合管道，可将任何监视数据路由到 SIEM 工具。 要实现此目的，需将数据流式传输至事件中心，然后在此处将数据拉取至合作伙伴工具。

此管道使用 [Azure 监视单一管道](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)来访问 Azure 环境中的监视数据。 这使得你能轻松设置 SIEM 和监视工具来使用数据。

以下各节介绍如何将数据配置为流式传输到事件中心。 步骤假设 Azure 订阅中已配置了 Azure 安全中心。

综合概述

![高级概述](media/security-center-export-data-to-siem/overview.png)

## <a name="what-is-the-azure-security-data-exposed-to-siem"></a>公开给 SIEM 的 Azure 安全数据是什么？

在此预览版中，我们将公开[安全警报。](../security-center/security-center-managing-and-responding-alerts.md) 在即将发布的版本中，我们将通过安全建议丰富数据集。

## <a name="how-to-setup-the-pipeline"></a>如何设置管道？ 

### <a name="create-an-event-hub"></a>创建事件中心 

开始之前，需[创建事件中心命名空间](../event-hubs/event-hubs-create.md)。 此命名空间和事件中心是所有监视数据的目标。

### <a name="stream-the-azure-activity-log-to-event-hubs"></a>将 Azure 活动日志流式传输到事件中心

请参阅以下文章：[将活动日志流式传输到事件中心](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)

### <a name="install-a-partner-siem-connector"></a>安装合作伙伴 SIEM 连接器 

通过 Azure Monitor 将监视数据路由到事件中心，可与合作伙伴 SIEM 和监视工具轻松集成。

请参阅以下链接，查看[支持的 SIEM](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub) 列表

## <a name="example-for-querying-data"></a>查询数据示例 

以下 Splunk 查询可用于拉取警报数据：

| **查询说明**                                | **查询**                                                                                                                              |
|---------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| 所有警报                                              | index=main Microsoft.Security/locations/alerts                                                                                         |
| 按名称汇总操作计数             | index=main sourcetype="amal:security" \| table operationName \| stats count by operationName                                |
| 获取警报信息：时间、名称、状态、ID 和订阅 | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>后续步骤

- [支持的 SIEM](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)
- [将活动日志流式传输到事件中心](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md)
- [安全警报。](../security-center/security-center-managing-and-responding-alerts.md)