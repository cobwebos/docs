---
title: 什么是自动 ML ML/automl
titleSuffix: Azure Machine Learning service
description: 了解 Azure 机器学习服务如何自动选取算法，以及如何通过它来生成模型，以便使用你提供的参数和条件为模型选择最佳算法，从而节省时间。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: 064fd0e2bf503d917c809aa576bbc332b5b18a77
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742358"
---
# <a name="what-is-automated-machine-learning"></a>什么是自动化机器学习？

自动机器学习 (也称为 autoML) 是自动执行机器学习模型开发的耗时的迭代任务的过程。 它允许数据科学家、分析人员和开发人员构建具有高缩放性、效率和生产力的 ML 模型, 同时维持模型质量。

传统的机器学习模型开发需要大量资源, 需要大量的域知识和时间来生成和比较数十个模型。 如果希望 Azure 机器学习使用指定的目标度量值定型和调整模型, 请应用自动 ML。 然后, 该服务会循环访问与功能选择配对的 ML 算法, 其中, 每次迭代都会生成一个包含定型分数的模型。 分数越高, 模型被视为 "拟合" 数据就越好。

使用自动化机器学习, 你将加快获取生产就绪 ML 模型所需的时间, 使其更轻松、更高效。

## <a name="when-to-use-automated-ml"></a>何时使用自动 ML

自动 ML 大众化机器学习模型开发过程, 并使其用户无论其数据科学知识, 都可以识别出任何问题的端到端机器学习管道。

跨行业的数据科学家、分析人员和开发人员可以使用自动 ML 执行以下操作:

+ 无需大量编程知识即可实现机器学习解决方案
+ 节省时间和资源
+ 利用数据科学最佳做法
+ 提供灵活的问题解决

## <a name="how-automated-ml-works"></a>自动 ML ML 的工作方式

使用**Azure 机器学习服务**, 可以使用以下步骤来设计和运行自动 ML 培训试验:

1. **确定要解决的 ML 问题**: 分类、预测或回归

1. **指定标记的定型数据的源和格式**:Numpy 数组或 Pandas 数据帧

1. **为模型定型配置计算目标**, 如[本地计算机、Azure 机器学习计算、远程 vm 或 Azure Databricks](how-to-set-up-training-targets.md)。  了解有关[远程资源](how-to-auto-train-remote.md)的自动化培训。

1. **配置自动机器学习参数**, 该参数确定不同模型上的迭代数、超参数设置、高级预处理/特征化和确定最佳模型时要查看的指标。  可以[在 Azure 门户](how-to-create-portal-experiments.md)或[SDK](how-to-configure-auto-train.md)中配置自动训练试验的设置。

1. **提交训练运行。**

  ![自动机器学习](./media/how-to-automated-ml/automl-concept-diagram2.png)

在训练过程中, Azure 机器学习服务会创建多个并行管道, 尝试使用不同的算法和参数。 一旦到达试验中定义的退出条件, 它就会停止。

你还可以检查记录的运行信息, 其中[包含](how-to-understand-automated-ml.md)在运行期间收集的指标。 训练运行会生成一个 Python 序列化对象`.pkl` (文件), 其中包含模型和数据预处理。

