---
title: 使用顺序保护按顺序发送相关消息
description: 使用 azure 服务总线在 Azure 逻辑应用中使用顺序保护模式按顺序发送相关消息
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: bd6b05489d13f835de4dce2aa3d885132285efca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987602"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>使用 azure 服务总线在 Azure 逻辑应用中使用顺序保护按顺序发送相关消息

如果需要按特定顺序发送相关消息，可以[在使用 Azure](../logic-apps/logic-apps-overview.md) [服务总线连接器](../connectors/connectors-create-api-servicebus.md)时遵循[*顺序保护*模式](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy)。 相关消息有一个属性，该属性定义这些消息之间的关系，例如服务总线中[会话](../service-bus-messaging/message-sessions.md)的 ID。

例如，假设有10条消息用于名为 "Session 1" 的会话，并且有5个 "会话 2" 会话消息，它们都发送到相同的[服务总线队列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)。 你可以创建一个逻辑应用，用于处理来自队列的消息，以便来自 "会话 1" 的所有消息都由单个触发器运行处理，而来自 "Session 2" 的所有消息都由下一个触发器运行处理。

![一般顺序保护模式](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

本文介绍如何使用 "**使用服务总线会话的相关按顺序传递**" 模板来创建实现此模式的逻辑应用。 此模板定义一个逻辑应用工作流，该工作流**在队列中收到消息时**，从服务总线连接器开始，该触发器从[服务总线队列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)接收消息。 下面是此逻辑应用执行的高级步骤：

* 根据触发器从服务总线队列读取的消息初始化会话。

* 在当前工作流运行期间，读取并处理来自队列中同一会话的所有消息。

若要查看此模板的 JSON 文件，请参阅[GitHub： service-bus-sessions.json](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json)。

有关详细信息，请参阅[顺序保护模式-Azure 体系结构云设计模式](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 服务总线命名空间和[服务总线队列](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)，它是将在逻辑应用中使用的消息实体。 这些项和你的逻辑应用需使用同一 Azure 订阅。 请确保在创建队列时选择 "**启用会话**"。 如果没有这些项目，请学习[如何创建服务总线命名空间和队列](../service-bus-messaging/service-bus-create-namespace-portal.md)。

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* 有关如何创建逻辑应用的基本知识。 如果不熟悉 Azure 逻辑应用，请尝试快速入门，[创建第一个自动化工作流](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>检查对服务总线命名空间的访问权限

如果不确定逻辑应用是否有权访问服务总线命名空间，请确认这些权限。

1. 登录到 [Azure 门户](https://portal.azure.com)。 找到并选择服务总线*命名空间*。

1. 在命名空间菜单上的“设置”下，选择“共享访问策略”。******** 在 "**声明**" 下，检查是否拥有该命名空间的 "**管理**" 权限。

   ![管理服务总线命名空间的权限](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. 现在获取服务总线命名空间的连接字符串。 稍后，在创建从逻辑应用到命名空间的连接时，可以使用此字符串。

   1. 在 "**共享访问策略**" 窗格的 "**策略**" 下，选择 " **RootManageSharedAccessKey**"。
   
   1. 在主连接字符串旁边选择复制按钮。 保存连接字符串供以后使用。

      ![复制服务总线命名空间连接字符串](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > 若要确认连接字符串是与服务总线命名空间关联还是与消息传送实体（例如队列）关联，请在该连接字符串中搜索 `EntityPath`  参数。 如果找到了该参数，则表示连接字符串适用于特定的实体，不是适用于逻辑应用的正确字符串。

## <a name="create-logic-app"></a>创建逻辑应用

在本部分中，将使用 "**使用服务总线会话的相关按顺序传递**" 模板创建逻辑应用，其中包括用于实现此工作流模式的触发器和操作。 还会创建一个到服务总线命名空间的连接，并指定要使用的服务总线队列的名称。

1. 在 [Azure 门户](https://portal.azure.com)中，创建一个空白逻辑应用。 在 Azure 主页上，选择 "**创建资源**  >  **集成**  >  **逻辑应用**"。

1. 模板库出现后，滚动视频和常见触发器部分。 从 "**模板**" 部分中，选择模板，**使用服务总线会话的相关按顺序传递**。

   ![选择 "使用服务总线会话的相关按顺序传递" 模板](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. 当确认框出现时，选择 "**使用此模板**"。

1. 在逻辑应用设计器上的 "**服务总线**" 形状中，选择 "**继续**"，然后选择 **+** 在形状中显示的加号（）。

   ![选择 "继续" 以连接到 Azure 服务总线](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. 现在，通过选择以下任一选项来创建服务总线连接：

   * 若要使用之前从服务总线命名空间复制的连接字符串，请执行以下步骤：

     1. 选择 "**手动输入连接信息**"。

     1. 为 "**连接名称**" 提供连接的名称。 对于 "**连接字符串**"，请粘贴命名空间连接字符串，然后选择 "**创建**"，例如：

        ![输入连接名称和服务总线连接字符串](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > 如果没有此连接字符串，请了解如何[查找和复制服务总线命名空间连接字符串](#permissions-connection-string)。

   * 若要从当前 Azure 订阅中选择服务总线命名空间，请执行以下步骤：

     1. 为 "**连接名称**" 提供连接的名称。 对于 "**服务总线命名空间**"，请选择服务总线命名空间，例如：

        ![输入连接名称并选择服务总线命名空间](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. 当下一个窗格出现时，选择你的服务总线策略，然后选择 "**创建**"。

        ![选择 "服务总线策略"，然后选择 "创建"](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. 完成后，选择 "**继续**"。

   现在，逻辑应用设计器会显示 "**使用服务总线会话进行关联顺序传递**" 模板，该模板包含带有触发器和操作的预填充工作流，其中包括实现遵循模式的错误处理的两个作用域 `Try-Catch` 。

现在，你可以了解有关模板中的触发器和操作的详细信息，也可以直接跳到[提供逻辑应用模板的值](#complete-template)。

<a name="template-summary"></a>

## <a name="template-summary"></a>模板摘要

下面是折叠详细信息时，**使用服务总线会话的相关按顺序传递**的顶层工作流模板：

![模板的顶层工作流](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| “属性” | 描述 |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | 根据指定的重复周期，此 Service Bus 触发器检查指定的服务总线队列中是否有任何消息。 如果队列中存在消息，则触发器将触发，这会创建和运行工作流实例。 <p><p>字词*查看-锁定*意味着触发器发送请求，以从队列中检索消息。 如果消息存在，触发器将检索并锁定消息，以便在锁定期限到期之前，不会对该消息进行其他处理。 有关详细信息，请[初始化会话](#initialize-session)。 |
| **`Init isDone`** | 此 " [**初始化变量**" 操作](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable)会创建一个设置为的布尔变量， `false` 并指示何时满足以下条件： <p><p>-会话中没有更多可供读取的消息。 <br>-不再需要续订会话锁，就能完成当前工作流实例。 <p><p>有关详细信息，请参阅[初始化会话](#initialize-session)。 |
| **`Try`** | 此[**作用域**操作](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)包含用于处理消息的操作。 如果作用域中出现问题 `Try` ，则后续的 `Catch` **作用域**操作会处理该问题。 有关详细信息，请参阅["Try" 作用域](#try-scope)。 |
| **`Catch`**| 此[**作用域**操作](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)包含上一个作用域中发生问题时运行的操作 `Try` 。 有关详细信息，请参阅["Catch" 范围](#catch-scope)。 |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>"Try" 范围

下面是 `Try` 折叠详细信息时[作用域操作](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)中的顶层流：

!["Try" 作用域操作工作流](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| “属性” | 描述 |
|------|-------------|
| **`Send initial message to topic`** | 您可以将此操作替换为您要处理队列中会话的第一条消息的任何操作。 会话 ID 指定会话。 <p><p>对于此模板，服务总线操作会将第一条消息发送到服务总线主题。 有关详细信息，请参阅[处理初始消息](#handle-initial-message)。 |
| （并行分支） | 此[并行分支操作](../logic-apps/logic-apps-control-flow-branches.md)创建两个路径： <p><p>-分支 #1：继续处理该消息。 有关详细信息，请参阅[Branch #1：完成队列中的第一条消息](#complete-initial-message)。 <p><p>-分支 #2：如果出现问题，则放弃消息，并通过另一个触发器运行来释放分拣。 有关详细信息，请参阅[Branch #2：放弃队列中的初始消息](#abandon-initial-message)。 <p><p>这两个路径稍后会在**队列的关闭会话中加入，并成功执行**操作，在下一行中进行了介绍。 |
| **`Close a session in a queue and succeed`** | 此服务总线操作加入前面所述的分支，并在发生以下事件之一后关闭队列中的会话： <p><p>-工作流完成队列中的可用消息处理。 <br>-工作流放弃初始消息，因为出现错误。 <p><p>有关详细信息，请参阅[关闭队列中的会话并成功](#close-session-succeed)。 |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>分支 #1：在队列中完成初始消息

| “属性” | 描述 |
|------|-------------|
| `Complete initial message in queue` | 此服务总线操作将成功检索的消息标记为完成，并从队列中删除该消息，以防重新处理。 有关详细信息，请参阅[处理初始消息](#handle-initial-message)。 |
| `While there are more messages for the session in the queue` | 这[**直到**循环](../logic-apps/logic-apps-control-flow-loops.md#until-loop)在消息存在或一小时后继续获取消息。 有关此循环中的操作的详细信息，请参阅在[队列中存在会话的更多消息](#while-more-messages-for-session)。 |
| **`Set isDone = true`** | 当不再有消息时，此[**设置变量**操作](../logic-apps/logic-apps-create-variables-store-values.md#set-variable)会将设置 `isDone` 为 `true` 。 |
| **`Renew session lock until cancelled`** | 此[ **Until**循环](../logic-apps/logic-apps-control-flow-loops.md#until-loop)确保在消息存在或一小时后，此逻辑应用持有会话锁。 有关此循环中的操作的详细信息，请参阅在[取消之前续订会话锁定](#renew-session-while-messages-exist)。 |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>分支 #2：放弃队列中的初始消息

如果处理第一条消息的操作失败，则服务总线操作会**放弃队列中的初始消息**，并为运行的另一个工作流实例释放该消息，以便选择并处理。 有关详细信息，请参阅[处理初始消息](#handle-initial-message)。

<a name="catch-scope"></a>

### <a name="catch-scope"></a>"Catch" 范围

如果作用域中的操作 `Try` 失败，则逻辑应用仍必须关闭会话。 作用 `Catch` [域操作](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)在 `Try` 作用域操作导致状态、、或时 `Failed` 运行 `Skipped` `TimedOut` 。 作用域返回一条错误消息，其中包含发生问题的会话 ID，并终止逻辑应用。

下面是 `Catch` 折叠详细信息时作用域操作中的顶层流：

!["Catch" 范围操作工作流](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| “属性” | 描述 |
|------|-------------|
| **`Close a session in a queue and fail`** | 此服务总线操作会关闭队列中的会话，以便会话锁定不会保持打开状态。 有关详细信息，请参阅[关闭队列中的会话和失败](#close-session-fail)。 |
| **`Find failure msg from 'Try' block`** | 此[**筛选数组**操作](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) `Try` 根据指定的条件，从作用域内的所有操作创建一个数组。 在这种情况下，此操作将返回导致状态的操作的输出 `Failed` 。 有关详细信息，请参阅[从 "Try" 块中查找失败消息](#find-failure-message)。 |
| **`Select error details`** | 此[**选择**操作](../logic-apps/logic-apps-perform-data-operations.md#select-action)根据指定的条件创建包含 JSON 对象的数组。 这些 JSON 对象是根据上一操作创建的数组中的值生成的 `Find failure msg from 'Try' block` 。 在这种情况下，此操作将返回一个数组，其中包含从上一个操作返回的错误详细信息创建的 JSON 对象。 有关详细信息，请参阅[选择错误详细信息](#select-error-details)。 |
| **`Terminate`** | 此[**终止**操作](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action)将停止运行工作流，取消正在进行的任何操作，跳过任何剩余操作，并返回指定状态、会话 ID 和操作的错误结果 `Select error details` 。 有关详细信息，请参阅[终止逻辑应用](#terminate-logic-app)。 |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>完成模板

若要在 "**使用服务总线会话的相关按顺序传递**" 模板中提供触发器和操作的值，请执行以下步骤。 在保存逻辑应用之前，必须提供所有必需的值（以星号（ **\*** ）标记）。

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>初始化会话

* 对于在**队列中收到消息（扫视锁定）** 触发器的时间，请提供此信息，以便模板可以使用**session id**属性初始化会话，例如：

  !["在队列中收到消息时，服务总线触发器的详细信息（速览-锁定）"](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > 最初，轮询间隔设置为三分钟，以便逻辑应用不会比预期运行更频繁，从而导致产生意外的计费费用。 理想情况下，将 "间隔" 和 "频率" 设置为30秒，以便逻辑应用在消息到达时立即触发。

  | Property | 此方案需要 | “值” | 说明 |
  |----------|----------------------------|-------|-------------|
  | **队列名称** | 是 | <*队列名称*> | 你以前创建的服务总线队列的名称。 此示例使用 "Fabrikam-服务总线队列"。 |
  | **队列类型** | 是 | **主页** | 主服务总线队列 |
  | **会话 id** | 是 | **下一个可用** | 此选项根据服务总线队列中的消息的会话 ID，为每个触发器运行获取一个会话。 此会话也被锁定，因此没有其他逻辑应用或其他客户端可以处理与此会话相关的消息。 工作流的后续操作将处理与该会话关联的所有消息，如本文后面所述。 <p><p>下面是有关其他**会话 id**选项的详细信息： <p>- **None**：默认选项，这将导致没有会话，并且不能用于实现顺序保护模式。 <p>- **输入自定义值**：如果你知道要使用的会话 id，并且始终需要为该会话 id 运行触发器，请使用此选项。 <p>**注意**：服务总线连接器可以一次从 Azure 服务总线将有限数量的唯一会话保存到连接器缓存。 如果会话计数超过此限制，则将从缓存中删除旧会话。 有关详细信息，请参阅[通过 Azure 逻辑应用和 Azure 服务总线在云中交换消息](../connectors/connectors-create-api-servicebus.md#connector-reference)。 |
  | **时间间隔** | 是 | <*间隔数*> | 检查消息之前重复发生间隔的时间单位数。 |
  | **频率** | 是 | “秒”、“分钟”、“小时”、“天”、“周”或“月”************************ | 用于检查消息的重复周期的时间单位。 <p>**提示**：若要添加**时区或****开始时间**，请从 "**添加新参数**" 列表中选择这些属性。 |
  |||||

  有关触发器的详细信息，请参阅[服务总线-在队列中收到消息时（速览-lock）](https://docs.microsoft.com/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock))。 触发器输出[ServiceBusMessage](https://docs.microsoft.com/connectors/servicebus/#servicebusmessage)。

初始化会话后，工作流将使用 "**初始化变量**" 操作创建一个最初设置为的布尔变量， `false` 并指示何时满足以下条件： 

* 会话中没有更多可供读取的消息。

* 不再需要续订会话锁即可完成当前工作流实例。

!["Init 操作" 的 "初始化变量" 操作详细信息](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

接下来，在**Try**块中，工作流在读取的第一条消息上执行操作。

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>处理初始消息

第一项操作是占位符服务总线操作，**将初始消息发送到主题**，你可以将其替换为你想要处理队列中会话的第一条消息的任何其他操作。 会话 ID 指定从其发起消息的会话。

占位符服务总线操作将第一条消息发送到由**Session Id**属性指定的服务总线主题。 这样一来，与特定会话关联的所有消息都将移至同一主题。 此模板中后续操作的所有**会话 id**属性均使用相同的会话 id 值。

!["向主题发送初始消息" 的服务总线操作详细信息](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. 在 "Service Bus" 操作中，在 "**队列" 中完成初始消息**，提供服务总线队列的名称，并保留操作中的所有其他默认属性值。

   !["在队列中完成初始消息" 的服务总线操作详细信息](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. 在服务总线操作中，**放弃队列中的初始消息**，提供服务总线队列的名称，并保留操作中的所有其他默认属性值。

   !["放弃队列中的初始消息" 的服务总线操作详细信息](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

接下来，您将为**在 "队列中完成初始消息**" 操作的后续操作提供必要信息。 将从中的操作开始，其中**队列循环中的会话有更多的消息**。

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>当队列中有更多的会话消息时

这[**直到**循环](../logic-apps/logic-apps-control-flow-loops.md#until-loop)在队列中存在消息或一小时后才运行这些操作。 若要更改循环的时间限制，请编辑循环的 "**超时**" 属性值。

* 消息存在时从队列中获取其他消息。

* 检查剩余消息的数量。 如果消息仍存在，则继续处理消息。 如果不再有消息，则工作流将 `isDone` 变量设置为 `true` ，并退出循环。

![在队列中时循环处理消息](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. 在服务总线操作中，**从会话获取其他消息**，提供服务总线队列的名称。 否则，请保留操作中的所有其他默认属性值。

   > [!NOTE]
   > 默认情况下，最大消息数设置为 `175` ，但此限制受服务总线中的消息大小和最大消息大小属性的影响。 有关详细信息，请参阅[队列的消息大小](../service-bus-messaging/service-bus-quotas.md)。

   ![服务总线操作-"从会话获取其他消息"](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   接下来，工作流会拆分为以下并行分支：

   * 如果检查其他消息时出现错误或失败，则将变量设置 `isDone` 为 `true` 。

   * **如果有任何**条件检查剩余消息数是否为零，则会处理消息。 如果错误和更多消息存在，则继续处理。 如果为 true，并且不存在更多消息，则工作流将 `isDone` 变量设置为 `true` 。

   ![条件-处理消息（如果有）](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   如果为**true** ，则**for each**循环将按先进先出顺序（FIFO）处理每个消息。 在循环的**设置**中，**并发控制**设置设置为 `1` ，因此一次只处理一个消息。

   !["For each" 循环-每次一个消息处理一个消息](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. 对于服务总线操作，请**完成队列中的消息**，并**放弃队列中的消息**，并提供服务总线队列的名称。

   ![服务总线操作-"完成队列中的消息" 和 "放弃队列中的消息"](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   完成后，**队列中的会话的消息**就会被设置 `isDone` 为 `true` 。

接下来，你将为**续订会话锁定**中的操作提供必要的信息，直到取消循环。

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>续订会话锁定，直到取消

此[ **Until**循环](../logic-apps/logic-apps-control-flow-loops.md#until-loop)确保此逻辑应用持有会话锁，而消息存在于队列中，或在一小时后运行这些操作。 若要更改循环的时间限制，请编辑循环的 "**超时**" 属性值。

* 延迟25秒或小于正在处理的队列的锁超时持续时间的时间。 最小的锁定持续时间为30秒，因此默认值就足够了。 不过，您可以通过适当调整来优化运行循环的次数。

* 检查变量是否 `isDone` 设置为 `true` 。

  * 如果 `isDone` 未设置为 `true` ，则工作流仍在处理消息，因此，工作流会在队列中续订会话的锁，并再次检查循环条件。

    需要在服务总线操作中提供服务总线队列的名称，并在[**队列中续订会话锁定**](#renew-lock-on-session)。

  * 如果 `isDone` 设置为 `true` ，则工作流不会在队列中续订会话的锁，而是退出循环。

  ![Until 循环-"在取消之前续订会话锁"](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>在队列中续订会话的锁定

当工作流仍在处理消息时，此服务总线操作会在队列中续订会话的锁。

* 在服务总线操作中，在**队列中续订会话**，提供服务总线队列的名称。

  ![服务总线操作-"更新队列中会话的锁定"](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

接下来，你将提供服务总线操作所需的信息，**关闭队列中的会话并成功**。

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>关闭队列中的会话并成功

此服务总线操作会在工作流完成处理队列中的所有可用消息后关闭队列中的会话，或工作流放弃初始消息。

* 在服务总线操作中，**关闭队列中的会话并成功**，提供服务总线队列的名称。

  ![服务总线操作-"关闭队列中的会话并成功"](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

以下各节描述了节中的操作，该操作 `Catch` 处理工作流中发生的错误和异常。

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>关闭队列中的会话并失败

此服务总线操作始终作为作用域中的第一个操作运行 `Catch` ，并关闭队列中的会话。

* 在服务总线操作中，**关闭队列中的会话并失败**，提供服务总线队列的名称。

  ![服务总线操作-"关闭队列中的会话并失败"](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

接下来，工作流创建一个数组，该数组包含作用域中所有操作的输入和输出， `Try` 以便逻辑应用可以访问有关发生的错误或失败的信息。

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>从 "Try" 块查找失败消息

此[**筛选数组**操作](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action) `Try` 根据指定的条件，使用[ `result()` 函数](../logic-apps/workflow-definition-language-functions-reference.md#result)创建一个数组，该数组具有范围内所有操作的输入和输出。 在这种情况下，此操作 `Failed` 通过使用[ `equals()` 函数](../logic-apps/workflow-definition-language-functions-reference.md#equals)和[ `item()` 函数](../logic-apps/workflow-definition-language-functions-reference.md#item)返回具有状态的操作的输出。

![筛选数组操作-"从 ' Try ' 块中查找失败消息"](./media/send-related-messages-sequential-convoy/find-failure-message.png)

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

接下来，工作流创建一个包含 JSON 对象的数组，该对象包含从操作返回的数组中的错误信息 `Find failure msg from 'Try' block` 。

<a name="select-error-details"></a>

### <a name="select-error-details"></a>选择错误详细信息

此[**选择**操作](../logic-apps/logic-apps-perform-data-operations.md#select-action)将创建一个数组，该数组包含基于从上一个操作输出的输入数组的 JSON 对象 `Find failure msg from 'Try' block` 。 具体而言，此操作将返回一个数组，该数组只包含数组中每个对象的指定属性。 在这种情况下，数组包含操作名称和错误结果属性。

![选择操作-"选择错误详细信息"](./media/send-related-messages-sequential-convoy/select-error-details.png)

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

接下来，工作流会停止逻辑应用运行，并返回运行状态以及有关发生的错误或失败的详细信息。

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>终止逻辑应用运行

此[**终止**操作](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action)将停止逻辑应用运行，并将其 `Failed` 作为逻辑应用的运行状态连同会话 ID 和操作的错误结果一起返回 `Select error details` 。

![终止操作以停止逻辑应用运行](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

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

完成模板后，现在可以保存逻辑应用。 在设计器工具栏上，选择“保存”。

若要测试逻辑应用，请将消息发送到服务总线队列。 

## <a name="next-steps"></a>后续步骤

* 了解有关[服务总线连接器的触发器和操作](https://docs.microsoft.com/connectors/servicebus/)的详细信息
