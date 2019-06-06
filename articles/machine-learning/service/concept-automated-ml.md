---
title: 自动 ML 算法选择和优化
titleSuffix: Azure Machine Learning service
description: 了解 Azure 机器学习服务如何自动选取算法，以及如何通过它来生成模型，以便使用你提供的参数和条件为模型选择最佳算法，从而节省时间。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 05/21/2019
ms.custom: seodec18
ms.openlocfilehash: 93eb0fba91ce5064d04a340e8b3e5b984ee73081
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515569"
---
# <a name="what-is-automated-machine-learning"></a>什么是自动化机器学习？

自动化的机器学习，也称为自动化机器学习是机器的自动学习模型开发的较长时间、 迭代任务的过程。 它允许数据科学家、 分析人员和开发人员，同时维护模型质量构建具有高缩放性、 效率和工作效率的机器学习模型。

传统的机器学习模型开发是占用大量资源，需要大量域知识和时间来生成和比较数十个模型。 应用自动的 ML 时希望 Azure 机器学习来训练和调整模型适用于您使用您指定目标度量值。 该服务然后循环访问机器学习算法与功能选择，其中每次迭代将生成具有培训评分的模型配对。 更高级评分，效果越佳模型被视为"fit"你的数据。

使用自动化的机器学习中，将加快所需获取生产的 ML 模型很好的轻松和高效的时间。

## <a name="when-to-use-automated-ml"></a>何时使用自动化的机器学习

自动的 ML 普及的机器学习模型开发过程中，并使其用户，无论他们的数据科学的专业知识，能够识别任何问题的端到端机器学习管道。

数据科研人员、 分析和各行各业的开发人员可以使用到的自动化机器学习：

+ 实现广泛的编程不知情的情况下的机器学习解决方案
+ 节省时间和资源
+ 利用数据科学的最佳做法
+ 提供敏捷解决问题

## <a name="how-automated-ml-works"></a>如何自动机器学习工作原理

使用**Azure 机器学习服务**，可以设计和使用这些步骤运行自动化的机器学习训练实验：

1. **确定机器学习问题**待解决： 分类，预测或回归

1. **指定的源和标记的训练数据的格式**:Numpy 数组或 Pandas 数据帧

1. **配置计算目标，以便为模型定型**，如你[本地计算机、 Azure 机器学习计算、 远程 Vm 或 Azure Databricks](how-to-set-up-training-targets.md)。  了解有关自动化培训[上的远程资源](how-to-auto-train-remote.md)。

1. **配置自动化的机器学习参数**，确定通过不同的模型超参数设置的迭代数高级预处理/特征化，以及要在确定最佳的模型时，查询的指标。  可以配置的设置自动训练实验[在 Azure 门户](how-to-create-portal-experiments.md)或[sdk](how-to-configure-auto-train.md)。

1. **提交训练运行。**

  ![自动化的机器学习](./media/how-to-automated-ml/automl-concept-diagram2.png)

在定型期间，Azure 机器学习服务中并行管道，请尝试不同的算法和参数创建的数。 它将停止后它会命中在试验中定义的退出条件。

此外可以检查记录运行的信息，其中包含运行期间收集的指标。 训练运行生成 Python 序列化对象 (`.pkl`文件)，其中包含的模型和数据预处理。

