---
title: 个性化体验创建服务的工作原理 - 个性化体验创建服务
description: 个性化器_循环_使用机器学习构建模型，预测内容的顶级操作。 模型仅针对您通过"排名"和"奖励"呼叫发送给它的数据进行专门培训。
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 836c207213ac52a60e27da6fc957418187059023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623741"
---
# <a name="how-personalizer-works"></a>个性化体验创建服务的工作原理

个性化资源，您的_学习循环_，使用机器学习来构建模型，预测内容的顶级操作。 模型仅针对您通过 **"排名**"和 **"奖励**"呼叫发送给它的数据进行专门培训。 每个循环都完全独立。

## <a name="rank-and-reward-apis-impact-the-model"></a>排名和奖励 API 影响模型

向 Rank API 发送_具有功能_和_上下文功能_的操作。 **排名** API 将决定使用以下操作之一：

* _利用 ：_ 当前模型，根据过去的数据决定最佳操作。
* _浏览_：选择其他操作而不是顶部操作。 在 Azure 门户中为个性化工具资源[配置此百分比](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt)。

确定奖励分数并将该分数发送到奖励 API。 **奖励** API：

* 通过记录每个排名调用的特征和奖励评分来收集用于训练模型的数据。
* 使用该数据根据_学习策略_中指定的配置更新模型。

## <a name="your-system-calling-personalizer"></a>您的系统调用个性化器

下图显示了排名和奖励调用的体系结构流程图：

![alt 文本](./media/how-personalizer-works/personalization-how-it-works.png "个性化的工作原理")

1. 向 Rank API 发送_具有功能_和_上下文功能_的操作。

    * 个性化程序决定是利用当前模型还是探索模型的新选择。
    * 排名结果将发送到事件中心。
1. 排名将作为_奖励行动 ID_返回到您的系统。
    您的系统会显示该内容，并根据您自己的业务规则确定奖励分数。
1. 您的系统将奖励分数返回到学习循环。
    * 当个性化体验创建服务收到奖励时，会将奖励发送到事件中心。
    * 排名和奖励相关联。
    * 根据关联结果更新 AI 模型。
    * 使用新模型更新推理引擎。

## <a name="personalizer-retrains-your-model"></a>个性化程序重新训练您的模型

个性化程序根据 Azure 门户中的个性化程序资源上的**模型频率更新**设置重新训练模型。

个性化程序使用当前保留的所有数据，具体取决于 Azure 门户中个性化程序资源上的 **"数据保留**"设置。

## <a name="research-behind-personalizer"></a>个性化体验创建服务幕后的研究

个性化体验创建服务基于[强化学习](concepts-reinforcement-learning.md)领域的前沿科研成果，包括 Microsoft 研究部门的论文、研究活动和持续探索。

## <a name="next-steps"></a>后续步骤

了解个性化程序[的顶级方案](where-can-you-use-personalizer.md)