---
title: 使用 Azure Monitor 监视 B2B 消息
description: 通过设置 Azure Monitor 日志和收集 Azure 逻辑应用的诊断数据来对 AS2、X12 和 EDIFACT 消息进行故障排除
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: e9ba5a516293eb72a715dc9d0df7db4d5a4ea3c5
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907976"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>设置在 Azure 逻辑应用中为 B2B 消息 Azure Monitor 日志和收集诊断数据

在集成帐户中设置贸易合作伙伴之间的 B2B 通信后，这些合作伙伴可以使用 AS2、X12 和 EDIFACT 等协议来交换消息。 若要检查此通信是否按预期方式工作，可以为集成帐户设置[Azure Monitor 日志](../azure-monitor/platform/data-platform-logs.md)。 [Azure Monitor](../azure-monitor/overview.md)可帮助你监视云和本地环境，使你能够更轻松地保持其可用性和性能。 通过使用 Azure Monitor 日志，可以在[Log Analytics 工作区](../azure-monitor/platform/resource-logs-collect-workspace.md)中记录和存储有关运行时数据和事件的数据，例如触发器事件、运行事件和操作事件。 对于消息，日志记录还会收集以下信息：

* 消息计数和状态
* 确认状态
* 消息与确认之间的关联
* 故障的详细错误说明

Azure Monitor 允许您创建[日志查询](../azure-monitor/log-query/log-query-overview.md)以帮助您查找和查看此信息。 你还可以[将此诊断数据用于其他 azure 服务](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)，例如 azure 存储和 Azure 事件中心。

