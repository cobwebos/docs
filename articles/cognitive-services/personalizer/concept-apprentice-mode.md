---
title: Apprentice 模式-Personalizer
description: ''
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: f793535f04b36d231cec384b7acd66e38a7eb039
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91253524"
---
# <a name="use-apprentice-mode-to-train-personalizer-without-affecting-your-existing-application"></a>使用 Apprentice 模式训练 Personalizer，而不影响现有应用程序

由于 **实际** 强化学习的性质，Personalizer 模型只能在生产环境中定型。 部署新用例时，Personalizer 模型不会有效地执行，因为模型需要花费时间才能充分定型。  **Apprentice 模式** 是一种学习行为，可简化这种情况，并使您能够在模型中获得信心，而不需要开发人员更改任何代码。

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="what-is-apprentice-mode"></a>什么是 Apprentice 模式？

与 apprentice 从主节点学习的方式类似，体验也更好;Apprentice 模式是一 _种_ 允许 Personalizer 通过观察从现有应用程序逻辑获得的结果来了解的行为。

Personalizer 火车 by 模拟与应用程序相同的输出。 随着事件的流动，Personalizer 可以与现有的应用程序 _保持同步_ ，而不会影响现有的逻辑和结果。 指标（可从 Azure 门户和 API 获取）可帮助你了解模型学习时的性能。

在 Personalizer 学习并获得一定程度的理解后，开发人员可以将行为从 "Apprentice 模式" 更改为 "联机" 模式。 此时，Personalizer 开始影响排名 API 中的操作。

## <a name="purpose-of-apprentice-mode"></a>Apprentice 模式的用途

Apprentice 模式为你提供了 Personalizer 服务及其机器学习功能的信任，并向确信提供了服务发送的信息。

使用 Apprentice 模式的两个主要原因是：

* 缓解 **冷启动**： Apprentice 模式可帮助管理和评估 "新" 模型的学习时间的成本-当它没有返回最佳操作，并且未实现约60-80% 的令人满意的有效性级别时。
* **验证操作和上下文功能**：在操作和上下文中发送的功能可能不足或不准确-太少、过多、不正确或因太少而无法定型 Personalizer 来获得理想的有效性率。 使用 [功能评估](concept-feature-evaluation.md) 查找并修复功能问题。

## <a name="when-should-you-use-apprentice-mode"></a>何时应使用 Apprentice 模式？

使用 Apprentice 模式培训 Personalizer，通过以下方案提高其有效性，同时使用户体验不受 Personalizer 影响：

* 你要在新用例中实现 Personalizer。
* 您已显著更改了在上下文或操作中发送的功能。
* 在计算回报时，你会发生显著变化。

Apprentice 模式并非一种有效的方式来测量 Personalizer 对奖励评分的影响。 若要测量 Personalizer 为每个排名调用选择最佳可能操作的方式，请使用 [脱机评估](concepts-offline-evaluation.md)。

## <a name="who-should-use-apprentice-mode"></a>谁应该使用 Apprentice 模式？

Apprentice 模式适用于开发人员、数据科学家和业务决策者：

* **开发人员** 可以使用 Apprentice 模式来确保在应用程序中正确使用排名和奖励 api，并确保从应用程序发送到 Personalizer 的功能不包含错误或不相关的功能，例如时间戳或 UserID 元素。

* **数据科学家** 可以使用 Apprentice 模式来验证这些功能是否有效地用于训练 Personalizer 模型，因为奖励等待时间不太长或短。

* **业务决策者** 可以使用 Apprentice 模式来评估 Personalizer 与现有业务逻辑相比 (提高结果的可能性) 。 这样，他们就可以做出明智的决策，影响用户体验，其中真实收入和用户满意度面临着实质性的影响。

## <a name="comparing-behaviors---apprentice-mode-and-online-mode"></a>比较行为-Apprentice 模式和联机模式

在 Apprentice 模式下学习在以下方面不同于联机模式。

