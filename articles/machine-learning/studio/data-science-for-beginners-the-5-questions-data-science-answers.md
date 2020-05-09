---
title: 适合初学者的数据科学
titleSuffix: ML Studio (classic) - Azure
description: 适合初学者的数据科学以 5 个较短的视频讲授基本概念，从 5 个数据科学可解答的问题开始。 来自 Azure 机器学习。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 350363641056802e09eea90c85b4ccf8e33d9051
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930142"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>适合初学者的数据科学视频 1：5 个数据科学可解答的问题
通过顶级数据科学家制作的“适合初学者的数据科学”  的五个简短视频，快速了解数据科学。 这些视频介绍基础知识，但无论是对研究数据科学感兴趣还是与数据科学家合作，这些内容都非常有用。

第一个视频是有关数据科学可回答的这类问题。 若要充分利用此系列内容，请观看所有视频。 [转到视频列表](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>本系列中的其他视频
“适合初学者的数据科学”  是对数据科学的简要介绍，学习时间共约 25 分钟。 观看所有五个视频：

* 视频 1：5 个数据科学可解答的问题
* 视频 2：[数据是否可用于数据科学？](data-science-for-beginners-is-your-data-ready-for-data-science.md) （4 分 56 秒） 
* 视频 3：[提出可用数据回答的问题](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md)（4 分钟 17 秒） 
* 视频 4：[使用简单的模型预测答案](data-science-for-beginners-predict-an-answer-with-a-simple-model.md)（7 分 42 秒） 
* 视频 5：[复制他人的工作以执行数据科学](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md)（3 分 18 秒） 

## <a name="transcript-the-5-questions-data-science-answers"></a>脚本：5 个数据科学可解答的问题
好！ 欢迎观看“适合初学者的数据科学”  系列视频。

数据科学知识的广博令人生畏，因此这里只简单介绍一些基础知识，不会涉及任何公式或计算机编程专业术语。

在第一个视频中，我们将讨论“5 个数据科学可解答的问题”。

数据科学使用数字和名称（也称为类别或标签）预测问题的答案。

这可能会让人感到惊讶，但数据科学只可解答以下五种问题  ：

* 这是 A 还是 B？
* 这是否很奇怪？
* 多少？
* 组织方式为何？
* 接下来该怎样做？

以上每个问题都由单独系列的机器学习方法（称为算法）回答。

将算法视为配方，数据视为成分可帮助理解。 算法告知如何组合以及混合数据以获取答案。 计算机如同搅拌机。 它们可快速完成算法的大部分繁琐工作。

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>问题 1：“这是 A 还是 B？”使用分类算法
让我们从这个问题开始：这是 A 还是 B？

![分类算法：这是 A 还是 B？](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

此系列算法称为双类分类。

对于任何仅有两种可能答案的问题很有用。

例如：

* 此轮胎是否会在下一 1,000 英里中出现故障：是或否？
* 以下哪种方案可吸引更多顾客：5 美元优惠券或 25% 折扣？

此问题还可进行改述，来包括两个以上的选项：这是 A、B、C 还是 D，等？  这称为多类分类，当有多个或数千个可能的答案时很有用。 多类分类选择可能性最大的一个答案。

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>问题 2：“这是否很奇怪？”使用异常检测算法
下一个数据科学可以回答的问题是：这是否很奇怪？ 此问题通过称为异常检测的算法系列进行回答。

![异常检测算法：这是否很奇怪？](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

如果有信用卡，则已从异常情况检测中获益。 信用卡公司分析购买模式，使他们可提醒用户可能的欺诈行为。 “异常”费用可能是在一家通常不会去购物的商店购物时，或购买非常昂贵的物品时所产生的费用。

此问题在很多方面都很有用。 例如：

* 如果汽车上配有压力表，可能会想知道：此压力表读数是否正常？
* 如果监视的是 internet，则需要知道：此邮件是否是来自 internet 的典型消息？

异常检测标志意外或异常事件或行为。 它会提供在何处查找问题的线索。

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>问题 3：“多少？”使用回归算法
机器学习还可以预测“多少？”这一问题的答案。 回答此问题的算法系列称为回归算法。

![回归算法：多少？](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

回归算法进行数字预测，例如：

* 下周二的气温是多少？  
* 我第四季度销售额有多少？

它们可帮助回答任何寻求数字答案的问题。

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>问题 4：“组织方式为何？”使用聚类分析算法
最后两个问题更高级一点。

有时希望了解数据集的结构 - 组织方式为何？ 对于此问题，你没有了解其结果的示例。

可通过多种方法梳理出数据结构。 其中一种方法就是聚类分析。 为方便解释，该方法将数据分成多个自然“群”。 使用聚类分析，不会存在正确答案。

![聚类分析算法：组织方式为何？](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

聚类分析问题的常见示例有：

* 哪些观众喜欢同类型的电影？
* 哪些打印机型号出现故障的方式相同？

通过了解数据的组织方式，可以更好地了解并预测行为和事件。  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>问题 5：“现在应该做什么？”使用强化学习算法
最后一个问题 – 现在应该做什么？ \- 使用称为强化学习的算法系列。

强化学习的灵感来自于老鼠和人类的大脑对惩罚和奖励的反应。 这些算法从结果中学习，并决定下一步操作。

通常，强化学习适用于自动系统，这些自动系统需要在没有人工指导的情况下做出大量小决策。

![强化学习算法：接下来该怎样做？](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

此算法总是用于回答此类问题：（通常指计算机或机器人）应采取何种操作。 示例如下：

* 如果我是房子的温度控制系统：调整温度还是保持原有温度？  
* 如果我是自动驾驶汽车：遇到黄灯时，是刹车还是加速？  
* 对于机器人吸尘器：继续吸尘或返回充电站？

强化学习算法在执行过程中收集数据，从试验和错误中学习。

这就是数据科学可以回答的 5 个问题。

## <a name="next-steps"></a>后续步骤
* [使用机器学习工作室（经典）尝试第一个数据科学试验](create-experiment.md)
* [在 Microsoft Azure 上获取机器学习简介](/azure/machine-learning/overview-what-is-azure-ml)
