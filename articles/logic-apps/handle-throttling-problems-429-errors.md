---
title: 处理限制问题，或 "429-请求太多" 错误
description: 如何在 Azure 逻辑应用中解决阻止问题，或 "HTTP 429 请求数过多" 错误
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: fbfd52065bc0522668488492de2181f252f86a4e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272672"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Azure 逻辑应用中的处理限制问题（429-"请求太多" 错误）

在[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)中，你的逻辑应用会在遇到限制时返回["HTTP 429 请求数过多" 错误](https://developer.mozilla.org/docs/Web/HTTP/Status/429)，这会在请求数超过特定时间内目标可以处理的速率时返回。 限制可能会造成一些问题，如延迟的数据处理、性能降低以及超过指定的重试策略。

![SQL Server 连接器中的限制](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

下面是逻辑应用可能遇到的一些常见类型的限制：

* [逻辑应用](#logic-app-throttling)
* [连接器](#connector-throttling)
* [目标服务或系统](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>逻辑应用限制

Azure 逻辑应用服务有其自己的[吞吐量限制](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 因此，如果逻辑应用超过这些限制，则会限制逻辑应用资源，而不只是特定实例或运行。

若要在此级别上查找阻止事件，请在 "Azure 门户中检查逻辑应用的**指标**窗格。

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用菜单的 "**监视**" 下，选择 "**指标**"。

1. 在 "**图表标题**" 下，选择 "**添加度量值**"，以将另一个指标添加到现有。

1. 在第一个度量值栏的 "**指标**" 列表中，选择 "**操作限制事件**"。 在第二个度量值栏的 "**指标**" 列表中，选择 "**触发限制事件**"。

若要在此级别处理限制，可以使用以下选项：

* 限制可以同时运行的逻辑应用实例的数目。

  默认情况下，如果逻辑应用的触发器条件同时达到一次，则逻辑应用的多个触发器实例将同时或*并行*运行。 此行为意味着每个触发器实例在上一个工作流实例完成运行之前激发。

  尽管可以并发运行的触发器实例的默认数目不[受](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)限制，但你可以通过[启用触发器的并发设置](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)来限制此数目，如有必要，请选择默认值以外的限制。

* 启用高吞吐量模式。

  逻辑应用[对于可以在5分钟滚动时间间隔内运行的操作数量有默认限制](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 若要将此限制提高到操作的最大数量，请打开逻辑应用上的[高吞吐量模式](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)。

* 禁用触发器中的数组解除批处理（"拆分"）行为。

  如果触发器为要处理的其余工作流操作返回数组，则触发器的[ **Split**设置](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)将拆分数组项，并为每个数组项启动一个工作流实例，从而有效地触发多个并发运行，直到达到[**拆分**限制](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)。 若要控制限制，请关闭**拆分**行为，让逻辑应用处理整个数组，只需进行一次调用，而不是处理每个调用的单个项。

* 将操作重构为较小的逻辑应用。

  如前文所述，逻辑应用限制为[可在5分钟内运行的默认操作数量](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 虽然你可以通过启用[高吞吐量模式](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)来增加此限制，但你也可以考虑是否要将逻辑应用的操作分解为较小的逻辑应用，以便在每个逻辑应用中运行的操作的数量保持在限制范围内。 这样一来，便可以降低单个逻辑应用资源的负担，并跨多个逻辑应用分发负载。 此解决方案更适合用于处理大型数据集或加速的操作，使多个并发运行的操作、循环迭代或每个循环迭代中的操作超过操作执行限制。

  例如，此逻辑应用执行所有工作来从 SQL Server 数据库中获取表，并从每个表中获取行。 **For each**循环同时循环访问每个表，以便 "**获取行**" 操作返回每个表的行。 根据这些表中的数据量，这些操作可能会超出执行操作的限制。

  ![重构之前的逻辑应用](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  重构后，逻辑应用现在为父逻辑应用和子逻辑应用。 父项从 SQL Server 获取表，然后为每个表调用子逻辑应用以获取行：

  ![为一个操作创建逻辑应用](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  下面是父逻辑应用程序调用的子逻辑应用，用于获取每个表的行：

  ![为第二个操作创建另一个逻辑应用](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>连接器限制

每个连接器都有其自己的限制限制，你可以在连接器的技术参考页上找到该限制。 例如， [Azure 服务总线连接器](https://docs.microsoft.com/connectors/servicebus/)具有每分钟允许多达6000个调用的限制，而 SQL Server 连接器的限制[因操作类型而异](https://docs.microsoft.com/connectors/sql/)。

某些触发器和操作（如 HTTP）具有["重试策略"](../logic-apps/logic-apps-exception-handling.md#retry-policies) ，可以根据[重试策略限制](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits)来自定义，以实现异常处理。 此策略指定当原始请求失败或超时并导致408、429或5xx 响应时，触发器或操作重试请求的时间和频率。 因此，当限制开始并返回429错误时，逻辑应用将遵循支持的重试策略。

若要了解触发器或操作是否支持重试策略，请检查触发器或操作的设置。 若要查看触发器或操作的重试次数，请参阅逻辑应用的运行历史记录，选择要查看的运行，然后展开该触发器或操作以查看有关输入、输出和任何重试的详细信息，例如：

![查看操作的运行历史记录、重试、输入和输出](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

尽管重试历史记录提供了错误信息，但你可能会遇到区分连接器阻止和[目标限制](#destination-throttling)的问题。 在这种情况下，您可能必须查看响应的详细信息或执行某些限制间隔计算来确定源。

对于全球多租户 Azure 逻辑应用服务中的逻辑应用，阻止在*连接*级别进行。 例如，对于在[integration service 环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中运行的逻辑应用，对非 ISE 连接仍会进行限制，因为这些应用在全局多租户逻辑应用服务中运行。 但是，ise 连接是由 ISE 连接器创建的，因为它们在 ISE 中运行。

若要在此级别处理限制，可以使用以下选项：

* 为单个操作设置多个连接，以使逻辑应用对要处理的数据进行分区。

  对于此选项，请考虑是否可以通过使用相同的凭据将操作的请求划分到同一目标的多个连接来分配工作负荷。

  例如，假定逻辑应用获取 SQL Server 数据库中的表，然后从每个表中获取行。 根据必须处理的行数，可以使用多个连接，**每个**循环使用多个连接，将总行数划分为较小的集进行处理。 此方案对**每个**循环使用两个，以将总行数分成一半。 第一个**for each**循环使用一个可获取前半部分的表达式。 另一个**for each**循环使用其他表达式来获取后半部分，例如：<p>

    * 表达式1： `take()`函数获取集合的前端。 有关详细信息，请参阅[ **`take()`** 函数](workflow-definition-language-functions-reference.md#take)。

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Expression 2： `skip()`函数删除集合的前面并返回所有其他项。 有关详细信息，请参阅[ **`skip()`** 函数](workflow-definition-language-functions-reference.md#skip)。

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    下面是一个演示如何使用这些表达式的直观示例：

    ![为单个操作创建和使用多个连接](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* 为每个操作设置不同的连接。

  对于此选项，请考虑是否可以通过将每个操作的请求传播到自己的连接来分配工作负荷，即使操作连接到相同的服务或系统并使用相同的凭据也是如此。

  例如，假定逻辑应用获取 SQL Server 数据库中的表，并获取每个表中的每一行。 您可以使用单独的连接，这样，获取表时将使用一个连接，而获取每个行将使用另一个连接。

  ![为每个操作创建和使用不同的连接](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* 更改["For each" 循环](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)的并发性或并行度。

  默认情况下，"For each" 循环迭代在[并发限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)的同时运行。 如果连接器在 "For each" 循环内受到限制，则可以减少并行运行的循环迭代数。 有关详细信息，请参阅以下主题：
  
  * ["For each" 循环-更改并发或按顺序运行](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [工作流定义语言架构-每个循环](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [工作流定义语言架构-更改 "For each" 循环并发](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [工作流定义语言架构-按顺序运行 "For each" 循环](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>目标服务或系统限制

如果连接器有自己的限制限制，则连接器调用的目标服务或系统可能也有限制。 例如，Microsoft Exchange Server 中的某些 Api 具有比 Office 365 Outlook connector 更严格的限制。

默认情况下，逻辑应用的实例和这些实例中的任何循环或分支*都是并行*运行的。 此行为表示多个实例可以同时调用相同的终结点。 每个实例都不知道另一个存在，因此尝试重试失败的操作可能会造成[争用情况](https://en.wikipedia.org/wiki/Race_condition)，在这种情况下，多个调用尝试同时运行，但如果成功，这些调用必须到达目标服务或系统，才能开始进行限制。

例如，假设有一个包含100项的数组。 使用 "for each" 循环来循环访问数组并打开循环的并发控制，以便可以将并行迭代的数目限制为20或[当前默认限制](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)。 在该循环中，操作将一个项从数组插入 SQL Server 数据库中，这只允许每秒15次调用。 这种情况会导致阻止问题，因为重新生成的积压工作（backlog）会重试，而不会运行。

此表介绍当操作的重试间隔为1秒时循环中发生的情况的时间线：

| 时间点 | 运行的操作数 | 失败的操作数 | 等待的重试次数 |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 秒 | 20个插入 | 5失败，因为 SQL 限制 | 5次重试 |
| T + 0.5 秒 | 由于前5次重试等待，导致插入15次 | 由于以前的 SQL 限制仍有效，导致其他0.5 秒，所有15次失败 | 20次重试 <br>（前 5 + 15 个新） |
| T + 1 秒 | 20个插入 | 5失败，以及以前的20次重试，因为 SQL 限制 | 25次重试（前 20 + 5 个新）
|||||

若要在此级别处理限制，可以使用以下选项：

* 创建逻辑应用，以便每个应用处理一个操作。

  * 继续本部分中的示例 SQL Server 方案，可以创建一个逻辑应用，用于将数组项放入队列中，如[Azure 服务总线队列](../connectors/connectors-create-api-servicebus.md)。 然后，创建另一个逻辑应用，该应用只执行该队列中每个项的插入操作。 这样一来，在任何特定时间都只运行一个逻辑应用实例，这会完成插入操作并移到队列中的下一项，或者实例会收到429个错误，但不会尝试产生效益等待重试。

  * 创建一个父逻辑应用，用于调用每个操作的子逻辑应用或嵌套逻辑应用。 如果父项需要根据父项的结果调用不同的子应用，则可以使用条件操作或切换操作来确定要调用的子应用。 此模式可帮助您减少调用或操作的数量。

    例如，假设您有两个逻辑应用，每个应用都有一个轮询触发器，每分钟检查一次您的电子邮件帐户（例如 "成功" 或 "失败"）。 此设置会导致每小时120个调用。 相反，如果创建单个父逻辑应用，该应用每分钟轮询一次，但调用的子逻辑应用是根据使用者是 "成功" 还是 "失败" 运行的，则在这种情况下，将轮询检查数剪切为半或60。

* 设置批处理。

  如果目标服务支持批处理操作，则可以通过在组或批中处理项来解决限制，而不是单独处理。 若要实现批处理解决方案，请创建一个 "batch 接收方" 逻辑应用和 "batch 发送方" 逻辑应用。 批处理发送方将收集消息或项，直到满足指定的条件，然后将这些消息或项发送到一个组中。 批处理接收方接受该组，并处理这些消息或项目。 有关详细信息，请参阅[批处理消息组](../logic-apps/logic-apps-batch-process-send-receive-messages.md)。

* 将 webhook 版本用于触发器和操作，而不是轮询版本。

  为什么？ 轮询触发器按特定的时间间隔继续检查目标服务或系统。 一个非常频繁的间隔，例如每秒，都可能产生限制问题。 但是，webhook 触发器或操作（如[HTTP webhook](../connectors/connectors-native-webhook.md)）仅创建对目标服务或系统的单一调用，该调用在订阅时发生，并请求目标仅在发生事件时通知触发器或操作。 这样，触发器或操作就不必继续检查目标。
  
  因此，如果目标服务或系统支持 webhook 或提供具有 webhook 版本的连接器，则此选项优于使用轮询版本。 若要确定 webhook 触发器和操作，请确认它们具有`ApiConnectionWebhook`类型，或不需要指定重复周期。 有关详细信息，请参阅[APIConnectionWebhook 触发器](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)和[APIConnectionWebhook 操作](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action)。

## <a name="next-steps"></a>后续步骤

* 了解有关[逻辑应用限制和配置的](../logic-apps/logic-apps-limits-and-config.md)详细信息
