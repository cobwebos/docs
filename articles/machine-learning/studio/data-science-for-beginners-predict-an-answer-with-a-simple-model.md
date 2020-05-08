---
title: 使用回归模型预测答案
titleSuffix: ML Studio (classic) - Azure
description: “适合初学者的数据科学”视频 4 中的“如何创建简单的回归模型来预测价格”。 包括具有目标数据的线性回归。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: ac9f4262d614b1e413f4283d3c459e60fc6634e2
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930227"
---
# <a name="predict-an-answer-with-a-simple-model"></a>使用简单的模型预测答案
## <a name="video-4-data-science-for-beginners-series"></a>视频 4：适合初学者的数据科学系列
在“适合初学者的数据科学”视频 4 中学习“如何创建简单的回归模型来预测钻石价格”。 我们将绘制一个具有目标数据的回归模型。

若要充分利用此系列内容，请观看所有视频。 [转到视频列表](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>本系列中的其他视频
“适合初学者的数据科学”  对数据科学进行简要介绍，共五个短视频。

* 视频 1：[数据科学可解答的 5 个问题](data-science-for-beginners-the-5-questions-data-science-answers.md)（5 分 14 秒） 
* 视频 2：[数据是否可用于数据科学？](data-science-for-beginners-is-your-data-ready-for-data-science.md) （4 分 56 秒） 
* 视频 3：[提出可用数据回答的问题](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md)（4 分钟 17 秒） 
* 视频 4：使用简单的模型预测答案
* 视频 5：[复制他人的工作以执行数据科学](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md)（3 分 18 秒） 

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>脚本：使用简单的模型预测答案
欢迎观看“适合初学者的数据科学”系列中的第四个视频。 在此视频中，我们将生成一个简单的模型并进行预测。

“模型”  是数据的简化形式。 我将解释这句话的意思。

## <a name="collect-relevant-accurate-connected-enough-data"></a>收集相关的、准确的、连续的和足够的数据
假设我要购买钻石。 我有一个从我祖母那继承的戒指，可放置 1.35 克拉的钻石，我想了解要花多少钱。 我带着笔记本和笔来到珠宝店，并记下当场所有钻石的价格和重量（以克拉为单位）。 从第一颗钻石开始 - 1.01 克拉，$7,366。

现在我继续看，并记下商店中所有钻石的这些信息。

![钻石数据列](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

请注意，我们的列表有两列。 每列有不同的属性 - 克拉数和价格 - 每一行则代表单个钻石的单个数据点。

我们实际上已在此处创建了一个较小数据集 - 一个表。 请注意，它满足我们的质量标准：

* 数据是“相关的”  - 重量肯定与价格相关
* 数据是“准确的”  - 我们核实了两次记下的价格
* 数据是“连续的”  - 这两列中均没有空格
* 而且可以看到，现在有“足够的”  数据来回答我们的问题

## <a name="ask-a-sharp-question"></a>提出尖锐问题
现在我们直接提出问题：“购买 1.35 克拉钻石需要多少钱？”

列表中没有 1.35 克拉的钻石，因此需要使用其他数据得出这一问题的答案。

## <a name="plot-the-existing-data"></a>绘制现有数据
我们首先绘制水平数字线（叫做轴），用图表表示重量。 重量的范围是 0 到 2，因此我们绘制一条包括此范围的线，并在每半克拉处做标记。

接下来，绘制纵轴记录价格，并将其与水平重量轴联系起来。 纵轴以美元为单位。 现在我们有了一组坐标轴。

![重量轴和价格轴](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

接下来获取数据并将其转换为散点图  。 这是一种可视化数字数据集的好方法。

对于第一个数据点，我们盯住 1.01 克拉的纵向线。 然后盯住 $7,366 的水平线。 在它们相交的位置描点。 这代表第一颗钻石。

现在为列表中的每颗钻石执行此操作。 完成后即可得到：多个点，每个点对应一颗钻石。

![散点图](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>连接数据点绘制模型
如果现在看这些点和重合的部分，这个集合看起来就像一条粗大、模糊的线。 我们可以使用记号笔绘制一条直线。

绘制完这条线便创建了一个“模型”  。 将它视为真实的模型并制作简化的动画版本。 现在的卡通版是错误的，因为线没有连接所有数据点。 但这是有用的简化方法。

![线性回归线](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

所有点未准确落在线上是可以接受的。 数据科学家将此解释为：有模型，就会有线 - 因此每个点都有一些与之关联的干扰  或方差  。 存在潜在的完美关系，同时也存在增加干扰和不确定性的真实世界。

因为我们正在尝试回答“多少钱？”  的问题，因此这就是一种“回归”  。 而我们使用的是直线，因此它又是“线性回归”  。

## <a name="use-the-model-to-find-the-answer"></a>使用模型找出答案
现在有了模型，我们向它提问：1.35 克拉的钻石要花多少钱？

为了回答问题，我们盯住 1.35 克拉并绘制一条纵向线。 当它与模型线相交后，我们盯住这一点到价格轴的水平线。 刚好为 10,000。 哇！ 这就是答案：1.35 克拉的钻石大约需要 $10,000。

![在模型上找出答案](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>创建一个置信区间
我们肯定想知道这个预测有多准确。 知道 1.35 克拉钻石的价格非常接近、还是远高于或远低于 $10,000 非常有用。 为了得出此答案，我们在回归线附近绘制一个信封形图形，覆盖大多数点。 这个信封称为“置信区间”  ：根据过去的经验，我们可以信任此信封形区域中的价格。 我们可以再绘制两条水平线：使 1.35 克拉线与此信封形区域的最低点和最高点相交。

![置信区间](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

现在可以从置信区间获取一些内容：我们可以自信地说 1.35 克拉钻石的价格约为 $10,000 - 但可能低至 $8,000 或高达 $12,000。

## <a name="were-done-with-no-math-or-computers"></a>完成，而且没有使用数学计算或计算机
我们完成了需要聘请数据科学家才能完成的工作，完成方法仅是通过绘制：

* 提出可使用数据回答的问题
* 使用线性回归  构建模型 
* 进行预测  ，并借助置信区间完成 

而且没有使用数学计算或计算机。

现在假设我们有更多信息，例如...

* 钻石的切割
* 颜色差异（钻石的颜色与白色的接近程度）
* 钻石中杂质量

...那么我们会有更多列。 在这种情况下，数学计算就很有用。 如果有两个以上的列，则很难在纸上描点。 数学计算能使线或平面与数据很好地对应。

此外，如果不是只有少量钻石，而是有两千颗或两百万颗，那么使用计算机能更快完成此工作。

我们今天讨论了如何执行线性回归并使用数据进行了预测。

务必观看 Microsoft Azure 机器学习工作室（经典）“适合初学者的数据科学”中的其他视频。

## <a name="next-steps"></a>后续步骤
* [使用机器学习工作室（经典）尝试第一个数据科学试验](create-experiment.md)
* [在 Microsoft Azure 上获取机器学习简介](/azure/machine-learning/overview-what-is-azure-ml)
