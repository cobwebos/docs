---
title: 配置个性化体验创建服务
description: 服务配置包括服务处理奖励的方式、服务的探索频率、重新训练模型的频率，以及存储的数据量。
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: ac31a9f907defeb44dbd4748a4395d3aec34d30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219353"
---
# <a name="configure-personalizer-learning-loop"></a>配置个性化学习循环

服务配置包括服务处理奖励的方式、服务的探索频率、重新训练模型的频率，以及存储的数据量。

在"**配置"** 页上，在 Azure 门户中为该个性化工具资源配置学习循环。

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="configure-rewards-for-the-feedback-loop"></a>为反馈循环配置奖励

为学习循环使用奖励配置服务。 对以下值的更改将重置当前个性化模型，并重新训练它与最后 2 天的数据。

> [!div class="mx-imgBorder"]
> ![配置反馈循环的奖励值](media/settings/configure-model-reward-settings.png)

|“值”|目的|
|--|--|
|奖励等待时间|设置个性化体验创建服务收集排名调用奖励值的时间长短，从发生排名调用的那一刻开始算起。 此值是通过询问："个人服务人员应该等待奖励呼叫多长时间？ 在此时间范围后抵达的任何奖励将被记录，但不用于学习。|
|默认奖励|如果个性化体验创建服务在“奖励等待时间”范围内未收到与排名调用关联的奖励调用，则个性化体验创建服务会分配默认奖励。 默认情况下，在大多数情况下，默认奖励为零 （0）。|
|奖励聚合|如果收到了同一排名 API 调用的多个奖励，则会使用此聚合方法：**sum** 或 **earliest**。 Earliest 拾取收到的最早评分，丢弃剩余的评分。 如果您希望在可能重复的呼叫之间获得唯一的奖励，则此功能非常有用。 |

更改这些值后，请确保选择 **"保存**"。

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>配置探索以允许学习循环适应

个性化可以通过探索替代方法而不是使用经过训练的模型预测来发现新模式并适应用户行为随时间的变化。 **"探索**"值确定使用探索应答的排名呼叫的百分比。

对此值的更改将重置当前个性化模型，并重新训练它与最后 2 天的数据。

![勘探值确定通过探索应答的排名呼叫的百分比](media/settings/configure-exploration-setting.png)

更改此值后，请确保选择 **"保存**"。

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>配置模型更新频率以进行模型培训

**模型更新频率**设置模型训练的频率。

|频率设置|目的|
|--|--|
|1 分钟|使用个人仪**调试**应用程序代码、执行演示或交互式测试机器学习方面时，一分钟的更新频率非常有用。|
|15 分钟|对于要**密切跟踪**用户行为变化的情况，高模型更新频率非常有用。 例如，涉及实时新闻、病毒内容或实时产品投标的站点。 在这些情况下，可以使用 15 分钟的频率。 |
|1 小时|对于大多数用例，更低的更新频率是有效的。|

![“模型更新频率”设置重新训练新个性化体验创建服务模型的频率。](media/settings/configure-model-update-frequency-settings-15-minutes.png)

更改此值后，请确保选择 **"保存**"。

## <a name="data-retention"></a>数据保留

“数据保留期”设置个性化体验创建服务要将数据日志保留多少天。**** 需要使用以往的数据日志来执行[脱机评估](concepts-offline-evaluation.md)。这些评估可以衡量个性化体验创建服务的有效性以及优化学习策略。

更改此值后，请确保选择 **"保存**"。

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>包括重置模型的设置

以下操作包括使用最近 2 天的数据立即对模型进行重新训练。

* 回报
* 浏览

要[清除](how-to-manage-model.md)所有数据，请使用 [模型和学习设置 ] 页面。

## <a name="next-steps"></a>后续步骤

[了解如何管理模型](how-to-manage-model.md)
