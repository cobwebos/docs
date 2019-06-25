---
title: '分类：预测流失情况、 亲和力，和向上销售 '
titleSuffix: Azure Machine Learning service
description: 此可视界面示例试验演示了二元分类器预测的改动，客户关系管理 (CRM) 的常见任务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 42724f5fcb3101015cef0d218a3d548f349646be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65785827"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>示例 5-分类：预测流失情况、 亲和力，和向上销售 

了解如何构建复杂的机器学习试验，而无需编写一行代码使用直观的界面。

此试验中训练三种模型，**双类提升的决策树**分类器，以预测客户关系管理 (CRM) 系统的常见任务： 流失情况、 亲和力，和向上销售。 跨多个数据源拆分和加密的匿名客户信息的数据值和标签，但是，我们仍可以使用可视界面合并数据集和定型模型使用的加密的值。

因为我们正在尝试回答问题"哪一种？" 这称为分类问题。 不过，您可以应用相同的步骤，在此试验中来解决任何类型的机器学习问题，无论是回归、 分类、 聚类分析，等等。

下面是此实验的已完成关系图：

![试验图](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>必备组件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 选择**打开**示例 5 实验的按钮。

    ![打开试验](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>数据

对于此试验，我们使用的数据是来自 KDD Cup 2009。 数据集具有 50000 行和 230 特征列。 该任务是预测流失情况、 亲和力，和向上销售的客户使用这些功能。 有关数据和任务的详细信息，请参阅[KDD 网站](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)。

## <a name="experiment-summary"></a>实验摘要

此可视界面示例试验演示了二元分类器预测流失情况、 亲和力，和向上销售，客户关系管理 (CRM) 的常见任务。

首先，我们进行一些简单的数据处理。

- 原始数据集包含大量的缺失值。 我们使用**清理缺失数据**模块来替换缺失值 0。

    ![清除数据集](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- 功能和相应的流失，亲和力，并追加销售标签以不同的数据集。 我们使用**中添加列**模块以将标签列追加到特征列。 第一列**Col1**，标签列。 列，其余**Var1**， **Var2**，依此类推，是特征列。

    ![添加列的数据集](./media/ui-sample-classification-predict-churn/added-column1.png)

- 我们使用**拆分数据**模块将数据集拆分为定型和测试集。

    我们然后使用提升决策树的二元分类器具有默认参数来生成预测模型。 我们构建一个模型，每个任务，即，一个模型每个预测向上销售、 亲和力和改动。

## <a name="results"></a>结果

可视化的输出**评估模型**模块，我们看到模型性能根据测试集。 向上销售任务中，对于 ROC 曲线显示则模型会比随机模型更好地。 曲线 (AUC) 下的区域是 0.857。 在阈值 0.5 时，精度为 0.7，重新调用是 0.463，F1 分数是 0.545。

![评估结果](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 可以移动**阈值**滑块和度量值更改为二进制分类任务，请参阅。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

了解可用于可视化界面的其他示例：

- [示例 1-回归：预测汽车的价格](ui-sample-regression-predict-automobile-price-basic.md)
- [示例 2-回归：比较进行汽车价格预测算法](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [示例 3-分类：预测信用风险](ui-sample-classification-predict-credit-risk-basic.md)
- [示例 4-分类：预测信用风险 （成本敏感）](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
