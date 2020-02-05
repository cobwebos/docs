---
title: 设计器：预测航班延误的示例
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习设计器生成分类器并通过自定义 R 代码预测航班延误。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: d459350572d68cc5dcbbfd56933483404b94f918
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963269"
---
# <a name="build-a-classifier--use-r-to-predict-flight-delays-with-azure-machine-learning-designer"></a>使用 Azure 机器学习设计器生成分类器并通过 R 预测航班延误

**设计器（预览版）示例 6**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

此管道使用历史航班和天气数据来预测计划的民航航班是否会延误 15 分钟以上。 可以通过预测以下两个类，将此问题作为分类问题来解决：延误或准时。

下面是本示例的最终管道图形：

[![管道图形](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png)](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png#lightbox)

## <a name="prerequisites"></a>必备条件

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 单击示例 6 将其打开。

## <a name="get-the-data"></a>获取数据

本示例使用“航班延误数据”数据集。  它是美国交通部提供的 TranStats 数据集合的一部分。 该数据集包含 2013 年 4 月到 10 月的航班延误信息。 该数据集已按如下所述进行预处理：

* 经过筛选，包括美国大陆最繁忙的 70 个机场。
* 为延误 15 分钟以上的已取消航班重新添加了标签。
* 筛选出了改道航班。
* 选择了 14 列。

为了补充航班数据，本示例使用了“天气数据集”。  天气数据包含美国国家海洋与大气管理局 (NOAA) 提供的每小时陆上天气观测数据，代表机场气象站的天气观测数据，其覆盖时段与航班数据集相同。 该数据集已按如下所述进行预处理：

* 气象站 ID 已映射到相应的机场 ID。
* 已删除与 70 个最繁忙的机场无关的气象站。
* Date 列已拆分为单独的列：Year、Month 和 Day。
* 选择了 26 列。

## <a name="pre-process-the-data"></a>预处理数据

数据集通常需要经过一定的预处理才能进行分析。

![data-process](./media/how-to-designer-sample-classification-flight-delay/data-process.png)

### <a name="flight-data"></a>航班数据

列 **Carrier**、**OriginAirportID** 和 **DestAirportID** 保存为整数。 但是，这些列是分类属性。可使用“编辑元数据”模块将其转换为分类属性。 

![edit-metadata](./media/how-to-designer-sample-classification-flight-delay/edit-metadata.png)

然后，使用“数据集”模块中的“选择列”从可能是目标泄漏器的数据集列中排除：  **DepDelay**、**DepDel15**、**ArrDelay**、**Canceled**、**Year**。 

若要将航班记录联接到每小时天气记录，请使用计划的出发时间作为联接键之一。 若要执行联接，必须在“执行 R 脚本”模块中将 CSRDepTime 列向下舍入到最接近的小时。  

### <a name="weather-data"></a>天气数据

使用“投影列”模块排除缺失值比例较大的列。  这些列包含所有字符串值列：**ValueForWindCharacter**、**WetBulbFarenheit**、**WetBulbCelsius**、**PressureTendency**、**PressureChange**、**SeaLevelPressure** 和 **StationPressure**。

然后，将“清理缺失数据”模块应用到剩余的列以删除包含缺失数据的行。 

天气观测时间向上舍入到最接近的整点小时。 计划的航班时间和天气观测时间按相反的方向进行舍入，以确保模型仅使用航班时间之前的天气数据。 

由于天气数据是以当地时间报告的，因此，将通过从计划的出发时间和天气观测时间中减去时区列来解决时区差。 使用“执行 R 脚本”模块执行这些操作。 

### <a name="joining-datasets"></a>联接数据集

使用“联接数据”模块将航班出发地 (**OriginAirportID**) 的航班记录联接到天气数据。 

 ![按出发地将航班联接到天气数据](./media/how-to-designer-sample-classification-flight-delay/join-origin.png)


使用航班目的地 (**DestAirportID**) 将航班记录联接到天气数据。

 ![按目的地将航班联接到天气数据](./media/how-to-designer-sample-classification-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>准备训练和测试示例

“拆分数据”模块将数据拆分为“四月到九月”记录用于训练，并拆分为“十月”记录用于测试。 

 ![拆分训练和测试数据](./media/how-to-designer-sample-classification-flight-delay/split.png)

使用“选择列”模块从训练数据集中删除年、月和时区列。

## <a name="define-features"></a>定义特征

在机器学习中，特征是用户感兴趣的某些内容的各个可测量属性。 查找一组极其有用的特征需要进行试验并具备领域知识。 有些特征比其他特征更适合用于预测目标。 此外，有些特征与其他特征密切相关，而不会在模型中添加新的信息。 可以删除这些特征。

若要生成模型，可以使用所有可用特征，或选择一部分特征。

## <a name="choose-and-apply-a-learning-algorithm"></a>选择并应用学习算法

使用“双类逻辑回归”模块创建一个模型，并基于训练数据集对其进行训练。  

“训练模型”模块的结果是经过训练的分类模型，可用于对新样本进行评分，以做出预测。  使用测试集从训练的模型生成评分。 然后，使用“评估模型”模块来分析和比较模型的质量。 
管道。运行管道后，可以通过单击输出端口并选择“可视化”来查看“评分模型”模块的输出。   输出中包含评分标签和标签概率。

最后，若要测试结果的质量，请将“评估模型”模块添加到管道画布，并将左侧输入端口连接到“评分模型”模块的输出。  运行管道，然后通过单击输出端口并选择“可视化”来查看“评估模型”模块的输出。  

## <a name="evaluate"></a>评估
逻辑回归模型在处理测试集后的 AUC 为 0.631。

 ![评估](media/how-to-designer-sample-classification-flight-delay/sample6-evaluate-1225.png)

## <a name="next-steps"></a>后续步骤

浏览可用于设计器的其他示例：

- [示例 1 - 回归：预测汽车的价格](how-to-designer-sample-regression-automobile-price-basic.md)
- [示例 2 - 回归：比较汽车价格预测的算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [示例 3 - 通过特征选择进行分类：收入预测](how-to-designer-sample-classification-predict-income.md)
- [示例 4 - 分类：预测信用风险（代价敏感）](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [示例 5 - 分类：预测流失率](how-to-designer-sample-classification-churn.md)
- [示例 7 - 文本分类：维基百科 SP 500 数据集](how-to-designer-sample-text-classification.md)