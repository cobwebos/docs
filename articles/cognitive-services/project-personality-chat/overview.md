---
title: 什么是项目个性聊天？
titlesuffix: Azure Cognitive Services
description: 本文概述 Azure 项目个性化聊天 - 用于增强机器人聊天功能的基于云的 API。
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: overview
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: 053d43d0f17df2008106bda38318615e60778935
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207507"
---
# <a name="what-is-project-personality-chat"></a>什么是项目个性聊天？

项目个性化聊天能够以独特的个性化处理简单的沟通，以此增强机器人的聊天能力。 个性化聊天使用意向分类器来识别一般的简单沟通意向，并生成与个性化一致的响应。 机器人基于意向和置信度评分从策划好的编辑库中选择最佳响应，或者使用深度神经网络 (DNN) 实时生成响应。 可以从三个默认角色中选择。 角色模型返回与所选个性化最相似的响应。

可以使用适用于一般简单沟通查询的自定义编辑集。 使用 Microsoft Bot Framework SDK 可以轻松集成该模型。 此 SDK 可以节省从头开始编写代码所需的时间和成本。

## <a name="getting-started-with-project-personality-chat"></a>项目个性化聊天入门

可以访问项目个性化聊天实验室页并与可用的演示聊天，还可以在服务推出时请求预先访问权限。
目前，还可以通过 Microsoft Bot Framework SDK 将仅限可自定义的编辑库集成到机器人中。 <br>
[示例：将个性化聊天集成到机器人中](https://github.com/Microsoft/BotBuilder-PersonalityChat/) <br>
[试用个性化聊天库](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp)

## <a name="generating-responses-using-neural-networks"></a>使用神经网络生成响应

个性化聊天使用深度学习模型来学习常规聊天模式并生成响应。 响应生成模型是一个递归神经网络 (RNN)。 此模型已基于数百万个聊天进行训练，能够理解和学习人机交互模式。 然后使用学习到的句子结构模式构建新查询并生成响应。 生成此新响应时，该模型将搜索单词的“写入词汇”，并选取最适合响应的前 n 个单词。 它使用集束搜索继续为生成的前几个单词搜索第二好的 n 个单词。 当模型选取“句子结束”(EOS) 单词时，即认为响应已完成。 获取所有响应后，它会基于完整响应的概率评分选择 n 个最佳响应。

该模型通过学习生成具有适当“结构”的区分上下文的相应轮次。 例如，从类似于“你现在想要交谈吗？”的问题中， 大致可以推理出合理回复的结构：回复也许以短语“是”或“不是”开头，而代词可能是“我”。 如果回复为“不是”，则很有可能会附带礼貌性的解释，等等。

系统会尝试在语言模型中学习聊天的基本结构。 此结构应该允许主题、个性化等外部约束对响应造成一定的影响。  由于这些约束是从广泛的模式中学习到的，因此适合（但不限于）用于简单沟通的应用程序。

![用于生成响应的序列间模型](./media/overview/sequence-to-sequence-model.png)

## <a name="personality-modeling"></a>个性化建模

 使用任何数据驱动型聊天模型时，持续提供一致的“个性化”会有一定的难度。 “角色”定义为在聊天交互过程中遇到的人物。 可将角色视为身份、语言行为和交互样式的构成元素。 当前版本的个性化聊天侧重于语言行为和交互样式。

此模型以向量或嵌入元素的形式表示每个讲话人。 它会将讲话人讲出的、会影响其响应内容和样式的信息进行编码。 然后，该模型基于不同讲话人提供的聊天内容来学习讲话人的陈述。 具有类似响应的讲话人往往具有类似的嵌入元素，在向量空间中占据邻近的位置。 这样，位于向量空间中邻近位置的讲话人的训练数据有助于提高讲话人模型的通用化功能。 该模型能够有效地将向量空间中具有类似陈述的讲话人聚集起来，以形成具有“角色 ID”的角色聚类。

对于默认角色，将使用属性和策划的响应来查找最匹配的讲话人聚类。 然后，将此聚类选作每个默认个性化的角色 ID。 对于采用一组机器人/品牌响应的任何个性化类型，可能会发生连续自定义。 然后生成聊天，以便准确模拟个人的角色，例如语言响应行为和其他主要特征。

![使用讲话人聚类进行角色建模](./media/overview/persona-modeling.png)

## <a name="small-talk-intent-understanding"></a>简单沟通意向理解

个性化聊天提供意向分类器来确保为简单沟通意向做出响应。 这些分类器不会妨碍任务或信息查询。 高级聊天分类器可以确定查询是属于简单沟通还是闲聊意向。 它使用基于词法和语义的分类器并结合其评分来做出决定。 将使用聊天数据（作为积极意向样本）和搜索/任务查询（作为消极意向样本）来训练这些意向。

其他子意向分类器用于识别简单沟通的子类，以约束服务响应的简单沟通类型，例如：问候、称赞、观点，等等。 这些使用卷积深度结构语义模型 (CDSSM) 的深度学习分类器将所有查询转换为向量。 它们是使用子意向的数万个策划查询训练的。 然后，分类器通过在向量空间中查找最匹配项，将某个查询与现有的已识别意向类进行匹配。

基于 Zo 等智能代理的知识构建，已经实施了一系列控制措施，以帮助防止不宜的响应。 默认情况下，项目个性聊天设置为仅响应已识别的用户意图。 你可能想要测试项目个性聊天是否适合你的情况。 如果你发现任何需要进一步培训的内容，欢迎你提供反馈。
