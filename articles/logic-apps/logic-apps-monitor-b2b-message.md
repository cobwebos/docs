---
title: 用 Azure Monitor 监视 B2B 消息
description: 使用 Azure Monitor 在 Azure 逻辑应用中为 AS2、X12 和 EDIFACT 消息设置诊断日志记录
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: cd96376f764ec3075b916bf2207ec6ee3dd3fcbd
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791940"
---
# <a name="set-up-diagnostics-logging-for-b2b-messages-in-azure-logic-apps-by-using-azure-monitor"></a>使用 Azure Monitor 在 Azure 逻辑应用中为 B2B 消息设置诊断日志记录

在集成帐户中的贸易合作伙伴之间建立 B2B 通信后，这些合作伙伴可以交换消息。 若要检查此通信是否按预期方式工作，可以监视 AS2、X12 和 EDIFACT 消息，并使用[Azure Monitor 日志](../log-analytics/log-analytics-overview.md)为集成帐户设置诊断日志记录。 此服务可监视云和本地环境，有助于保持其可用性和性能以及收集运行时详细信息和事件以进行更丰富的调试。 此外可以将此数据与其他服务一起使用，例如 Azure 存储和 Azure 事件中心。

> [!NOTE]
> 此页面可能仍然引用了 Microsoft Operations Management Suite (OMS)，它将[于 2019 年 1 月停用](../azure-monitor/platform/oms-portal-transition.md)，但尽可能使用 Azure Log Analytics 替换这些步骤。 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>必备组件

* 已设置诊断日志记录的逻辑应用。 了解[如何创建逻辑应用](quickstart-create-first-logic-app-workflow.md)以及[如何为逻辑应用设置日志记录](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)。

* 满足上述要求后，还需要一个 Log Analytics 工作区，用于通过 Azure Monitor 日志监视和跟踪 B2B 通信。 如果没有 Log Analytics 工作区，请了解[如何创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。

* 关联到逻辑应用的集成帐户。 了解[如何创建关联到逻辑应用的集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。

## <a name="turn-on-diagnostics-logging"></a>启用诊断日志记录

可以从集成帐户直接启用日志记录，也可以[通过 Azure Monitor 服务](#azure-monitor-service)启用。 Aure Monitor 通过基础结构级数据提供基本监视。 详细了解 [Azure Monitor](../azure-monitor/overview.md)。

### <a name="turn-on-logging-from-integration-account"></a>从集成帐户启用日志记录

1. 在 [Azure 门户](https://portal.azure.com)中，查找并选择“集成帐户”。 在“监视”下，选择“诊断设置”。

   ![查找并选择“集成帐户”，选择“诊断设置”](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. 现可找到集成帐户并进行选择。 在筛选器列表中，选择应用到集成帐户的值。
完成后，选择“添加诊断设置”。

   | properties | Value | 描述 | 
   |----------|-------|-------------|
   | 订阅 | <*Azure-subscription-name*> | 与集成帐户关联的 Azure 订阅 | 
   | **资源组** | <*Azure-resource-group-name*> | 集成帐户的 Azure 资源组 | 
   | **资源类型** | **集成帐户** | 要在其中启用日志记录的 Azure 资源类型 | 
   | **资源** | <integration-account-name> | 要在其中启用日志记录的 Azure 资源的名称 | 
   ||||  

   例如：

   ![为集成帐户设置诊断](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. 命名新的诊断设置并选择 Log Analytics 工作区和要记录的数据。

   1. 选择“发送到 Log Analytics”。 

   1. 在“Log Analytics”下，选择“配置”。 

   1. 在“OMS 工作区”下，选择要用于日志记录的 Log Analytics 工作区。 

      > [!NOTE]
      > OMS 工作区即将替换为 Log Analytics 工作区。 

   1. 在“日志”下，选择“IntegrationAccountTrackingEvents”类别，然后选择“保存”。

   例如： 

   ![设置 Azure Monitor 日志，以便可以将诊断数据发送到日志](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. 现在，[在 Azure Monitor 日志中设置 B2B 消息的跟踪](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)。

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>通过 Azure Monitor 启用日志记录

1. 在 [Azure 门户](https://portal.azure.com)中的 Azure 主菜单上，选择“监视”。 选择“设置”下的“诊断设置”。 

   ![选择“监视”>“诊断设置”>“集成帐户”](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. 现可找到集成帐户并进行选择。 在筛选器列表中，选择应用到集成帐户的值。
完成后，选择“添加诊断设置”。

   | properties | Value | 描述 | 
   |----------|-------|-------------|
   | 订阅 | <*Azure-subscription-name*> | 与集成帐户关联的 Azure 订阅 | 
   | **资源组** | <*Azure-resource-group-name*> | 集成帐户的 Azure 资源组 | 
   | **资源类型** | **集成帐户** | 要在其中启用日志记录的 Azure 资源类型 | 
   | **资源** | <integration-account-name> | 要在其中启用日志记录的 Azure 资源的名称 | 
   ||||  

   例如：

   ![为集成帐户设置诊断](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. 命名新的诊断设置并选择 Log Analytics 工作区和要记录的数据。

   1. 选择“发送到 Log Analytics”。 

   1. 在“Log Analytics”下，选择“配置”。 

   1. 在“OMS 工作区”下，选择要用于日志记录的 Log Analytics 工作区。 

      > [!NOTE]
      > OMS 工作区即将替换为 Log Analytics 工作区。 

   1. 在“日志”下，选择“IntegrationAccountTrackingEvents”类别，然后选择“保存”。

   例如： 

   ![设置 Azure Monitor 日志，以便可以将诊断数据发送到日志](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. 现在，[在 Azure Monitor 日志中设置 B2B 消息的跟踪](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)。

## <a name="use-diagnostic-data-with-other-services"></a>将诊断数据与其他服务一起使用

除了 Azure Monitor 日志，你还可以扩展将逻辑应用的诊断数据与其他 Azure 服务配合使用的方式，例如： 

* [在 Azure 存储中存档 Azure 诊断日志](../azure-monitor/platform/archive-diagnostic-logs.md)
* [将 Azure 诊断日志流式传输到 Azure 事件中心](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

然后，可通过使用其他服务（如 [Azure 流分析](../stream-analytics/stream-analytics-introduction.md)和 [Power BI](../azure-monitor/platform/powerbi.md)）的遥测数据和分析来进行实时监视。 例如：

* [将数据从事件中心流式传输到流分析](../stream-analytics/stream-analytics-define-inputs.md)
* [在 Power BI 中使用流分析来分析流数据，并创建实时分析仪表板](../stream-analytics/stream-analytics-power-bi-dashboard.md)

根据要设置的选项，确保先[创建 Azure 存储帐户](../storage/common/storage-create-storage-account.md)或[创建 Azure 事件中心](../event-hubs/event-hubs-create.md)。 然后，可选择要将诊断数据发送至何处。
仅当你选择使用存储帐户时才应用保留期。

![将数据发送到 Azure 存储帐户或事件中心](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>支持的跟踪架构

Azure 支持这些跟踪架构类型，它们都具有除自定义类型以外的固定架构。

* [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12 跟踪架构](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [自定义跟踪架构](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>后续步骤

* [在 Azure Monitor 日志中跟踪 B2B 消息](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "在 Azure Monitor 日志中跟踪 B2B 消息")
* [了解有关 Enterprise Integration Pack 的详细信息](../logic-apps/logic-apps-enterprise-integration-overview.md "了解 Enterprise Integration Pack")

