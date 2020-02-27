---
title: 个性化体验创建服务的工作原理 - 个性化体验创建服务
description: Personalizer_循环_使用机器学习来构建模型，以便预测您的内容的最大操作。 该模型仅针对您向其发送了排名和奖励呼叫的数据进行定型。
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 836c207213ac52a60e27da6fc957418187059023
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623741"
---
# <a name="how-personalizer-works"></a>个性化体验创建服务的工作原理

Personalizer 资源是_学习循环_，它使用机器学习来构建模型，以便预测内容的最高操作。 该模型仅针对您向其发送了**排名**和**奖励**呼叫的数据进行定型。 每个循环都是完全独立的。

## <a name="rank-and-reward-apis-impact-the-model"></a>排名和奖励 Api 影响模型

向排名 API 发送_具有功能_和_上下文功能_的操作。 **排名** API 将决定使用以下操作之一：

* _利用_：当前模型根据过去的数据确定最佳操作。
* _浏览_：选择其他操作，而不是操作顶部操作。 在 Azure 门户中配置 Personalizer 资源的[此百分比](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt)。

确定奖励分数并将评分发送到奖励 API。 **奖励** API：

* 通过记录每个排名调用的特征和奖励评分来收集用于训练模型的数据。
* 使用该数据根据_学习策略_中指定的配置更新模型。

## <a name="your-system-calling-personalizer"></a>你的系统调用 Personalizer

下图显示了排名和奖励调用的体系结构流程图：

![替换文字](./media/how-personalizer-works/personalization-how-it-works.png "个性化设置的工作原理")

1. 向排名 API 发送_具有功能_和_上下文功能_的操作。

    * Personalizer 决定是利用当前模型还是浏览模型的新选项。
    * 排名结果将发送到事件中心。
1. 顶部排名作为_奖励操作 ID_返回到系统。
    系统会提供该内容，并根据自己的业务规则确定奖励分数。
1. 系统会将奖励评分返回到学习循环中。
    * 当个性化体验创建服务收到奖励时，会将奖励发送到事件中心。
    * 排名和奖励相关联。
    * 根据关联结果更新 AI 模型。
    * 使用新模型更新推理引擎。

## <a name="personalizer-retrains-your-model"></a>Personalizer retrains 模型

Personalizer retrains Azure 门户中的 Personalizer 资源上的**模型频率更新**设置。

Personalizer 使用当前保留的所有数据，这些数据基于 "Azure 门户中 Personalizer 资源的天数内的**数据保留**设置。

## <a name="research-behind-personalizer"></a>个性化体验创建服务幕后的研究

个性化体验创建服务基于[强化学习](concepts-reinforcement-learning.md)领域的前沿科研成果，包括 Microsoft 研究部门的论文、研究活动和持续探索。

## <a name="next-steps"></a>后续步骤

了解 Personalizer 的[热门方案](where-can-you-use-personalizer.md)