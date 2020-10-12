---
title: 使用顺序保护按顺序发送相关消息
description: 通过 Azure 服务总线在 Azure 逻辑应用中使用顺序保护模式按顺序发送相关消息
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 8c00d2e4f622bcfad7b2468013336f0d936e318c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87048667"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>通过 Azure 服务总线在 Azure 逻辑应用中使用顺序保护按顺序发送相关消息

如果需要按特定顺序发送相关消息，可以[在使用 Azure](../logic-apps/logic-apps-overview.md) [服务总线连接器](../connectors/connectors-create-api-servicebus.md)时遵循[*顺序保护*模式](/azure/architecture/patterns/sequential-convoy)。 相关消息有一个用于定义这些消息之间的关系的属性，例如服务总线中的[会话](../service-bus-messaging/message-sessions.md)的 ID。

例如，假设你有 10 条消息用于名为“会话 1”的会话，有 5 条消息用于名为“会话 2”的会话，且所有这些消息都发送到同一[服务总线队列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)。 你可以创建一个逻辑应用，用于处理来自队列的消息，使来自“会话 1”的所有消息都由单个触发器运行处理，使来自“会话 2”的所有消息则都由下一个触发器运行处理。

![常规顺序保护模式](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

本文介绍了如何通过“使用服务总线会话按顺序传送相关消息”模板来创建实现此模式的逻辑应用。 此模板定义了一个逻辑应用工作流，该工作流从服务总线连接器的“队列中收到消息时(扫视锁定)”触发器开始，而该触发器从[服务总线队列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)接收消息。 下面是此逻辑应用执行的大致步骤：

* 根据触发器从服务总线队列读取的消息初始化会话。

* 在当前工作流运行期间，读取并处理来自队列中同一会话的所有消息。

若要查看此模板的 JSON 文件，请参阅 [GitHub：service-bus-sessions.json](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json)。

有关详细信息，请参阅 [顺序保护模式-Azure 体系结构云设计模式](/azure/architecture/patterns/sequential-convoy)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 服务总线命名空间和[服务总线队列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)，后者是将在逻辑应用中使用的消息传送实体。 这些项和你的逻辑应用需使用同一 Azure 订阅。 在创建队列时，请确保选择“启用会话”。 如果没有这些项，请了解[如何创建服务总线命名空间和队列](../service-bus-messaging/service-bus-create-namespace-portal.md)。

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* 有关如何创建逻辑应用的基础知识。 如果不熟悉 Azure 逻辑应用，请尝试学习快速入门：[创建你的第一个自动化工作流](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>检查对服务总线命名空间的访问权限

如果不确定逻辑应用是否有权访问服务总线命名空间，请确认这些权限。

1. 登录到 [Azure 门户](https://portal.azure.com)。 找到并选择你的服务总线命名空间。

1. 在命名空间菜单上的“设置”下，选择“共享访问策略”。  在“声明”下，检查你是否有该命名空间的“管理”权限。 

   ![管理服务总线命名空间的权限](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. 现在，获取你的服务总线命名空间的连接字符串。 稍后在创建从逻辑应用到命名空间的连接时可以使用此字符串。

   1. 在“共享访问策略”窗格中，在“策略”下，选择“RootManageSharedAccessKey”  。
   
   1. 在主连接字符串旁边选择复制按钮。 保存连接字符串供以后使用。

      ![复制服务总线命名空间连接字符串](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > 若要确认连接字符串是与服务总线命名空间关联还是与消息传送实体（例如队列）关联，请在该连接字符串中搜索 `EntityPath`  参数。 如果找到了该参数，则表示连接字符串适用于特定的实体，不是适用于逻辑应用的正确字符串。

## <a name="create-logic-app"></a>创建逻辑应用

在此部分中，你将使用“使用服务总线会话按顺序传送相关消息”模板来创建逻辑应用，该模板包括用于实现此工作流模式的触发器和操作。 另外，请创建一个到服务总线命名空间的连接，并指定要使用的服务总线队列的名称。

1. 在 [Azure 门户](https://portal.azure.com)中，创建一个空白逻辑应用。 在 Azure 主页中，选择“创建资源” > “集成” > “逻辑应用”。  

1. 在模板库出现后，滚动经过视频和常用触发器部分。 从“模板”部分中，选择“使用服务总线会话按顺序传送相关消息”模板。

   ![选择“使用服务总线会话按顺序传送相关消息”模板](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. 在确认框出现后，选择“使用此模板”。

1. 在逻辑应用设计器中，在“服务总线”形状中，选择“继续”，然后选择形状中显示的加号 ( **+** )。

   ![选择“继续”以连接到 Azure 服务总线](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. 现在，通过选择以下任一选项来创建服务总线连接：

   * 若要使用之前从服务总线命名空间复制的连接字符串，请执行以下步骤：

     1. 选择“手动输入连接信息”。

     1. 对于“连接名称”，请为连接提供一个名称。 对于“连接字符串”，请粘贴你的命名空间连接字符串，然后选择“创建”，例如： 

        ![输入连接名称和服务总线连接字符串](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > 如果你没有此连接字符串，请了解如何[查找并复制服务总线命名空间连接字符串](#permissions-connection-string)。

   * 若要从当前 Azure 订阅中选择服务总线命名空间，请执行以下步骤：

     1. 对于“连接名称”，请为连接提供一个名称。 对于“服务总线命名空间”，请选择你的服务总线命名空间，例如：

        ![输入连接名称并选择服务总线命名空间](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. 当下一个窗格出现时，请选择你的服务总线策略，然后选择“创建”。

        ![选择服务总线策略，然后选择“创建”](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. 完成后，选择“继续”。

   逻辑应用设计器现在会显示“使用服务总线会话按顺序传送相关消息”模板，该模板包含一个预先填充了触发器和操作的工作流，其中包括的两个作用域实现了遵循 `Try-Catch` 模式的错误处理。

现在，你可以了解有关模板中的触发器和操作的详细信息，也可以直接跳到[为逻辑应用模板提供值](#complete-template)。

<a name="template-summary"></a>

## <a name="template-summary"></a>模板摘要

下面是折叠详细信息后，“使用服务总线会话按顺序传送相关消息”模板中的大致工作流：

![模板的大致工作流](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| 名称 | 说明 |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | 此服务总线触发器会根据指定的重复周期检查指定的服务总线队列中是否有任何消息。 如果队列中存在消息，则触发器将触发，这将创建并运行一个工作流实例。 <p><p>“扫视锁定”这一术语是指触发器发送从队列中检索消息的请求。 如果有消息存在，则触发器会检索并锁定该消息，防止在锁定期限到期之前对该消息进行其他处理。 有关详细信息，请参阅[初始化会话](#initialize-session)。 |
| **`Init isDone`** | 此[**初始化变量**操作](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable)创建一个设置为 `false` 的布尔变量，并指示何时满足以下条件： <p><p>- 会话中没有剩余可供读取的消息。 <br>- 不再需要续订会话锁，就能完成当前工作流实例。 <p><p>有关详细信息，请参阅[初始化会话](#initialize-session)。 |
| **`Try`** | 此 [**作用域** 操作](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) 包含用于处理消息的操作。 如果 `Try` 作用域中出现问题，则后续的 `Catch` **作用域**操作会处理该问题。 有关详细信息，请参阅[“Try”作用域](#try-scope)。 |
| **`Catch`**| 此 [**作用域** 操作](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) 包含上一个作用域中发生问题时运行的操作 `Try` 。 有关详细信息，请参阅[“Catch”作用域](#catch-scope)。 |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>“Try”作用域

下面是 `Try` 折叠详细信息时 [作用域操作](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) 中的顶层流：

![“Try”作用域操作工作流](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| 名称 | 说明 |
|------|-------------|
| **`Send initial message to topic`** | 可以将此操作替换对来自队列中会话的第一条消息进行处理时需要执行的任何操作。 会话 ID 用于指定会话。 <p><p>对于此模板，服务总线操作会将第一条消息发送到一个服务总线主题。 有关详细信息，请参阅[处理初始消息](#handle-initial-message)。 |
| （并行分支） | 此[并行分支操作](../logic-apps/logic-apps-control-flow-branches.md)创建两个路径： <p><p>- 分支 1：继续处理消息。 有关详细信息，请参阅[分支 1：完成队列中的初始消息](#complete-initial-message)。 <p><p>- 分支 2：如果出现问题，则放弃该消息，将其释放，供另一个触发器运行来拾取。 有关详细信息，请参阅[分支 2：放弃队列中的初始消息](#abandon-initial-message)。 <p><p>这两个路径稍后会在“关闭队列中的会话并成功”操作中汇合，详见下一行中的说明。 |
| **`Close a session in a queue and succeed`** | 此服务总线操作将前面所述的分支汇合，并在发生以下事件之一后关闭队列中的会话： <p><p>- 工作流完成了对队列中可用消息的处理。 <br>- 工作流因为某个错误放弃了初始消息。 <p><p>有关详细信息，请参阅[关闭队列中的会话并成功](#close-session-succeed)。 |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>分支 1：完成队列中的初始消息

| 名称 | 说明 |
|------|-------------|
| `Complete initial message in queue` | 此服务总线操作会将成功检索的消息标记为完成，然后从队列中删除该消息，防止重新处理。 有关详细信息，请参阅[处理初始消息](#handle-initial-message)。 |
| `While there are more messages for the session in the queue` | 此 [**Until** 循环](../logic-apps/logic-apps-control-flow-loops.md#until-loop)会在有消息存在时或一小时过后继续获取消息。 有关此循环中的操作的详细信息，请参阅[当队列中的会话存在剩余消息时](#while-more-messages-for-session)。 |
| **`Set isDone = true`** | 当不存在剩余消息时，此[“设置变量”操作](../logic-apps/logic-apps-create-variables-store-values.md#set-variable)会将 `isDone` 设置为 `true`。 |
| **`Renew session lock until cancelled`** | 此 [**Until** 循环](../logic-apps/logic-apps-control-flow-loops.md#until-loop)可确保此逻辑应用在有消息存在时或一小时过后持有会话锁。 有关此循环中的操作的详细信息，请参阅[续订会话锁直至取消](#renew-session-while-messages-exist)。 |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>分支 2：放弃队列中的初始消息

如果处理第一条消息的操作失败，则服务总线操作“放弃队列中的初始消息”会释放此消息，供另一个工作流实例运行来拾取并处理。 有关详细信息，请参阅[处理初始消息](#handle-initial-message)。

<a name="catch-scope"></a>

### <a name="catch-scope"></a>“Catch”作用域

如果 `Try` 作用域中的操作失败，则逻辑应用仍必须关闭会话。 作用 `Catch` [域操作](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) 在 `Try` 作用域操作导致状态、、或时 `Failed` 运行 `Skipped` `TimedOut` 。 此作用域会返回一条错误消息（其中包含发生问题的会话 ID）并终止逻辑应用。

下面是折叠详细信息后 `Catch` 作用域操作中的大致流：

![“Catch”作用域操作工作流](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| 名称 | 说明 |
|------|-------------|
| **`Close a session in a queue and fail`** | 此服务总线操作会关闭队列中的会话，使会话锁不再保持打开状态。 有关详细信息，请参阅[关闭队列中的会话并失败](#close-session-fail)。 |
| **`Find failure msg from 'Try' block`** | 此[“筛选数组”操作](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action)按照指定的条件基于 `Try` 作用域内的所有操作的输入和输出创建一个数组。 在这种情况下，此操作会返回导致 `Failed` 状态的操作的输出。 有关详细信息，请参阅[从“Try”块查找失败消息](#find-failure-message)。 |
| **`Select error details`** | 此[“选择”操作](../logic-apps/logic-apps-perform-data-operations.md#select-action)根据指定的条件创建包含 JSON 对象的数组。 这些 JSON 对象是根据上一操作（`Find failure msg from 'Try' block`）创建的数组中的值生成的。 在这种情况下，此操作会返回一个数组，其中包含根据从上一操作返回的错误详细信息创建的 JSON 对象。 有关详细信息，请参阅[“选择”错误详细信息](#select-error-details)。 |
| **`Terminate`** | 此[“终止”操作](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action)会停止工作流的运行，取消正在进行的任何操作，跳过任何剩余的操作，并返回指定的状态、会话 ID 以及来自“`Select error details`”操作的错误结果。 有关详细信息，请参阅[终止逻辑应用](#terminate-logic-app)。 |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>完成模板

若要为“使用服务总线会话按顺序传送相关消息”模板中的触发器和操作提供值，请执行以下步骤。 在保存逻辑应用之前，必须提供所有必需的值，这些值标有星号 ( **\*** )。

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>初始化会话

* 对于“队列中收到消息时(扫视锁定)”触发器，请提供此信息，以便模板可以使用“会话 ID”属性来初始化会话，例如：

  ![服务总线触发器“队列中收到消息时(扫视锁定)”的详细信息](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > 轮询间隔一开始设置为三分钟，这样逻辑应用的运行频率就不会超出预期，因此不会产生意外的账单费用。 理想情况下，可将时间间隔和频率设置为 30 秒，这样逻辑应用就会在消息到达时立即触发。

  | 属性 | 此方案所需 | Value | 说明 |
  |----------|----------------------------|-------|-------------|
  | **队列名称** | 是 | <queue-name> | 之前创建的服务总线队列的名称。 此示例使用“Fabrikam-Service-Bus-Queue”。 |
  | **队列类型** | 是 | **主页** | 你的主服务总线队列 |
  | **会话 ID** | 是 | **下一个可用** | 此选项会根据服务总线队列中消息的会话 ID 为每个触发器运行获取一个会话。 此会话也被锁定，以防止其他逻辑应用或其他客户端处理与此会话相关的消息。 工作流的后续操作将处理与该会话关联的所有消息，如本文稍后所述。 <p><p>下面是有关其他**会话 ID** 选项的详细信息： <p>- **无**：默认选项，会导致没有会话，不能用于实现顺序保护模式。 <p>- **输入自定义值**：如果你知道要使用的会话 ID，并且始终要为该会话 ID 运行触发器，请使用此选项。 <p>**注意**：服务总线连接器一次可以将 Azure 服务总线的有限数量的唯一会话保存到连接器缓存。 如果会话计数超过此限制，则将从缓存中删除旧会话。 有关详细信息，请参阅[使用 Azure 逻辑应用和 Azure 服务总线在云中交换消息](../connectors/connectors-create-api-servicebus.md#connector-reference)。 |
  | **时间间隔** | 是 | <时间间隔数> | 在重复检查是否存在消息之前，在两个重复周期之间间隔的时间单位数。 |
  | **频率** | 是 | “秒”、“分钟”、“小时”、“天”、“周”或“月”      | 在检查是否存在消息时要使用的重复周期的时间单位。 <p>**提示**：若要添加“时区”或“开始时间”，请从“添加新参数”列表中选择这些属性。   |
  |||||

  如需更多的触发器信息，请参阅[服务总线 - 队列中收到消息时(扫视锁定)](/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock))。 触发器会输出一条 [ServiceBusMessage](/connectors/servicebus/#servicebusmessage)。

初始化会话以后，工作流会使用“初始化变量”操作创建一个最初设置为 `false` 的布尔变量，并指示何时满足以下条件： 

* 会话中没有剩余可供读取的消息。

* 不再需要续订会话锁，就能完成当前工作流实例。

![与“Init isDone”相对应的“初始化变量”操作的详细信息](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

接下来，在 **Try** 块中，工作流对读取的第一条消息执行操作。

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>处理初始消息

第一个操作是占位符服务总线操作“将初始消息发送到主题”，你可以将其替换为对队列中会话的第一条消息进行处理时需要执行的任何操作。 会话 ID 指定产生消息的会话。

此占位符服务总线操作会将第一条消息发送到由“会话 ID”属性指定的服务总线主题。 这样一来，与特定会话关联的所有消息都将移至同一主题。 此模板中的后续操作的所有“会话 ID”属性均使用同一会话 ID 值。

![服务总线操作“将初始消息发送到主题”的详细信息](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. 在服务总线操作“完成队列中的初始消息”中，提供你的服务总线队列的名称，并保留操作中的所有其他默认属性值。

   ![服务总线操作“完成队列中的初始消息”的详细信息](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. 在服务总线操作“放弃队列中的初始消息”中，提供你的服务总线队列的名称，并保留操作中的所有其他默认属性值。

   ![服务总线操作“放弃队列中的初始消息”的详细信息](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

接下来，你将为“完成队列中的初始消息”操作后面的操作提供必要信息。 你将从“当队列中的会话存在剩余消息时”循环中的操作开始。

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>当队列中的会话存在剩余消息时

此 [**Until** 循环](../logic-apps/logic-apps-control-flow-loops.md#until-loop)会在队列中有消息存在时或一小时过后运行这些操作。 若要更改循环的时间限制，请编辑循环的“超时”属性值。

* 当有消息存在时从队列中获取其他消息。

* 检查剩余消息的数目。 如果仍然有消息存在，则继续处理消息。 如果不存在剩余消息，则工作流会将 `isDone` 变量设置为 `true`，并退出循环。

![Until 循环 - 处理队列中的消息](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. 在服务总线操作“从会话中获取其他消息”中，提供你的服务总线队列的名称。 另外，请保留操作中的所有其他默认属性值。

   > [!NOTE]
   > 默认情况下，消息的最大数目设置为 `175`，但此限制受服务总线中的消息大小和最大消息大小属性的影响。 有关详细信息，请参阅[队列的消息大小](../service-bus-messaging/service-bus-quotas.md)。

   ![服务总线操作 -“从会话中获取其他消息”](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   接下来，工作流会拆分为以下并行分支：

   * 如果检查是否存在其他消息时发生错误或失败，则将 `isDone` 变量设置为 `true`。

   * “在获得消息的情况下处理消息”条件检查剩余消息数是否为零。 如果不为零并且存在剩余消息，则继续处理消息。 如果为零并且不存在剩余消息，则工作流会将 `isDone` 变量设置为 `true`。

   ![条件 - 在有消息的情况下处理消息](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   在 **If false** 部分中，每个 **For each** 循环按先进先出顺序 (FIFO) 处理每条消息。 在循环的“设置”中，“并发控制”设置设定为 `1`，因此一次只处理一条消息。 。

   ![“For each”循环 - 一次处理一条消息](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. 对于服务总线操作“完成队列中的消息”和“放弃队列中的消息” ，请提供你的服务总线队列的名称。

   ![服务总线操作 -“完成队列中的消息”和“放弃队列中的消息”](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   “当队列中的会话存在剩余消息时”循环完成后，工作流会将 `isDone` 变量设置为 `true`。

接下来，你将为“续订会话锁直至取消”循环中的操作提供必要信息。

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>续订会话锁直至取消

此 [**Until** 循环](../logic-apps/logic-apps-control-flow-loops.md#until-loop)可通过运行这些操作确保此逻辑应用在有消息存在于队列中时或一小时过后持有会话锁。 若要更改循环的时间限制，请编辑循环的“超时”属性值。

* 延迟 25 秒或延迟比正在处理的队列的锁超时持续时间小的某个时间。 最小的锁持续时间为 30 秒，因此默认值就足够了。 不过，可以通过适当调整来优化循环的运行次数。

* 检查 `isDone` 变量是否已设置为 `true`。

  * 如果 `isDone` 未设置为 `true`，则表明工作流仍在处理消息，因此工作流会续订队列中的会话锁，并再次检查循环条件。

    你需要在服务总线操作[**续订队列中的会话锁**](#renew-lock-on-session)中提供你的服务总线队列的名称。

  * 如果 `isDone` 设置为 `true`，则工作流不会续订队列中的会话锁，而是退出循环。

  ![Until 循环 -“续订会话锁直至取消”](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>续订队列中的会话锁

当工作流仍在处理消息时，此服务总线操作会续订队列中的会话锁。

* 在服务总线操作“续订队列中的会话锁”中，提供你的服务总线队列的名称。

  ![服务总线操作 -“续订队列中的会话锁”](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

接下来，为服务总线操作“关闭队列中的会话并成功”提供必要的信息。

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>关闭队列中的会话并成功

此服务总线操作会在工作流处理完队列中的所有可用消息或放弃初始消息后关闭队列中的会话。

* 在服务总线操作“关闭队列中的会话并成功”中，提供你的服务总线队列的名称。

  ![服务总线操作 -“关闭队列中的会话并成功”](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

下面的各部分介绍了 `Catch` 部分中的操作，这些操作处理工作流中发生的错误和异常。

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>关闭队列中的会话并失败

此服务总线操作始终作为 `Catch` 作用域中的第一个操作运行，并会关闭队列中的会话。

* 在服务总线操作“关闭队列中的会话并失败”中，提供你的服务总线队列的名称。

  ![服务总线操作 -“关闭队列中的会话并失败”](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

接下来，工作流会创建一个数组，该数组具有 `Try` 作用域内所有操作的输入和输出，使逻辑应用可以访问已发生的错误或失败的相关信息。

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>从“Try”块查找失败消息

此[“筛选数组”操作](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action)会按照 [`result()` 函数](../logic-apps/workflow-definition-language-functions-reference.md#result)指定的条件创建一个数组，该数组包含 `Try` 作用域内所有操作的输入和输出。 在这种情况下，此操作会通过使用 [`equals()` 函数](../logic-apps/workflow-definition-language-functions-reference.md#equals)和 [`item()` 函数](../logic-apps/workflow-definition-language-functions-reference.md#item)返回状态为 `Failed` 的操作的输出。

![“筛选数组”操作 -“从‘Try’块查找失败消息”](./media/send-related-messages-sequential-convoy/find-failure-message.png)

下面是此操作的 JSON 定义：

```json
"Find_failure_msg_from_'Try'_block": {
   "inputs": {
      "from": "@Result('Try')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "Close_the_session_in_the_queue_and_fail": [
         "Succeeded"
      ]
   },
   "type": "Query"
},
```

接下来，工作流创建一个包含 JSON 对象的数组，该对象包含从“`Find failure msg from 'Try' block`”操作返回的数组中的错误信息。

<a name="select-error-details"></a>

### <a name="select-error-details"></a>“选择”错误详细信息

此[“选择”操作](../logic-apps/logic-apps-perform-data-operations.md#select-action)根据输入数组（上面的操作“`Find failure msg from 'Try' block`”的输出）创建包含 JSON 对象的数组。 具体说来，此操作会返回一个数组，该数组只包含输入数组中每个对象的指定属性。 在此示例中，数组包含操作名称和错误结果属性。

![“选择”操作 -“选择”错误详细信息](./media/send-related-messages-sequential-convoy/select-error-details.png)

下面是此操作的 JSON 定义：

```json
"Select_error_details": {
   "inputs": {
      "from": "@body('Find_failure_msg_from_''Try''_block')[0]['outputs']",
      "select": {
         "action": "@item()['name']",
         "errorResult": "@item()"
      }
   },
   "runAfter": {
      "Find_failure_msg_from_'Try'_block": [
         "Succeeded"
      ]
   },
   "type": "Select"
},
```

接下来，工作流会停止逻辑应用运行，并返回运行状态以及已发生的错误或失败的详细信息。

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>终止逻辑应用运行

此[“终止”操作](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action)会停止逻辑应用运行，并返回 `Failed`（作为逻辑应用的运行状态）以及会话 ID 和来自“`Select error details`”操作的错误结果。

![用于停止逻辑应用运行的“终止”操作](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

下面是此操作的 JSON 定义：

```json
"Terminate": {
   "description": "This Failure Termination only runs if the Close Session upon Failure action runs - otherwise the LA will be terminated as Success",
   "inputs": {
      "runError": {
         "code": "",
         "message": "There was an error processing messages for Session ID @{triggerBody()?['SessionId']}. The following error(s) occurred: @{body('Select_error_details')['errorResult']}"
         },
         "runStatus": "Failed"
      },
      "runAfter": {
         "Select_error_details": [
            "Succeeded"
         ]
      },
      "type": "Terminate"
   }
},
```

## <a name="save-and-run-logic-app"></a>保存并运行逻辑应用

完成模板后，可以保存你的逻辑应用。 在设计器工具栏上选择“保存”。

若要测试逻辑应用，请向服务总线队列发送消息。 

## <a name="next-steps"></a>后续步骤

* 详细了解[服务总线连接器的触发器和操作](/connectors/servicebus/)
