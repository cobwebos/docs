---
title: 配置个性化体验创建服务
titleSuffix: Azure Cognitive Services
description: 服务配置包括服务处理奖励的方式、服务的探索频率、重新训练模型的频率，以及存储的数据量。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: d20f81bf7db2e098f2bca674c5540bc067577f30
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833911"
---
# <a name="configure-personalizer"></a>配置个性化体验创建服务

服务配置包括服务处理奖励的方式、服务的探索频率、重新训练模型的频率，以及存储的数据量。

## <a name="create-personalizer-resource"></a>创建个性化体验创建服务资源

为每个反馈循环创建一个个性化体验创建服务资源。

1. 登录到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。 前面的链接会将你带到个性化体验创建服务的“创建”页。
1. 输入服务名称，选择订阅、位置、定价层和资源组。
1. 选择确认，然后选择“创建”。

<a name="configure-service-settings-in-the-azure-portal"></a>

## <a name="configure-service-in-the-azure-portal"></a>在 Azure 门户中配置服务

1. 登录 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。
1. 找到你的个性化体验创建服务资源。
1. 在 "**资源管理**" 部分，选择 "**配置**"。

    在离开 Azure 门户之前，请从“密钥”页复制某个资源密钥。 需要该密钥才能使用[个性化体验创建服务 SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer)。

<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

### <a name="configure-reward-for-the-feedback-loop-based-on-use-case"></a>基于用例配置反馈循环的奖励

为你的反馈循环使用奖励配置服务。 对以下值所做的更改将重置当前 Personalizer 模型，并在过去2天的数据中重新训练：

![配置反馈循环的奖励值](media/settings/configure-model-reward-settings.png)

|值|用途|
|--|--|
|奖励等待时间|设置个性化体验创建服务收集排名调用奖励值的时间长短，从发生排名调用的那一刻开始算起。 此值的设置方法是： "应 Personalizer 等待回报调用的时间长度？" 在此时间范围后抵达的任何奖励将被记录，但不用于学习。|
|默认奖励|如果个性化体验创建服务在“奖励等待时间”范围内未收到与排名调用关联的奖励调用，则个性化体验创建服务会分配默认奖励。 默认情况下（并且是大多数情况下），默认奖励为零。|
|奖励聚合|如果收到了同一排名 API 调用的多个奖励，则会使用此聚合方法：**sum** 或 **earliest**。 Earliest 拾取收到的最早评分，丢弃剩余的评分。 如果你希望在可能重复的调用之间保持唯一的奖励，则此方法很有用。 |

更改这些值后，请确保选择 "**保存**"。

### <a name="configure-exploration"></a>配置探索

个性化功能可以发现新的模式，并通过探索备选项来不断适应用户的行为变化。 **探索**值决定了通过浏览应答的排名调用的百分比。

对此值所做的更改将重置当前 Personalizer 模型，并在过去2天的数据中重新训练该模型。

![探索值决定了通过探索回答了多少排名调用百分比](media/settings/configure-exploration-setting.png)

更改此值后，请确保选择 "**保存**"。

### <a name="model-update-frequency"></a>模型更新频率

从每个活动事件的奖励 API 调用训练得来的最新模型不会被个性化体验创建服务排名调用自动使用。 **模型更新频率**设置排名调用所使用的模型的更新频率。

如果你希望密切跟踪用户行为变化，则高模型更新频率非常有用。 例如，涉及实时新闻、病毒内容或实时产品投标的站点。 在这些情况下，可以使用 15 分钟的频率。 对于大多数用例，更低的更新频率是有效的。 在使用个性化体验创建服务调试应用程序代码、进行演示或以交互方式测试机器学习方面时，一分钟的更新频率非常有用。

![“模型更新频率”设置重新训练新个性化体验创建服务模型的频率。](media/settings/configure-model-update-frequency-settings-15-minutes.png)

更改此值后，请确保选择 "**保存**"。

### <a name="data-retention"></a>数据保留

“数据保留期”设置个性化体验创建服务要将数据日志保留多少天。 需要使用以往的数据日志来执行[脱机评估](concepts-offline-evaluation.md)。这些评估可以衡量个性化体验创建服务的有效性以及优化学习策略。

更改此值后，请确保选择 "**保存**"。

## <a name="export-the-personalizer-model"></a>导出个性化体验创建服务模型

从 "资源管理" 的 "**模型和学习设置**" 部分中，查看 "模型创建" 和 "上次更新日期" 并导出当前模型。 可以使用 Azure 门户或个性化体验创建服务 API 导出模型文件以进行存档。

![导出当前个性化体验创建服务模型](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>清除学习循环的数据

1. 在 Azure 门户的 Personalizer 资源的 "**模型和学习设置**" 页上，选择 "**清除数据**"。
1. 为了清除所有数据，并将学习循环重置为原始状态，请选择 "所有3个复选框"。

    ![在 Azure 门户中，清除 Personalizer 资源中的数据。](./media/settings/clear-data-from-personalizer-resource.png)

    |值|用途|
    |--|--|
    |记录个性化和奖励数据。|此日志记录数据用于脱机评估。 如果要重置资源，请清除数据。|
    |重置 Personalizer 模型。|此模型将在每次重新训练时进行更改。 此训练频率在**配置**页上的 "**上载模型频率**" 中指定。 |
    |将学习策略设置为默认值。|如果在脱机评估过程中更改了学习策略，则会将其重置为原始学习策略。|

1. 选择 "**清除所选数据**" 以开始清除过程。 状态在 Azure 通知的右上方导航中报告。

## <a name="next-steps"></a>后续步骤

[了解如何管理学习策略](how-to-learning-policy.md)
