---
title: 什么是自动化 ML/AutoML
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习如何自动选取算法，以及如何通过它来生成模型，以便使用你提供的参数和条件为模型选择最佳算法，从而节省时间。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 02/28/2020
ms.openlocfilehash: c8864e00be9f491d87478c253286070b9334a26e
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383185"
---
# <a name="what-is-automated-machine-learning"></a>什么是自动化机器学习？

自动化机器学习也称为自动化 ML，是将机器学习模型开发过程中耗时的反复性任务自动化的过程。 数据科学家、分析师和开发人员可以使用它来生成高度可缩放、高效且高产能的 ML 模型，同时保证模型的质量。 自动化 ML 基于 [Microsoft Research 部门](https://arxiv.org/abs/1705.05355)的突破性技术。

传统的机器学习模型开发是资源密集型的，需要具备丰富的领域知识，并需要花费大量的时间来生成和比较数十个模型。 使用自动化机器学习可以缩减生成生产就绪型 ML 模型所需的时间，同时使工作变得更轻松、更高效。

 


## <a name="when-to-use-automated-ml"></a>何时使用自动化 ML

想要通过 Azure 机器学习使用指定的目标指标训练和优化模型时，可以运用自动化 ML。 自动化 ML 可将机器学习模型开发过程标准化，并使其用户（无论是否具备数据科学知识）能够在端到端的机器学习管道中识别任何问题。

跨行业的数据科学家、分析师和开发人员可以使用自动 ML：

+ 无需丰富的编程知识，即可实现机器学习解决方案
+ 节省时间和资源
+ 利用数据科学最佳做法
+ 提供灵活的问题解决方法

下表列出了常见的自动化 ML 用例。 

分类| 时序预测 | 回归
---|---|---
[欺诈检测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[销售预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[CPU 性能预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[营销预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[需求预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[新闻组数据分类](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[饮料生产预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="design-automated-ml-experiments"></a>设计自动化 ML 实验

使用 **Azure 机器学习**可以通过以下步骤设计和运行自动化 ML 训练试验：

1. **识别要解决的 ML 问题**：分类、预测或回归

1. **指定标记的训练数据的源和格式**：数字数组或熊猫数据框

1. **配置模型训练的计算目标**，例如[本地计算机、Azure 机器学习计算、远程 VM 或 Azure Databricks](how-to-set-up-training-targets.md)。  了解如何对[远程资源](how-to-auto-train-remote.md)进行自动训练。

1. **配置自动化机器学习参数**，用于确定要对不同模型运行的迭代次数、超参数设置、高级预处理/特征化，以及在确定最佳模型时要查看的指标。  可以在 [Azure 机器学习工作室](https://ml.azure.com)中或使用 [SDK](how-to-configure-auto-train.md) 配置自动训练试验的设置。 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **提交培训运行。**

## <a name="how-automated-ml-works"></a>自动化 ML 的工作原理

在训练期间，Azure 机器学习会创建多个尝试不同算法和参数的并行管道。 该服务通过 ML 算法与要素选择配对进行迭代，其中每个迭代生成具有训练分数的模型。 模型的评分越高，则认为它可以更好地“拟合”数据。  一旦达到试验中定义的退出条件，机器学习就会停止。 下图对此过程进行了说明： 

  ![自动化机器学习](./media/concept-automated-ml/automl-concept-diagram2.png)


还可以检查记录的运行信息，其中包含运行期间收集的[指标](how-to-understand-automated-ml.md)。 训练运行会生成一个包含模型和数据预处理的 Python 序列化对象（`.pkl` 文件）。

模型生成是自动化的，同时，你也可以[了解特征对于生成的模型而言如何重要或者彼此相关](how-to-configure-auto-train.md#explain)。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>预处理

在每个自动化机器学习试验中，数据将使用默认方法进行预处理，有时还会通过高级预处理技术进行预处理。

> [!NOTE]
> 自动机器学习预处理步骤（特征规范化、处理缺失数据，将文本转换为数字等）成为基础模型的一部分。 使用模型进行预测时，训练期间应用的相同预处理步骤将自动应用于输入数据。

### <a name="automatic-preprocessing-standard"></a>自动预处理（标准）

在每个自动化机器学习试验中，数据将自动缩放或规范化，以帮助确保算法的良好性能。  在模型训练过程中，将对每个模型应用以下缩放或规范化技术之一。

|缩放和规范化| 描述 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 通过删除平均值并缩放到单位差异来标准化特征  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 通过缩放每个要素，按该列的最小和最大值来转换要素  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |按特征的最大绝对值缩放每个特征 |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |按特征的分位数范围缩放特征 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |使用数据的单值分解进行线性维度化简，以将其投影到低维空间 |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |此转换器通过截断的单值分解 (SVD) 执行线性维度化简。 与 PCA 相反，此估计器在计算奇异值分解之前不会将数据居中，这意味着它可以有效地使用 scipy.稀疏矩阵 |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 每个样本（即数据矩阵的每一行）至少有一个非零分量，独立于其他样本重新缩放，以便其规范 （l1 或 l2） 等于一个 |

### <a name="advanced-preprocessing-optional-featurization"></a>高级预处理：可选特征化

还提供其他高级预处理和壮举化，如数据护栏、编码和转换。 [详细了解包含哪些特征化](how-to-use-automated-ml-for-ml-models.md#featurization)。 可通过以下方式启用此设置：

+ Azure 机器学习工作室：[使用这些步骤](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)在 **"查看其他配置**"部分中启用**自动任务化**。

+ Python SDK：`"feauturization": 'auto' / 'off' / 'FeaturizationConfig'`为[`AutoMLConfig`类](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)指定 。 

## <a name="classification--regression"></a>分类&回归

分类和回归是最常见的机器学习任务类型。 两者都是监督学习的类型，其中模型使用培训数据学习，并将这些学习应用于新数据。 Azure 机器学习专门针对这些任务提供壮举，例如用于分类的深层神经网络文本函数。 了解有关[壮举选项](how-to-use-automated-ml-for-ml-models.md#featurization)的详细信息。 

分类模型的主要目标是根据培训数据中的学习来预测新数据将属于哪些类别。 常见的分类示例包括欺诈检测、手写识别和对象检测。  了解更多信息，并查看[自动机器学习的分类](tutorial-train-models-with-aml.md)示例。

回归模型不同于预测值为分类的分类，回归模型基于独立预测变量预测数值。 在回归中，目标是通过估计一个变量如何影响其他变量来帮助建立这些独立预测变量之间的关系。 例如，汽车价格基于功能，如汽油里程，安全评级等。 了解更多，并查看[自动机器学习的回归](tutorial-auto-train-models.md)示例。

## <a name="time-series-forecasting"></a>时序预测

构建预测是任何业务不可或缺的一部分，无论是收入、库存、销售还是客户需求。 可以使用自动化 ML 来合并多种技术和方法，获得推荐的高质量时序预测结果。

自动化时序试验被视为多元回归问题。 过去的时间序列值被"透视"，成为回归者与其他预测变量的其他维度。 与传统时序方法不同，这种方法的优点是，在训练过程中自然包含多个上下文变量及其相互关系。 自动化 ML 会针对数据集和预测时间范围内的所有项目，习得通常有内部分支的单个模型。 这样就可以使用更多的数据来估计模型参数，使得未知系列的泛化成为可能。

详细了解[用于时序预测的自动化机器学习](how-to-auto-train-forecast.md)并查看示例。 或者，请参阅[能源需求笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)中的高级预测配置的详细代码示例，包括：

* 假日检测和特征化
* 时序和 DNN 教学器（Auto-ARIMA、Prophet、ForecastTCN）
* 许多模型通过分组支持
* 滚动原点交叉验证
* 可配置滞后
* 滚动窗口聚合特征

## <a name="ensemble-models"></a><a name="ensemble"></a> 系综模型

自动化机器学习支持默认已启用的系综模型。 系综学习通过组合多个模型而不是使用单个模型，来改善机器学习结果和预测性能。 系综迭代显示为运行的最后一个迭代。 自动化机器学习使用投票和堆叠系综方法来组合模型：

* **投票**：根据预测类概率（对于分类任务）或预测回归目标（对于回归任务）的加权平均值进行预测。
* **堆叠**：堆叠方法组合异构的模型，并根据各个模型的输出训练元模型。 当前的默认元模型是 LogisticRegression（对于分类任务）和 ElasticNet（对于回归/预测任务）。

提供排序系综初始化的 [Caruana 系综选择算法](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)用于决定要在系综中使用的模型。 在高级别上，此算法使用最多五个具有最佳个人分数的模型初始化了该集合，并验证这些模型是否在最佳分数的 5% 阈值内，以避免初始集合不佳。 然后，对于每个系综迭代，会将一个新模型添加到现有系综，并计算最终评分。 如果新模型改善了现有的系综评分，则会更新系综以包含新模型。

请参阅[操作指南](how-to-configure-auto-train.md#ensemble)来了解如何在自动化机器学习中更改默认系综设置。

## <a name="use-with-onnx-in-c-apps"></a>与 C# 应用中的 ONNX 配合使用

借助 Azure 机器学习，可以使用自动化 ML 来生成 Python 模型并将其转换为 ONNX 格式。 ONNX 运行时支持 C#，因此，可以在 C# 应用中使用自动生成的模型，而无需重新编写代码，同时可避免 REST 终结点造成的任何网络延迟。 在[此 Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) 中查看此流的示例。

## <a name="automated-ml-in-azure-machine-learning"></a>Azure 机器学习中的自动 ML

Azure 机器学习提供了两种使用自动 ML 的体验

* 对于代码经验丰富的客户[，Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* 对于有限/无代码体验客户，Azure 机器学习工作室[https://ml.azure.com](https://ml.azure.com/)  

下面总结了每种经验中支持的高级别自动 ML 功能。

<a name="parity"></a>

### <a name="experiment-settings"></a>实验设置 

以下设置允许您配置自动 ML 实验。 

| | Python SDK| 演播室
----|:----:|:----:
将数据拆分为训练/验证集| ✓|✓
支持 ML 任务：分类、回归和预测| ✓| ✓
基于主要指标进行优化| ✓| ✓
支持 AML 计算作为计算目标 | ✓|✓
配置预测范围、目标滞后&滚动窗口|✓|✓
设置退出条件 |✓|✓ 
设置并发迭代| ✓|✓
删除列| ✓|✓
块算法|✓|✓
交叉验证 |✓|✓
支持 Azure 数据砖块群集的培训| ✓|
查看工程特征名称|✓|
技术化摘要| ✓|
假日壮举|✓|
日志文件的详细程度级别| ✓|

### <a name="model-settings"></a>型号设置

这些设置可以应用于最佳模型，这是自动 ML 实验的结果。

||Python SDK|演播室
----|:----:|:----:
最佳型号注册| ✓|✓
最佳模型部署| ✓| ✓
最佳模型可解释性| ✓|✓
启用投票组&堆栈集合模型| ✓|✓
基于非主指标显示最佳模型|✓|
启用/禁用 ONNX 模型兼容性|✓|
测试模型 | ✓| |

### <a name="run-control-settings"></a>运行控制设置

这些设置允许您查看和控制实验运行及其子运行。 

||Python SDK| 演播室
----|:----:|:----:
运行摘要表| ✓|✓
取消运行| ✓|✓
取消子运行| ✓| ✓
获取护栏| ✓|✓
暂停运行| ✓| 
恢复运行| ✓| 

## <a name="next-steps"></a>后续步骤

查看示例并了解如何使用自动化机器学习生成模型：

+ 遵循[教程：使用 Azure 机器学习自动训练回归模型](tutorial-auto-train-models.md)

+ 配置自动训练试验的设置：
  + 在 Azure 机器学习工作室中[使用这些步骤](how-to-use-automated-ml-for-ml-models.md)。
  + 在 Python SDK 中[使用这些步骤](how-to-configure-auto-train.md)。

+ 了解如何[使用这些步骤](how-to-auto-train-forecast.md)通过时序数据自动进行训练。

+ 查看[自动化机器学习的 Jupyter Notebook 示例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* 自动 ML 还可用于其他 Microsoft[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)解决方案，如[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)[ML.NET、HDInsight、Power](../hdinsight/spark/apache-spark-run-machine-learning-automl.md) [BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)和 SQL 服务器
