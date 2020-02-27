---
title: 奖励评分 - 个性化体验创建服务
description: 奖励评分指示为用户生成的个性化选项 (RewardActionID) 的好坏程度。 奖励评分值由业务逻辑根据用户行为的观察结果来确定。 个性化体验创建服务通过评估奖励来训练其机器学习模型。
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: 734e4d0fdcec25884f8535ec61ccd10569fa8890
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623786"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>奖励评分表示个性化的成败

奖励评分指示为用户生成的个性化选项 ([RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response)) 的好坏程度。 奖励评分值由业务逻辑根据用户行为的观察结果来确定。

个性化体验创建服务通过评估奖励来训练其机器学习模型。

了解[如何](how-to-settings.md#configure-rewards-for-the-feedback-loop)在 Personalizer 资源的 Azure 门户中配置默认奖励分数。

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>使用奖励 API 向个性化体验创建服务发送奖励评分

奖励由[奖励 API](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward) 发送到个性化体验创建服务。 通常，奖励是0到1之间的数字。 在某些情况下，可以使用值为-1 的负奖励，只应在有强化学习（RL）的情况下使用。 个性化体验创建服务将训练模型，以实现一段时间内可能的最高奖励总分。

发生用户行为（这可能是几天以后的事）后，将发送奖励。 如果发生事件之前个性化体验创建服务等待了最长允许时间，则视为没有奖励。可以在 Azure 门户中使用[奖励等待时间](#reward-wait-time)配置默认奖励。

如果在**奖励等待时间**内未收到某个事件的奖励评分，则会应用**默认奖励**。 通常， **[默认奖励](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** 配置为 0。


## <a name="behaviors-and-data-to-consider-for-rewards"></a>要考虑奖励的行为和数据

在奖励分数的上下文中考虑以下信号和行为：

* 当涉及到选项时，用户直接输入建议（“你是说 X 吗？”）。
* 会话长度。
* 会话之间的间隔时间。
* 对用户交互进行情绪分析。
* 直接提问和小型调查，其中机器人要求用户提供关于有用性、准确性的反馈。
* 响应警报或延迟响应警报。

## <a name="composing-reward-scores"></a>构成奖励评分

必须在业务逻辑中计算奖励评分。 评分可以表示为：

* 发送一次的单个数字
* 立即发送的评分（例如 0.8），以及稍后发送的另一个评分（通常为 0.2）。

## <a name="default-rewards"></a>默认奖励

如果在[奖励等待时间](#reward-wait-time)（自调用排名以来的持续时间）未收到奖励，个性化体验创建服务会向排名事件隐式应用**默认奖励**。

## <a name="building-up-rewards-with-multiple-factors"></a>使用多种因素构成奖励

为实现有效的个性化，可以根据多个因素来构建奖励分数。

例如，可应用以下规则来个性化视频内容的列表：

|用户行为|部分评分值|
|--|--|
|单击排名最高项的用户。|+0.5 奖励|
|打开该项目实际内容的用户。|+0.3 奖励|
|观看内容 5 分钟或观看内容 30%（以较长者为准）的用户。|+0.2 奖励|
|||

然后，可以向 API 发送奖励总分。

## <a name="calling-the-reward-api-multiple-times"></a>多次调用奖励 API

还可以使用相同的事件 ID 调用奖励 API，以发送不同的奖励评分。 当 Personalizer 获得这些奖励时，它通过按 Personalizer 配置中的指定将其聚合来确定该事件的最终回报。

聚合值：

*  **第一**种：获取针对事件接收的首个积分评分，并放弃其余部分。
* **Sum**：获取为 eventId 收集的所有奖励评分，并将它们相加。

将丢弃在**奖励等待时间**以后为某个事件收到的所有奖励，模型训练不受影响。

通过添加奖励评分，最终回报可能超出预期分数范围。 这不会使服务失败。

## <a name="best-practices-for-calculating-reward-score"></a>有关计算奖励评分的最佳做法

* **考虑成功的个性化设置的真正指示符**：可以很容易地在单击中进行考虑，但最好是根据您希望用户*实现*的，而不是您希望用户执行的*操作*。  例如，基于点击数的奖励可能会导致诱导性点击。

* **使用奖励评分来了解个性化设置的工作原理**：个性化电影建议会使用户观看电影并为其提供高评级。 由于电影评级取决于许多因素（例如演技、用户的心情等），因此，这种奖励不能很好地反映个性化的效果。 但是，观看电影前几分钟的用户也许可以更好地反映个性化的效果，在 5 分钟后发送奖励评分 1 是一个更好的信号。

* **奖励仅适用于 RewardActionID**： Personalizer 应用回报以了解 RewardActionID 中指定的操作的效力。 如果你选择显示其他操作，而用户单击了这些操作，则奖励应该为 0。

* **考虑意想不到的后果**：创建奖励功能，使[道德和责任使用](ethics-responsible-use.md)成为责任。

* **使用增量奖励**：为较小的用户行为添加部分奖励有助于 Personalizer 获得更好的回报。 此增量奖励可让算法知道它即将在最终的所需行为中与用户互动。
    * 如果你正在显示电影列表，而用户将鼠标悬停在第一部电影上片刻时间以查看详细信息，则可以确定发生了某种用户互动。 可为该行为提供奖励评分 0.1。
    * 如果用户打开页面然后退出，则奖励评分可为 0.2。

## <a name="reward-wait-time"></a>奖励等待时间

个性化体验创建服务将排名调用的信息与奖励调用中发送的奖励相关联，以训练模型。 这些事件可能发生在不同的时间。 即使以非活动事件的形式发出了排名调用，然后将其激活，个性化体验创建服务也只会等待有限的时间（从发生排名调用开始算起）。

如果**奖励等待时间**已过且未收到奖励信息，则会将默认奖励应用到该事件以进行训练。 最长等待持续时间为 6 天。

## <a name="best-practices-for-reward-wait-time"></a>奖励等待时间的最佳实践

请遵循以下建议来改善结果。

* 请将奖励等待时间设置得尽量短，同时为获取用户反馈留出足够的时间。

* 等待持续时间不应短于获取反馈所需的时间。 例如，如果某些奖励需要在用户观看视频 1 分钟后才会传入，则试验长度应至少是 2 分钟。

## <a name="next-steps"></a>后续步骤

* [强化学习](concepts-reinforcement-learning.md)
* [试用排名 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [试用奖励 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
