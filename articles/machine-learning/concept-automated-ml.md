---
title: 什么是自动 ML ML/AutoML
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习如何自动为你选择算法，并使用提供的参数和标准为你的模型选择最佳算法，从而为你节省时间，从而为你节省时间。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 02/28/2020
ms.openlocfilehash: 501158ffa8d05bc34dd39c21680012b1f3308def
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127203"
---
# <a name="what-is-automated-machine-learning"></a>什么是自动化机器学习？

自动机器学习，也称为自动 ML，是自动执行机器学习模型开发的耗时、迭代任务的过程。 它允许数据科学家、分析人员和开发人员构建具有高缩放性、效率和生产力的 ML 模型，同时维持模型质量。 自动 ML 基于我们的[Microsoft 研究部门](https://arxiv.org/abs/1705.05355)的突破。

传统的机器学习模型开发需要大量资源，需要大量的域知识和时间来生成和比较数十个模型。 如果希望 Azure 机器学习使用指定的目标度量值定型和调整模型，请应用自动 ML。 然后，该服务会循环访问与功能选择配对的 ML 算法，其中，每次迭代都会生成一个包含定型分数的模型。 分数越高，模型被视为 "拟合" 数据就越好。

使用自动化机器学习，你将加快获取生产就绪 ML 模型所需的时间，使其更轻松、更高效。

## <a name="when-to-use-automated-ml"></a>何时使用自动 ML

自动 ML 大众化机器学习模型开发过程，并使其用户无论其数据科学知识，都可以识别出任何问题的端到端机器学习管道。

跨行业的数据科学家、分析人员和开发人员可以使用自动 ML 执行以下操作：

+ 无需大量编程知识即可实现机器学习解决方案
+ 节省时间和资源
+ 利用数据科学最佳做法
+ 提供灵活的问题解决

下表列出了常见的自动 ML 用例。 

分类| 时序预测 | 回归
---|---|---
[欺诈检测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[销售预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[CPU 性能预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[市场营销预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[需求预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[新闻组数据分类](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[饮料生产预测](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="how-automated-ml-works"></a>自动 ML ML 的工作方式

使用**Azure 机器学习**，可以使用以下步骤来设计和运行自动 ML 培训试验：

1. **确定要解决的 ML 问题**：分类、预测或回归

1. **指定标记的定型数据的源和格式**： Numpy 数组或 Pandas 数据帧

1. **为模型定型配置计算目标**，如[本地计算机、Azure 机器学习计算、远程 vm 或 Azure Databricks](how-to-set-up-training-targets.md)。  了解有关[远程资源](how-to-auto-train-remote.md)的自动化培训。

1. **配置自动机器学习参数**，该参数确定不同模型上的迭代数、超参数设置、高级预处理/特征化和确定最佳模型时要查看的指标。  可以在[Azure 机器学习 studio](https://ml.azure.com)或[SDK](how-to-configure-auto-train.md)中配置自动训练试验的设置。 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **提交训练运行。**

  ![自动机器学习](./media/concept-automated-ml/automl-concept-diagram2.png)

在训练过程中，Azure 机器学习会创建多个尝试不同算法和参数的并行管道。 一旦到达试验中定义的退出条件，它就会停止。

你还可以检查记录的运行信息，其中[包含](how-to-understand-automated-ml.md)在运行期间收集的指标。 训练运行会生成一个 Python 序列化对象（`.pkl` 文件），其中包含模型和数据预处理。

虽然模型生成是自动进行的，但您也可以了解生成的模型的[重要或相关功能](how-to-configure-auto-train.md#explain)。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>预处理

在每个自动机器学习试验中，均使用默认方法对数据进行预处理，并可选择通过高级预处理进行预处理。

> [!NOTE]
> 自动机器学习预处理步骤（特征规范化、处理缺失数据，将文本转换为数字等）成为基础模型的一部分。 使用模型进行预测时，训练期间应用的相同预处理步骤将自动应用于输入数据。

### <a name="automatic-preprocessing-standard"></a>自动预处理（标准）

在每个自动机器学习试验中，数据会自动缩放或规范化，以帮助算法正常执行。  在模型定型过程中，将对每个模型应用以下缩放或规范化技术之一。

|&nbsp;标准化 &缩放&nbsp;| 说明 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 通过删除平均值并缩放到单位差异来实现功能标准化  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 通过按列的最小值和最大值缩放每个特征来转换特征  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |按功能的最大绝对值缩放每个功能 |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |此 Scaler 功能的分位范围 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |使用数据的单数值分解缩减线性维数，以将数据投影到较低的空间 |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |此转换器通过截断的单值分解（SVD）来执行线性维度缩减。 与 PCA 相反，此估计器在计算单数值分解之前不会将数据居中，这意味着它可以有效地使用 scipy 矩阵 |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 每个示例（即，数据矩阵的每个行）至少具有一个非零的组件，因此它的标准（l1 或 l2）等于一个重新缩放 |

### <a name="advanced-preprocessing-optional-featurization"></a>高级预处理：可选特征化

还提供了其他高级预处理和特征化，例如 data guardrails、encoding 和转换。 [详细了解所包含的特征化](how-to-use-automated-ml-for-ml-models.md#featurization)。 启用此设置，使用：

+ Azure 机器学习 studio：在 "**查看附加配置**" 部分中[使用以下步骤](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)启用**自动特征化**。

+ Python SDK：指定[`AutoMLConfig` 类](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)的 `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'`。 

## <a name="prevent-over-fitting"></a>阻止过度调整

如果模型适合定型数据，则在机器学习中进行过度调整，因此无法准确预测不可见的测试数据。 换句话说，模型只是在定型数据中缘故特定的模式和噪音，但并不太灵活，无法预测实际数据。 在大多数严重情况下，过度拟合的模型将假定在训练过程中出现的功能值组合将始终生成与目标完全相同的输出。 

阻止过度调整的最佳方式是遵循 ML 最佳实践，包括：

* 使用更多定型数据，消除统计偏差
* 阻止目标泄露
* 使用较少的功能
* **正则化和超参数优化**
* **模型复杂性限制**
* **交叉验证**

在自动 ML 的上下文中，上述前三个项目是**你实现的最佳实践**。 最后三个粗体项是默认情况下**自动实现的最佳实践 ML** ，以防止过度调整。 除了自动 ML 的设置外，所有六种最佳做法都是为了避免过度调整模型。

### <a name="best-practices-you-implement"></a>实现的最佳实践

使用**更多的数据**是防止过度调整的最简单且最可行的方法，作为额外的奖金通常会提高准确性。 使用更多数据时，模型会使模型记住确切的模式变得更加困难，并强制访问更灵活的解决方案，以适应更多条件。 还必须识别**统计偏差**，以确保定型数据不包含实时预测数据中不存在的隔离模式。 这种情况很难解决，因为定型集和测试集之间可能没有过度调整，但与实时测试数据相比，可能会存在过度调整。

目标泄漏是一个类似的问题，在这种情况下，你可能不会在训练/测试集之间看到过度调整，而是在预测时间显示。 当你的模型 "作弊" 通过访问其通常不应预测时间的数据时，会发生目标泄漏。 例如，如果您的问题是在星期一预测商品的价格是在星期五，而您的某个功能意外包含来自星期四的数据，则这是模型在预测时不会有的数据，因为它不能看到未来。 目标泄漏是一种容易出错的错误，但通常是由于问题的异常的高准确性。 如果你正在尝试预测股票价格，并以95% 的准确性对模型进行了定型，则功能中的某个位置可能存在目标泄漏。

删除功能还可防止模型使用太多字段来记住特定模式，从而使其更具灵活性，从而有助于实现过度调整。 这可能很难度量而言，但如果您可以删除功能并保持相同的准确性，则您很可能使模型更灵活，并且降低了过度拟合的风险。

### <a name="best-practices-automated-ml-implements"></a>最佳做法自动 ML ML 实现

正则化是最大程度地减少成本函数以妨碍复杂且过度拟合的模型的过程。 有不同类型的正则化函数，但通常它们都妨碍模型系数大小、变化和复杂性。 自动 ML 以不同的组合使用 L1 （套索）、L2 （凸）和 ElasticNet （L1 和 L2）来控制过度调整的不同模型超参数设置。 简单而言，自动 ML 会改变模型的管控程度，并选择最佳结果。

自动 ML 还实现了明确的模型复杂性限制，以防止过度拟合。 在大多数情况下，此实现专用于决策树或林算法，其中每个树的最大深度受到限制，并且在林或系综技术中使用的树总数有限。

交叉验证（CV）是在每个子集上采用完整定型数据的多个子集和定型模型的过程。 其思路是，模型可能会获得 "幸运的"，并且具有一个子集的准确度，但通过使用多个子集，模型每次都无法实现这种高度准确性。 当执行 CV 时，你将提供一个验证维持的数据集，指定你的 CV 折叠（子集数），自动 ML 会训练你的模型并调整超参数以最大程度地减少验证集上的错误。 一个 CV 折页可能会过度调整，但使用其中的许多方法可以降低最终模型超出的概率。 缺点是 CV 确实会导致定型时间较长，从而导致更高的成本，因为不是定型一次模型，而是对每个*n*个 CV 子集进行一次训练。

> [!NOTE]
> 默认情况下不启用交叉验证;它必须在自动 ML 设置中进行配置。 但是，在配置 CV 并提供验证数据集后，会自动执行此过程。

### <a name="identifying-over-fitting"></a>标识覆盖

请考虑以下训练模型及其相应的训练和测试准确性。

| “模型” | 定型准确度 | 测试准确性 |
|-------|----------------|---------------|
| A | 99.9% | 95% |
| B | 87% | 87% |
| C | 99.9% | 45% |

考虑到模型**A**，有一种常见的误解是，如果不可见数据的测试准确性低于定型准确度，则表明模型是过度拟合的。 但是，测试准确性应始终小于定型准确度，并且适合调整到适当的大小，*以使其不太*准确。 

在比较模型**a**和**B**时，模型**a**是更好的模型，因为它具有更高的测试准确性，但在95% 的测试准确性略有降低，这并不是建议过度调整的重要差异。 不只是因为定型和测试准确性更接近一起选择模型**B** 。

模型**C**表示过度拟合的清晰大小写;训练准确性非常高，但测试准确性并不接近任何位置。 这种区别很主观，但却是您的问题和数据的知识，而度的错误是可接受的。 

## <a name="classification--regression"></a>分类 & 回归

分类和回归是最常见的机器学习任务类型。 这两者都是监视使用定型数据的模型，并将这些知识应用于新数据的监控类型。 Azure 机器学习为这些任务专门提供 featurizations，例如分类的深层神经网络文本 featurizers。 了解有关[特征化选项](how-to-use-automated-ml-for-ml-models.md#featurization)的详细信息。 

分类模型的主要目标是根据知识的定型数据来预测新数据将归入的类别。 常见分类示例包括欺诈检测、手写识别和对象检测。  了解更多详细信息，并查看[使用自动机器学习进行分类](tutorial-train-models-with-aml.md)的示例。

不同于分类的预测输出值是分类的，回归模型基于独立预测值预测数值输出值。 在回归中，目标是通过估计一个变量如何影响其他变量来帮助建立这些独立预测器变量之间的关系。 例如，基于诸如、气体里程、安全评级等功能的汽车价格。 了解更多详细信息，并查看[自动机器学习的回归](tutorial-auto-train-models.md)示例。

## <a name="time-series-forecasting"></a>时序预测

生成预测是任何业务的有机组成部分，无论是收入、库存、销售还是客户需求。 你可以使用自动 ML 来合并技术和方法，并获得推荐的高质量时序预测。

自动时间系列实验被视为多元回归问题。 过去的时间序列值将 "透视" 成为回归量与其他预测值的其他维度。 与传统时序方法不同，这种方法的优点是，在定型过程中自然包含多个上下文变量以及它们之间的关系。 自动 ML 会为数据集中的所有项目学习单个但通常在内部分支的模型，并预测视野。 这样就可以使用更多的数据来估计模型参数，并使其成为不可见系列。

了解更多详细信息，并查看[时序预测自动机器学习](how-to-auto-train-forecast.md)的示例。 或者，有关高级预测配置的详细代码示例，请参阅[能源需求笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)，其中包括：

* 假日检测和特征化
* 时序和 DNN 学习者（自动 ARIMA、Prophet、ForecastTCN）
* 许多模型都支持通过分组
* 滚动-原点交叉验证
* 可配置滞后
* 滚动窗口聚合功能

## <a name="ensemble"></a>系综模型

自动机器学习支持默认情况下启用的系综模型。 系综学习通过组合多个模型（而不是使用单个模型）来改进机器学习结果和预测性能。 系综迭代作为运行的最后一个迭代显示。 自动机器学习使用投票和堆栈系综方法来合并模型：

* **投票**：根据预测类概率的加权平均值（适用于分类任务）或预测回归目标（适用于回归任务）进行预测。
* **堆栈**：堆栈结合了异类模型，并根据各个模型的输出定型了元模型。 当前默认的元模型 LogisticRegression 用于分类任务，ElasticNet 用于回归/预测任务。

使用已排序的系综初始化的[Caruana 系综选择算法](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)用于决定要在系综中使用的模型。 从较高层次来看，此算法使用最多5个模型初始化系综，该模型最多可获得最佳分数，并验证这些模型是否处于最佳分数的5% 阈值内，以避免初始系综不佳。 然后，对于每个系综迭代，会将一个新模型添加到现有系综，并计算生成的分数。 如果新模型改进了现有的系综分数，则会更新系综以包含新模型。

请参阅[如何](how-to-configure-auto-train.md#ensemble)在自动机器学习中更改默认系综设置。

## <a name="imbalance"></a>不均衡数据

不均衡数据通常在机器学习分类方案的数据中找到，并且是指在每个类中包含不相称的观测值的数据。 这种不平衡可能会导致对模型准确性产生虚假的积极影响，因为输入数据对一个类有偏差，这会导致训练的模型模拟该偏量。 

作为简化机器学习工作流的目标的一部分，自动 ML 具有内置功能，可帮助处理不均衡的数据，例如， 

- **权重列**：自动 ML 支持加权列作为输入，导致数据中的行增加或减少，这会使类更多或更少 "重要"。

- 自动 ML 使用的算法可正确处理最大为20:1 的不平衡，这意味着，最常见的类在数据中的行数可能会多于最小公共类的20倍。

### <a name="identify-models-with-imbalanced-data"></a>标识具有不均衡数据的模型

由于分类算法通常是按准确性计算的，因此检查模型的准确性分数是确定是否受不均衡数据影响的好方法。 对于某些类，它的准确性是否准确或准确性较低？

此外，自动 ML 运行自动生成以下图表，这有助于了解模型分类的正确性，并识别可能会受到不均衡数据影响的模型。

图表| 说明
---|---
[混淆矩阵](how-to-understand-automated-ml.md#confusion-matrix)| 根据数据的实际标签计算正确分类的标签。 
[精度-撤回](how-to-understand-automated-ml.md#precision-recall-chart)| 计算正确标签与数据的找到标签实例比率的比率 
[ROC 曲线](how-to-understand-automated-ml.md#roc)| 计算正确标签与假标签比率的比值。

### <a name="handle-imbalanced-data"></a>处理不均衡的数据 

以下技术是在自动 ML 之外处理不均衡数据的其他选项。 

- 即使类不平衡，也可以通过向上采样较小的类或向下采样更大的类来重新采样。 这些方法需要专业技能来处理和分析。

- 使用性能指标，可更好地处理不均衡数据。 例如，F1 分数是精度和召回的加权平均值。 精度度量值分类器的 exactness--低精度表示大量误报--,，而召回度量值为分类器的完整性-低召回表示很多假负。 

## <a name="use-with-onnx-in-c-apps"></a>在应用中C#使用 with ONNX

使用 Azure 机器学习，可以使用自动 ML 来构建 Python 模型，并将其转换为 ONNX 格式。 ONNX 运行时支持C#，因此你可以使用在C#应用程序中自动生成的模型，而无需进行无编码或 REST 终结点引入的任何网络延迟。 [在此 Jupyter 笔记本中](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)试用此流的示例。

## <a name="automated-ml-in-azure-machine-learning"></a>Azure 机器学习中的自动 ML

Azure 机器学习提供了两种使用自动 ML 的体验

* 对于经验丰富的客户， [Azure 机器学习 PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* 对于有限/无代码体验客户，Azure 机器学习 studio [https://ml.azure.com](https://ml.azure.com/)  

下面总结了每种体验中支持的高级自动化 ML 功能。

<a name="parity"></a>

### <a name="experiment-settings"></a>试验设置 

以下设置允许配置自动 ML 试验。 

| | Python SDK| studio
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
假日特征化|✓|
日志文件的详细级别| ✓|

### <a name="model-settings"></a>模型设置

这些设置可作为自动 ML 试验的结果应用于最佳模型。

||Python SDK|studio
----|:----:|:----:
最佳模型注册| ✓|✓
最佳模型部署| ✓| ✓
最佳模型 explainability| ✓|✓
启用投票系综 & stack 系综模型| ✓|✓
基于非主要指标显示最佳模型|✓|启用/禁用 ONNX 模型兼容性|✓|
测试模型 | ✓| |

### <a name="run-control-settings"></a>运行控件设置

通过这些设置，你可以查看和控制实验运行及其子运行。 

||Python SDK| studio
----|:----:|:----:
运行摘要表| ✓|✓
取消运行| ✓|✓
取消子运行| ✓| ✓
获取 guardrails| ✓|✓
暂停运行| ✓| 
继续运行| ✓| 

## <a name="next-steps"></a>后续步骤

请参阅示例并了解如何使用自动机器学习来构建模型：

+ 按照[教程操作：使用 Azure 机器学习自动为回归模型定型](tutorial-auto-train-models.md)

+ 配置自动定型试验的设置：
  + 在 Azure 机器学习 studio 中，请[使用以下步骤](how-to-use-automated-ml-for-ml-models.md)。
  + 使用 Python SDK 时，请[使用以下步骤](how-to-configure-auto-train.md)。

+ 若要了解如何使用时序数据自动定型，请[使用以下步骤](how-to-auto-train-forecast.md)。

+ 试用[自动机器学习 Jupyter Notebook 示例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* 自动 ML 还可用于其他 Microsoft 解决方案，如[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)、 [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md)、 [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)和[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
