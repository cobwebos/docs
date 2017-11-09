---
title: "数据是否可用于数据科研？ 数据评估 - Azure 机器学习 | Microsoft Docs"
description: "了解数据可用于数据科学的 4 个条件。 “适合初学者的数据科学”的视频 2 中提供具体示例，帮助了解基本数据评估。"
keywords: "相关数据, 评估数据, 准备数据, 数据条件, 数据就绪情况"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: d502062c-da70-4b21-9054-0bfd9902612e
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2017
ms.author: cgronlun
ms.openlocfilehash: 9b5cf776981af0dff57195d5c7f1923b8d9a3862
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="is-your-data-ready-for-data-science"></a>数据是否可用于数据科研？
## <a name="video-2-data-science-for-beginners-series"></a>视频 2：适合初学者的数据科学系列
了解如何评估数据，确保其满足进行数据科学研究所需的基本条件。

若要充分利用此系列内容，请观看所有视频。 [转到视频列表](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>本系列中的其他视频
“适合初学者的数据科学”对数据科学进行简要介绍，共五个短视频。

* 视频 1：[数据科学可解答的 5 个问题](data-science-for-beginners-the-5-questions-data-science-answers.md)（5 分 14 秒）
* 视频 2：数据是否可用于数据科学？
* 视频 3：[提出可用数据回答的问题](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md)（4 分钟 17 秒）
* 视频 4：[使用简单的模型预测答案](data-science-for-beginners-predict-an-answer-with-a-simple-model.md)（7 分 42 秒）
* 视频 5：[复制他人的工作以研究数据科学](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md)（3 分 18 秒）

## <a name="transcript-is-your-data-ready-for-data-science"></a>脚本：数据是否可用于数据科学？
欢迎观看“数据是否可用于数据科学？” “适合初学者的数据科学”系列中的第二个视频。  

必须先提供一些优质原材料供数据科学分析，它才能提供你所需的答案。 就像做披萨一样，开始制作前准备的原料越好，最终的产品越好。 

## <a name="criteria-for-data"></a>数据条件
因此，对于数据科学，需要集中使用某些成分。

我们需要满足以下条件的数据：

* 相关
* 已连接
* 准确
* 足够使用

## <a name="is-your-data-relevant"></a>数据是否相关？
因此，第一要素是需要相关的数据。

![相关数据与不相关数据 - 评估数据](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

观察左侧的表。 我们在波士顿酒吧外遇到了七个人，测量了他们的血液酒精含量，红袜队在最后一场比赛中的击球率，以及最邻近的便利店中的牛奶价格。

此数据完全合法。 唯一的问题是它不相关。 这些数字之间没有明显的关系。 如果提供现在的牛奶价格和红袜队的击球率，无法猜出血液酒精含量。

现在，观察右侧的表。 这一次，我们测量每个人的体重，并计算他们喝了多少瓶酒。  现在，每行中的数字彼此相关。 如果提供体重和喝的玛格丽塔数量，便可猜测血液酒精含量。

## <a name="do-you-have-connected-data"></a>数据是否互连？
下一个要素是数据互连。

![互连数据互不相连的数据 - 数据条件、数据就绪情况](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

以下是一些关于汉堡包质量的相关数据：烤制温度、肉饼重量和以及在当地食物杂志中的等级。 但请注意左侧表中的空白处。

大多数数据集都缺少某些值。 这样的空白很常见，这一问题是可以解决的。 但是如果值缺失过多，数据看起来就像瑞士奶酪。

如果观察左侧的表，会发现有太多缺失数据，因此很难找出烤制温度和肉饼重量之间存在任何关系。 这是互不相连数据的示例。

但是，右侧的表数据很完整，是已连接数据的示例。

## <a name="is-your-data-accurate"></a>数据是否准确？
我们需要的下一要素是准确性。 以下是我们希望射箭击中的四个靶子。

![准确数据与错误数据 - 数据条件](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

观察右上方的靶子。 我们在靶心附近位置获得了一组紧密集聚的数据。 当然，这是准确数据。 但奇怪的是，在数据科学语言中，右下方靶子所呈现的数据也被视为准确数据。

如果标出这些箭支的中心点，会发现其非常靠近靶心。 箭支分散在靶子四周，因此认为其不准确。但它们都是以靶心为中心围绕分布，所以认为其是准确。

现在，观察左上方靶子。 在这个靶子上，箭支击中的点非常集中，形成一个紧密集聚的组。 他们是非常精确的，但因其中心远离靶心，所以他们所反应的数据是错误数据。 当然，左下方靶子中的箭支是错误且不精确的。 这名弓箭手需多加练习。

## <a name="do-you-have-enough-data-to-work-with"></a>数据是否足够使用？
最后，我们需要的第 4 项要素是充足的数据。

![是否具有充足的用于分析的数据？ 数据评估](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

将表中的每个数据点视为画作中的每一笔触。 如果只有少许几笔，则这幅画会很模糊，很难分辨它是什么。

添加的笔触越多，画的轮廓就越清晰。

当笔触只是勉强足够时，就只能作出宽泛的判断。 这是不是我想游览的某个地方？ 画面看起来很明亮，那边似乎是清澈的河水 - 是的，我是要去这里度假来着。

随着添加的数据增多，图片变得更为清晰，可以进行一些更具体的判断。 现在，我可以看到左河岸上的三个旅店。 我确实很喜欢近景中那家旅店的建筑特色。 我会住在那家旅店的三楼。

有了相关、互连、准确且充足的数据，就有了进行优质数据科学分析所需的全部要素。

务必观看 Microsoft Azure 机器学习的“适合初学者的数据科学”中的其他视频。

## <a name="next-steps"></a>后续步骤
* [使用机器学习工作室尝试第一个数据科学实验](create-experiment.md)
* [在 Microsoft Azure 上获取机器学习简介](what-is-machine-learning.md)