|领域|学徒模式|“联机” 模式|
|--|--|--|
|对用户体验的影响|你可以使用现有的用户行为来定型 Personalizer，方法是让其观察 (不会影响 **默认操作** 的) 以及获得的奖励。 这意味着用户的体验和业务结果不受影响。|显示从排名调用返回的顶部操作以影响用户行为。|
|学习速度|在 Apprentice 模式下，Personalizer 的学习速度会比在联机模式下学习时更慢。 Apprentice 模式只能通过观察你的 **默认操作**获得的奖励来了解，这会限制学习速度，因为无法执行任何浏览。|更快地学习，因为它可以利用当前模型并探索新的趋势。|
|学习有效性 "上限"|Personalizer 可以大致接近、极少匹配，并且永远不会超过基础业务逻辑的性能 (通过每个排名调用) 的 **默认操作** 实现的回报总量。|Personalizer 应超过应用程序基准，一段时间后，您应该执行脱机计算和功能评估，以继续对模型进行改进。 |
|RewardActionId 的排名 API 值|用户的体验不受影响，因为 _rewardActionId_ 始终是在排名请求中发送的第一个操作。 换句话说，排名 API 在 Apprentice 模式下不会对应用程序显示任何内容。 在应用程序中奖励 Api 不应更改它在一种模式和另一种模式之间使用奖励 API 的方式。|Personalizer 为应用程序选择的 _rewardActionId_ 将更改用户的体验。 |
|评估|Personalizer 比较了默认业务逻辑获得的奖励总数，并且在该时间点处于联机模式时，将获得奖励合计 Personalizer。 此资源的 Azure 门户中提供了比较|通过运行 [脱机评估](concepts-offline-evaluation.md)来评估 Personalizer 的有效性，这使你可以将 Personalizer 的总回报与应用程序基准的潜在回报进行比较。|

有关 apprentice 模式有效性的说明：

* Personalizer 在 Apprentice 模式下的有效性极少达到应用程序基线的 100%;并且永远不会超过它。
* 最佳做法是不尝试获得100% 的实现;但60% –80% 的范围应根据用例进行定向。

## <a name="using-apprentice-mode-to-train-with-historical-data"></a>使用 Apprentice 模式训练历史数据

如果有大量的历史数据，需要使用训练 Personalizer，可以使用 Apprentice 模式通过 Personalizer 重播数据。

在 Apprentice 模式下设置 Personalizer，并创建一个脚本，该脚本使用历史数据中的操作和上下文功能调用排名。 根据此数据中的记录计算，调用奖励 API。 你将需要大约50000个历史事件来查看一些结果，但建议使用500000来更好地信任结果。

当从历史数据中定型时，建议在上下文和操作 (功能中发送的数据、在用于排名请求的 JSON 中的布局以及此定型数据集中的奖励计算) ，与现有应用程序中提供的数据 (功能和计算) 的计算结果相匹配。

脱机和嘈杂的数据往往更不完整，并且格式不相同。 尽管可以从历史数据进行定型，但这样做可能不会有任何结论，而且并不是 Personalizer 学习情况的良好预测性，尤其是在过去的数据和现有应用程序之间的功能不同的情况下。

通常，对于 Personalizer，与使用历史数据进行定型相比，将行为更改为 Apprentice 模式并从现有应用程序中进行学习是一个具有有效模型的更有效路径，具有较少的劳动、数据工程和清理工作。

## <a name="using-apprentice-mode-versus-ab-tests"></a>使用 Apprentice 模式与/B 测试

仅当验证并在联机模式下学习后，才能对 Personalizer 处理执行 A/B 测试。 在 Apprentice 模式下，仅使用 **默认操作** ，这意味着所有用户都可以有效地看到控制体验。

即使 Personalizer 只是 _处理_，验证数据是否适合培训 Personalizer 时也存在同样的挑战。 可以改用 Apprentice 模式，使用100% 的流量，并让所有用户 (不受影响的) 体验。

使用 Personalizer 并联机学习使用案例后，可以使用 A/B 试验进行控制队列，并比较比用于回报的信号更复杂的结果。 A/B 测试可以回答的示例问题是： `In a retail website, Personalizer optimizes a layout and gets more users to _check out_ earlier, but does this reduce total revenue per transaction?`

## <a name="next-steps"></a>后续步骤

* 了解 [活动和非活动事件](concept-active-inactive-events.md)
