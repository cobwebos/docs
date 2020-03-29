---
title: 故障排除 - 个性化程序
description: 本文包含有关个人化器的常见问题解答解答。
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: 904953f028eb31afe42cf477ac05be43e8b72a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336030"
---
# <a name="personalizer-troubleshooting"></a>个性化故障排除

本文包含有关个人化器的常见问题解答解答。

## <a name="transaction-errors"></a>事务错误

<details>
<summary><b>我从服务收到 HTTP 429（请求太多）响应。我能做什么？</b></summary>

**答**：如果您在创建个人化实例时选择了免费价格层，则允许的 Rank 请求数存在配额限制。 查看 Rank API 的 API 调用速率（在个性化工具资源的 Azure 门户中的"指标"窗格中），并调整定价层（在定价层窗格中），如果呼叫量预计增长超过所选定价层的阈值。

</details>

<details>
<summary><b>我在排名或奖励 API 上收到 5xx 错误。我该怎么办？</b></summary>

**答**：这些问题应该是透明的。 如果它们继续，请在"**支持 + 故障排除**"部分中为个性化工具资源选择"支持 + 故障排除"部分中**的新支持请求**来联系支持人员。

</details>

## <a name="learning-loop"></a>学习循环

<details>
<summary>
<b>如果没有个性化程序，学习循环不会达到与系统100%匹配。如何解决此问题？</b></summary>

**答**：你通过学习循环没有达到目标的原因：
* 使用 Rank API 调用发送的功能不足
* 发送的功能中的 Bug - 例如将非聚合要素数据（如时间戳）发送到 Rank API
* 具有循环处理的 Bug - 例如不向奖励 API 发送奖励数据的事件

要进行修复，您需要通过更改发送到循环的功能来更改处理，或者确保奖励是正确评估 Rank 响应的质量。

</details>

<details>
<summary>
<b>学习循环似乎没有学习。如何解决此问题？</b></summary>

**答**：在排名呼叫有效确定优先级之前，学习循环需要几千个奖励呼叫。

如果您不确定学习循环当前的行为情况，请运行[脱机评估](concepts-offline-evaluation.md)，并应用已更正的学习策略。

</details>

<details>
<summary><b>我不断获得所有项目相同的概率的排名结果。我如何知道个性化者正在学习？</b></summary>

**答**：当排名 API 结果刚刚启动且具有_空_模型时，或者当您重置个性化器循环时，您的模型仍在**模型更新频率**期间内，则个性化程序在 Rank API 结果中返回相同的概率。

当新的更新期间开始时，将使用更新的模型，您将看到概率更改。

</details>

<details>
<summary><b>学习循环是学习，但似乎不再学习，排名结果的质量不是那么好。我该怎么办？</b></summary>

**答**：
* 请确保在 Azure 门户中完成并应用了该个性化工具资源（学习循环）的一个评估。
* 确保所有奖励都通过奖励 API 发送并处理。

</details>


<details>
<summary><b>我如何知道学习循环正在定期更新，并用于对数据进行评分？</b></summary>

**答**：您可以在 Azure 门户的 **"模型和学习设置"** 页中找到模型上次更新的时间。 如果您看到旧的时间戳，则很可能是因为您没有发送"排名"和"奖励"呼叫。 如果服务没有传入数据，则不会更新学习。 如果您看到学习循环更新不够频繁，则可以编辑循环的**模型更新频率**。

</details>

## <a name="offline-evaluations"></a>脱机评估

<details>
<summary><b>脱机评估的功能重要性返回包含数百或数千个项目的长列表。发生了什么事？</b></summary>

**答**：这通常是由于时间戳、用户 ID 或其他一些细粒度功能发送。

</details>

<details>
<summary><b>我创建了一个离线评估，它几乎立即成功。为什么？我没有看到任何结果？</b></summary>

**答**：脱机评估使用该时间段内事件训练的模型数据。 如果在评估开始和结束时间之间的时间段内未发送任何数据，则数据将完成，而不会产生任何结果。 通过选择已发送到个人化器的事件的时间范围，提交新的脱机评估。

</details>


## <a name="learning-policy"></a>学习政策

<details>
<summary><b>如何导入学习策略？</b></summary>

**答**：详细了解[学习策略概念](concept-active-learning.md#understand-learning-policy-settings)[以及如何应用](how-to-manage-model.md)新的学习策略。 如果不想选择学习策略，可以使用[脱机评估](how-to-offline-evaluation.md)根据当前事件建议学习策略。

</details>

## <a name="security"></a>安全性

<details>
<summary><b>我的循环的 API 密钥已泄露。我能做什么？</b></summary>

**答**：您可以在将客户端交换为使用另一个密钥后重新生成一个密钥。 具有两个键允许您以延迟的方式传播密钥，而无需有任何停机时间。 作为安全措施，我们建议在常规周期中执行此操作。

</details>

## <a name="next-steps"></a>后续步骤

[配置模型更新频率](how-to-settings.md#model-update-frequency)