虽然模型生成是自动进行的, 但您也可以了解生成的模型的[重要或相关功能](how-to-configure-auto-train.md#explain)。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>预处理

在每个自动机器学习试验中, 均使用默认方法对数据进行预处理, 并可选择通过高级预处理进行预处理。

### <a name="automatic-preprocessing-standard"></a>自动预处理 (标准)

在每个自动机器学习试验中, 数据会自动缩放或规范化, 以帮助算法正常执行。  在模型定型过程中, 将对每个模型应用以下缩放或规范化技术之一。

|缩放&nbsp;标准化&&nbsp;| 描述 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 通过删除平均值并缩放到单位差异来实现功能标准化  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 通过按列的最小值和最大值缩放每个特征来转换特征  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |按功能的最大绝对值缩放每个功能 |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |此 Scaler 功能的分位范围 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |使用数据的单数值分解缩减线性维数, 以将数据投影到较低的空间 |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |此转换器通过截断的单值分解 (SVD) 来执行线性维度缩减。 与 PCA 相反, 此估计器在计算单数值分解之前不会将数据居中。 这意味着它可以有效地使用 scipy 矩阵 |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 具有至少一个非零分量的每个示例 (即, 数据矩阵的每个行) 都独立于其他示例进行了调整, 以便其标准 (l1 或 l2) 等于1 |

### <a name="advanced-preprocessing-optional-featurization"></a>高级预处理: 可选特征化

还提供了其他高级预处理和特征化, 例如缺失值插补法、编码和转换。 [详细了解所包含的特征化](how-to-create-portal-experiments.md#preprocess)。 启用此设置, 使用:

+ Azure 门户：在[这些步骤](how-to-create-portal-experiments.md)中, 选择 "**高级设置**" 中的 "**预处理**" 复选框。

+ Python SDK:为类指定`"preprocess": True` 。 [ `AutoMLConfig` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)


## <a name="time-series-forecasting"></a>时序预测
生成预测是任何业务的有机组成部分, 无论是收入、库存、销售还是客户需求。 你可以使用自动 ML 来合并技术和方法, 并获得推荐的高质量时序预测。

自动时间系列实验被视为多元回归问题。 过去的时间序列值将 "透视" 成为回归量与其他预测值的其他维度。 与传统时序方法不同, 这种方法的优点是, 在定型过程中自然包含多个上下文变量以及它们之间的关系。 自动 ML 会为数据集中的所有项目学习单个但通常在内部分支的模型, 并预测视野。 这样就可以使用更多的数据来估计模型参数, 并使其成为不可见系列。

了解更多详细信息, 并查看[时序预测自动机器学习](how-to-auto-train-forecast.md)的示例。

## <a name="ensemble"></a>系综模型

自动机器学习支持默认情况下启用的系综模型。 系综学习通过组合多个模型 (而不是使用单个模型) 来改进机器学习结果和预测性能。 系综迭代作为运行的最后一个迭代显示。 自动机器学习使用投票和堆栈系综方法来合并模型:

* **投票**: 根据预测类概率的加权平均值 (适用于分类任务) 或预测回归目标 (适用于回归任务) 进行预测。
* **堆栈**: 堆栈结合了异类模型, 并根据各个模型的输出定型了元模型。 当前默认的元模型 LogisticRegression 用于分类任务, ElasticNet 用于回归/预测任务。

使用已排序的系综初始化的[Caruana 系综选择算法](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)用于决定要在系综中使用的模型。 从较高层次来看, 此算法使用最多5个模型初始化系综, 其中最多有5个模型, 并验证这些模型是否处于最佳分数的 5% 阈值内, 以避免初始系综不佳。 然后, 对于每个系综迭代, 会将一个新模型添加到现有系综, 并计算生成的分数。 如果新模型改进了现有的系综分数, 则会更新系综以包含新模型。

请参阅[如何](how-to-configure-auto-train.md#ensemble)在自动机器学习中更改默认系综设置。

## <a name="use-with-onnx-in-c-apps"></a>在应用中C#使用 with ONNX

使用 Azure 机器学习, 可以使用自动 ML 来构建 Python 模型, 并将其转换为 ONNX 格式。 ONNX 运行时支持C#, 因此你可以使用在C#应用程序中自动生成的模型, 而无需进行无编码或 REST 终结点引入的任何网络延迟。 [在此 Jupyter 笔记本中](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb)试用此流的示例。

## <a name="automated-ml-across-microsoft"></a>跨 Microsoft 自动 ML

自动 ML 还可用于其他 Microsoft 解决方案, 如:

|集成|描述|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|使用 Visual Studio 和 Visual Studio Code 与 ML.NET 自动 ML (预览版) 配合使用的 .NET 应用中的自动模型选择和培训。|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|并行在 HDInsight 群集中的 Spark 上横向扩展自动 ML 培训作业。|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|直接在 Power BI (预览版) 中调用机器学习模型。|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|在 SQL Server 2019 大数据群集中通过数据创建新的机器学习模型。|

## <a name="next-steps"></a>后续步骤

请参阅示例并了解如何使用自动机器学习来构建模型:

+ [按照教程操作:使用 Azure 自动化机器学习自动为回归模型定型](tutorial-auto-train-models.md)

+ 配置自动定型试验的设置:
  + 在 Azure 门户接口中, 请[使用以下步骤](how-to-create-portal-experiments.md)。
  + 使用 Python SDK 时, 请[使用以下步骤](how-to-configure-auto-train.md)。

+ 若要了解如何使用时序数据自动定型, 请[使用以下步骤](how-to-auto-train-forecast.md)。

+ 试用[自动机器学习 Jupyter Notebook 示例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
