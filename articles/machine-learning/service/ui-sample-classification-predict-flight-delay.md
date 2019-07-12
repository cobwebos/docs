---
title: 分类：预测航班延误
titleSuffix: Azure Machine Learning service
description: 本文介绍如何生成机器学习模型来预测航班延误使用可视拖放界面和自定义 R 代码。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 773e55fe4b5ca5acf27ba1765e5a16075f625187
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607632"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>示例 6-分类：预测航班延误使用 R

此实验使用历史航班和天气数据来预测排定的航班是否将延迟超过 15 分钟。

为分类问题，预测两个类-延迟，或在时间上，可以被解决此问题。 若要生成分类器，此模型中使用大量的历史航班数据中的示例。

以下是最终实验关系图：

[![实验的关系图](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 选择**打开**示例 6 实验的按钮：

    ![打开试验](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>获取数据

此实验使用**航班延迟数据**数据集。 它的一部分从美国的 TranStats 数据收集运输部。 数据集包含从年 4 月到 2013 年 10 月航班延迟信息。 将数据上传到可视界面之前, 预先处理，如下所示：

* 筛选，以包括在美国本土 70 个最繁忙的机场。
* 对于已取消的航班，重新标记为延误超过 15 分钟。
* 筛选出转机航班。
* 所选 14 列。

若要补充航班数据**天气数据集**使用。 天气数据包含从 NOAA，每小时的陆基天气观测值，表示从机场气象站，涵盖相同时间段的年 4 月到 10 月 2013年的观测值。 上传到 Azure 机器学习的可视界面，它已预先处理，如下所示：

* 气象站 Id 已映射到相应的机场 Id。
* 与 70 个最繁忙的机场无关的气象站已删除。
* 日期列已拆分为单独的列：年、 月和日。
* 所选的 26 列。

## <a name="pre-process-the-data"></a>预处理数据

数据集通常需要一些预先处理就可在进行分析。

![data-process](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>航班数据

列**承运人**， **OriginAirportID**，并**DestAirportID**另存为整数。 但是，它们分类属性，请使用**编辑元数据**模块将转换为分类。

![edit-metadata](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

然后，使用**选择的列**排除在可能的目标 leakers 的数据集列的数据集模块中：**DepDelay**， **DepDel15**， **ArrDelay**，**取消**，**年**。 

若要联接的航班记录与每小时天气记录，为一个联接键使用的计划的出发时间。 若要执行该联接，CSRDepTime 列必须是向下舍入到最接近的小时，可通过在**执行 R 脚本**模块。 

### <a name="weather-data"></a>天气数据

使用将排除具有较大比例的缺失值的列**投影列**模块。 这些列包含字符串值的所有列：**ValueForWindCharacter**， **WetBulbFarenheit**， **WetBulbCelsius**， **PressureTendency**， **PressureChange**，**SeaLevelPressure**，并**StationPressure**。

**清理缺失数据**模块然后应用于其余列来删除缺少的数据行。

天气观察时间向上舍入到最接近的完整小时。 排定的航班和天气观察时间将按相反的方向，以确保该模型使用飞行时间之前唯一天气舍入。 

因为在当地时间的报告天气数据，时区差异被考虑通过减去从计划的出发时间和天气观察时间的时区列。 完成这些操作使用**执行 R 脚本**模块。

### <a name="joining-datasets"></a>联接数据集

与在飞行的原点上的天气数据联接航班记录 (**OriginAirportID**) 使用**加入数据**模块。

 ![源进行联接航班和天气](media/ui-sample-classification-predict-flight-delay/join-origin.png)


与使用在飞行的目标的天气数据联接航班记录 (**DestAirportID**)。

 ![联接航班和天气的目标](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>准备定型和测试示例

**拆分数据**模块将数据插入年 4 月拆分年 9 月记录进行培训，并为测试记录年 10 月。

 ![拆分训练和测试数据](media/ui-sample-classification-predict-flight-delay/split.png)

年、 月和时区列已从使用选择列模块的定型数据集。

## <a name="define-features"></a>定义特征

在机器学习中，功能都感兴趣的内容的各个可测量属性。 若要找到一系列强大的功能需要试验和域知识。 有些特征比其他特征更适合用于预测目标。 此外，某些功能可能有更强的关联与其他功能，并且不会向模型添加新的信息。 可以删除这些功能。

若要生成一个模型，可以使用所有可用的功能，或选择的功能的子集。

## <a name="choose-and-apply-a-learning-algorithm"></a>选择并应用学习算法

创建模型使用**双类逻辑回归**模块，并在训练数据集上训练。 

结果**训练模型**模块是一个经过训练的分类模型，可用于新样本评分以进行预测。 使用测试集来训练模型生成分数。 然后，使用**评估模型**模块来分析和比较模型的质量。

运行此试验后，可以查看的输出**评分模型**通过单击输出端口并选择模块**可视化**。 输出包括评分的标签和标签的概率。

最后，若要测试结果的质量，添加**评估模型**模块到实验画布上，然后将左侧输入的端口连接至评分模型模块的输出。 运行实验并查看的输出**评估模型**模块，通过单击输出端口并选择**可视化**。

## <a name="evaluate"></a>评估
逻辑回归模型的 AUC 为 0.631 的测试设置。

 ![评估](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>后续步骤

了解可用于可视化界面的其他示例：

- [示例 1-回归：预测汽车的价格](ui-sample-regression-predict-automobile-price-basic.md)
- [示例 2-回归：比较进行汽车价格预测算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [示例 3-分类：预测信用风险](ui-sample-classification-predict-credit-risk-basic.md)
- [示例 4-分类：预测信用风险 （成本敏感）](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [示例 5-分类：预测客户流失](ui-sample-classification-predict-churn.md)
