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
ms.date: 04/22/2020
ms.openlocfilehash: f328b86d07a997ea761b4381f1d6a2f8a1dae269
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683077"
---
# <a name="what-is-automated-machine-learning-automl"></a>什么是自动化机器学习 (AutoML)？

自动化机器学习也称为自动化 ML 或 AutoML，是将机器学习模型开发过程中耗时的反复性任务自动化的过程。 数据科学家、分析师和开发人员可以使用它来生成高度可缩放、高效且高产能的 ML 模型，同时保证模型的质量。 自动化 ML 基于 [Microsoft Research 部门](https://arxiv.org/abs/1705.05355)的突破性技术。

传统的机器学习模型开发是资源密集型的，需要具备丰富的领域知识，并需要花费大量的时间来生成和比较数十个模型。 使用自动化机器学习可以缩减生成生产就绪型 ML 模型所需的时间，同时使工作变得更轻松高效。

## <a name="when-to-use-automl-classify-regression--forecast"></a>何时使用 AutoML：分类、回归和预测

想要通过 Azure 机器学习使用指定的目标指标训练和优化模型时，可以运用自动化 ML。 自动化 ML 可将机器学习模型开发过程标准化，并使其用户（无论是否具备数据科学知识）能够在端到端的机器学习管道中识别任何问题。

跨行业的数据科学家、分析师和开发人员可以使用自动化 ML 来实现以下目的：
+ 无需丰富的编程知识，即可实现机器学习解决方案
+ 节省时间和资源
+ 利用数据科学最佳做法
+ 提供灵活的问题解决方法

### <a name="classification"></a>分类

分类是一个常见的机器学习任务。 分类是一种监督式学习，其中的模型使用训练数据进行学习，并将学习所得应用于新数据。 Azure 机器学习为这些任务专门提供特征化，例如用于分类的深度神经网络文本特征化器。 详细了解[特征化选项](how-to-use-automated-ml-for-ml-models.md#featurization)。 

分类模型的主要目标是根据从训练数据习得的内容来预测新数据属于哪些类别。 常见分类的示例包括欺诈检测、手写识别和对象检测。  详细了解[使用自动化机器学习的分类](tutorial-train-models-with-aml.md)并查看其示例。

参阅以下 Python 笔记本中的分类和自动化机器学习示例：[欺诈检测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)、[营销预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)和[新闻组数据分类](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)

### <a name="regression"></a>回归
类似于分类，回归任务也是常见的监督式学习任务。 Azure 机器学习[专门为这些任务提供特征化](how-to-use-automated-ml-for-ml-models.md#featurization)。

不同于分类（其中的预测输出值是分类的），回归模型基于独立的预测器预测数字输出值。 在回归中，目标是通过评估变量的相互影响，在这些独立的预测器变量之间建立关系。 例如，基于里程油耗、安全评级等特征预测汽车价格。 详细了解[使用自动化机器学习的回归](tutorial-auto-train-models.md)并查看其示例。

参阅以下 Python 笔记本中用于预测的回归和自动化机器学习示例：[CPU 性能预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb) 

### <a name="time-series-forecasting"></a>时序预测

生成预测是任何业务（无论是收入、库存、销售还是客户需求）中不可或缺的组成部分。 可以使用自动化 ML 来合并多种技术和方法，获得推荐的高质量时序预测结果。 在以下操作指南中了解详细信息：[用于时序预测的自动化机器学习](how-to-auto-train-forecast.md)。 

自动化时序试验被视为多元回归问题。 将“透视”过去的时序值，使其成为回归量与其他预测器的附加维度。 与传统时序方法不同，这种方法的优点是，在训练过程中自然包含多个上下文变量及其相互关系。 自动化 ML 会针对数据集和预测时间范围内的所有项目，习得通常有内部分支的单个模型。 这样就可以使用更多的数据来估计模型参数，使得未知系列的泛化成为可能。

高级预测配置包括：
* 假日检测和特征化
* 时序和 DNN 教学器（Auto-ARIMA、Prophet、ForecastTCN）
* 通过分组实现的多模型支持
* 滚动原点交叉验证
* 可配置滞后
* 滚动窗口聚合特征


参阅以下 Python 笔记本中用于预测的回归和自动化机器学习示例：[销售预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)、[需求预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)和[饮料生产预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)。

## <a name="how-automl-works"></a>AutoML 的工作原理

在训练期间，Azure 机器学习会创建多个尝试不同算法和参数的并行管道。 该服务将迭代与特征选择配对的 ML 算法，每次迭代都会生成带有训练评分的模型。 模型的评分越高，则认为它可以更好地“拟合”数据。  一旦达到试验中定义的退出条件，机器学习就会停止。 

使用 **Azure 机器学习**可以通过以下步骤设计和运行自动化 ML 训练试验：

1. **识别要解决的 ML 问题**：分类、预测或回归

1. **选择是要使用 Python SDK 还是工作室 Web 体验**：了解 [Python SDK 与工作室 Web 体验](#parity)之间的搭配用法。

   * 如果你的编程经验有限或者没有经验，请在 [https://ml.azure.com](https://ml.azure.com/) 上尝试使用 Azure 机器学习工作室 Web 体验  
   * Python 开发人员请查看 [Azure 机器学习 Python SDK](how-to-configure-auto-train.md) 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]  
    
1. **指定已标记训练数据的源和格式**：Numpy 数组或 Pandas 数据帧

1. **配置模型训练的计算目标**，例如[本地计算机、Azure 机器学习计算、远程 VM 或 Azure Databricks](how-to-set-up-training-targets.md)。  了解如何[对远程资源](how-to-auto-train-remote.md)进行自动训练。

1. **配置自动化机器学习参数**，用于确定要对不同模型运行的迭代次数、超参数设置、高级预处理/特征化，以及在确定最佳模型时要查看的具体指标。  
1. **提交训练运行。**

1. **查看结果** 

下图演示了此过程。 
![自动化机器学习](./media/concept-automated-ml/automl-concept-diagram2.png)


还可以检查记录的运行信息，其中[包含的指标](how-to-understand-automated-ml.md)是在运行期间收集的。 训练运行会生成一个包含模型和数据预处理的 Python 序列化对象（`.pkl` 文件）。

模型生成是自动化的，同时，你也可以[了解特征对于生成的模型而言如何重要或者彼此相关](how-to-configure-auto-train.md#explain)。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>预处理

在每个自动化机器学习试验中，数据将使用默认方法进行预处理，有时还会通过高级预处理技术进行预处理。

> [!NOTE]
> 自动机器学习预处理步骤（特征规范化、处理缺失数据，将文本转换为数字等）成为基础模型的一部分。 使用模型进行预测时，训练期间应用的相同预处理步骤将自动应用于输入数据。

### <a name="automatic-preprocessing-standard"></a>自动预处理（标准）

在每个自动化机器学习试验中，数据将自动缩放或规范化，以帮助确保算法的良好性能。  在模型训练过程中，将对每个模型应用以下缩放或规范化技术之一。 了解 autoML 如何帮助[防止模型中出现过度拟合与数据不平衡](concept-manage-ml-pitfalls.md)。

|缩放和规范化| 说明 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 通过删除平均值并缩放到单位方差来标准化特征  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 通过按该列的最小值和最大值缩放每个特征来转换特征  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |按特征的最大绝对值缩放每个特征 |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |按特征的分位数范围缩放特征 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |使用数据的单值分解进行线性维度化简，以将其投影到低维空间 |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |此转换器通过截断的单值分解 (SVD) 执行线性维度化简。 与 PCA 相反，此估算器在计算单值分解之前不会将数据居中，这意味着它可以有效地处理 scipy.sparse 矩阵 |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 重新缩放至少包含一个非零成分的每个样本（即，数据矩阵的每个行），而不管其他样本如何，使其范数（l1 或 l2）等于 1 |

### <a name="advanced-preprocessing--featurization"></a>高级预处理和特征化

还可以使用其他高级预处理和特征化，例如数据护栏、编码和转换。 [详细了解包含哪些特征化](how-to-use-automated-ml-for-ml-models.md#featurization)。 可通过以下方式启用此设置：

+ Azure 机器学习工作室：[使用这些步骤](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)在“查看其他配置”部分中启用“自动特征化” 。

+ Python SDK：为 [`AutoMLConfig` 类](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)指定 `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'`。 



## <a name="ensemble-models"></a><a name="ensemble"></a> 系综模型

自动化机器学习支持默认已启用的系综模型。 系综学习通过组合多个模型而不是使用单个模型，来改善机器学习结果和预测性能。 系综迭代显示为运行的最后一个迭代。 自动化机器学习使用投票和堆叠系综方法来组合模型：

* **投票**：根据预测类概率（针对分类任务）或预测回归目标（针对回归任务）的加权平均值进行预测。
* **堆叠**：堆叠方法组合异构的模型，并根据各个模型的输出训练元模型。 当前的默认元模型是 LogisticRegression（针对分类任务）和 ElasticNet（针对回归/预测任务）。

提供排序系综初始化的 [Caruana 系综选择算法](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)用于决定要在系综中使用的模型。 从较高层面看，此算法使用个体评分最高的最多五个模型来初始化系综，并验证这些模型是否在最佳评分的 5% 阈值范围内，以避免初始系综不佳。 然后，对于每个系综迭代，会将一个新模型添加到现有系综，并计算最终评分。 如果新模型改善了现有的系综评分，则会更新系综以包含新模型。

请参阅[操作指南](how-to-configure-auto-train.md#ensemble)，了解如何在自动化机器学习中更改默认系综设置。

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>有关本地和远程托管 ML 计算目标的指导

自动化 ML 的 Web 界面始终使用远程[计算目标](concept-compute-target.md)。  但使用 Python SDK 时，可以选择本地计算或远程计算目标进行自动化 ML 训练。

* **本地计算**：训练在本地便携式计算机或 VM 计算中发生。 
* **远程计算**：训练在机器学习计算群集中发生。  

### <a name="choose-compute-target"></a>选择计算目标
选择计算目标时请考虑以下因素：

 * **选择本地计算**：如果你的方案涉及到使用小数据和短训练（即，每个子运行持续几秒或几分钟）进行初始探索或演示，则可能更适合在本地计算机上进行训练。  这样就无需进行设置，并且可以直接使用基础结构资源（电脑或 VM）。
 * **选择远程 ML 计算群集**：如果使用较大的数据集进行训练（例如，在生产训练中创建需要较长时间训练的模型），则远程计算可以提供好得多的端到端时间性能，因为 `AutoML` 会在群集节点之间并行化训练。 在远程计算上，内部基础结构的启动时间大约会根据每个子运行增加 1.5 分钟，如果 VM 尚未启动并运行，则群集基础结构的启动时间也会额外增加几分钟。

### <a name="pros-and-cons"></a>优点和缺点
选择是要使用本地还是远程计算时，请考虑两者的以下优点和缺点。

|  | 优点（优势）  |缺点（劣势）  |
|---------|---------|---------|---------|
|**本地计算目标** |  <li> 无需花费时间来启动环境   | <li>  特征子集<li>  无法并行化运行 <li> 对于大数据表现较差。 <li>训练时无数据流式处理 <li>  没有基于 DNN 的特征化 <li> 仅限 Python SDK |
|**远程 ML 计算群集**|  <li> 完整的特征集 <li> 并行化子运行 <li>   大数据支持<li>  基于 DNN 的特征化 <li>  计算群集的按需动态可伸缩性 <li> 还提供无代码体验 (Web UI)  |  <li> 需要花费时间来启动群集节点 <li> 需要花费时间来启动每个子运行    |

### <a name="feature-availability"></a>功能可用性 

 使用远程计算时，有更多的功能可用，如下表中所示。 其中一些功能仅在企业工作区中可用。

| Feature                                                    | Remote | Local | 需要 <br>企业工作区 |
|------------------------------------------------------------|--------|-------|-------------------------------|
| 数据流式处理（最高 100 GB 的大数据支持）          | ✓      |       | ✓                             |
| 基于 DNN-BERT 的文本特征化和训练             | ✓      |       | ✓                             |
| 现成的 GPU 支持（训练和推理）        | ✓      |       | ✓                             |
| 图像分类和标记支持                  | ✓      |       | ✓                             |
| 用于预测的 Auto-ARIMA、Prophet 和 ForecastTCN 模型 | ✓      |       | ✓                             |
| 并行执行多个运行/迭代                       | ✓      |       | ✓                             |
| 在 AutoML 工作室 Web 体验 UI 中创建具有可解释性的模型      | ✓      |       | ✓                             |
| 工作室 Web 体验 UI 中的特征工程自定义                        | ✓      |       | ✓                              |
| Azure ML 超参数优化                             | ✓      |       |                               |
| Azure ML 管道工作流支持                         | ✓      |       |                               |
| 继续运行                                             | ✓      |       |                               |
| 预测                                                | ✓      | ✓     | ✓                             |
| 在笔记本中创建和运行试验                    | ✓      | ✓     |                               |
| 在 UI 中注册和可视化试验的信息与指标 | ✓      | ✓     |                               |
| 数据护栏                                            | ✓      | ✓     |                               |

## <a name="many-models"></a>多模型 

[多模型解决方案加速器](https://aka.ms/many-models)（预览版）构建在 Azure 机器学习的基础之上，可让你使用自动化 ML 来训练、操作和管理数百甚至数千个机器学习模型。

例如，在下面的方案中为每个实例或个体生成模型可以改善结果：

* 预测每家店铺的销售额
* 对数百口油井进行预测性维护
* 为个人用户定制体验。

有关详细信息，请参阅 GitHub 上的[多模型解决方案加速器](https://aka.ms/many-models)。

## <a name="automl-in-azure-machine-learning"></a>Azure 机器学习中的 AutoML

Azure 机器学习提供两个用于操作自动化 ML 的体验

* 编程经验丰富的客户可以使用 [Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* 编程经验有限/无经验的客户可以使用 [https://ml.azure.com](https://ml.azure.com/) 上的 Azure 机器学习工作室  

<a name="parity"></a>

### <a name="experiment-settings"></a>试验设置 

可以使用以下设置来配置自动化 ML 试验。 

| |Python SDK|工作室 Web 体验|
----|:----:|:----:
将数据拆分为训练/验证集| ✓|✓
支持 ML 任务：分类、回归和预测| ✓| ✓
基于主要指标进行优化| ✓| ✓
支持将 AML 计算用作计算目标 | ✓|✓
配置预测时间范围、目标滞后和滚动窗口|✓|✓
设置退出条件 |✓|✓ 
设置并发迭代数| ✓|✓
删除列| ✓|✓
阻止算法|✓|✓
交叉验证 |✓|✓
支持在 Azure Databricks 群集上训练| ✓|
查看工程特征名称|✓|
特征化摘要| ✓|
假日特征化|✓|
日志文件详细级别| ✓|

### <a name="model-settings"></a>模型设置

可将这些设置应用到自动化 ML 试验生成的最佳模型。

| |Python SDK|工作室 Web 体验|
|----|:----:|:----:|
|最佳模型注册、部署、可解释性| ✓|✓|
|启用投票系综和堆栈系综模型| ✓|✓|
|基于非主要指标显示最佳模型|✓||
|启用/禁用 ONNX 模型兼容性|✓||
|测试模型 | ✓| |

### <a name="run-control-settings"></a>运行控制设置

使用这些设置可以查看和控制试验运行及其子运行。 

| |Python SDK|工作室 Web 体验|
|----|:----:|:----:|
|运行摘要表| ✓|✓|
|取消运行和子运行| ✓|✓|
|获取护栏| ✓|✓|
|暂停和恢复运行| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML 和 ONNX

借助 Azure 机器学习，可以使用自动化 ML 来生成 Python 模型并将其转换为 ONNX 格式。 在模型采用 ONNX 格式后，可以在各种平台和设备上运行这些模型。 详细了解如何[使用 ONNX 加速 ML 模型](concept-onnx.md)。

[在此 Jupyter 笔记本示例中](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)了解如何转换为 ONNX 格式。 了解 [ONNX 支持的算法](how-to-configure-auto-train.md#select-your-experiment-type)。

ONNX 运行时还支持 C#。因此，你可以在 C# 应用中使用自动生成的模型，而无需重新编写代码，同时可避免 REST 终结点造成的任何网络延迟。 详细了解如何[使用 ONNX 运行时 C# API 推理 ONNX 模型](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md)。 

## <a name="next-steps"></a>后续步骤

查看示例并了解如何使用自动化机器学习来生成模型：

+ 配置自动训练试验的设置：
  + 在 Azure 机器学习工作室中[使用这些步骤](how-to-use-automated-ml-for-ml-models.md)。
  + 在 Python SDK 中[使用这些步骤](how-to-configure-auto-train.md)。

+ 了解如何使用[远程计算目标](how-to-auto-train-remote.md)

+ 遵循[教程：使用 Azure 机器学习自动训练回归模型](tutorial-auto-train-models.md) 

+ 了解如何[使用这些步骤](how-to-auto-train-forecast.md)通过时序数据自动进行训练。

+ 尝试学习[自动化机器学习的 Jupyter Notebook 示例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
* 自动化 ML 也可用于其他 Microsoft 解决方案，例如 [ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)、[HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md)、[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) 和 [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
