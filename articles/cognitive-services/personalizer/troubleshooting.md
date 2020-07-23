---
title: 故障排除-Personalizer
description: 本文包含有关 Personalizer 的常见问题的解答。
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: ca19fbfc505e3e46338a0930773b1879dce788c1
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586246"
---
# <a name="personalizer-troubleshooting"></a>Personalizer 故障排除

本文包含有关 Personalizer 的常见问题的解答。

## <a name="configuration-issues"></a>配置问题

### <a name="i-changed-a-configuration-setting-and-now-my-loop-isnt-performing-at-the-same-learning-level-what-happened"></a>我更改了某个配置设置，但现在我的循环没有在同一学习级别上执行。 发生了什么情况？

某些配置设置会[重置你的模型](how-to-settings.md#settings-that-include-resetting-the-model)。 应仔细计划配置更改。

### <a name="when-configuring-personalizer-with-the-api-i-received-an-error-what-happened"></a>使用 API 配置 Personalizer 时，收到错误消息。 发生了什么情况？

如果使用单个 API 请求来配置服务并更改学习行为，会收到错误。 需要进行两个单独的 API 调用：首先配置服务，然后切换学习行为。

## <a name="transaction-errors"></a>事务错误

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>收到来自服务的 HTTP 429 （请求过多）响应。 我该怎么办？

如果在创建 Personalizer 实例时选择了免费价格层，则允许的排名请求数有配额限制。 查看排名 API 的 API 调用速率（在 Personalizer 资源的 Azure 门户中的 "度量值" 窗格中），并调整定价层（在 "定价层" 窗格中）（如果你的呼叫量预计超出所选定价层的阈值）。

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>我在排名或奖励 Api 上收到5xx 错误。   应采取何种操作？

这些问题应是透明的。 如果他们继续，请通过在 "**支持 + 故障排除**" 部分中选择 "支持 + 故障排除" 部分中的 "**新支持请求**" 与 Personalizer 资源的 Azure 门户联系。

## <a name="learning-loop"></a>学习循环

### <a name="the-learning-loop-doesnt-attain-a-100-match-to-the-system-without-personalizer-how-do-i-fix-this"></a>在没有 Personalizer 的情况下，学习循环与系统之间的匹配不会达到100%。 如何修复此问题？

您无法通过学习循环实现您的目标的原因：
* 通过排名 API 调用发送的功能不足
* 发送的功能中的 bug，例如，将非聚合功能数据（如时间戳）发送到排名 API
* 具有循环处理的 bug，如不将奖励数据发送给用于事件的奖励 API

若要解决此问题，需要更改处理方式：更改发送到循环的功能，或确保奖励是排名响应质量的正确评估。

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>学习循环似乎没有学习。 如何修复此问题？

学习循环需要几千个奖励调用才能有效地排定排名。

如果不确定学习循环当前的行为方式，请运行[脱机评估](concepts-offline-evaluation.md)，并应用已更正的学习策略。

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>我继续获得排名结果，所有项目的概率都相同。 如何实现知道 Personalizer 是学习吗？

当 Personalizer 刚刚启动并且具有_空_模型，或重置 Personalizer 循环，并且模型仍在**模型更新频率**期间内时，它将在排名 API 结果中返回相同的概率。

新的更新周期开始时，将使用更新的模型，你会看到概率发生变化。

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>学习循环正在学习，但似乎不再了解，排名结果的质量并不好。   应采取何种操作？

* 请确保已在该 Personalizer 资源的 Azure 门户中完成和应用了一个评估（学习循环）。
* 请确保通过奖励 API 发送所有回报，并进行处理。

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>如何实现知道学习循环会定期更新，并用于评分我的数据？

您可以在 Azure 门户的 "**模型和学习设置**" 页中找到模型的上次更新时间。 如果你看到旧时间戳，则很可能是因为你未发送排名和奖励呼叫。 如果服务没有传入数据，则不会更新学习。 如果您看到学习循环的更新频率不够，则可以编辑该循环的**模型更新频率**。

## <a name="offline-evaluations"></a>脱机评估

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>脱机评估的功能重要性会返回包含数百个或数千个项目的长列表。 发生了什么情况？

这通常是由中发送的时间戳、用户 Id 或一些其他精细功能导致的。

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>我创建了脱机评估版，几乎立即成功了。 为什么会这样？ 我看不到任何结果？

脱机计算使用该时间段内事件的定型模型数据。 如果在评估的开始时间和结束时间之间未发送任何数据，则它将在不进行任何结果的情况下完成。 通过选择已发送到 Personalizer 的事件的时间范围来提交新的脱机评估。

## <a name="learning-policy"></a>学习策略

### <a name="how-do-i-import-a-learning-policy"></a>如何实现导入学习策略吗？

了解有关[学习策略概念](concept-active-learning.md#understand-learning-policy-settings)以及[如何应用](how-to-manage-model.md)新学习策略的详细信息。 如果你不想要选择学习策略，可以根据当前事件，使用[脱机评估](how-to-offline-evaluation.md)建议学习策略。


## <a name="security"></a>安全性

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>我的循环的 API 密钥已泄露。 我该怎么办？

在交换客户端以使用另一个密钥后，你可以重新生成一个密钥。 通过使用两个密钥，可以迟缓方式传播密钥，而无需停机。 建议定期执行此操作，作为一种安全措施。


## <a name="next-steps"></a>后续步骤

[配置模型更新频率](how-to-settings.md#model-update-frequency)