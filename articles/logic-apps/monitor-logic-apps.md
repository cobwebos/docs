---
title: 监视状态、查看历史记录和设置警报
description: 通过检查运行状态、查看触发器历史记录和启用 Azure 逻辑应用中的警报来排除逻辑应用的故障
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 495877f1c839de2cf3583a37180054c91bd9f139
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907768"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>监视运行状态、查看触发器历史记录并为 Azure 逻辑应用设置警报

[创建并运行逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)后，可以检查逻辑应用的运行状态、[运行历史记录](#review-runs-history)、[触发历史记录](#review-trigger-history)和性能。 要获取关于故障或其他可能问题的通知，请设置[警报](#add-azure-alerts)。 例如，可以创建一个警报，用于检测“一小时内运行失败超过五次的情况”。

要进行实时事件监视和更丰富的调试，请使用[Azure 监视器日志](../azure-monitor/overview.md)为逻辑应用设置诊断日志记录。 此 Azure 服务可帮助您监视云和本地环境，以便更轻松地维护其可用性和性能。 然后，您可以查找和查看事件，如触发器事件、运行事件和操作事件。 通过在[Azure 监视器日志](../azure-monitor/platform/data-platform-logs.md)中存储此信息，可以创建[日志查询](../azure-monitor/log-query/log-query-overview.md)，以帮助您查找和分析此信息。 还可以将此诊断数据用于其他 Azure 服务，如 Azure 存储和 Azure 事件中心。 有关详细信息，请参阅使用[Azure 监视器监视逻辑应用](../logic-apps/monitor-logic-apps-log-analytics.md)。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>查看运行历史记录

每次触发项目或事件时，逻辑应用引擎都会为每个项或事件创建并运行单独的工作流实例。 默认情况下，每个工作流实例并行运行，因此无需工作流等待才能开始运行。 您可以查看该运行期间发生的情况，包括工作流中每个步骤的状态以及每个步骤的输入和输出。

1. 在[Azure 门户](https://portal.azure.com)中，在逻辑应用设计器中查找并打开逻辑应用。

   要在主 Azure 搜索框中查找逻辑应用，请输入`logic apps`，然后选择**逻辑应用**。

   ![查找并选择"逻辑应用"服务](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure 门户显示与 Azure 订阅关联的所有逻辑应用。 您可以根据名称、订阅、资源组、位置等筛选此列表。

   ![查看与订阅关联的逻辑应用](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. 选择逻辑应用，然后选择 **"概述**"。

   在"**运行历史记录"** 下的"概述"窗格中，将显示逻辑应用的所有过去、当前和任何等待运行。 如果列表显示多个运行，并且找不到所需的条目，请尝试筛选列表。 如果找不到预期的数据，请尝试在工具栏中选择“刷新”****。

   ![概述、运行历史记录和其他逻辑应用信息](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   以下是逻辑应用运行的可能状态：

   | 状态 | 描述 |
   |--------|-------------|
   | **已取消** | 工作流正在运行，但收到取消请求 |
   | **失败** | 至少有一个操作失败，并且未设置工作流中的后续操作来处理失败 |
   | **正在运行** | 当前工作流正在运行。 <p>此状态也可能出现在受限制的工作流中，或者由于当前定价计划。 有关详细信息，请参阅[定价的操作限制页](https://azure.microsoft.com/pricing/details/logic-apps/)。 如果设置[诊断日志记录](../logic-apps/monitor-logic-apps.md)，则可以获取有关发生的任何节流事件的信息。 |
   | **成功** | 所有操作成功。 <p>**注意**：如果特定操作中发生任何失败，工作流中的后续操作将处理该失败。 |
   | **等待** | 工作流尚未启动或暂停，例如，由于早期工作流仍在运行。 |
   |||

1. 要查看特定运行的步骤和其他信息，请在 **"运行历史记录"** 下选择该运行。

   ![选择要查看的特定运行](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   **Logic 应用运行**窗格显示所选运行中的每个步骤、每个步骤的运行状态以及每个步骤的运行时间，例如：

   ![特定运行中的每个操作](./media/monitor-logic-apps/logic-app-run-pane.png)

   要以列表形式查看此信息，请在**逻辑应用运行**工具栏上选择 **"运行详细信息**"。

   ![在工具栏上，选择"运行详细信息"](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   "运行详细信息"视图显示每个步骤、其状态和其他信息。

   ![查看有关运行中每个步骤的详细信息](./media/monitor-logic-apps/review-logic-app-run-details.png)

   例如，您可以获取运行的**关联 ID**属性，当您[对逻辑应用](https://docs.microsoft.com/rest/api/logic)使用 REST API 时可能需要该属性。

1. 要获取有关特定步骤的详细信息，请选择任一选项：

   * 在 **"逻辑"应用运行**窗格中选择步骤，以便形状展开。 现在，您可以查看输入、输出等信息以及该步骤中发生的任何错误，例如：

     ![在逻辑应用运行窗格中，查看失败步骤](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * 在 **"逻辑"应用运行详细信息**窗格中，选择所需的步骤。

     ![在运行详细信息窗格中，查看失败步骤](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     现在，您可以查看该步骤的输入和输出等信息，例如：

   > [!NOTE]
   > 所有运行时详细信息和事件都在逻辑应用服务中进行加密。 只有当用户请求查看该数据时，才会进行解密。 您可以使用[Azure 基于角色的访问控制 （RBAC）](../role-based-access-control/overview.md)[隐藏运行历史记录中的输入和输出](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate)，或控制用户访问此信息。

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>查看触发器历史记录

每个逻辑应用运行都以触发器开始。 触发器历史记录列出了逻辑应用进行的所有触发器尝试，以及有关每个触发器尝试的输入和输出的信息。

1. 在[Azure 门户](https://portal.azure.com)中，在逻辑应用设计器中查找并打开逻辑应用。

   要在主 Azure 搜索框中查找逻辑应用，请输入`logic apps`，然后选择**逻辑应用**。

   ![查找并选择"逻辑应用"服务](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure 门户显示与 Azure 订阅关联的所有逻辑应用。 您可以根据名称、订阅、资源组、位置等筛选此列表。

   ![查看与订阅关联的逻辑应用](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. 选择逻辑应用，然后选择 **"概述**"。

1. 在逻辑应用的菜单中，选择“概述”****。 在 **"摘要"** 部分中，在 **"评估"** 下，选择 **"查看触发历史记录**"。

   ![查看逻辑应用的触发器历史记录](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   触发器历史记录窗格显示逻辑应用已进行的所有触发器尝试。 每次触发项目或事件时，逻辑应用引擎都会创建运行工作流的单独逻辑应用实例。 默认情况下，每个实例并行运行，因此在启动运行前无需等待任何工作流。 因此，如果逻辑应用同时触发多个项目，则每个项目都会显示具有相同日期和时间的触发器条目。

   ![不同项的多次触发尝试](./media/monitor-logic-apps/logic-app-trigger-history.png)

   触发器尝试可能的状态如下：

   | 状态 | 描述 |
   |--------|-------------|
   | **失败** | 出现了错误。 若要查看失败触发器生成的任何错误消息，请选择该触发器尝试并选择“输出”****。 例如，你可能发现输入无效。 |
   | **跳** | 触发器已检查终结点，但未找到任何数据。 |
   | **成功** | 触发器已检查终结点并找到可用数据。 通常，此状态还会伴随出现“已触发”状态。 如果没有，触发器定义可能未满足某一条件或 `SplitOn` 命令。 <p>此状态可应用于手动触发器、定期触发器或轮询触发器。 如果操作生成未处理的错误，尽管可以成功运行触发器，但运行本身可能仍会失败。 |
   |||

   > [!TIP]
   > 可以重新检查触发器，无需等待下一个周期。 在概览工具栏上，选择 **"运行触发器**"并选择强制检查的触发器。 或者，在“逻辑应用设计器”工具栏中选择“运行”****。

1. 要查看有关特定触发器尝试的信息，请在触发器窗格中选择该触发器事件。 如果列表显示许多触发器尝试，并且找不到所需的条目，请尝试筛选列表。 如果找不到预期的数据，请尝试在工具栏中选择“刷新”****。

   ![查看特定触发器尝试](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   您现在可以查看有关所选触发器事件的信息，例如：

   ![查看特定触发器信息](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>设置监视警报

要根据特定指标获取警报或逻辑应用的阈值超过，[请在 Azure 监视器 中设置警报](../azure-monitor/platform/alerts-overview.md)。 了解 [Azure 中的指标](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。 要设置警报而不使用[Azure 监视器](../log-analytics/log-analytics-overview.md)，请按照以下步骤操作。

1. 在逻辑应用菜单上，在 **"监视**"下，选择 **"警报** > **新警报规则**"。

   ![为逻辑应用添加警报](./media/monitor-logic-apps/add-new-alert-rule.png)

1. 在 **"创建规则**"窗格（**在"资源**"下）上，选择逻辑应用（如果尚未选中）。 在 **"条件**"下，选择 **"添加**"，以便可以定义触发警报的条件。

   ![添加规则的条件](./media/monitor-logic-apps/add-condition-for-rule.png)

1. 在 **"配置信号逻辑**窗格"上，查找并选择要为其获取警报的信号。 您可以使用搜索框，或者按字母顺序对信号进行排序，请选择 **"信号名称**列"标题。

   例如，如果要在触发器失败时发送警报，请按照以下步骤操作：

   1. 在 **"信号名称**"列中，查找并选择**触发器失败**信号。

      ![选择用于创建警报的信号](./media/monitor-logic-apps/find-and-select-signal.png)

   1. 在为所选信号打开的信息窗格中，在 **"警报"逻辑**下设置您的条件，例如：

   1. 对于**运算符**，选择**大于 或 等于**。

   1. 对于**聚合类型**，选择 **"计数**"。

   1. 对于**阈值**，输入`1`。

   1. 在 **"条件预览**"下，确认您的病情看起来正确。

   1. **在"基于 评估"** 下，设置运行警报规则的间隔和频率。 对于**聚合粒度（期间），** 选择对数据进行分组的期间。 对于**评估频率**，选择要检查条件的频率。

   1. 准备就绪后，选择“完成”。****

   下面是已完成的条件：

   ![设置警报条件](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   **"创建规则**"页现在显示您创建的条件和运行该警报的成本。

   !["创建规则"页上的新警报](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. 为警报指定名称、可选说明和严重性级别。 在创建设置**打开时保持启用规则**，或关闭，直到准备好启用该规则。

1. 完成后，选择 **"创建警报规则**"。

> [!TIP]
> 要从警报运行逻辑应用，可以在工作流中包括[请求触发器](../connectors/connectors-native-reqres.md)，从而可以执行类似以下示例的任务：
> 
> * [发布到 Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [发送短信](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [向队列添加消息](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>后续步骤

* [使用 Azure 监视器监视逻辑应用](../logic-apps/monitor-logic-apps-log-analytics.md)