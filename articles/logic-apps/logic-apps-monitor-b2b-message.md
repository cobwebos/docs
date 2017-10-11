---
title: "监视 B2B 事务并设置日志记录 - Azure 逻辑应用 | Microsoft Docs"
description: "监视 AS2、X12 和 EDIFACT 消息，启动集成帐户的诊断日志记录"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: f717dae9a70a96944b623f22b90cf8c5a943f382
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="monitor-and-set-up-diagnostics-logging-for-b2b-communication-in-integration-accounts"></a>监视和设置用于在集成帐户中进行 B2B 通信的诊断日志记录

在你通过集成帐户在两个运行的业务流程或应用程序之间设置 B2B 通信之后，这些实体可以彼此交换消息。 要确认此通信是否按预期方式工作，可通过 [Azure Log Analytics](../log-analytics/log-analytics-overview.md) 服务设置对 AS2、X12 和 EDIFACT 消息的监视以及对集成帐户的诊断日志记录。 [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) 中的此项服务可监视云和本地环境，有助于保持其可用性和性能，还可以收集运行时详细信息和事件以进行更丰富的调试。 此外，还可以[将诊断数据用于其他服务](#extend-diagnostic-data)，如 Azure 存储和 Azure 事件中心。

## <a name="requirements"></a>要求

* 设置有诊断日志记录的逻辑应用。 了解[如何对该逻辑应用设置日志记录](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)。

  > [!NOTE]
  > 满足此要求后，在 [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) 中应该已经有一个工作区。 为集成帐户设置日志记录时，应使用相同的 OMS 工作区。 如果没有 OMS 工作区，了解[如何创建 OMS 工作区](../log-analytics/log-analytics-get-started.md)。

* 关联到逻辑应用的集成帐户。 了解[如何创建关联到逻辑应用的集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>为集成帐户启用诊断日志记录

可以从集成帐户直接启用日志记录，也可以[通过 Azure Monitor 服务](#azure-monitor-service)启用。 Aure Monitor 通过基础结构级数据提供基本监视。 详细了解 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md)。

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>直接从集成帐户启用诊断日志记录

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择“集成帐户”。 在“监视”下，选择“诊断日志”，如下所示：

   ![查找并选择“集成帐户”，然后选择“诊断日志”](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. 选择“集成帐户”后，会自动选择以下值。 如果这些值正确无误，请选择“启用诊断”。 否则，请选择所需的值：

   1. 在“订阅”下，选择要用于集成帐户的 Azure 订阅。
   2. 在“资源”下，选择要用于集成帐户的资源组。
   3. 在“资源类型”下，选择“集成帐户”。 
   4. 在“资源”下，选择“集成帐户”。 
   5. 选择“启用诊断”。

   ![为集成帐户设置诊断](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. 在“诊断设置”、“状态”下，选择“启用”。

   ![启用 Azure 诊断](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. 现在选择用于日志记录的 OMS 工作区和数据，如下所示：

   1. 选择“发送到 Log Analytics”。 
   2. 在“Log Analytics”下，选择“配置”。 
   3. 在“OMS 工作区”下，选择要用于日志记录的 OMS 工作区。
   4. 在“日志”下，选择“IntegrationAccountTrackingEvents”类别。
   5. 选择“保存”。

   ![设置 Log Analytics，这样便可以将诊断数据发送到日志](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. 现在[在 OMS 中设置对 B2B 消息的跟踪](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)。

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>通过 Azure Monitor 启用诊断日志记录

1. 在 [Azure 门户](https://portal.azure.com)的 Azure 主菜单中，依次选择“监视”、“诊断日志”。 然后选择“集成帐户”，如下所示：

   ![选择“监视”、“诊断日志”，然后选择“集成帐户”](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. 选择“集成帐户”后，会自动选择以下值。 如果这些值正确无误，请选择“启用诊断”。 否则，请选择所需的值：

   1. 在“订阅”下，选择要用于集成帐户的 Azure 订阅。
   2. 在“资源”下，选择要用于集成帐户的资源组。
   3. 在“资源类型”下，选择“集成帐户”。
   4. 在“资源”下，选择“集成帐户”。
   5. 选择“启用诊断”。

   ![为集成帐户设置诊断](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. 在“诊断设置”下，选择“打开”。

   ![启用 Azure 诊断](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. 现在选择日志记录的 OMS 工作区和事件类别，如下所示：

   1. 选择“发送到 Log Analytics”。 
   2. 在“Log Analytics”下，选择“配置”。 
   3. 在“OMS 工作区”下，选择要用于日志记录的 OMS 工作区。
   4. 在“日志”下，选择“IntegrationAccountTrackingEvents”类别。
   5. 完成后，选择“保存”。

   ![设置 Log Analytics，这样便可以将诊断数据发送到日志](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. 现在[在 OMS 中设置对 B2B 消息的跟踪](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)。

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>扩展将诊断数据用于其他服务的方式和位置

结合使用 Azure Log Analytics 后，可以扩展将逻辑应用的诊断数据用于其他 Azure 服务的方式，例如： 

* [在 Azure 存储中存档 Azure 诊断日志](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [将 Azure 诊断日志流式传输到 Azure 事件中心](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

然后，可通过使用其他服务（如 [Azure 流分析](../stream-analytics/stream-analytics-introduction.md)和 [Power BI](../log-analytics/log-analytics-powerbi.md)）的遥测数据和分析来进行实时监视。 例如：

* [将数据从事件中心流式传输到流分析](../stream-analytics/stream-analytics-define-inputs.md)
* [在 Power BI 中使用流分析来分析流数据，并创建实时分析仪表板](../stream-analytics/stream-analytics-power-bi-dashboard.md)

根据要设置的选项，确保首先[创建 Azure 存储帐户](../storage/common/storage-create-storage-account.md)或[创建 Azure 事件中心](../event-hubs/event-hubs-create.md)。 然后选择要发送诊断数据的位置选项：

![将数据发送到 Azure 存储帐户或事件中心](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> 仅当你选择使用存储帐户时才应用保留期。

## <a name="supported-tracking-schemas"></a>支持的跟踪架构

Azure 支持这些跟踪架构类型，它们都具有除自定义类型以外的固定架构。

* [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 跟踪架构](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [自定义跟踪架构](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>后续步骤

* [在 OMS 中跟踪 B2B 消息](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "在 OMS 中跟踪 B2B 消息")
* [了解有关 Enterprise Integration Pack 的详细信息](../logic-apps/logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack")

