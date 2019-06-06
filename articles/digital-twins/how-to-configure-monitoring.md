---
title: 如何在 Azure 数字孪生中配置监视 | Microsoft Docs
description: 如何在 Azure 数字孪生中配置监视。
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 9765af13a22a0ae9db8a45c396e7d5a32b71b79c
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730324"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>如何在 Azure 数字孪生中配置监视

Azure 数字孪生支持可靠的日志记录、监视和分析。 解决方案开发人员可以使用 Azure Monitor 日志、 诊断日志，活动日志记录和其他服务来支持复杂的监视需求的 IoT 应用。 可以将日志记录选项组合在一起，用于查询或显示多个服务的记录，并为许多服务提供精细的日志记录范围。

本文总结了日志记录和监视选项以及如何以 Azure 数字孪生特定的方式组合它们。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>查看活动日志

通过 Azure [活动日志](../azure-monitor/platform/activity-logs-overview.md) 可以快速了解每个 Azure 服务实例的订阅级别事件和操作历史记录。

订阅级别事件包括：

* 创建资源
* 删除资源
* 创建应用机密
* 与其他服务集成

Azure 数字孪生的活动日志记录默认启用，可以通过以下方式在 Azure 门户中找到它：

1. 选择 Azure 数字孪生实例。
1. 选择“活动日志”  以调出显示面板：

    [![活动日志](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

对于高级活动日志记录：

1. 选择“日志”  选项以显示“Activity Log Analytics 概述”  ：

    [![所选内容](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. “Activity Log Analytics 概述”  汇总了基本的活动日志数据：

    [![活动 log analytics 概述]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>使用活动日志  可以快速了解订阅级事件。

## <a name="enable-customer-diagnostic-logs"></a>启用客户诊断日志

可以为每个 Azure 实例设置 Azure [诊断设置](../azure-monitor/platform/diagnostic-logs-overview.md)来补充活动日志记录。 虽然活动日志与订阅级别事件相关，但诊断日志记录可提供有关资源本身的操作历史记录的见解。

诊断日志记录的示例包括：

* 用户定义的函数的执行时间
* 成功的 API 请求的响应状态代码
* 从保管库中检索应用密钥

为实例启用诊断日志：

1. 在 Azure 门户中打开资源。
1. 单击“诊断设置”  ：

    [![一个诊断设置](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. 单击“启用诊断”  收集数据（如果之前未启用）。
1. 填写请求的字段并选择保存数据的方式和位置：

    [![诊断设置两个](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    通常使用保存诊断日志[Azure 文件存储](../storage/files/storage-files-deployment-guide.md)以及与[Azure Monitor 日志](../azure-monitor/log-query/get-started-portal.md)。 可以同时选择这两个选项。

>[!TIP]
>使用诊断日志  了解资源操作。

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor 和 Log Analytics

IoT 应用程序将不同的资源、设备、位置和数据合并到一个位置。 细粒度日志记录提供有关整个应用程序体系结构的每个特定部分、服务或组件的详细信息，但维护和调试通常需要统一的概述。

Azure 监视器包含强大的日志分析服务，这使日志记录源进行查看和分析在一个位置。 因此，Azure Monitor 非常适用于分析复杂的 IoT 应用中的日志。

使用示例包括：

* 查询多个诊断日志历史记录
* 查看若干个用户定义的函数的日志
* 在特定时间范围内显示两个或多个服务的日志

通过提供查询日志已满[Azure Monitor 日志](../azure-monitor/log-query/log-query-overview.md)。 设置这些强大的功能：

1. 在 Azure 门户中搜索“Log Analytics”  。
1. 你将看到您的可用**Log Analytics 工作区**实例。 选择一个实例，然后选择“日志”  进行查询：

    [![日志分析](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. 如果还没有**Log Analytics 工作区**实例，您可以通过单击创建一个工作区**添加**按钮：

    [![创建 OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

一次你**Log Analytics 工作区**预配实例后，可以使用功能强大的查询在多个日志中查找条目或搜索使用特定的条件使用**日志管理**:

   [![日志管理](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

有关功能强大的查询操作的详细信息，请参阅[开始使用查询](../azure-monitor/log-query/get-started-queries.md)。

> [!NOTE]
> 发送到事件时，可能会遇到 5 分钟的延迟**Log Analytics 工作区**第一次。

Azure Monitor 日志还提供了功能强大的错误和警报通知服务，可通过单击查看**诊断并解决问题**:

   [![警报和错误通知](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>使用**Log Analytics 工作区**到查询日志历史记录，发现多个应用程序功能、 订阅或服务。

## <a name="other-options"></a>其他选项

Azure 的数字孪生还支持特定于应用程序的日志记录和安全审核。 有关 Azure 数字孪生实例可用的所有 Azure 日志记录选项的完整概述，请参阅 [Azure 日志审核](../security/azure-log-audit.md)一文。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure [活动日志](../azure-monitor/platform/activity-logs-overview.md)。

- 通过阅读[诊断日志概述](../azure-monitor/platform/diagnostic-logs-overview.md)深入了解 Azure 诊断设置。

- 详细了解[Azure Monitor 日志](../azure-monitor/log-query/get-started-portal.md)。
