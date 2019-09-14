---
title: 配置设置-Personalizer
titleSuffix: Azure Cognitive Services
description: 服务配置包括服务处理奖励的方式、服务的探索频率、重新训练模型的频率，以及存储的数据量。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 376c2efbf3269092d0534870108ef6d753f8743e
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962513"
---
# <a name="personalizer-settings"></a>个性化体验创建服务设置

服务配置包括服务处理奖励的方式、服务的探索频率、重新训练模型的频率，以及存储的数据量。

## <a name="create-personalizer-resource"></a>创建个性化体验创建服务资源

为每个反馈循环创建一个个性化体验创建服务资源。 

1. 登录到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。 前面的链接会将你带到个性化体验创建服务的“创建”页。 
1. 输入服务名称，选择订阅、位置、定价层和资源组。
1. 选择确认，然后选择“创建”。

## <a name="configure-service-settings-in-the-azure-portal"></a>在 Azure 门户中配置服务设置

1. 登录到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。
1. 找到你的个性化体验创建服务资源。 
1. 在“资源管理”部分选择“设置”。

    在离开 Azure 门户之前，请从“密钥”页复制某个资源密钥。 需要该密钥才能使用[个性化体验创建服务 SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer)。

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>根据用例配置反馈循环的奖励设置

配置服务的设置，使反馈循环使用奖励。 更改以下设置会重置当前个性化体验创建服务模型，并使用过去 2 天的数据重新训练模型：

![配置反馈循环的奖励设置](media/settings/configure-model-reward-settings.png)

|设置|用途|
|--|--|
|奖励等待时间|设置个性化体验创建服务收集排名调用奖励值的时间长短，从发生排名调用的那一刻开始算起。 此值是通过提出以下问题设置的：“个性化体验创建服务要等待奖励调用多长时间？” 在此时间范围后抵达的任何奖励将被记录，但不用于学习。|
|默认奖励|如果个性化体验创建服务在“奖励等待时间”范围内未收到与排名调用关联的奖励调用，则个性化体验创建服务会分配默认奖励。 默认情况下（并且是大多数情况下），默认奖励为零。|
|奖励聚合|如果收到了同一排名 API 调用的多个奖励，则会使用此聚合方法：**sum** 或 **earliest**。 Earliest 拾取收到的最早评分，丢弃剩余的评分。 如果你希望在可能重复的调用之间保持唯一的奖励，则此方法很有用。 |

更改这些设置后，请务必选择“保存”。

### <a name="exploration-setting"></a>探索设置 

个性化功能可以发现新的模式，并通过探索备选项来不断适应用户的行为变化。 “探索”设置确定要使用探索应答的排名调用百分比。 

更改此设置会重置当前个性化体验创建服务模型，并使用过去 2 天的数据重新训练模型。

![探索设置确定要使用探索应答的排名调用百分比](media/settings/configure-exploration-setting.png)

更改此设置后，请务必选择“保存”。

### <a name="model-update-frequency"></a>模型更新频率

从每个活动事件的奖励 API 调用训练得来的最新模型不会被个性化体验创建服务排名调用自动使用。 **模型更新频率**设置排名调用所使用的模型的更新频率。 

如果你希望密切跟踪用户行为变化，则高模型更新频率非常有用。 例如，涉及实时新闻、病毒内容或实时产品投标的站点。 在这些情况下，可以使用 15 分钟的频率。 对于大多数用例，更低的更新频率是有效的。 在使用个性化体验创建服务调试应用程序代码、进行演示或以交互方式测试机器学习方面时，一分钟的更新频率非常有用。

![“模型更新频率”设置重新训练新个性化体验创建服务模型的频率。](media/settings/configure-model-update-frequency-settings-15-minutes.png)

更改此设置后，请务必选择“保存”。

### <a name="data-retention"></a>数据保留

“数据保留期”设置个性化体验创建服务要将数据日志保留多少天。 需要使用以往的数据日志来执行[脱机评估](concepts-offline-evaluation.md)。这些评估可以衡量个性化体验创建服务的有效性以及优化学习策略。

更改此设置后，请务必选择“保存”。

## <a name="export-the-personalizer-model"></a>导出个性化体验创建服务模型

在“模型和策略”的“资源管理”部分，查看模型创建日期和上次更新日期，然后导出当前模型。 可以使用 Azure 门户或个性化体验创建服务 API 导出模型文件以进行存档。 

![导出当前个性化体验创建服务模型](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>导入和导出学习策略

在“模型和策略”的“资源管理”部分，导入新的学习策略或导出当前学习策略。
你可以从以前的导出获取学习策略文件，或下载在脱机评估期间发现的优化策略。 对这些文件进行手动更改将影响机器学习性能和脱机评估的准确性，并且 Microsoft 不能保证机器学习和评估的准确性，也不能为手动编辑的策略导致的服务异常。

## <a name="next-steps"></a>后续步骤

<!--
[How to use the Personalizer container](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->
[了解适用地区](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