若要为集成帐户设置日志记录，请在 Azure 门户中[安装逻辑应用 B2B 解决方案](#install-b2b-solution)。 此解决方案提供了 B2B 消息事件的聚合信息。 然后，若要为此信息启用日志记录和创建查询，请设置[Azure Monitor 日志](#set-up-resource-logs)。

本文介绍如何为集成帐户启用 Azure Monitor 日志记录。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisites

* Log Analytics 工作区。 如果没有 Log Analytics 工作区，请了解[如何创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。

* 使用 Azure Monitor 日志记录设置的逻辑应用，并将该信息发送到 Log Analytics 工作区。 了解[如何为逻辑应用设置 Azure Monitor 日志](../logic-apps/monitor-logic-apps.md)。

* 链接到逻辑应用的集成帐户。 了解[如何将集成帐户链接到逻辑应用](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>安装逻辑应用 B2B 解决方案

在 Azure Monitor 日志可以跟踪逻辑应用的 B2B 消息之前，请将**逻辑应用 B2B**解决方案添加到 Log Analytics 工作区。

1. 在[Azure 门户](https://portal.azure.com)的 "搜索" 框中，输入 `log analytics workspaces`，然后选择 " **Log Analytics 工作区**"。

   ![选择 "Log Analytics 工作区"](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. 在**Log Analytics 工作区**"下，选择工作区。

   ![选择 Log Analytics 工作区](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. 在 "概述" 窗格的 " **Log Analytics 开始**" > **配置监视解决方案**"下，选择"**查看解决方案**"。

   ![在 "概述" 窗格上，选择 "查看解决方案"](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. 在 "概述" 窗格上，选择 "**添加**"。

   ![在 "概述" 窗格中添加新的解决方案](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. 打开**Marketplace**后，在 "搜索" 框中输入 "`logic apps b2b`"，然后选择 "**逻辑应用 B2B**"。

   ![从 Marketplace 选择 "逻辑应用管理"](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. 在解决方案说明窗格上，选择 "**创建**"。

   ![选择 "创建" 以添加 "逻辑应用 B2B" 解决方案](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. 查看并确认要安装解决方案的 Log Analytics 工作区，并再次选择 "**创建**"。

   ![为 "逻辑应用 B2B" 选择 "创建"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Azure 将解决方案部署到包含 Log Analytics 工作区的 Azure 资源组后，解决方案将显示在工作区的 "摘要" 窗格中。 处理 B2B 消息时，将更新此窗格上的消息计数。

   ![工作区摘要窗格](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>设置 Azure Monitor 日志

可以直接从集成帐户启用 Azure Monitor 日志记录。

1. 在[Azure 门户](https://portal.azure.com)中，查找并选择你的集成帐户。

   ![查找并选择集成帐户](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. 在集成帐户的菜单的 "**监视**" 下，选择 "**诊断设置**"。 选择 "**添加诊断设置**"。

   ![在 "监视" 下，选择 "诊断设置"](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. 若要创建设置，请执行以下步骤：

   1. 提供设置的名称。

   1. 选择 "**发送到 Log Analytics**"。

   1. 对于 "**订阅**"，请选择与 Log Analytics 工作区关联的 Azure 订阅。

   1. 对于**Log Analytics 工作区**，请选择要使用的工作区。

   1. 在 "**日志**" 下，选择 " **IntegrationAccountTrackingEvents** " 类别，其中指定要记录的事件类别。

   1. 完成后，请选择 "**保存**"。

   例如: 

   ![设置 Azure Monitor 日志以收集诊断数据](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>查看消息状态

逻辑应用运行后，你可以在 Log Analytics 工作区中查看有关这些消息的状态和数据。

1. 在[Azure 门户](https://portal.azure.com)搜索框中，找到并打开 Log Analytics 工作区。

1. 在工作区菜单中，选择 "**工作区摘要**" > **逻辑应用 B2B**。

   ![工作区摘要窗格](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > 如果在运行后逻辑应用 B2B 磁贴不会立即显示结果，请尝试选择**刷新**或等待一小段时间，然后重试。

   默认情况下，"**逻辑应用 B2B** " 磁贴显示基于单个日期的数据。 若要将数据范围更改为其他间隔，请选择页面顶部的范围控件：

   ![更改间隔](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. 显示消息状态仪表板后，你可以查看特定消息类型的更多详细信息，其中显示了一天内的数据。 选择**AS2**、 **X12**或**EDIFACT**的磁贴。

   ![查看消息状态](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   此时将显示所选磁贴的消息列表。 例如，AS2 消息列表可能如下所示：

   ![AS2 消息的状态和详细信息](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   若要详细了解每种消息类型的属性，请参阅以下消息属性说明：

   * [AS2 消息属性](#as2-message-properties)
   * [X12 消息属性](#x12-message-properties)
   * [EDIFACT 消息属性](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../log-analytics/log-analytics-log-searches.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>AS2、X12 和 EDIFACT 消息的属性说明和名称格式

对于每种消息类型，以下是已下载消息文件的属性说明和名称格式。

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>AS2 消息属性说明

下面是每个 AS2 消息的属性说明。

| properties | description |
|----------|-------------|
| **寄信** | "**接收设置**" 中指定的来宾合作伙伴，或 AS2 协议的 "**发送设置**" 中指定的主机伙伴 |
| **端** | "**接收设置**" 中指定的主机伙伴，或 AS2 协议的 "**发送设置**" 中指定的来宾合作伙伴 |
| **逻辑应用** | 用于设置 AS2 操作的逻辑应用 |
| **状态值** | AS2 消息状态 <br>成功 = 接收或发送了有效的 AS2 消息。 未设置 MDN。 <br>成功 = 接收或发送了有效的 AS2 消息。 已设置并接收 MDN，或发送 MDN。 <br>Failed = 接收到无效的 AS2 消息。 未设置 MDN。 <br>挂起 = 收到或发送了有效的 AS2 消息。 已设置 MDN，并且应为 MDN。 |
| **ACK** | MDN 消息状态 <br>已接受 = 接收或发送了肯定 MDN。 <br>挂起 = 正在等待接收或发送 MDN。 <br>已拒绝 = 收到或发送了否定 MDN。 <br>不需要 = 协议中未设置 MDN。 |
| **方向键** | AS2 消息方向 |
| **跟踪 ID** | 关联逻辑应用中所有触发器和操作的 ID |
| **消息 ID** | AS2 消息标头中的 AS2 消息 ID |
| **标志** | AS2 操作处理消息的时间 |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>X12 消息属性说明

下面是每个 X12 消息的属性说明。

| properties | description |
|----------|-------------|
| **寄信** | "**接收设置**" 中指定的来宾合作伙伴，或 X12 协议的 "**发送设置**" 中指定的主机伙伴 |
| **端** | "**接收设置**" 中指定的主机伙伴或 X12 协议的 "**发送设置**" 中指定的来宾合作伙伴 |
| **逻辑应用** | 设置 X12 操作的逻辑应用 |
| **状态值** | X12 消息状态 <br>成功 = 接收或发送了有效的 X12 消息。 未设置功能确认。 <br>成功 = 接收或发送了有效的 X12 消息。 已设置并接收功能确认，或者发送功能确认。 <br>Failed = 接收或发送了无效的 X12 消息。 <br>挂起 = 收到或发送了有效的 X12 消息。 已设置功能确认，并且应为功能确认。 |
| **ACK** | 功能确认（997）状态 <br>已接受 = 接收或发送正功能确认。 <br>已拒绝 = 收到或发送了否定功能确认。 <br>挂起 = 应为功能确认，但未收到。 <br>挂起 = 生成了功能确认，但无法发送到合作伙伴。 <br>不需要 = 未设置功能确认。 |
| **方向键** | X12 消息方向 |
| **跟踪 ID** | 关联逻辑应用中所有触发器和操作的 ID |
| **消息类型** | EDI X12 消息类型 |
| **ICN** | X12 消息的交换控制编号 |
| **TSCN** | X12 消息的事务集控制编号 |
| **标志** | X12 操作处理消息的时间 |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>EDIFACT 消息属性说明

下面是每个 EDIFACT 消息的属性说明。

| properties | description |
|----------|-------------|
| **寄信** | 在 "**接收设置**" 中指定的来宾合作伙伴，或在 EDIFACT 协议的 "**发送设置**" 中指定的主机伙伴 |
| **端** | 在 "**接收设置**" 中指定的主机伙伴，或 EDIFACT 协议的 "**发送设置**" 中指定的来宾合作伙伴 |
| **逻辑应用** | 设置 EDIFACT 操作的逻辑应用 |
| **状态值** | EDIFACT 消息状态 <br>成功 = 接收或发送了有效的 EDIFACT 消息。 未设置功能确认。 <br>成功 = 接收或发送了有效的 EDIFACT 消息。 已设置并接收功能确认，或者发送功能确认。 <br>失败 = 收到或发送了无效的 EDIFACT 消息 <br>挂起 = 收到或发送了有效的 EDIFACT 消息。 已设置功能确认，并且应为功能确认。 |
| **ACK** | 功能确认（CONTRL）状态 <br>已接受 = 接收或发送正功能确认。 <br>已拒绝 = 收到或发送了否定功能确认。 <br>挂起 = 应为功能确认，但未收到。 <br>挂起 = 生成了功能确认，但无法发送到合作伙伴。 <br>不需要 = 未设置功能确认。 |
| **方向键** | EDIFACT 消息方向 |
| **跟踪 ID** | 关联逻辑应用中所有触发器和操作的 ID |
| **消息类型** | EDIFACT 消息类型 |
| **ICN** | EDIFACT 消息的交换控制编号 |
| **TSCN** | EDIFACT 消息的事务集控制编号 |
| **标志** | EDIFACT 操作处理消息的时间 |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>后续步骤

* [创建监视和跟踪查询](../logic-apps/create-monitoring-tracking-queries.md)