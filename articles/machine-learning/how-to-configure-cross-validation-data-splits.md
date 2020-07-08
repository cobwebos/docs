---
title: 在自动机器学习试验中配置交叉验证和数据拆分
titleSuffix: Azure Machine Learning
description: 了解如何为自动机器学习试验配置交叉验证和数据集拆分
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 06/16/2020
ms.openlocfilehash: 9f8c45b39c0a027735643464d8e936f3039bdeff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85100828"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>在自动机器学习中配置数据拆分和交叉验证

本文介绍为自动机器学习、AutoML、试验配置定型/验证数据拆分和交叉验证的不同选项。

在 Azure 机器学习中，当你使用 AutoML 来生成多个 ML 模型时，每个子运行都需要通过计算该模型的质量指标（例如准确性或 AUC 加权）来验证相关的模型。 这些度量值的计算方法是将每个模型所做的预测与验证数据中过去观察到的实标签进行比较。 

AutoML 试验会自动执行模型验证。 以下部分介绍了如何通过[Azure 机器学习 PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py)进一步自定义验证设置。 

有关低代码或无代码体验，请参阅[在 Azure 机器学习 studio 中创建自动化机器学习试验](how-to-use-automated-ml-for-ml-models.md)。 

> [!NOTE]
> Studio 当前支持定型/验证数据拆分和交叉验证选项，但它不支持为验证集指定单独的数据文件。 

## <a name="prerequisites"></a>先决条件

对于这篇文章，你需要

* Azure 机器学习工作区。 若要创建工作区，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* 熟悉如何使用 Azure 机器学习 SDK 设置自动机器学习试验。 遵循[教程](tutorial-auto-train-models.md)或[操作方法](how-to-configure-auto-train.md)，了解基本的自动化机器学习试验设计模式。

* 了解交叉验证和定型/验证数据拆分为 ML 的概念。 有关高级说明，请

    * [关于训练、验证和测试集机器学习](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [了解交叉验证](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd)

## <a name="default--data-splits-and-cross-validation"></a>默认数据拆分和交叉验证

使用[AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)对象定义试验和培训设置。 请注意，在以下代码段中，只定义了所需的参数，而 `n_cross_validation` `validation_ data` **不**包含或的参数。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

如果未显式指定 `validation_data` 或 `n_cross_validation` 参数，则 AutoML 将根据提供的单个数据集中的行数应用默认方法 `training_data` ：

|定型 &nbsp; 数据 &nbsp; 大小| 验证方法 |
|---|-----|
|**大于 &nbsp; &nbsp; 20000 &nbsp; 行**| 应用训练/验证数据拆分。 默认情况下，需要将10% 的初始定型数据集设置为验证集。 反过来，该验证集用于指标计算。
|**小于 &nbsp; &nbsp; 20000 &nbsp; 行**| 应用交叉验证方法。 默认折叠数取决于行数。 <br> **如果数据集小于1000行**，则使用10个折叠。 <br> **如果行介于1000和20000之间**，则使用三个折叠。

## <a name="provide-validation-data"></a>提供验证数据

在这种情况下，您可以从单个数据文件开始，将其拆分为定型集和验证集，也可以为验证集提供单独的数据文件。 无论采用哪种方式， `validation_data` 对象中的参数都将 `AutoMLConfig` 分配要用作验证集的数据。 此参数仅接受[Azure 机器学习 dataset](how-to-create-register-datasets.md)或 pandas 数据帧格式的数据集。   

下面的代码示例显式定义 `dataset` 要用于定型和验证的中提供数据的部分。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>提供验证集大小

在这种情况下，只为试验提供了一个数据集。 也就是说， `validation_data` **未**指定参数，并将提供的数据集分配给 `training_data` 参数。  在您的 `AutoMLConfig` 对象中，您可以设置 `validation_size` 参数以保留用于验证的定型数据的一部分。 这意味着验证集将由 AutoML 从提供的初始中拆分 `training_data` 。 此值应介于0.0 到1.0 之间（例如，0.2 表示为验证数据保留了20% 的数据）。

请参阅以下代码示例：

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="set-the-number-of-cross-validations"></a>设置交叉验证的数目

若要执行交叉验证，请包括 `n_cross_validations` 参数并将其设置为值。 此参数基于相同数量的折叠设置要执行的交叉验证的数量。

在下面的代码中，定义了用于交叉验证的五个折叠。 因此，五个不同培训，每次使用4/5 数据进行定型，每次使用1/5 的数据进行验证，每次都使用不同的维持折叠。

因此，使用5个验证度量值计算度量值。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>指定自定义交叉验证数据折叠

还可以提供自己的交叉验证（CV）数据折叠。 这被视为更高级的方案，因为你要指定要拆分和用于验证的列。  在定型数据中包括自定义 CV 拆分列，并通过填充参数中的列名来指定哪些列 `cv_split_column_names` 。 每个列表示一个交叉验证拆分，并用整数值1或 0-表示，其中1表示行应用于定型，0表示行应用于验证。

以下代码片段包含具有两个 CV 拆分列 "cv1" 和 "cv2" 的银行营销数据。

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> 若要 `cv_split_column_names` 与 `training_data` 和一起使用 `label_column_name` ，请升级 Azure 机器学习 Python SDK 1.6.0 或更高版本。 对于以前的 SDK 版本，请参阅使用 `cv_splits_indices` ，但请注意，它仅与 `X` 和 `y` 数据集输入一起使用。 

## <a name="next-steps"></a>后续步骤

* [阻止不均衡数据和过度拟合](concept-manage-ml-pitfalls.md)。
* [教程：使用自动机器学习预测出租车费用-拆分数据部分](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets)。
* 如何[自动定型时序预测模型](how-to-auto-train-forecast.md)。
