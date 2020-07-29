---
title: AutoML 试验中的特征化
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习提供哪些特征化设置以及自动化机器学习试验如何支持特征工程。
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/28/2020
ms.openlocfilehash: b01d6c36b31ef4f03522d03ca327439cfa31be8d
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373736"
---
# <a name="featurization-in-automated-machine-learning"></a>自动化机器学习中的特征化

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本指南介绍：

- Azure 机器学习提供了哪些特征化设置。
- 如何为[自动化机器学习试验](concept-automated-ml.md)自定义这些功能。

“特征工程”是使用数据领域知识创建特征的过程，这些特征有助于机器学习 (ML) 算法提高学习效果。 在 Azure 机器学习中，应用了数据缩放和规范化技术来简化特征工程。 这些技术和此特征工程在自动化机器学习 (AutoML) 试验中统称为特征化。 

本文假设你已知道如何配置 AutoML 试验。 有关配置的信息，请参阅以下文章：

- 对于“代码优先”体验：[使用适用于 Python 的 Azure 机器学习 SDK 配置自动化机器学习试验](how-to-configure-auto-train.md)。
- 对于低代码或无代码体验：[使用 Azure 机器学习工作室创建、查看和部署自动化机器学习模型](how-to-use-automated-ml-for-ml-models.md)。

## <a name="configure-featurization"></a>配置特征化

