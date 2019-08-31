---
title: 了解自动 ML 结果
titleSuffix: Azure Machine Learning service
description: 了解如何查看和了解每个自动机器学习运行的图表和指标。
services: machine-learning
author: nilesha
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: d626a7768e7070360a6f0249486bfa83407fe998
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192066"
---
# <a name="understand-automated-machine-learning-results"></a>了解自动化机器学习结果

本文介绍如何查看和了解每个自动机器学习运行的图表和指标。 

了解有关以下方面的详细信息：
+ [分类模型的指标、图表和曲线](#classification)
+ [回归模型的度量值、图表和图形](#regression)
+ [模型 interpretability 和特征重要性](#explain-model)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

* 使用 SDK 或 Azure 门户创建自动化机器学习试验。

    * 使用 SDK 生成[分类模型](how-to-auto-train-remote.md)或[回归模型](tutorial-auto-train-models.md)
    * 使用[Azure 门户](how-to-create-portal-experiments.md)通过上传适当的数据来创建分类或回归模型。

## <a name="view-the-run"></a>查看运行

运行自动机器学习试验后, 可以在机器学习服务工作区中找到运行历史记录。 

1. 转到你的工作区。

1. 在工作区的左侧面板中, 选择 "**试验**"。

   ![试验菜单的屏幕截图](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. 在试验列表中, 选择要浏览的实验。

   [![实验列表](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. 在下表中, 选择 "**运行号码**"。

   [试验运行![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. 在 "迭代" 表中, 选择要进一步浏览的模型的**迭代号**。

   [![试验模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

当你使用`RunDetails` [Jupyter 小组件](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)时, 还会在运行过程中看到这些相同的结果。

## <a name="classification"></a>分类结果

三个以下指标和图表适用于你使用的自动化机器学习功能构建的每个分类模型 Azure 机器学习

+ [指标](#classification-metrics)
+ [混淆矩阵](#confusion-matrix)
+ [精度-召回率图表](#precision-recall-chart)
+ [接收方操作特征 (ROC)](#roc)
+ [提升曲线](#lift-curve)
+ [增益曲线](#gains-curve)
+ [校准图](#calibration-plot)

### <a name="classification-metrics"></a>分类指标

分类任务的每次运行迭代中都保存了以下指标。

|指标|描述|计算|其他参数
--|--|--|--|
AUC_macro| AUC 是接收方操作特性曲线下面的区域。 Macro 是每个类的 AUC 算术平均值。  | [计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC 是接收方操作特性曲线下面的区域。 微的计算方法是将每个类中的真正的正值和假正值组合在一起。| [计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC 是接收方操作特性曲线下面的区域。 加权是每个类的分数的算术平均值, 由每个类中的真实实例的数目加权。| [计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|Accuracy 是与真实标签完全匹配的预测标签百分比。 |[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |无|
average_precision_score_macro|平均精度以每个阈值实现的加权精度汇总精度-召回率曲线，使用前一阈值中的召回率增量作为权重。 宏是每个类的平均精度分数的算术平均值。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|平均精度以每个阈值实现的加权精度汇总精度-召回率曲线，使用前一阈值中的召回率增量作为权重。 微计算是通过在每个截止时合并真正的正值和假正值, 来全局计算。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|平均精度以每个阈值实现的加权精度汇总精度-召回率曲线，使用前一阈值中的召回率增量作为权重。 加权是每个类的平均精度分数的算术平均值, 由每个类中的真实实例的数目加权。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|平衡准确度是每个类的召回率算术平均值。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 评分是精度和召回率的调和平均值。 宏是每个类的 F1 分数的算术平均值。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 评分是精度和召回率的调和平均值。 微通过计算总真实正值、假负和误报计算出全局计算。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 评分是精度和召回率的调和平均值。 按每个类的 F1 评分类频率计算的加权平均值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|这是（多项式） 逻辑回归及其扩展（例如神经网络）中使用的损失函数，在给定概率分类器的预测的情况下，定义为真实标签的负对数可能性。 对于在{0,1} ca-yt = 1 的情况下具有 true 标签 ca-yt 和估计概率 yp 的单个示例, 日志丢失为-log P (&#124;ca-yt yp) =-(ca-yt log (yp) + (1-ca-yt) log (1-yp))。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|None|
norm_macro_recall|规范化宏召回率是已规范化的宏召回率，因此，随机性能的评分为 0，完美性能的评分为 1。 这是通过 norm_macro_recall: = (recall_score_macro-R)/(1-R) 实现的, 其中 R 是用于随机预测的 recall_score_macro 的预期值 (即, 对二元分类使用 R = 0.5, 对 C 类分类问题使用 R = (1/C))。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "宏" |
precision_score_macro|Precision 是标记为特定类的，实际位于该类中的元素百分比。 宏是每个类的精度算术平均值。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Precision 是标记为特定类的，实际位于该类中的元素百分比。 通过计算总的实际正值和假正值, 对微进行全局计算。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Precision 是标记为特定类的，实际位于该类中的元素百分比。 加权是每个类的精度的算术平均值, 每个类中的真实实例的数量为加权。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Recall 是实际位于某个类中的已正确标记的元素百分比。 宏是每个类的算术平均值。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Recall 是实际位于某个类中的已正确标记的元素百分比。 通过计算总真实正值、假负值和假正值, 来全局计算微|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Recall 是实际位于某个类中的已正确标记的元素百分比。 加权是每个类的每个类的算术平均值, 按每个类中的真实实例的数目加权。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|加权准确度是精确的, 其中每个示例给定的权重等于该示例的 true 类中的真实实例的比例。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight 是等于目标中每个元素的该类比例的向量|

### <a name="confusion-matrix"></a>混淆矩阵

混淆矩阵用于描述分类模型的性能。 每一行显示真实类的实例，每一列表示预测类的实例。 混淆矩阵显示给定模型的正确分类标签和错误分类标签。

对于分类问题，Azure 机器学习会自动为生成的每个模型提供一个混淆矩阵。 对于每个混淆矩阵, 自动 ML 都将显示每个预测标签和每个真正标签的交集的频率。 颜色越暗, 矩阵特定部分中的计数就越高。 理想情况下, 最暗的颜色将沿着矩阵的对角线。 

示例 1：准确度不佳的分类模型![准确度不佳的分类模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

示例 2：准确度较高的分类模型（理想状态）![准确度较高的分类模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>精度-召回率图表

使用此图表可以比较每个模型的精度-召回率曲线，以确定哪个模型的精度与召回率关系可接受，可以解决特定的业务问题。 此图表显示宏观平均精度-召回率、微观平均精度-召回率，以及与模型的所有类关联的精度-召回率。

术语“精度”表示分类器正确标记所有实例的能力。 “召回率”表示分类器查找特定标签的所有实例的能力。 精度-召回率曲线显示这两个概念之间的关系。 在理想情况下，模型具有 100% 的精度和 100% 的准确度。

示例 1：精度和召回率较低的分类模型![精度和召回率较低的分类模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

示例 2：精度和召回率大约为 100% 的分类模型（理想状态）![精度和召回率较高的分类模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC

接收方操作特征 (ROC) 是特定模型的正确分类标签与错误分类标签的对比图。 以较高的偏差基于数据集训练模型时，ROC 曲线提供的信息可能较少，因为它不会显示误报标签。

示例 1：真实标签较少、虚假标签较多的分类模型![真实标签较少、虚假标签较多的分类模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

示例 2：真实标签较多、虚假标签较少的分类模型![真实标签较多、虚假标签较少的分类模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>提升曲线

可以根据基线比较 Azure 机器学习自动生成的模型的性能提升，以查看该特定模型的值增益。

提升图用于评估分类模型的性能。 它显示使用模型比不使用模型预期能够将性能改善多少。 

示例 1：模型的性能比随机选择模型更差![性能比随机选择模型更差的分类模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

示例 2：模型的性能比随机选择模型更好![性能更好的分类模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>增益曲线

增益图按每个数据部分评估分类模型的性能。 它针对数据集的每个百分位，显示性能预期要比随机选择模型改善多少。

借助累积增益图，可以使用一个对应于模型所需增益的百分比来选择分类截止值。 此信息提供了查看随附提升图中的结果的另一种方式。

示例 1：增益极低的分类模型![增益极低的分类模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

示例 2：增益极高的分类模型![增益极高的分类模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>校准图

对于所有分类问题，可以查看微观平均、宏观平均以及给定预测模型中每个类的校准行。 

校准图用于显示预测模型的置信度。 为此，它会显示预测概率与实际概率之间的关系。其中，“概率”表示特定实例属于某个标签的可能性。 适当校准的模型与 y=x 行对齐，表示它在预测中具有合理的置信度。 置信度过高的模型与 y=0 行对齐，其中会显示预测概率，但不显示实际概率。

示例 1：适当校准的模型![适当校准的模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

示例 2：置信度过高的模型![置信度过高的模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>回归结果

三个以下指标和图表适用于使用的自动化机器学习功能构建的每个回归模型 Azure 机器学习

+ [指标](#reg-metrics)
+ [预测与真实](#pvt)
+ [残差直方图](#histo)


### <a name="reg-metrics"></a>回归指标

对于回归或预测任务, 在每次运行迭代中保存以下度量值。

|指标|描述|计算|其他参数
--|--|--|--|
explained_variance|解释方差是数学模型计算给定数据集的方差时遵循的比例。 它是原始数据方差与误差方差之间的递减百分比。 如果误差平均值为 0，则它等于解释方差。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|无|
r2_score|R2 是与输出平均值的基线模型相比，平方误差的确定系数或递减百分比。 |[计算](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|None|
spearman_correlation|斯皮尔曼相关是两个数据集之间的关系单一性的非参数测量法。 与皮尔逊相关不同，斯皮尔曼相关不假设两个数据集呈正态分布。 与其他相关系数一样，此参数在 -1 和 +1 之间变化，0 表示不相关。 -1 或 +1 相关表示确切的单一关系。 正相关表示 y 随着 x 的递增而递增。 负相关表示 y 随着 x 的递增而递减。|[计算](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|None|
mean_absolute_error|平均绝对误差是目标与预测之间的差的预期绝对值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|无|
normalized_mean_absolute_error|规范化平均绝对误差是平均绝对误差除以数据范围后的值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|除以数据范围|
median_absolute_error|平均绝对误差是目标与预测之间的所有绝对差的中间值。 此损失值可靠地反映离群值。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|None|
normalized_median_absolute_error|规范化中间绝对误差是中间绝对误差除以数据范围后的值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|除以数据范围|
root_mean_squared_error|均方根误差是目标与预测之间的预期平方差的平方根|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|无|
normalized_root_mean_squared_error|规范化均方根误差是均方根误差除以数据范围后的值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|除以数据范围|
root_mean_squared_log_error|均方根对数误差是预期平方对数误差的平方根|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|无|
normalized_root_mean_squared_log_error|规范化均方根对数误差指均方根对数误差除以数据范围后的值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|除以数据范围|

### <a name="pvt"></a>预测与True

预测与“真实”显示回归问题的预测值与其相关真实值之间的关系。 可以使用此图形来衡量模型的性能，因为预测值与 y=x 行越接近，预测模型的准确度就越高。

每次运行后，可以查看每个回归模型的预测与真实图形。 为了保护数据隐私，值已装箱在一起，每个箱的大小在图表区域的下半部分显示为条形图。 可将预测模型（带有浅色阴影，其中显示了误差边际）与模型的理想值进行比较。

示例 1：预测准确度较低的回归模型![预测准确度较低的回归模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

示例 2：在其预测中具有高准确度的回归模型预测[ ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>残差直方图

残差表示观测到的 y - 预测的 y。 若要显示偏差较小的误差边际，应该以 0 为中心，将残差直方图绘制成钟形曲线。 

示例 1：误差中带有偏差的回归模型![误差中带有偏差的 SA 回归模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

示例 2：误差更均匀分布的回归模型![误差更均匀分布的回归模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>模型 interpretability 和特征重要性

功能重要性允许您查看每个功能在模型构造中有多宝贵。 此计算在默认情况下处于关闭状态, 因为这会显著增加运行时间。   您可以为所有模型启用模型说明, 或者只说明最佳模型。

可以查看整个模型的特征重要性评分，也可以查看预测模型中每个类的该评分。 可以查看每个特征的重要性如何对每个类和整个模型进行比较。

![模型解释功能](./media/how-to-understand-automated-ml/feature-importance.gif)

有关启用 interpretability 功能的详细信息, 请参阅[在 Python 中配置自动 ML 试验](how-to-configure-auto-train.md#explain-the-model-interpretability)。  有关说明最佳模型的示例, 请参阅[最佳模型说明](how-to-auto-train-remote.md#explain)。

## <a name="next-steps"></a>后续步骤

+ 详细了解 Azure 机器学习中的[自动 ml](concept-automated-ml.md) 。
+ 试用[自动机器学习模型解释](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation)示例笔记本。
