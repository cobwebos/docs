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
ms.openlocfilehash: ce51a1b25453a5bbacbd268b37f2bd21cfe37fea
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983459"
---
# <a name="what-is-automated-machine-learning-automl"></a>什么是自动化机器学习（AutoML）？

自动机器学习（也称为自动 ML ML 或 AutoML）是自动执行机器学习模型开发的耗时的迭代任务的过程。 数据科学家、分析师和开发人员可以使用它来生成高度可缩放、高效且高产能的 ML 模型，同时保证模型的质量。 自动化 ML 基于 [Microsoft Research 部门](https://arxiv.org/abs/1705.05355)的突破性技术。

传统的机器学习模型开发是资源密集型的，需要具备丰富的领域知识，并需要花费大量的时间来生成和比较数十个模型。 使用自动化机器学习可以缩减生成生产就绪型 ML 模型所需的时间，同时使工作变得更轻松、更高效。

## <a name="when-to-use-automl-classify-regression--forecast"></a>何时使用 AutoML：分类、回归、& 预测

想要通过 Azure 机器学习使用指定的目标指标训练和优化模型时，可以运用自动化 ML。 自动化 ML 可将机器学习模型开发过程标准化，并使其用户（无论是否具备数据科学知识）能够在端到端的机器学习管道中识别任何问题。

跨行业的数据科学家、分析人员和开发人员可以使用自动 ML 执行以下操作：
+ 实现 ML 解决方案，而无需广泛的编程知识
+ 节省时间和资源
+ 利用数据科学最佳做法
+ 提供灵活的问题解决方法

### <a name="classification"></a>分类

分类是一种常见的机器学习任务。 分类是一种监督学习，其中的模型使用定型数据进行学习，并将这些知识应用于新数据。 Azure 机器学习为这些任务专门提供 featurizations，例如分类的深层神经网络文本 featurizers。 了解有关[特征化选项](how-to-use-automated-ml-for-ml-models.md#featurization)的详细信息。 

分类模型的主要目标是根据知识的定型数据来预测新数据将归入的类别。 常见分类示例包括欺诈检测、手写识别和对象检测。  了解更多详细信息，并查看[使用自动机器学习进行分类](tutorial-train-models-with-aml.md)的示例。

请参阅以下 Python 笔记本中分类和自动化机器学习的示例：[欺诈检测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)、[营销预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)和[新闻组数据分类](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)

### <a name="regression"></a>回归测试
与分类类似，回归任务也是常见的监督式学习任务。 Azure 机器学习[为这些任务专门提供 featurizations](how-to-use-automated-ml-for-ml-models.md#featurization)。

不同于分类的预测输出值是分类的，回归模型基于独立预测值预测数值输出值。 在回归中，目标是通过估计一个变量如何影响其他变量来帮助建立这些独立预测器变量之间的关系。 例如，基于诸如、气体里程、安全评级等功能的汽车价格。 了解更多详细信息，并查看[自动机器学习的回归](tutorial-auto-train-models.md)示例。

请参阅以下 Python 笔记本中预测的回归和自动化机器学习示例： [CPU 性能预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)、 

### <a name="time-series-forecasting"></a>时序预测

生成预测是任何业务的有机组成部分，无论是收入、库存、销售还是客户需求。 可以使用自动化 ML 来合并多种技术和方法，获得推荐的高质量时序预测结果。 有关时序预测的这一操作方法：[自动化机器学习的](how-to-auto-train-forecast.md)详细信息，请参阅。 

自动化时序试验被视为多元回归问题。 过去的时间序列值将 "透视" 成为回归量与其他预测值的其他维度。 与传统时序方法不同，这种方法的优点是，在训练过程中自然包含多个上下文变量及其相互关系。 自动化 ML 会针对数据集和预测时间范围内的所有项目，习得通常有内部分支的单个模型。 这样就可以使用更多的数据来估计模型参数，使得未知系列的泛化成为可能。

高级预测配置包括：
* 假日检测和特征化
* 时序和 DNN 教学器（Auto-ARIMA、Prophet、ForecastTCN）
* 许多模型都支持通过分组
* 滚动原点交叉验证
* 可配置滞后
* 滚动窗口聚合特征


请参阅以下 Python 笔记本中预测的回归和自动化机器学习示例：[销售预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)、[需求预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)和[饮料生产预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)。

## <a name="how-automl-works"></a>AutoML 的工作原理

在训练过程中，Azure 机器学习会并行创建多个管道，尝试使用不同的算法和参数。 服务循环访问与功能选择配对的 ML 算法，其中每个迭代都生成一个具有定型分数的模型。 模型的评分越高，则认为它可以更好地“拟合”数据。  一旦达到试验中定义的退出条件，机器学习就会停止。 

使用 **Azure 机器学习**可以通过以下步骤设计和运行自动化 ML 训练试验：

1. **识别要解决的 ML 问题**：分类、预测或回归

1. **选择是要使用 PYTHON sdk 还是 studio web 体验**：了解[python sdk 和 studio web 体验](#parity)之间的奇偶校验。

   * 如果有限制或没有代码体验，请尝试在 Azure 机器学习 studio web 体验[https://ml.azure.com](https://ml.azure.com/)  
   * 对于 Python 开发人员，请查看[Azure 机器学习 PYTHON SDK](how-to-configure-auto-train.md) 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]  
    
1. **指定标记的定型数据的源和格式**： Numpy 数组或 Pandas 数据帧

1. **配置模型训练的计算目标**，例如[本地计算机、Azure 机器学习计算、远程 VM 或 Azure Databricks](how-to-set-up-training-targets.md)。  了解如何对[远程资源](how-to-auto-train-remote.md)进行自动训练。

1. **配置自动化机器学习参数**，用于确定要对不同模型运行的迭代次数、超参数设置、高级预处理/特征化，以及在确定最佳模型时要查看的指标。  
1. **提交训练运行。**

1. **查看结果** 

下图对此过程进行了说明： 
![自动机器学习](./media/concept-automated-ml/automl-concept-diagram2.png)


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

|缩放和规范化| 说明 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 通过删除平均值并缩放到单位差异来标准化特征  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 通过按列的最小值和最大值缩放每个特征来转换特征  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |按特征的最大绝对值缩放每个特征 |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |按特征的分位数范围缩放特征 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |使用数据的单值分解进行线性维度化简，以将其投影到低维空间 |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |此转换器通过截断的单值分解 (SVD) 执行线性维度化简。 与 PCA 相反，此估计器在计算单数值分解之前不会将数据居中，这意味着它可以有效地使用 scipy 矩阵 |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 每个示例（即，数据矩阵的每个行）至少具有一个非零的组件，因此它的标准（l1 或 l2）等于一个重新缩放 |

### <a name="advanced-preprocessing--featurization"></a>高级预处理 & 特征化

还提供了其他高级预处理和特征化，例如 data guardrails、encoding 和转换。 [详细了解包含哪些特征化](how-to-use-automated-ml-for-ml-models.md#featurization)。 可通过以下方式启用此设置：

+ Azure 机器学习 studio：在 "**查看附加配置**" 部分中[使用以下步骤](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)启用**自动特征化**。

+ Python SDK：为`"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` [ `AutoMLConfig`类](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)指定。 



## <a name="ensemble-models"></a><a name="ensemble"></a> 系综模型

自动化机器学习支持默认已启用的系综模型。 系综学习通过组合多个模型而不是使用单个模型，来改善机器学习结果和预测性能。 系综迭代显示为运行的最后一个迭代。 自动化机器学习使用投票和堆叠系综方法来组合模型：

* **投票**：根据预测类概率（对于分类任务）或预测回归目标（对于回归任务）的加权平均值进行预测。
* **堆叠**：堆叠方法组合异构的模型，并根据各个模型的输出训练元模型。 当前的默认元模型是 LogisticRegression（对于分类任务）和 ElasticNet（对于回归/预测任务）。

提供排序系综初始化的 [Caruana 系综选择算法](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)用于决定要在系综中使用的模型。 从较高层次来看，此算法使用最多5个模型初始化系综，该模型最多可获得最佳分数，并验证这些模型是否处于最佳分数的5% 阈值内，以避免初始系综不佳。 然后，对于每个系综迭代，会将一个新模型添加到现有系综，并计算最终评分。 如果新模型改善了现有的系综评分，则会更新系综以包含新模型。

请参阅[操作指南](how-to-configure-auto-train.md#ensemble)来了解如何在自动化机器学习中更改默认系综设置。

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>有关本地和远程托管 ML 计算目标的指南

自动 ML 的 web 界面始终使用远程[计算目标](concept-compute-target.md)。  但在使用 Python SDK 时，将为自动 ML 培训选择本地计算或远程计算目标。

* **本地计算**：在本地便携式计算机或 VM 计算上进行定型。 
* **远程计算**：机器学习计算群集上进行定型。  

### <a name="choose-compute-target"></a>选择计算目标
选择计算目标时，请考虑以下因素：

 * **选择本地计算**：如果你的方案涉及到使用小数据和简短培训（即，每个子运行的秒数或几分钟）的探索，则本地计算机上的培训可能是更好的选择。  没有设置时间，基础结构资源（您的 PC 或 VM）可直接使用。
 * **选择远程 ML 计算群集**：如果你要使用较大的数据集（例如在生产培训中创建需要更长训练的模型）进行培训，则远程计算将提供更好`AutoML`的端到端时间性能，因为将在群集的节点上并行进行定型。 在远程计算上，内部基础结构的启动时间大约增加了每个子运行1.5 分钟，还增加了群集基础结构的分钟数（如果尚未启动并运行这些 Vm）。

### <a name="pros-and-cons"></a>优点和缺点
选择使用本地与远程时，请考虑这些优点和缺点。

|  | 优点（优点）  |缺点（Handicaps）  |
|---------|---------|---------|---------|
|**本地计算目标** |  <li> 无环境启动时间   | <li>  功能子集<li>  无法并行化运行 <li> 对于大型数据更糟糕。 <li>培训时无数据流 <li>  没有基于 DNN 的特征化 <li> 仅 Python SDK |
|**远程 ML 计算群集**|  <li> 完整的功能集 <li> 并行化子运行 <li>   大数据支持<li>  基于 DNN 的特征化 <li>  根据需要动态缩放计算群集 <li> 还提供无代码体验（web UI）  |  <li> 启动群集节点的时间 <li> 每个子运行的启动时间    |

### <a name="feature-availability"></a>功能可用性 

 使用远程计算时，有更多的功能可用，如下表所示。 其中一些功能仅在企业工作区中可用。

| 功能                                                    | 远程 | 本地 | 需要 <br>企业工作区 |
|------------------------------------------------------------|--------|-------|-------------------------------|
| 数据流（大数据支持，高达 100 GB）          | ✓      |       | ✓                             |
| DNN-基于经理 BERT 的文本特征化和培训             | ✓      |       | ✓                             |
| 全新 GPU 支持（培训和推理）        | ✓      |       | ✓                             |
| 图像分类和标签支持                  | ✓      |       | ✓                             |
| 用于预测的自动 ARIMA、Prophet 和 ForecastTCN 模型 | ✓      |       | ✓                             |
| 并行多个运行/迭代                       | ✓      |       | ✓                             |
| 在 AutoML studio web 体验 UI 中创建具有 interpretability 的模型      | ✓      |       | ✓                             |
| Studio web 体验 UI 中的功能设计自定义                        | ✓      |       | ✓                              |
| Azure ML 超参数优化                             | ✓      |       |                               |
| Azure ML 管道工作流支持                         | ✓      |       |                               |
| 继续运行                                             | ✓      |       |                               |
| 预测                                                | ✓      | ✓     | ✓                             |
| 在笔记本中创建和运行试验                    | ✓      | ✓     |                               |
| 在 UI 中注册和可视化试验的信息和度量值 | ✓      | ✓     |                               |
| 数据 guardrails                                            | ✓      | ✓     |                               |


## <a name="automated-ml-in-azure-machine-learning"></a>Azure 机器学习中的自动 ML

Azure 机器学习提供了两种使用自动 ML 的体验

* 对于经验丰富的客户， [Azure 机器学习 PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* 对于有限/无代码体验客户，Azure 机器学习 studio[https://ml.azure.com](https://ml.azure.com/)  

<a name="parity"></a>

### <a name="experiment-settings"></a>试验设置 

以下设置允许配置自动 ML 试验。 

| |Python SDK|Studio web 体验|
----|:----:|:----:
将数据拆分为定型/验证集| ✓|✓
支持 ML 任务：分类、回归和预测| ✓| ✓
基于主要指标进行优化| ✓| ✓
支持将 AML 计算作为计算目标 | ✓|✓
配置预测范围，目标滞后 & 滚动窗口|✓|✓
设置退出条件 |✓|✓ 
设置并发迭代| ✓|✓
删除列| ✓|✓
块算法|✓|✓
交叉验证 |✓|✓
支持 Azure Databricks 群集的培训| ✓|
查看工程特征名称|✓|
特征化摘要| ✓|
特征化假日|✓|
日志文件详细级别| ✓|

### <a name="model-settings"></a>模型设置

这些设置可作为自动 ML 试验的结果应用于最佳模型。

| |Python SDK|Studio web 体验|
|----|:----:|:----:|
|最佳模型注册、部署、explainability| ✓|✓|
|启用投票系综 & stack 系综模型| ✓|✓|
|基于非主要指标显示最佳模型|✓||
|启用/禁用 ONNX 模型兼容性|✓||
|测试模型 | ✓| |

### <a name="run-control-settings"></a>运行控件设置

通过这些设置，你可以查看和控制实验运行及其子运行。 

| |Python SDK|Studio web 体验|
|----|:----:|:----:|
|运行摘要表| ✓|✓|
|取消运行 & 子运行| ✓|✓|
|获取 guardrails| ✓|✓|
|暂停 & 恢复运行| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

借助 Azure 机器学习，可以使用自动化 ML 来生成 Python 模型并将其转换为 ONNX 格式。 模型采用 ONNX 格式后，可在各种平台和设备上运行。 详细了解如何[利用 ONNX 加速 ML 模型](concept-onnx.md)。

请参阅如何转换为[ONNX 格式。](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) 了解[ONNX 中受支持的算法](how-to-configure-auto-train.md#select-your-experiment-type)。

ONNX 运行时还支持 c #，因此你可以使用 c # 应用程序中自动生成的模型，而无需进行任何编码或 REST 终结点引入的任何网络延迟。 [通过 ONNX 运行时 c # API 了解有关推断 ONNX 模型的](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md)详细信息。 

## <a name="next-steps"></a>后续步骤

查看示例并了解如何使用自动化机器学习生成模型：

+ 配置自动训练试验的设置：
  + 在 Azure 机器学习工作室中[使用这些步骤](how-to-use-automated-ml-for-ml-models.md)。
  + 在 Python SDK 中[使用这些步骤](how-to-configure-auto-train.md)。

+ 了解如何使用[远程计算目标](how-to-auto-train-remote.md)

+ 按照[教程操作：使用 Azure 机器学习自动为回归模型定型](tutorial-auto-train-models.md) 

+ 了解如何[使用这些步骤](how-to-auto-train-forecast.md)通过时序数据自动进行训练。

+ 查看[自动化机器学习的 Jupyter Notebook 示例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
* 自动 ML 还可用于其他 Microsoft 解决方案，如[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)、 [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md)、 [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)和[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