在每一个自动化机器学习试验中，默认情况下都会将[自动缩放和规范化技术](#featurization)应用于数据。 这些技术是特征化的类型，用于帮助对不同规模数据的特征敏感的某些算法。 不过，你还可以启用其他特征化，例如缺失值插补、编码和转换  。

> [!NOTE]
> 自动化机器学习特征化步骤（例如特征规范化、处理缺失数据，或将文本转换为数字）成为了基础模型的一部分。 使用模型进行预测时，将自动向输入数据应用在训练期间应用的相同特征化步骤。

对于使用 Python SDK 配置的试验，你可以启用或禁用特征化设置，并进一步指定要用于试验的特征化步骤。 如果使用的是 Azure 机器学习工作室，请参阅[启用特征化的步骤](how-to-use-automated-ml-for-ml-models.md#customize-featurization)。

下表列出了 [AutoMLConfig 类](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)中 `featurization` 的已接受设置：

|特征化配置 | 说明|
------------- | ------------- |
|`"featurization": 'auto'`| 指定在预处理过程中自动执行[数据护栏和特征化步骤](#featurization)。 此设置为默认设置。|
|`"featurization": 'off'`| 指定不自动执行特征化步骤。|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 指定将使用自定义特征化步骤。 [了解如何自定义特征化](#customize-featurization)。|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>自动特征化

下表汇总了自动应用于你的数据的技术。 这些方法适用于使用 SDK 或工作室配置的试验。 若要禁用此行为，请在 `AutoMLConfig` 对象中设置 `"featurization": 'off'`。

> [!NOTE]
> 如果计划将 AutoML 创建的模型导出到 [ONNX 模型](concept-onnx.md)，则 ONNX 格式中仅支持使用星号（“*”）指示的特征化选项。 [详细了解如何将模型转换为 ONNX](concept-automated-ml.md#use-with-onnx)。

|特征化步骤| 说明 |
| ------------- | ------------- |
|删除高基数或者无差异的特征* |从训练集和验证集中删除这些特征。 适用于所有值都缺失的特征、所有行使用同一值的特征，或者包含高基数（例如哈希、ID 或 GUID）的特征。|
|插补缺少的值* |对于数字特征，将在列中插补平均值。<br/><br/>对于分类特征，将插补最常用值。|
|生成其他特征* |对于日期时间特征：年、月、日、星期、年日期、季、年周、小时、分钟、秒。<br/><br/>对于文本特征：基于单元语法、双元语法和三元语法的字词频率。 了解有关[如何通过经理 bert 完成此操作](#bert-integration)的详细信息。|
|转换和编码*|将唯一值较少的数字特征转换为分类特征。<br/><br/>将为低基数分类特征使用 One-hot 编码。 将为高基数分类特征使用 One-hot-hash 编码。|
|单词嵌入|文本特征化器使用预先训练的模型将文本标记的矢量转换为句子矢量。 每个单词在文档中的嵌入矢量与其余矢量聚合在一起，以生成文档特征矢量。|
|目标编码|对于分类特征，此步骤将每个类别映射到回归问题的平均目标值，并映射到分类问题的每个类的类概率。 应用基于频率的加权和 k 折交叉验证，以减少稀疏数据类别导致的映射过度拟合与干扰。|
|文本目标编码|对于文本输入，将使用带有词袋的堆叠线性模型来生成每个类的概率。|
|证据权重 (WoE)|将 WoE 计算为分类列与目标列的关联度量。 WoE 的计算公式为类内概率与类外概率的比的对数。 此步骤为每个类生成一个数字特征列，无需显式插补缺失值和处理离群值。|
|群集距离|基于所有数字列训练 k 平均聚类模型。 生成 k 个新特征（每个聚类一个新数字特征），这些特征包含每个样本与每个聚类质心之间的距离。|

## <a name="data-guardrails"></a>数据护栏

“数据护栏”有助于识别数据的潜在问题（例如缺少值或[类不平衡](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)）。 它们还可帮助你采取纠正措施来改进结果。

数据护栏适用于：

- **对于 SDK 试验**：当在 `AutoMLConfig` 对象中指定了参数 `"featurization": 'auto'` 或 `validation=auto` 时。
- **对于工作室试验**：当启用了自动特征化时。

可通过以下方式查看试验的数据护栏：

- 使用 SDK 提交试验时设置 `show_output=True`。

- 访问工作室中自动化机器学习运行的“数据护栏”选项卡。

### <a name="data-guardrail-states"></a>数据护栏状态

数据护栏显示以下三种状态之一：

|状态| 说明 |
|----|---- |
|**已通过**| 未检测到任何数据问题，你无需执行任何操作。 |
|**已完成**| 已对数据应用更改。 我们建议你查看 AutoML 采取的纠正措施，以确保所做的更改与预期的结果一致。 |
|**出现警告**| 检测到数据问题，但无法修正。 我们建议你进行修正并解决此问题。|

### <a name="supported-data-guardrails"></a>支持的数据护栏

下表描述了当前支持的数据护栏，以及你在提交试验时可能会看到的相关状态：

护栏|状态|触发器的条件&nbsp;&nbsp;
---|---|---
插补缺少的特征值 |Passed <br><br><br> 完成| 在训练数据中未检测到缺失特征值。 详细了解[缺失值插补。](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> 在训练数据中检测到缺失特征值并进行了插补。
高基数特征处理 |Passed <br><br><br> 完成| 已分析输入，但未检测到任何高基数特征。 <br><br> 在输入中检测到了高基数特征，并进行了处理。
验证拆分处理 |完成| 已将验证配置设置为 `'auto'`，并且训练数据包含的行少于 20,000 行。 <br> 已使用交叉验证来验证经过训练的模型的每个迭代。 详细了解[验证数据](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data)。 <br><br> 已将验证配置设置为 `'auto'`，并且训练数据包含的行多于 20,000 行。 <br> 输入数据已被拆分成训练数据集和验证数据集，以用于验证模型。
类均衡检测 |Passed <br><br><br><br><br> 收到警报 | 输入已经过分析，训练数据中的所有类都是均衡的。 如果某个数据集中每个类都有良好的表示形式（按样本的数量和比率进行度量），则将该数据集视为均衡的数据集。 <br><br><br> 在输入中检测到了不均衡类。 若要修复模型偏差，请解决均衡问题。 详细了解[不均衡数据](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)。
内存问题检测 |Passed <br><br><br><br> 完成 |<br> 已分析了选定的值（范围、滞后、滚动窗口），但未检测到潜在的内存不足问题。 详细了解时序[预测配置](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)。 <br><br><br>已分析了选定的值（范围、滞后、滚动窗口），可能会导致你的试验遇到内存不足问题。 滞后或滚动窗口配置已关闭。
频率检测 |Passed <br><br><br><br> 完成 |<br> 已分析了时序，所有数据点都与检测到的频率保持一致。 <br> <br> 已分析时序，检测到了与已检测到的频率不一致的数据点。 这些数据点已从数据集中删除。 详细了解[时序预测的数据准备](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)。

## <a name="customize-featurization"></a>自定义特征化

你可以自定义特征化设置，以确保用于训练机器学习模型的数据和特征能够产生相关的预测。

若要自定义特征化，请在 `AutoMLConfig` 对象中指定  `"featurization": FeaturizationConfig`。 如果使用 Azure 机器学习工作室进行试验，请参阅[操作方法文章](how-to-use-automated-ml-for-ml-models.md#customize-featurization)。 若要为 forecastings 任务类型自定义特征化，请参阅[预测操作方法](how-to-auto-train-forecast.md#customize-featurization)。

支持的自定义项包括：

|自定义|定义|
|--|--|
|列用途更新|重写指定列的自动检测到的功能类型。|
|转换器参数更新 |更新指定转换器的参数。 当前支持 Imputer（平均值、最频繁使用的值和中值）和 HashOneHotEncoder。 |
|删除列 |指定要从特征化中删除的列。|
|阻止转换器| 指定要在特征化过程中使用的块转换器。|

使用 API 调用创建 `FeaturizationConfig` 对象：

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="bert-integration"></a>经理 BERT 集成 
[经理 bert](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657)在自动 ML 的特征化层中使用。 在此层中，我们检测列是否包含可用文本或其他类型的数据，例如时间戳或简单数字，并相应地进行特征。 对于经理 BERT，我们通过使用用户提供的标签来微调/定型模型，接下来，我们会输出文档嵌入（对于经理 BERT，这些是与特殊 [CLS] 标记相关联的最终隐藏状态）作为功能（如基于时间戳的功能，如一周中的某一天）或数字（例如，一周中的某一天），或许多典型数据 

若要启用经理 BERT，应使用 GPU 计算进行定型。 如果使用 CPU 计算，则 AutoML 将启用 BiLSTM DNN 特征化器，而不是经理 BERT。 若要调用经理 BERT，必须在 automl_settings 中设置 "enable_dnn： True"，并使用 GPU 计算（例如 vm_size = "STANDARD_NC6" 或更高的 GPU）。 有关示例，请参阅[此笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)。

对于经理 BERT，AutoML 需要执行以下步骤（请注意，必须在 automl_settings 中设置 "enable_dnn： True" 才能出现这些项）：

1. 预处理包括所有文本列的标记（将在最终模型的特征化摘要中看到 "StringCast" 转换器。 请访问[此笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)，查看有关如何使用方法生成模型的特征化摘要的示例 `get_featurization_summary()` 。

```python
text_transformations_used = []
for column_group in fitted_model.named_steps['datatransformer'].get_featurization_summary():
    text_transformations_used.extend(column_group['Transformations'])
text_transformations_used
```

2. 将所有文本列连接到一个文本列，因此您将在最终模型中看到 "StringConcatTransformer"。 

> [!NOTE]
> 经理 BERT 的实现将训练示例的总文本长度限制为128个标记。 这意味着在连接时所有文本列，最理想的长度为128个标记。 理想情况下，如果存在多个列，则应将每个列修剪为满足此条件。 例如，如果数据中有两个文本列，则这两个文本列都应修剪为64标记（假设您希望两个列在最终串联的文本列中均匀表示），然后再将数据馈送到 AutoML。 对于串联列 >128 标记，经理 BERT 的标记器层会将此输入截断为128标记。

3. 在功能扫描步骤中，AutoML 会根据数据的样本比较经理 BERT 与基线（一袋关键字 features + 预先训练 word 嵌入），并确定经理 BERT 是否会提供准确性。 如果它确定经理 BERT 的性能比基线更好，AutoML 将使用经理 BERT 作为最佳特征化策略，并继续 featurizing 整个数据。 在这种情况下，您将在最终模型中看到 "PretrainedTextDNNTransformer"。

AutoML 目前支持100种语言，根据数据集的语言，AutoML 选择适当的经理 BERT 模型。 对于德语数据，我们使用德语经理 BERT 模型。 对于英语，我们使用英语经理 BERT 模型。 对于所有其他语言，我们使用多语言经理 BERT 模型。

在下面的代码中，将触发德语经理 BERT 模型，因为数据集语言已指定为 "deu"，这是德语的3个字母代码（根据[ISO 分类](https://iso639-3.sil.org/code/hbs)）：

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>后续步骤

* 了解如何设置自动化机器学习试验：

    * 对于“代码优先”体验：[使用 Azure 机器学习 SDK 配置自动化机器学习试验](how-to-configure-auto-train.md)。
    * 对于低代码或无代码体验：[在 Azure 机器学习工作室中创建自动化机器学习试验](how-to-use-automated-ml-for-ml-models.md)。

* 详细了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。

* 详细了解[如何使用自动化机器学习训练回归模型](tutorial-auto-train-models.md)或[如何在远程资源上使用自动化机器学习进行训练](how-to-auto-train-remote.md)。