即使自动化模型构建，还可以[了解如何重要或相关的功能是](how-to-configure-auto-train.md#explain)到生成的模型。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>预处理

在每个自动化机器学习实验，你的数据进行预处理使用默认的方法和 （可选） 通过高级预处理。

### <a name="automatic-preprocessing-standard"></a>自动预处理 （标准）

在每个自动化机器学习实验，你的数据进行自动缩放或规范化以帮助算法很好地运行。  在模型定型过程的以下缩放或规范化方法之一将应用于每个模型。

|缩放&nbsp;&&nbsp;规范化| 描述 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 对功能进行标准化，通过删除平均值和缩放到单位方差  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 通过缩放该列的最小值和最大值的每个功能来转换功能  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |通过其最大绝对值缩放每个功能 |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |此情况下它们的分位范围的 Scaler 功能 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |使用数据的奇异值分解以将它投影到较低维空间线性维数约简 |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |此转换器通过截断奇异值分解 (SVD) 执行线性维数约简。 违背 PCA，此估算器不在正中数据之前计算奇异值分解。 这意味着它可以有效地处理使用 scipy.sparse 矩阵 |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 每个示例 （即，数据矩阵的每个行） 与至少一个非零值组件是重新缩放独立于其他示例，以便其 norm （l1 或 l2） 等于一 |

### <a name="advanced-preprocessing-optional-featurization"></a>高级预处理： 可选特征化

其他高级预处理和特征化，还提供，例如缺少值插补、 编码和转换。 [了解更多有关哪些特征化信息包含](how-to-create-portal-experiments.md#preprocess)。 启用此设置，但：

+ Azure 门户：选择**预处理**中的复选框**高级设置**[执行这些步骤](how-to-create-portal-experiments.md)。

+ Python SDK:指定`"preprocess": True`有关[`AutoMLConfig`类](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)。

## <a name="ensemble-models"></a>系综模型

使用具有自动的机器学习的系综模型训练[Caruana 系综选择算法与已排序的系综初始化](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)。 系综学习的项而不是使用单个模型的多个模型，改进了机器学习结果和预测性能。 系综迭代将显示为您运行的最后一个迭代。

## <a name="training-metric-output"></a>训练指标输出

有多种方法可以查看每个运行迭代训练准确性指标。

* 使用 Jupyter 小组件。
* 使用`get_metrics()`函数对任何`Run`对象。
* 在试验中在 Azure 门户中查看的指标。

### <a name="classification-metrics"></a>分类指标

以下度量值将保存在分类任务的每个运行迭代。

|指标|描述|计算|其他参数
--|--|--|--|
AUC_macro| AUC 是接收方操作特性曲线下面的区域。 Macro 是每个类的 AUC 算术平均值。  | [计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC 是接收方操作特性曲线下面的区域。 通过组合每个类中的真报率和误报率来全局计算 Micro| [计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC 是接收方操作特性曲线下面的区域。 Weighted 是每个类的评分算术平均值，按每个类中的真实实例数加权| [计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|Accuracy 是与真实标签完全匹配的预测标签百分比。 |[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |无|
average_precision_score_macro|平均精度以每个阈值实现的加权精度汇总精度-召回率曲线，使用前一阈值中的召回率增量作为权重。 Macro 是每个类的平均精度评分算术平均值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|平均精度以每个阈值实现的加权精度汇总精度-召回率曲线，使用前一阈值中的召回率增量作为权重。 通过组合每个交接中的真报率和误报率来全局计算 Micro|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|平均精度以每个阈值实现的加权精度汇总精度-召回率曲线，使用前一阈值中的召回率增量作为权重。 Weighted 是每个类的平均精度评分算术平均值，按每个类中的真实实例数加权|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|平衡准确度是每个类的召回率算术平均值。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 评分是精度和召回率的调和平均值。 Macro 是每个类的 F1 评分算术平均值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 评分是精度和召回率的调和平均值。 通过统计真报率、漏报率和误报率总值来全局计算 Micro|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 评分是精度和召回率的调和平均值。 按每个类的 F1 评分类频率计算的加权平均值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|这是（多项式） 逻辑回归及其扩展（例如神经网络）中使用的损失函数，在给定概率分类器的预测的情况下，定义为真实标签的负对数可能性。 对于在 {0,1} 中包含真实标签 yt，且包含 yt=1 的估计概率 yp 的单个样本，对数损失为 -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp))|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|无|
norm_macro_recall|规范化宏召回率是已规范化的宏召回率，因此，随机性能的评分为 0，完美性能的评分为 1。 可以通过公式 norm_macro_recall := (recall_score_macro - R)/(1 - R) 来计算此值，其中，R 是随机预测的 recall_score_macro 预期值（例如，对于二元分类，R=0.5；对于 C 类分类问题，R=(1/C)）|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "macro"，(recall_score_macro - R)/(1 - R)，其中，R 是随机预测的 recall_score_macro 预期值（例如，对于二元分类，R=0.5；对于 C 类分类问题，R=(1/C)）|
precision_score_macro|Precision 是标记为特定类的，实际位于该类中的元素百分比。 Macro 是每个类的精度算术平均值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Precision 是标记为特定类的，实际位于该类中的元素百分比。 通过统计真报率和误报率总值来全局计算 Micro|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Precision 是标记为特定类的，实际位于该类中的元素百分比。 Weighted 是每个类的精度算术平均值，按每个类中的真实实例数加权|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Recall 是实际位于某个类中的已正确标记的元素百分比。 Macro 是每个类的召回率算术平均值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Recall 是实际位于某个类中的已正确标记的元素百分比。 通过统计真报率和漏报率总值来全局计算 Micro|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Recall 是实际位于某个类中的已正确标记的元素百分比。 Weighted 是每个类的召回率算术平均值，按每个类中的真实实例数加权|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|加权准确度是当分配给每个示例的权重等于该示例的真实类中的真实实例比例时的准确度|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight 是等于目标中每个元素的该类比例的向量|

### <a name="regression-and-forecasting-metrics"></a>回归和预测指标

以下度量值将保存在每个运行迭代的回归或预测任务。

|指标|描述|计算|其他参数
--|--|--|--|
explained_variance|解释方差是数学模型计算给定数据集的方差时遵循的比例。 它是原始数据方差与误差方差之间的递减百分比。 如果误差平均值为 0，则它等于解释方差。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|无|
r2_score|R2 是与输出平均值的基线模型相比，平方误差的确定系数或递减百分比。 如果误差平均值为 0，则它等于解释方差。|[计算](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|无|
spearman_correlation|斯皮尔曼相关是两个数据集之间的关系单一性的非参数测量法。 与皮尔逊相关不同，斯皮尔曼相关不假设两个数据集呈正态分布。 与其他相关系数一样，此参数在 -1 和 +1 之间变化，0 表示不相关。 -1 或 +1 相关表示确切的单一关系。 正相关表示 y 随着 x 的递增而递增。 负相关表示 y 随着 x 的递增而递减。|[计算](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|无|
mean_absolute_error|平均绝对误差是目标与预测之间的差的预期绝对值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|无|
normalized_mean_absolute_error|规范化平均绝对误差是平均绝对误差除以数据范围后的值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|除以数据范围|
median_absolute_error|平均绝对误差是目标与预测之间的所有绝对差的中间值。 此损失值可靠地反映离群值。|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|无|
normalized_median_absolute_error|规范化中间绝对误差是中间绝对误差除以数据范围后的值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|除以数据范围|
root_mean_squared_error|均方根误差是目标与预测之间的预期平方差的平方根|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|无|
normalized_root_mean_squared_error|规范化均方根误差是均方根误差除以数据范围后的值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|除以数据范围|
root_mean_squared_log_error|均方根对数误差是预期平方对数误差的平方根|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|无|
normalized_root_mean_squared_log_error|规范化均方根对数误差指均方根对数误差除以数据范围后的值|[计算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|除以数据范围|


## <a name="use-with-onnx-in-c-apps"></a>与中的 ONNX 配合使用C#应用程序

与 Azure 机器学习，可以使用自动化的机器学习生成 Python 模型并将其转换为 ONNX 格式。 ONNX 运行时支持C#，因此可以使用中自动生成的模型在C#的应用而无需重新编写代码或任何 REST 终结点引入的网络延迟。 请尝试此流的示例[此 Jupyter notebook 中](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb)。

## <a name="automated-ml-across-microsoft"></a>在 Microsoft 自动化机器学习

自动化机器学习如现在还可供其他 Microsoft 解决方案：

|集成|描述|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|自动模型选择和定型使用 ML.NET 使用 Visual Studio 和 Visual Studio Code 的.NET 应用程序中实现自动化机器学习 （预览版）。|
|[HDIsnight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|横向扩展自动化机器学习训练作业在 HDInsight 中的 Spark 群集中并行。|
|[PowerBI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|调用直接在 Power BI （预览版） 中的机器学习模型。|

## <a name="next-steps"></a>后续步骤

查看示例并了解如何使用自动化机器学习构建模型：

+ 请按照[教程：使用 Azure 自动机器学习自动训练分类模型](tutorial-auto-train-models.md)

+ 配置自动训练实验的设置：
  + 在 Azure 门户界面[使用以下步骤](how-to-create-portal-experiments.md)。
  + 使用 Python SDK[使用以下步骤](how-to-configure-auto-train.md)。

+ 了解如何自动使用时序数据，训练[使用以下步骤](how-to-auto-train-forecast.md)。

+ 试用[Jupyter 笔记本示例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
