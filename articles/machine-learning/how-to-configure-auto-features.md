---
title: AutoML 试验中的特征化
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习提供哪些特征化设置，以及如何在自动 ML 试验中支持功能工程。
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 05/28/2020
ms.custom: seodec18
ms.openlocfilehash: 11bb692027d8a2e5033c7bdaf8eb2c565d1562b0
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86205701"
---
# <a name="featurization-in-automated-machine-learning"></a>自动机器学习中的特征化

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本指南中，你将学习：

- Azure 机器学习提供哪些特征化设置。
- 如何为[自动机器学习试验](concept-automated-ml.md)自定义这些功能。

*功能设计*是使用数据的域知识来创建功能的过程，这些功能可帮助机器学习 (ML) 算法来更好地了解。 在 Azure 机器学习中，应用了数据缩放和规范化技术，使功能工程更加简单。 在自动化机器学习或*AutoML*试验中，这些技术和此功能工程共同称为*特征化*。

本文假设你已了解如何配置 AutoML 试验。 有关配置的详细信息，请参阅以下文章：

- 对于代码优先体验：[使用适用于 Python 的 AZURE 机器学习 SDK 配置自动 ML 试验](how-to-configure-auto-train.md)。
- 对于低代码或无代码体验：[使用 Azure 机器学习 Studio 创建、查看和部署自动化机器学习模型](how-to-use-automated-ml-for-ml-models.md)。

## <a name="configure-featurization"></a>配置特征化

