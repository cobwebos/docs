---
title: 设计器：分类、预测航班延迟
titleSuffix: Azure Machine Learning
description: 本文介绍如何使用拖放设计器和自定义 R 代码生成机器学习模型，以预测航班延误。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 11/04/2019
ms.openlocfilehash: 10b3b78b4878eeddf2f786fda90ab9709e4bd2b4
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647199"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>示例 6-分类：使用 R 预测航班延迟
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

此管道使用历史航班和天气数据来预测计划的乘客航班是否将延迟15分钟以上。 此问题可作为分类问题来解决，可预测两个类：延迟或按时进行。

下面是此示例的最终管道图：

[管道 ![图](media/how-to-ui-sample-classification-predict-flight-delay/pipeline-graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 单击 "示例 6" 将其打开。

## <a name="get-the-data"></a>获取数据

此示例使用**航班延迟数据**数据集。 这是美国运输部门的 TranStats 数据收集的一部分。 数据集包含2013年4月到10月的航班延迟信息。 已预先处理数据集，如下所示：

* 筛选为包括大陆美国中70最繁忙的机场。
* 将已取消的航班标记为延迟15分钟以上。
* 已筛选出转移航班。
* 选定的14列。

若要补充航班数据，请使用**天气数据集**。 天气数据包含来自 NOAA 的每小时、基于居住的天气预报，表示来自机场天气的天气预报，涵盖与航班数据集相同的时间段。 它已预先处理，如下所示：

* 天气工作站 Id 映射到相应的机场 Id。
* 不与70最繁忙的机场关联的天气工作站已被删除。
* 日期列拆分为单独的列： Year、Month 和 Day。
* 已选择26列。

## <a name="pre-process-the-data"></a>预先处理数据

数据集通常需要预先处理才能进行分析。

![数据-进程](media/how-to-ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>航班数据

将**OriginAirportID**和**DestAirportID** **列作为**整数保存。 但是，它们是分类属性，使用**编辑元数据**模块将它们转换为分类。

![编辑-元数据](media/how-to-ui-sample-classification-predict-flight-delay/edit-metadata.png)

然后，使用 "**选择**数据集中的列" 模块从可能为 target Leakers： **DepDelay**、 **DepDel15**、 **ArrDelay**、**已取消** **年**的数据集列中排除。 

若要将航班记录与每小时天气记录联接，请使用计划的出发时间作为联接键之一。 若要执行联接，必须将 CSRDepTime 列向下舍入到最接近的小时，这是在**执行 R 脚本**模块中完成的。 

### <a name="weather-data"></a>天气数据

使用 "**项目列**" 模块排除具有大量缺失值的列。 这些列包含所有字符串值列： **ValueForWindCharacter**、 **WetBulbFarenheit**、 **WetBulbCelsius**、 **PressureTendency**、 **PressureChange**、 **SeaLevelPressure**和**StationPressure**.

然后，将 "**清理缺失数据**" 模块应用到其余列以删除缺少数据的行。

天气观察时间将向上舍入到最接近的全小时。 计划航班时间和天气观察时间以相反方向舍入，以确保模型仅在航班时间之前使用天气。 

由于天气数据是以本地时间报告的，因此，通过从计划的出发时间和天气观察时间中减去时区列来考虑时区差异。 这些操作通过**执行 R 脚本**模块完成。

### <a name="joining-datasets"></a>联接数据集

使用**联接数据**模块将航班记录与航班（**OriginAirportID**）的源中的天气数据相联接。

 ![按来源联接航班和天气](media/how-to-ui-sample-classification-predict-flight-delay/join-origin.png)


航班记录使用航班目的地（**DestAirportID**）与天气数据相联接。

 ![按目的地加入航班和天气](media/how-to-ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>准备定型和测试示例

**拆分数据**模块将数据拆分为四月份到九月的定型记录和10月的记录，以供测试之用。

 ![拆分定型和测试数据](media/how-to-ui-sample-classification-predict-flight-delay/split.png)

使用 "选择列" 模块从定型数据集中删除 "年"、"月" 和 "时区" 列。

## <a name="define-features"></a>定义特征

在机器学习中，功能是你感兴趣的东西的单个可度量属性。 查找一组强大的功能需要试验和域知识。 有些特征比其他特征更适合用于预测目标。 此外，某些功能可能与其他功能具有很强的相关性，并且不会将新信息添加到模型中。 可以删除这些功能。

若要生成模型，可以使用所有可用的功能，或选择功能的子集。

## <a name="choose-and-apply-a-learning-algorithm"></a>选择并应用学习算法

使用 "**双类逻辑回归**" 模块创建一个模型，并在定型数据集上训练该模型。 

**训练模型**模块的结果是经过训练的分类模型，可用于对用于进行预测的新样本进行评分。 使用测试集生成定型模型的分数。 然后，使用 "**评估模型**" 模块来分析和比较模型的质量。
管道运行管道后，可以通过单击输出端口并选择 "**可视化**" 来查看 "**评分模型**" 模块的输出。 输出包括评分标签和标签的概率。

最后，要测试结果的质量，请将 "**评估模型**" 模块添加到管道画布，然后将左侧的输入端口连接到 "评分模型" 模块的输出。 运行管道并查看 "**评估模型**" 模块的输出，方法是单击输出端口并选择 "**可视化**"。

## <a name="evaluate"></a>评估
在测试集上，逻辑回归模型的 AUC 为0.631。

 ![评估](media/how-to-ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>后续步骤

浏览可用于设计器的其他示例：

- [示例 1-回归：预测汽车的价格](how-to-designer-sample-regression-automobile-price-basic.md)
- [示例 2-回归：比较汽车价格预测的算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [示例 3-通过功能选择进行分类：收入预测](how-to-designer-sample-classification-predict-income.md)
- [示例 4-分类：预测信用风险（区分成本）](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [示例 5-分类：预测改动](how-to-designer-sample-classification-churn.md)
- [示例 7-文本分类：维基百科 SP 500 数据集](how-to-designer-sample-text-classification.md)