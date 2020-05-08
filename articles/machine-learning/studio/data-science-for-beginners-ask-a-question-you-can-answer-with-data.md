---
title: 提出数据可解答的问题
titleSuffix: ML Studio (classic) - Azure
description: 通过学习“适合初学者的数据科学”视频 3，了解如何将棘手的数据科学问题公式化。 包括分类和回归问题的比较。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 3f0a92b86a2c18863052249d401e70fa36e17a12
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930244"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>提出数据方面的可解答问题
## <a name="video-3-data-science-for-beginners-series"></a>视频 3：适合初学者的数据科学系列
通过学习“适合初学者的数据科学”视频 3，了解如何将数据科学难题公式化为一个问题。 此视频包括对分类和回归算法问题的比较。

若要充分利用此系列内容，请观看所有视频。 [转到视频列表](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>本系列中的其他视频
“适合初学者的数据科学”  对数据科学进行简要介绍，共五个短视频。

* 视频 1：[数据科学可解答的 5 个问题](data-science-for-beginners-the-5-questions-data-science-answers.md)（5 分 14 秒） 
* 视频 2：[数据是否可用于数据科学？](data-science-for-beginners-is-your-data-ready-for-data-science.md) （4 分 56 秒） 
* 视频 3：提出数据方面的可解答问题
* 视频 4：[使用简单的模型预测答案](data-science-for-beginners-predict-an-answer-with-a-simple-model.md)（7 分 42 秒） 
* 视频 5：[复制他人的工作以执行数据科学](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md)（3 分 18 秒） 

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>脚本：提出数据方面的可解答问题
欢迎观看“适合初学者的数据科学”系列中的第二个视频。  

此视频中提供了一些提示，可制定问题以便用数据解答。

如果先前观看了本系列以下两个视频，再观看本视频可能会更有收获：“数据科学可解答的 5 个问题”和“数据是否可用于数据科学？”

## <a name="ask-a-sharp-question"></a>提出尖锐问题
我们已经探讨过数据科学如何使用名称（也称为类别或标签）和数字预测某个问题的答案的过程。 但这个问题并不指代任何问题；它必须是一个清晰具体的问题。 

模糊问题不必用名称或数字来回答。 必须是清晰具体的问题。

想象一下，发现了一盏神灯，里面的精灵会如实地回答你问的任何问题。 但这个精灵非常淘气，它会试图给出模糊混淆的回答，以免受错误的惩罚。 需要提出一个无懈可击的问题，阻止它的上述行为，迫使它只能告诉你想要知道的答案。

如果问它一个模糊的问题，比如“我的股票接下来如何？”，精灵可能会回答，“价格会改变”。 这是一个真实的答案，但不是非常有帮助。

但是，如果提出一个清晰具体的问题，如“下周，我的股票售价如何？”，精灵便只能给出具体的回答，并预测售价。

## <a name="examples-of-your-answer-target-data"></a>答案示例：目标数据
制定问题后，请检查数据中是否有示例答案。

如果问题是“下周，我的股票售价如何？” 则必须确保数据包括股票价格历史记录。

如果问题是“车队中的那辆车会首先出现故障？” 则必须确保数据包括以前故障的相关信息。

![目标数据 - 示例答案 制定数据科学问题。](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

这些示例答案称为目标。 目标是我们试图预测的未来数据点的相关信息，可以是类别或数字。

如果没有任何目标数据，则需获取一些。 若没有目标数据，则无法回答问题。

## <a name="reformulate-your-question"></a>重新制定问题
有时可以改写问题，获得更有用的答案。

问题“此数据指向 A 还是 B？” 预测事物类别（或名称/标签）。 若要回答此问题，请使用分类算法  。

问题“多少？” 或“数量？” 可对量进行预测。 若要回答此问题，请使用回归算法  。

若要了解如何转换这些问题，让我们来看看这个问题：“读者最感兴趣的是哪个新闻故事？” 这要求从多种可能性中预测某个单一选择 - 换句话说，“这是 A 还是 B 或 C 或 D？” - 这将使用分类算法。

但是，如果将问题改写为“读者对该列表中每个故事的感兴趣度为多少？”，则这个问题可能更容易回答。 现在，可以给每篇文章一个数值分数，并可轻松确定最高分的文章。 这是将分类问题改写为回归问题或“多少”问题？

![重新制定问题。 分类问题与回归问题。](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

提问方式是确定使用哪种算法提供答案的线索。

会发现某些算法系列（如上述新闻故事示例）是密切相关的。 可以通过重新制定问题，从而使用能提供最佳答案的算法。

但最重要的是，提出的问题必须尖锐（即可以用数据回答）。 并确保拥有可解答该问题的适当数据。

我们已经探讨了一些有关如何提出可用数据回答的问题的基本原则。

务必观看 Microsoft Azure 机器学习工作室（经典）“适合初学者的数据科学”中的其他视频。

## <a name="next-steps"></a>后续步骤
* [使用机器学习工作室（经典）尝试第一个数据科学试验](create-experiment.md)
* [在 Microsoft Azure 上获取机器学习简介](/azure/machine-learning/overview-what-is-azure-ml)
