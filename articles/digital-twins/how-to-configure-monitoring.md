---
title: 如何在 Azure 数字孪生中配置监视 | Microsoft Docs
description: 如何在 Azure 数字孪生中配置监视。
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 2749a5c6c4e6003c51523d83c46b48d3b55b3d45
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807578"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>如何在 Azure 数字孪生中配置监视

Azure 数字孪生支持可靠的日志记录、监视和分析。 解决方案开发人员可以使用 Azure Log Analytics、诊断日志、活动日志记录和其他服务来支持 IoT 应用复杂的监视需求。 可以将日志记录选项组合在一起，用于查询或显示多个服务的记录，并为许多服务提供精细的日志记录范围。

本文总结了日志记录和监视选项以及如何以 Azure 数字孪生特定的方式组合它们。

## <a name="review-activity-logs"></a>查看活动日志

通过 Azure [活动日志](../azure-monitor/platform/activity-logs-overview.md) 可以快速了解每个 Azure 服务实例的订阅级别事件和操作历史记录。

订阅级别事件包括：

* 创建资源
* 删除资源
* 创建应用机密
* 与其他服务集成

Azure 数字孪生的活动日志记录默认启用，可以通过以下方式在 Azure 门户中找到它：

1. 选择 Azure 数字孪生实例。
1. 选择“活动日志”以调出显示面板：

    ![活动日志][1]

对于高级活动日志记录：

1. 选择“日志”选项以显示“Activity Log Analytics 概述”：

    ![选项][2]

1. “Activity Log Analytics 概述”汇总了基本的活动日志数据：

    ![Activity Log Analytics 概述][3]

>[!TIP]
>使用活动日志可以快速了解订阅级事件。

## <a name="enable-customer-diagnostic-logs"></a>启用客户诊断日志

可以为每个 Azure 实例设置 Azure [诊断设置](../azure-monitor/platform/diagnostic-logs-overview.md)来补充活动日志记录。 虽然活动日志与订阅级别事件相关，但诊断日志记录可提供有关资源本身的操作历史记录的见解。

诊断日志记录的示例包括：

* 用户定义的函数的执行时间
* 成功的 API 请求的响应状态代码
* 从保管库中检索应用密钥

为实例启用诊断日志：

1. 在 Azure 门户中打开资源。
1. 单击“诊断设置”：

    ![诊断设置一][4]

1. 单击“启用诊断”收集数据（如果之前未启用）。
1. 填写请求的字段并选择保存数据的方式和位置：

    ![诊断设置二][5]

    诊断日志通常使用 [Azure 文件存储](../storage/files/storage-files-deployment-guide.md)保存，并与 [Azure Log Analytics](../azure-monitor/log-query/get-started-portal.md) 共享。 可以同时选择这两个选项。

>[!TIP]
>使用诊断日志了解资源操作。

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor 和 Log Analytics

IoT 应用程序将不同的资源、设备、位置和数据合并到一个位置。 细粒度日志记录提供有关整个应用程序体系结构的每个特定部分、服务或组件的详细信息，但维护和调试通常需要统一的概述。

Azure Monitor 包含功能强大的 Log Analytics 服务，该服务允许在一个位置查看和分析日志记录源。 因此，Azure Monitor 非常适用于分析复杂的 IoT 应用中的日志。

使用示例包括：

* 查询多个诊断日志历史记录
* 查看若干个用户定义的函数的日志
* 在特定时间范围内显示两个或多个服务的日志

通过 [Azure Log Analytics](../azure-monitor/log-query/log-query-overview.md) 可提供完整日志查询。 设置这些强大的功能：

1. 在 Azure 门户中搜索“Log Analytics”。
1. 将看到可用的 Log Analytics 实例。 选择一个实例，然后选择“日志”进行查询：

    ![Log Analytics][6]

1. 如果你还没有 Log Analytics 实例，可以通过单击“添加”按钮创建一个工作区：

    ![创建 OMS][7]

预配 Log Analytics 实例后，可以使用功能强大的查询在多个日志中查找条目，或通过日志管理使用特定条件进行搜索：

   ![日志管理][8]

有关功能强大的查询操作的详细信息，请参阅[开始使用查询](../azure-monitor/log-query/get-started-queries.md)。

> [!NOTE]
> 将事件首次发送到 Log Analytics 时，可能会遇到 5 分钟的延迟。

Azure Log Analytics 还提供强大的错误和警报通知服务，可通过单击“诊断并解决问题”来查看：

   ![警报和错误通知][9]

>[!TIP]
>使用 Log Analytics 查询多个应用功能、订阅或服务的日志历史记录。

## <a name="other-options"></a>其他选项

Azure 的数字孪生还支持特定于应用程序的日志记录和安全审核。 有关 Azure 数字孪生实例可用的所有 Azure 日志记录选项的完整概述，请参阅 [Azure 日志审核](../security/azure-log-audit.md)一文。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure [活动日志](../azure-monitor/platform/activity-logs-overview.md)。

- 通过阅读[诊断日志概述](../azure-monitor/platform/diagnostic-logs-overview.md)深入了解 Azure 诊断设置。

- 详细了解 [Azure Log Analytics](../azure-monitor/log-query/get-started-portal.md)。

<!-- Images -->
[1]: media/how-to-configure-monitoring/activity-log.png
[2]: media/how-to-configure-monitoring/activity-log-select.png
[3]: media/how-to-configure-monitoring/log-analytics-overview.png
[4]: media/how-to-configure-monitoring/diagnostic-settings-one.png
[5]: media/how-to-configure-monitoring/diagnostic-settings-two.png
[6]: media/how-to-configure-monitoring/log-analytics.png
[7]: media/how-to-configure-monitoring/log-analytics-oms.png
[8]: media/how-to-configure-monitoring/log-analytics-management.png
[9]: media/how-to-configure-monitoring/log-analytics-notifications.png