在每一个自动化机器学习试验中，默认情况下都会将[自动缩放和规范化技术](#featurization)应用于数据。 这些技术是特征化的一种类型，用于帮助*特定*的算法对不同规模的功能敏感。 但是，还可以启用其他特征化，如*缺少值插补法*、*编码*和*转换*。

> [!NOTE]
> 自动机器学习的步骤特征化 (例如功能规范化、处理丢失的数据或将文本转换为数字) 成为基础模型的一部分。 使用模型进行预测时，在训练过程中应用的相同特征化步骤会自动应用于输入数据。

对于使用 Python SDK 配置的试验，可以启用或禁用特征化设置，并进一步指定要用于试验的特征化步骤。 如果使用的是 Azure 机器学习 studio，请参阅[启用特征化的步骤](how-to-use-automated-ml-for-ml-models.md#customize-featurization)。

下表显示了 `featurization` [AutoMLConfig 类](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)中的已接受的设置：

|特征化配置 | 说明|
------------- | ------------- |
|`"featurization": 'auto'`| 指定在预处理过程中，将自动执行[数据 guardrails 和特征化步骤](#featurization)。 此设置为默认设置。|
|`"featurization": 'off'`| 指定特征化步骤不会自动执行。|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 指定要使用的自定义特征化步骤。 [了解如何自定义特征化](#customize-featurization)。|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>自动特征化

下表汇总了自动应用于数据的技术。 这些方法适用于使用 SDK 或 studio 配置的试验。 若要禁用此行为，请在 `AutoMLConfig` 对象中设置 `"featurization": 'off'`。

> [!NOTE]
> 如果计划将 AutoML 创建的模型导出到[ONNX 模型](concept-onnx.md)，则仅支持使用星号 ( "*" ) 指示的特征化选项。 [详细了解如何将模型转换为 ONNX](concept-automated-ml.md#use-with-onnx)。

|特征化步骤| 说明 |
| ------------- | ------------- |
|删除高基数或者无差异的特征* |从定型集和验证集删除这些功能。 适用于所有缺少值的功能，这些值在所有行中具有相同的值，或具有高基数 (例如，哈希、Id 或 Guid) 。|
|插补缺少的值* |对于数值特征，归结列中的值的平均值。<br/><br/>对于分类特征，具有最常数值的归结。|
|生成其他特征* |对于日期时间特征：年、月、日、星期、年日期、季、年周、小时、分钟、秒。<br/><br/>对于文本功能：基于获得单元语法、二元语法和 trigrams 的术语 frequency。|
|转换和编码*|将具有几个唯一值的数值特征转换为分类特征。<br/><br/>一种热编码用于低基数分类功能。 一种热哈希编码用于高基数分类功能。|
|单词嵌入|文本特征化器使用预先训练模型将文本标记的矢量转换为句型向量。 文档中的每个单词的嵌入向量都与 rest 进行聚合，以生成文档功能矢量。|
|目标编码|对于分类功能，此步骤会将每个类别映射为回归问题的平均目标值，并将每个类别映射到分类问题的每个类的类概率。 应用基于频率的权重和 k 折交叉验证，以减少稀疏数据类别导致的映射和干扰的过度拟合。|
|文本目标编码|对于文本输入，将使用带有词袋的堆叠线性模型来生成每个类的概率。|
|证据权重 (WoE)|将 WoE 计算为分类列与目标列的关联度量。 WoE 计算为类与类外概率的比率的对数。 此步骤为每个类生成一个数值特征列，无需显式归结缺失值和离群值处理。|
|群集距离|在所有数值列上训练 k 平均值聚类分析模型。 生成*k*个新功能，每个) 群集 (一个新的数值功能，其中包含每个样本与每个分类的质心的距离。|

## <a name="data-guardrails"></a>数据护栏

*数据 guardrails*有助于识别数据 (的潜在问题，例如，缺少值或) 不[平衡](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)。 它们还可帮助你采取纠正措施来提高结果。

应用数据 guardrails：

- **对于 SDK 试验**：在 `"featurization": 'auto'` `validation=auto` 对象中指定参数或时 `AutoMLConfig` 。
- **对于 studio 试验**：启用自动特征化。

可以查看 guardrails 的数据：

- 通过在 `show_output=True` 使用 SDK 提交试验时设置。

- 在工作室中，在自动 ML 运行的 "**数据 guardrails** " 选项卡上。

### <a name="data-guardrail-states"></a>数据护栏状态

数据 guardrails 显示以下三种状态之一：

|状态| 说明 |
|----|---- |
|**已通过**| 未检测到任何数据问题，无需执行任何操作。 |
|**已完成**| 已对数据应用更改。 我们鼓励您查看 AutoML 进行的纠正操作，以确保所做的更改与预期的结果一致。 |
|收到警报| 检测到数据问题，但无法纠正。 我们鼓励你修改并解决该问题。|

### <a name="supported-data-guardrails"></a>支持的数据 guardrails

下表描述了当前支持的数据 guardrails，以及在提交实验时可能看到的相关状态：

护栏|状态|触发器的条件&nbsp;&nbsp;
---|---|---
插补缺少的特征值 |Passed <br><br><br> 完成| 在训练数据中未检测到缺失特征值。 了解有关[缺失值插补法的](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options)详细信息。 <br><br> 在定型数据中检测到缺少的功能值，并数据估算。
高基数特征处理 |Passed <br><br><br> 完成| 已分析你的输入，但未检测到高基数功能。 <br><br> 在输入中检测到了高基数功能，已对其进行处理。
验证拆分处理 |完成| 验证配置已设置为 `'auto'` ，定型数据包含的*行少于20000行*。 <br> 使用交叉验证对定型模型的每个迭代进行验证。 了解有关[验证数据](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data)的详细信息。 <br><br> 验证配置设置为 `'auto'` ，定型数据包含*超过20000行*。 <br> 输入数据已被拆分成训练数据集和验证数据集，以用于验证模型。
类均衡检测 |Passed <br><br><br><br><br> 收到警报 | 输入已经过分析，训练数据中的所有类都是均衡的。 如果数据集中的每个类都有良好的表示形式，则将数据集视为均衡，如样本的数量和比率而度量。 <br><br><br> 在输入中检测到了不均衡类。 若要修复模型偏差，请解决均衡问题。 了解有关[不均衡数据](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)的详细信息。
内存问题检测 |Passed <br><br><br><br> 完成 |<br> 已分析选定的值 (范围、延迟、滚动窗口) ，但未检测到任何可能的内存不足问题。 了解有关时序[预测配置](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)的详细信息。 <br><br><br>已分析选定的值 (范围、延迟、滚动窗口) ，并可能会导致实验用尽内存。 延迟或滚动窗口配置已关闭。
频率检测 |Passed <br><br><br><br> 完成 |<br> 分析了时序，并将所有数据点与检测到的频率对齐。 <br> <br> 分析了时序，检测到与检测频率不一致的数据点。 这些数据点已从数据集中删除。 了解有关[时序预测的数据准备情况的](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)详细信息。

## <a name="customize-featurization"></a>自定义特征化

你可以自定义特征化设置，以确保用于训练 ML 模型的数据和功能导致相关的预测。

若要自定义特征化，请在 `AutoMLConfig` 对象中指定  `"featurization": FeaturizationConfig`。 如果你正在使用 Azure 机器学习 studio 进行试验，请参阅操作[指南一文](how-to-use-automated-ml-for-ml-models.md#customize-featurization)。

支持的自定义项包括：

|自定义|定义|
|--|--|
|列用途更新|重写指定列的功能类型。|
|转换器参数更新 |更新指定转换器的参数。 目前支持*Imputer* (平均值、最常见和中间) 和*HashOneHotEncoder*。|
|删除列 |指定要从特征化中删除的列。|
|阻止转换器| 指定要在特征化进程中使用的块转换器。|

`FeaturizationConfig`使用 API 调用创建对象：

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

## <a name="next-steps"></a>后续步骤

* 了解如何设置自动 ML 试验：

    * 对于代码优先体验：[使用 AZURE 机器学习 SDK 配置自动 ML 试验](how-to-configure-auto-train.md)。
    * 对于低代码或无代码体验：[在 Azure 机器学习 studio 中创建自动 ML 试验](how-to-use-automated-ml-for-ml-models.md)。

* 详细了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。

* 详细了解[如何使用自动机器学习来训练回归模型](tutorial-auto-train-models.md)，或者[如何通过在远程资源上使用自动机器学习来训练](how-to-auto-train-remote.md)。
