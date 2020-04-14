---
title: 处理限制问题，或"429 - 请求太多"错误
description: 如何解决 Azure 逻辑应用中的限制问题或"HTTP 429 请求太多"错误
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: fbfd52065bc0522668488492de2181f252f86a4e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272672"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>在 Azure 逻辑应用中处理限制问题（429 - "请求太多"错误）

在[Azure 逻辑应用中](../logic-apps/logic-apps-overview.md)，逻辑应用在遇到限制时返回["HTTP 429 请求太多"错误](https://developer.mozilla.org/docs/Web/HTTP/Status/429)，当请求数超过目标在特定时间可以处理的速率时，就会发生此错误。 限制可能会造成延迟数据处理、性能降低和错误（如超出指定的重试策略）等问题。

![SQL 服务器连接器中的限制](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

以下是逻辑应用可能会遇到的一些常见限制类型：

* [逻辑应用](#logic-app-throttling)
* [连接](#connector-throttling)
* [目的地服务或系统](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>逻辑应用限制

Azure 逻辑应用服务有自己的[吞吐量限制](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 因此，如果逻辑应用超过这些限制，逻辑应用资源将受到限制，而不仅仅是特定的实例或运行。

要查找此级别的限制事件，请在 Azure 门户中检查逻辑应用的 **"指标"** 窗格。

1. 在 [Azure 门户](https://portal.azure.com)的逻辑应用设计器中打开逻辑应用。

1. 在逻辑应用菜单上，在 **"监视**"下，选择 **"指标**"。

1. 在 **"图表标题**"下，选择 **"添加指标**"，以便向现有指标添加另一个指标。

1. 在第一个指标栏中，从**METRIC**列表中选择 **"操作限制事件**"。 在第二个指标栏中，从**METRIC**列表中选择 **"触发限制事件**"。

要处理此级别的限制，您有以下选项：

* 限制可同时运行的逻辑应用实例数。

  默认情况下，如果逻辑应用的触发器条件同时满足多次，则逻辑应用的多个触发器实例同时运行或*并行*运行 。 此行为意味着每个触发器实例在前面的工作流实例完成运行之前触发。

  尽管可以同时运行的触发器实例的默认数量是[无限的](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)，但您可以通过[打开触发器的并发设置](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)来限制此数字，如有必要，请选择默认值以外的限制。

* 启用高吞吐量模式。

  逻辑应用对于可在[5 分钟滚动间隔内运行的操作数具有默认限制](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 要将此限制提高到最大操作数，请打开逻辑应用中[的高吞吐量模式](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)。

* 禁用触发器中的数组辩论（"拆分"）行为。

  如果触发器返回要处理的剩余工作流操作的数组，则触发器的[**"拆分打开"** 设置](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)将拆分数组项并为每个数组项启动工作流实例，从而有效地触发多个并发运行，最多达到[**拆分 On**限制](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)。 要控制限制，请关闭**拆分打开**行为，让逻辑应用使用单个调用处理整个阵列，而不是处理每个调用的单个项。

* 将操作重构为较小的逻辑应用。

  如前所述，逻辑应用仅限于可在 5[分钟内运行的默认操作数](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)。 尽管可以通过启用[高吞吐量模式](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)来增加此限制，但您也可以考虑是否要将逻辑应用的操作分解为较小的逻辑应用，以便在每个逻辑应用中运行的操作数保持在限制之下。 这样，您可以减轻单个逻辑应用资源的负担，并将负载分布在多个逻辑应用之间。 此解决方案更适合处理大型数据集或在每个循环迭代中旋转大量并发运行的操作、循环迭代或超出操作执行限制的操作。

  例如，此逻辑应用执行所有工作，从 SQL Server 数据库获取表并从每个表获取行。 **对于每个**循环并发遍每个表，以便**Get 行**操作返回每个表的行。 根据这些表中的数据量，这些操作可能会超过操作执行的限制。

  ![逻辑应用"之前"重构](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  重构后，逻辑应用现在是父逻辑应用和子逻辑应用。 父级从 SQL Server 获取表，然后为每个表调用子逻辑应用以获取行：

  ![为一个操作创建逻辑应用](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  下面是父逻辑应用调用的子逻辑应用，用于获取每个表的行：

  ![为第二个操作创建另一个逻辑应用](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>连接器限制

每个连接器都有自己的限制限制，您可以在连接器的技术参考页面上找到这些限制。 例如[，Azure 服务总线连接器](https://docs.microsoft.com/connectors/servicebus/)具有限制限制，允许每分钟最多 6，000 个呼叫，而 SQL Server 连接器具有[限制限制，这些限制因操作类型而异](https://docs.microsoft.com/connectors/sql/)。

某些触发器和操作（如 HTTP）具有["重试策略"，](../logic-apps/logic-apps-exception-handling.md#retry-policies)您可以根据[重试策略限制](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits)进行自定义以实现异常处理。 此策略指定当原始请求失败或超时时触发器或操作重试请求，并生成 408、429 或 5xx 响应时重试请求的频率。 因此，当限制开始并返回 429 错误时，逻辑应用将遵循受支持的重试策略。

要了解触发器或操作是否支持重试策略，请检查触发器或操作的设置。 要查看触发器或操作的重试尝试，请转到逻辑应用的运行历史记录，选择要查看的运行，并展开该触发器或操作以查看有关输入、输出和任何重试的详细信息，例如：

![查看操作的运行历史记录、重试、输入和输出](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

尽管重试历史记录提供错误信息，但连接器限制和目标[限制](#destination-throttling)之间可能难以区分。 在这种情况下，您可能需要查看响应的详细信息或执行一些限制间隔计算以标识源。

对于全局多租户 Azure 逻辑应用服务中的逻辑应用，限制发生在*连接*级别。 因此，例如，对于在[集成服务环境 （ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)中运行的逻辑应用，对于非 ISE 连接，限制仍然会发生，因为它们在全局多租户逻辑应用服务中运行。 但是，由 ISE 连接器创建的 ISE 连接不会受到限制，因为它们在 ISE 中运行。

要处理此级别的限制，您有以下选项：

* 为单个操作设置多个连接，以便逻辑应用对要处理的数据进行分区。

  对于此选项，请考虑是否可以使用相同的凭据跨多个连接将操作的请求划分为同一目标来分配工作负荷。

  例如，假设您的逻辑应用从 SQL Server 数据库获取表，然后从每个表获取行。 根据必须处理的行数，可以使用多个连接和多个**For 每个**循环将行总数划分为较小的集进行处理。 此方案使用两**个 For 每个**循环将行总数拆分为两半。 第一**个 对于 每个**循环使用获取前半部分的表达式。 另一**个 对于 每个**循环使用不同的表达式来获取后半部分，例如：<p>

    * 表达式 1：`take()`函数获取集合的前面。 有关详细信息，请参阅[**`take()`** 函数](workflow-definition-language-functions-reference.md#take)。

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * 表达式 2：`skip()`函数删除集合的前面并返回所有其他项。 有关详细信息，请参阅[**`skip()`** 函数](workflow-definition-language-functions-reference.md#skip)。

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    下面是一个可视示例，演示如何使用这些表达式：

    ![为单个操作创建和使用多个连接](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* 为每个操作设置不同的连接。

  对于此选项，请考虑是否可以通过将每个操作的请求分散到其自己的连接来分配工作负载，即使操作连接到同一服务或系统并使用相同的凭据也是如此。

  例如，假设您的逻辑应用从 SQL Server 数据库获取表，并获取每个表中的每一行。 您可以使用单独的连接，以便获取表使用一个连接，而获取每行使用另一个连接。

  ![为每个操作创建和使用不同的连接](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* 更改["每个"循环](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)上的并发或并行性。

  默认情况下，"对于每个"循环迭代同时运行，最多达到[并发限制](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)。 如果连接器在"每个"循环内受到限制，则可以减少并行运行的循环迭代次数。 有关详细信息，请参阅以下主题：
  
  * ["每个"循环 - 更改并发或按顺序运行](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [工作流定义语言架构 - 对于每个循环](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [工作流定义语言架构 - 更改"每个"循环并发](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [工作流定义语言架构 - 按顺序运行"每个"循环](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>目标服务或系统限制

虽然连接器有其自己的限制限制，但连接器调用的目标服务或系统也可能具有限制限制。 例如，Microsoft Exchange Server 中的某些 API 具有比 Office 365 Outlook 连接器更严格的限制限制。

默认情况下，逻辑应用的实例以及这些实例中的任何循环或分支*并行*运行 。 此行为意味着多个实例可以同时调用同一终结点。 每个实例都不知道对方的存在，因此尝试重试失败操作可能会创建[争用条件](https://en.wikipedia.org/wiki/Race_condition)，其中多个调用尝试同时运行，但要成功，这些调用必须在开始限制之前到达目标服务或系统。

例如，假设您有一个包含 100 个项目的数组。 您可以使用"每个"循环遍历数组并打开循环的并发控件，以便可以将并行迭代数限制为 20 或[当前默认限制](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)。 在该循环中，操作将数组中的项插入到 SQL Server 数据库中，该数据库每秒只允许 15 个调用。 此方案会导致限制问题，因为积压的重试会累积起来，并且永远不会运行。

下表描述了当操作的重试间隔为 1 秒时循环中发生的情况的时间线：

| 时间点 | 运行的操作数 | 失败的操作数 | 等待的重试次数 |
|---------------|----------------------------|-----------------------------|---------------------------|
| T = 0 秒 | 20 个刀片 | 5 失败，由于 SQL 限制 | 5 次重试 |
| T = 0.5 秒 | 15 个插入，由于前 5 次重试等待 | 所有 15 个失败，由于以前的 SQL 限制仍然有效 0.5 秒 | 20 次重试 <br>（前 5 + 15 新） |
| T = 1 秒 | 20 个刀片 | 5 次失败加上以前的 20 次重试，由于 SQL 限制 | 25 次重试（前 20 + 5 个新）
|||||

要处理此级别的限制，您有以下选项：

* 创建逻辑应用，以便每个应用处理单个操作。

  * 继续本节中的 SQL Server 示例，您可以创建一个逻辑应用，将数组项放入队列中，如[Azure 服务总线队列](../connectors/connectors-create-api-servicebus.md)。 然后创建另一个逻辑应用，该应用仅对该队列中的每个项执行插入操作。 这样，在任何特定时间只有一个逻辑应用实例运行，该实例要么完成插入操作并移动到队列中的下一个项，要么实例收到 429 个错误，但不会尝试非生产性重试。

  * 创建一个父逻辑应用，为每个操作调用子项或嵌套逻辑应用。 如果父级需要根据父级的结果调用不同的子应用，则可以使用条件操作或切换操作来确定要调用的子应用。 此模式可以帮助您减少呼叫或操作的数量。

    例如，假设您有两个逻辑应用，每个应用都有一个轮询触发器，每分钟检查电子邮件帐户的特定主题，例如"成功"或"失败"。 此设置每小时产生 120 次呼叫。 相反，如果创建每分钟轮询的单个父逻辑应用，但调用基于主题为"成功"还是"失败"运行的子逻辑应用，则将轮询检查数减少到一半，在这种情况下将轮询检查数减少到 60 个。

* 设置批处理。

  如果目标服务支持批处理操作，则可以通过按组或批处理处理项目来解决限制问题，而不是单独处理。 要实现批处理解决方案，请创建"批处理接收方"逻辑应用和"批处理发送方"逻辑应用。 批处理发件人收集邮件或项目，直到满足指定的条件，然后在单个组中发送这些邮件或项目。 批处理接收方接受该组并处理这些消息或项。 有关详细信息，请参阅[组中的批处理消息](../logic-apps/logic-apps-batch-process-send-receive-messages.md)。

* 对触发器和操作（而不是轮询版本）使用 Webhook 版本。

  为什么？ 轮询触发器继续在特定时间间隔检查目标服务或系统。 非常频繁的间隔（如每一秒）会产生限制问题。 但是，Webhook 触发器或操作（如[HTTP Webhook）](../connectors/connectors-native-webhook.md)仅创建对目标服务或系统的单个调用，该调用发生在订阅时间，并且请求目标仅在事件发生时通知触发器或操作。 这样，触发器或操作不必持续检查目标。
  
  因此，如果目标服务或系统支持 Webhook 或提供具有 Webhook 版本的连接器，则此选项比使用轮询版本更好。 要标识 Webhook 触发器和操作，请确认它们具有`ApiConnectionWebhook`类型或它们不需要指定重复。 有关详细信息，请参阅[APIConnectionWebhook 触发器](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)和[APIConnectionWebhook 操作](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action)。

## <a name="next-steps"></a>后续步骤

* 了解有关[逻辑应用限制和配置](../logic-apps/logic-apps-limits-and-config.md)的更多信息
