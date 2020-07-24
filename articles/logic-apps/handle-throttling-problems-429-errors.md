---
title: 处理限制问题，或 "429-请求太多" 错误
description: 如何解决 Azure 逻辑应用中的限制问题或“HTTP 429 请求过多”错误
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 495847d31682aff64fed3c81b1d5d68cf67dfd38
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086432"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>处理 Azure 逻辑应用中的限制问题（429 -“请求过多”错误）

在 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)中，当遇到限制问题时，逻辑应用会返回[“HTTP 429 请求过多”错误](https://developer.mozilla.org/docs/Web/HTTP/Status/429)。如果在特定的时间内，请求数超过了目标可以处理的速率，则会发生此错误。 限制可能会造成各种问题，例如，数据处理延迟、性能速度下降，以及超过指定的重试策略等错误。

![SQL Server 连接器中的限制](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

下面是逻辑应用可能会遇到的一些常见限制类型：

* [逻辑应用](#logic-app-throttling)
* [连接器](#connector-throttling)
* [目标服务或系统](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>逻辑应用限制

Azure 逻辑应用服务具有自身的[吞吐量限制](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 因此，如果逻辑应用超过这些限制，则逻辑应用资源（而不仅仅是特定的实例或运行）将受到限制。

若要在此级别查找限制事件，请在 Azure 门户中检查逻辑应用的“指标”窗格。 

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用菜单中，在“监视”下，选择“指标”。  

1. 在“图表标题”下，选择“添加指标”以添加另一个指标。  

1. 在第一个指标栏中，从“指标”列表中选择“操作受限制事件”。   在第二个指标栏中，从“指标”列表中选择“触发器受限制事件”。  

若要处理在此级别发生的限制，可以使用以下选项：

* 限制可以同时运行的逻辑应用实例数。

  默认情况下，如果同时满足逻辑应用的触发条件多次，则会并发或并行运行逻辑应用的多个触发器实例。  此行为意味着，每个触发器实例会在上一个工作流实例完成运行之前激发。

  尽管可以并发运行的默认触发器实例数[没有限制](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)，但可以通过[启用触发器的并发性设置](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)来限制此数目；如果需要，可选择一个不同于默认值的限制。

* 启用高吞吐量模式。

  逻辑应用[在 5 分钟滚动时间间隔内可运行的操作数目存在默认限制](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 若要将此限制提高到最大操作数目，请在逻辑应用中启用[高吞吐量模式](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)。

* 在触发器中禁用数组解批（“拆分”）行为。

  如果触发器返回了要处理的剩余工作流操作数组，则触发器的[**拆分**设置](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)会拆分数组项，并针对每个数组项启动一个工作流实例，从而有效触发多个并发运行（不超过[**拆分**限制](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)）。 若要控制限制，请禁用**拆分**行为，让逻辑应用通过单个调用处理整个数组，而不是每个调用处理一个项。

* 将操作重构为更小的逻辑应用。

  如前所述，逻辑应用[在 5 分钟期限内可运行的操作数目存在默认限制](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 虽然可以通过启用[高吞吐量模式](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)来提高此限制，但也可以考虑是否要将逻辑应用的操作分解为更小的逻辑应用，使每个逻辑应用中运行的操作数目不会超过限制。 这样，便可以减轻单个逻辑应用的资源负担，并跨多个逻辑应用分配负载。 此解决方案更适合需要处理大型数据集的操作，或者执行大量并发运行的操作、循环迭代，或每个循环迭代中超过操作执行限制的操作。

  例如，此逻辑应用会执行从 SQL Server 数据库中获取表并从每个表中获取行的所有工作。 **For each** 循环以并发方式遍历每个表，使 **Get rows** 操作返回每个表的行。 这些操作可能会超出操作执行限制，具体取决于这些表中的数据量。

  ![重构前的逻辑应用](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  重构之后，该逻辑应用现在成了一个父子逻辑应用。 父级从 SQL Server 中获取表，然后针对每个表调用子逻辑应用来获取行：

  ![为一个操作创建逻辑应用](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  下面是父逻辑应用调用的用以获取每个表的行的子逻辑应用：

  ![为另一个操作创建另一个逻辑应用](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>连接器限制

每个连接器具有自身的限制，可以在连接器的技术参考页上找到这些限制。 例如，[Azure 服务总线连接器](/connectors/servicebus/)的限制为每分钟最多允许 6000 次调用，而 SQL Server 连接器的[限制因操作类型而异](/connectors/sql/)。

某些触发器和操作（例如 HTTP）具有[“重试策略”](../logic-apps/logic-apps-exception-handling.md#retry-policies)，可以根据[重试策略限制](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits)对该策略进行自定义以实现异常处理。 此策略指定当原始请求失败或超时并生成 408、429 或 5xx 响应时，触发器或操作是否以及以何频率重试请求。 因此，当限制开始并返回了 429 错误时，逻辑应用在受支持的情况下将遵循重试策略。

若要了解触发器或操作是否支持重试策略，请检查该触发器或操作的设置。 若要查看触发器或操作的重试尝试，请转到逻辑应用的运行历史记录，选择要查看的运行，然后展开该触发器或操作以查看有关输入、输出和任何重试的详细信息，例如：

![查看操作的运行历史记录、重试、输入和输出](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

尽管重试历史记录提供了有关错误的信息，但在区分连接器限制和[目标限制](#destination-throttling)时可能会遇到麻烦。 在这种情况下，可能必须查看响应的详细信息，或执行一些限制间隔计算来确定来源。

对于全局多租户 Azure 逻辑应用服务中的逻辑应用，限制是在连接级别发生的。  例如，对于在[integration service 环境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中运行的逻辑应用，对非 ISE 连接仍会进行限制，因为这些应用在全局多租户逻辑应用服务中运行。 但是，ise 连接是由 ISE 连接器创建的，因为它们在 ISE 中运行。

若要处理在此级别发生的限制，可以使用以下选项：

* 为单个操作设置多个连接，使逻辑应用对要处理的数据进行分区。

  对于此选项，请考虑是否可以通过以下方式来分配工作负荷：使用相同的凭据将操作的请求划分到与同一目标建立的多个连接。

  例如，假设逻辑应用从某个 SQL Server 数据库中获取表，然后从每个表中获取行。 根据需要处理的行数，可以使用多个连接和多个 **For each** 循环将总行数划分为较小的集进行处理。 此方案使用两个 **For each** 循环将总行数划分为两半。 第一个 **For each** 循环使用一个表达式来获取前半部分。 另一个 **For each** 循环使用另一个表达式获取后半部分，例如：<p>

    * 表达式1： `take()` 函数获取集合的前端。 有关详细信息，请参阅[ **`take()`** 函数](workflow-definition-language-functions-reference.md#take)。

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Expression 2： `skip()` 函数删除集合的前面并返回所有其他项。 有关详细信息，请参阅[ **`skip()`** 函数](workflow-definition-language-functions-reference.md#skip)。

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    下面是一个演示如何使用这些表达式的直观示例：

    ![为单个操作创建和使用多个连接](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* 为每个操作设置不同的连接。

  对于此选项，请考虑是否可以通过以下方式分配工作负荷：通过每个操作的请求自身的连接来分散这些请求，即使操作连接到同一服务或系统并使用相同的凭据。

  例如，假设逻辑应用从某个 SQL Server 数据库中获取表，并获取每个表中的行。 可以使用不同的连接，以便在获取表时使用一个连接，获取每个行时使用另一个连接。

  ![为每个操作创建并使用不同的连接](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* 更改[“For each”循环](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)中的并发度或并行度。

  默认情况下，“For each”循环迭代会同时运行，但不超过[并发限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 如果连接器在“For each”循环内部受到限制，你可以减少并行运行的循环迭代数。 有关详细信息，请参阅以下主题：
  
  * [“For each”循环 - 更改并发度或按顺序运行](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [工作流定义语言架构 - For each 循环](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [工作流定义语言架构 - 更改“For each”循环并发度](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [工作流定义语言架构 - 按顺序运行“For each”循环](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>目标服务或系统限制

连接器存在自身的限制，同时，该连接器调用的目标服务或系统也可能存在限制。 例如，Microsoft Exchange Server 中某些 API 的限制比 Office 365 Outlook 连接器的限制更严格。

默认情况下，逻辑应用的实例以及这些实例中的任何循环或分支将会并行运行。  此行为意味着，多个实例可以同时调用同一个终结点。 每个实例不知道其他实例是否存在，因此重试失败的操作可能会造成[争用状况](https://en.wikipedia.org/wiki/Race_condition)，在这种情况下，多个调用尝试会同时运行，但若要成功，这些调用必须在开始发生限制之前抵达目标服务或系统。

例如，假设你的某个数组包含 100 个项。 你使用“for each”循环来迭代该数组并启用循环的并发控制，以便可以将并行迭代数限制为 20 或[当前默认限制](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)。 在该循环中，某个操作将数组中的某个项插入 SQL Server 数据库，此时每秒只允许 15 次调用。 这种情况会导致限制问题，因为重试会不断积压，而永远不会运行。

下表描述了当操作的重试间隔为 1 秒时，循环中发生的情况的时间线：

| 时间点 | 运行的操作数目 | 失败的操作数目 | 等待的重试次数 |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 秒 | 20 次插入 | 5 个操作由于 SQL 限制而失败 | 5 次重试 |
| T + 0.5 秒 | 由于前 5 次重试处于等待状态而产生了 15 次插入 | 所有 15 个操作由于前面的 SQL 限制保持生效 0.5 秒而失败 | 20 次重试 <br>（前 5 次 + 15 次新重试） |
| T + 1 秒 | 20 次插入 | 5 次加上前 20 次重试由于 SQL 限制而失败 | 25 次重试（前 20 次 + 5 次新重试）
|||||

若要处理在此级别发生的限制，可以使用以下选项：

* 创建逻辑应用，让每个逻辑应用处理一个操作。

  * 沿用本部分中的示例 SQL Server 方案。可以创建一个逻辑应用，用于将数组项放入队列（例如 [Azure 服务总线队列](../connectors/connectors-create-api-servicebus.md)）。 然后，创建另一个逻辑应用，用于仅对该队列中的每个项执行插入操作。 这样，在任意特定时间只会运行一个逻辑应用实例，该实例完成插入操作并转到队列中的下一项；或者，该实例会收到 429 错误，但不会毫无意义地进行重试。

  * 创建一个父逻辑应用，用于对每个操作调用子逻辑应用或嵌套的逻辑应用。 如果父级需要根据其结果调用不同的子应用，则你可以使用条件操作或开关操作来确定要调用哪个子应用。 此模式可帮助减少调用或操作的数目。

    例如，假设你有两个逻辑应用，每个应用通过一个轮询触发器每隔一分钟检查电子邮件帐户中的特定主题（例如“成功”或“失败”）。 此设置会导致每小时发出 120 次调用。 如果创建单个父逻辑应用，该应用每隔一分钟轮询一次，但会调用根据主题是“成功”还是“失败”运行的子逻辑应用，则你可以将轮询检查次数减少一半（在本例中为 60 次）。

* 设置批处理。

  如果目标服务支持批处理操作，则你可以通过按组或按批对各个项进行处理（而不是逐个处理）来解决限制。 若要实现批处理解决方案，请创建一个“批接收器”逻辑应用和一个“批发送器”逻辑应用。 批发送器不断收集消息或项，直到满足指定的条件，然后将这些消息或项发送到单个组中。 批接收器接受该组，并处理这些消息或项。 有关详细信息，请参阅[按组对消息进行批处理](../logic-apps/logic-apps-batch-process-send-receive-messages.md)。

* 请使用触发器和操作的 Webhook 版本，而不是轮询版本。

  为什么？ 轮询触发器按特定的间隔持续检查目标服务或系统。 非常频繁的间隔（例如每秒）可能会造成限制问题。 但是，Webhook 触发器或操作（例如 [HTTP Webhook](../connectors/connectors-native-webhook.md)）只会针对目标服务或系统创建单个调用，该调用是在订阅时发生的，并请求目标仅在发生事件时才通知触发器或操作。 这样，触发器或操作就没有必要持续检查目标。
  
  因此，如果目标服务或系统支持 Webhook 或提供了使用 Webhook 版本的连接器，则此选项比使用轮询版本更好。 可以通过以下方法识别 Webhook 触发器和操作：它们具有 `ApiConnectionWebhook` 类型，它们也不要求指定重复周期。 有关详细信息，请参阅 [APIConnectionWebhook 触发器](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)和 [APIConnectionWebhook 操作](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action)。

## <a name="next-steps"></a>后续步骤

* 详细了解[逻辑应用限制和配置](../logic-apps/logic-apps-limits-and-config.md)
