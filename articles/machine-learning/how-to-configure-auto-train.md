---
title: 创建自动化机器学习试验
titleSuffix: Azure Machine Learning
description: 自动化机器学习将为你选择一种算法，并生成随时可用于部署的模型。 了解可用于配置自动化机器学习试验的选项。
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 08/10/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 05a70274e075ddda8770e57c71a7f55807cf3dcc
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182130"
---
# <a name="configure-automated-ml-experiments-in-python"></a>使用 Python 配置自动化 ML 试验
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本指南介绍如何通过 [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 定义各种自动机器学习试验的配置设置。 自动化机器学习将自动选择算法和超参数，并生成随时可用于部署的模型。 可以使用多个选项来配置自动化机器学习试验。

若要查看自动机器学习试验的示例，请参阅[教程：使用自动机器学习](tutorial-auto-train-models.md)或云中的[自动化机器学习](how-to-auto-train-remote.md)来训练分类模型。

自动化机器学习提供的配置选项：

* 选择试验类型：分类、回归或时序预测
* 数据源、格式和提取数据
* 选择计算目标：本地或远程
* 自动化机器学习试验设置
* 运行自动化机器学习试验
* 探索模型指标
* 注册和部署模型

如果你更喜欢无代码体验，还可以[在 Azure 机器学习工作室中创建自动化学习试验](how-to-use-automated-ml-for-ml-models.md)。

## <a name="prerequisites"></a>先决条件

在本文中，你需要： 
* Azure 机器学习工作区。 若要创建工作区，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* 已安装 Azure 机器学习 Python SDK。
    若要安装 SDK，你可以： 
    * 创建一个计算实例，该实例将自动安装 SDK，并预先配置了 ML 工作流。 有关详细信息，请参阅[什么是 Azure 机器学习计算实例？](concept-compute-instance.md#managing-a-compute-instance) 。 

    * [自行安装 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。 只需确保包括多余的 `automl` 。 

## <a name="select-your-experiment-type"></a>选择试验类型

在开始试验之前，应确定要解决的机器学习问题类型。 自动机器学习支持、和的任务类型 `classification` `regression` `forecasting` 。 详细了解[任务类型](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast)。

下面的代码使用 `task` 构造函数中的参数将 `AutoMLConfig` 试验类型指定为 `classification` 。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>数据源和格式

自动化机器学习支持驻留在本地桌面上或云中（例如 Azure Blob 存储）的数据。 数据可以读入 Pandas 数据帧或 Azure 机器学习 TabularDataset 中 。 [了解有关数据集的详细信息](how-to-create-register-datasets.md)。

训练数据的要求：
- 数据必须为表格格式。
- 要预测的值（目标列）必须位于数据中。

**对于远程试验**，必须可从远程计算访问定型数据。 AutoML 仅在处理远程计算时接受[Azure 机器学习 TabularDatasets](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) 。 

Azure 机器学习数据集公开功能：

* 轻松地将数据从静态文件或 URL 源传输到工作区。
* 在云计算资源上运行时，使数据可用于训练脚本。 有关使用类将数据装载到远程计算目标的示例，请参阅如何使用[数据集进行训练](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) `Dataset` 。

下面的代码从 web url 创建 TabularDataset。 有关如何从本地文件和数据存储等其他源创建数据集的代码示例，请参阅[创建 TabularDatasets](how-to-create-register-datasets.md#create-a-tabulardataset) 。

```python
from azureml.core.dataset import Dataset
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"
dataset = Dataset.Tabular.from_delimited_files(data)
  ```
**对于本地计算试验**，我们建议 pandas dataframes 以提高处理速度。

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="training-validation-and-test-data"></a>定型、验证和测试数据

您可以直接在构造函数中指定单独的**定型集和验证集** `AutoMLConfig` 。 详细了解[如何配置数据拆分和交叉验证](how-to-configure-cross-validation-data-splits.md)（针对 AutoML 试验）。 

如果未显式指定 `validation_data` 或 `n_cross_validation` 参数，则 AutoML 将应用默认技术来确定验证的执行方式。 此决定取决于数据集中分配给参数的行数 `training_data` 。 

|训练数据大小| 验证技术 |
|---|-----|
|**大于 20,000 行**| 将应用训练/验证数据拆分。 默认行为是将初始训练数据集的 10% 用作验证集。 然后，该验证集将用于指标计算。
|**小于 20,000 行**| 将应用交叉验证方法。 默认折数取决于行数。 <br> **如果数据集小于 1,000 行**，则使用 10 折。 <br> **如果行数在 1,000 到 20,000 之间**，则使用 3 折。

此时，需要为模型评估提供自己的**测试数据**。 有关为模型评估引入自己的测试数据的代码示例，请参阅[此 Jupyter 笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)的**测试**部分。

## <a name="compute-to-run-experiment"></a>用于运行试验的计算环境

接下来，确定要在何处训练模型。 自动机器学习训练实验可对以下计算选项运行。 了解[本地和远程计算选项的优点和缺点](concept-automated-ml.md#local-remote)。 

* **本地**计算机（如本地桌面计算机或便携式计算机）-通常，当你有一个小型数据集并且仍处于探索阶段时。 有关本地计算示例，请参阅[此笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/local-run-classification-credit-card-fraud/auto-ml-classification-credit-card-fraud-local.ipynb)。 
 
* 云中的**远程**计算机- [Azure 机器学习托管计算](concept-compute-target.md#amlcompute)是一种托管服务，可用于在 Azure 虚拟机群集上定型机器学习模型。 

    有关使用 Azure 机器学习托管计算的远程示例，请参阅[此笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)。 

* Azure 订阅中的**Azure Databricks 群集**。 可在此处找到更多详细信息-[设置 Azure Databricks 群集以实现自动 ML](how-to-configure-environment.md#azure-databricks)。 有关包含 Azure Databricks 的示例 Notebook，请参阅此 [GitHub 站点](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)。

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>配置试验设置

可以使用多个选项来配置自动化机器学习试验。 通过实例化 `AutoMLConfig` 对象来设置这些参数。 有关参数的完整列表，请参阅 [AutoMLConfig 类](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)。

示例包括：

1. 使用 AUC 作为主要指标加权的分类实验，其中实验超时分钟数设置为 30 分钟，且包含 2 折交叉验证。

   ```python
       automl_classifier=AutoMLConfig(
       task='classification',
       primary_metric='AUC_weighted',
       experiment_timeout_minutes=30,
       blocked_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
1. 下面的示例是一个在60分钟之后结束的回归实验，其中包含5次验证交叉折叠。

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      allowed_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```


1. 预测任务需要其他设置，请参阅[自动训练时序预测模型一](how-to-auto-train-forecast.md)文以了解更多详细信息。 

    ```python
    time_series_settings = {
        'time_column_name': time_column_name,
        'time_series_id_column_names': time_series_id_column_names,
        'drop_column_names': ['logQuantity'],
        'forecast_horizon': n_test_periods
    }
    
    automl_config = AutoMLConfig(task = 'forecasting',
                                 debug_log='automl_oj_sales_errors.log',
                                 primary_metric='normalized_root_mean_squared_error',
                                 experiment_timeout_minutes=20,
                                 training_data=train_data,
                                 label_column_name=label,
                                 n_cross_validations=5,
                                 path=project_folder,
                                 verbosity=logging.INFO,
                                 **time_series_settings)
    ```
    
### <a name="supported-models"></a>支持的模型

自动化机器学习在自动化和优化过程中尝试不同的模型和算法。 用户不需要指定算法。 

`task`第三个任务类型 (三个不同的参数值 `forecasting` ，并使用类似的算法池作为 `regression` 任务) 确定要应用的算法和模型的列表。 使用 `allowed_models` 或 `blocked_models` 参数通过要包含或排除的可用模型来进一步修改迭代。 可在[SupportedModels 类](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels)中找到支持的模型的列表，以便进行[分类](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification)、[预测](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)和[回归](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)。


### <a name="primary-metric"></a>主要指标
`primary metric`参数确定要在模型定型过程中用于优化的指标。 你可选择的可用指标取决于所选择的任务类型，下表显示了每种任务类型的有效主要指标。

如需了解上述指标的具体定义，请参阅[了解自动化机器学习结果集](how-to-understand-automated-ml.md)。

|分类 | 回归 | 时序预测
|--|--|--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-featurization"></a>数据特征化

在每个自动化机器学习实验中，数据都是*自动缩放和规范化*，以帮助对不同规模上的特征敏感的某些算法。 这种缩放和规范化称为特征化。 有关更多详细信息和代码示例，请参阅[AutoML 中的特征化](how-to-configure-auto-features.md#)。 

在 `AutoMLConfig` 对象中配置试验时，可以启用/禁用设置 `featurization`。 下表显示了[AutoMLConfig 对象](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)中特征化的已接受设置。 

|特征化配置 | 说明 |
| ------------- | ------------- |
|`"featurization": 'auto'`| 指示在处理过程中自动执行[数据护栏和特征化步骤](how-to-configure-auto-features.md#featurization)。 默认设置。|
|`"featurization": 'off'`| 指示不应自动执行特征化步骤。|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 指示应当使用自定义特征化步骤。 [了解如何自定义特征化](how-to-configure-auto-features.md#customize-featurization)。|

> [!NOTE]
> 自动化机器学习特征化步骤（特征规范化、处理缺失数据，将文本转换为数字等）成为了基础模型的一部分。 使用模型进行预测时，将自动向输入数据应用在训练期间应用的相同特征化步骤。

<a name="ensemble"></a>

### <a name="ensemble-configuration"></a> 集成配置

默认情况下启用系综模型，并在 AutoML 运行中显示为最终的运行迭代。 目前支持**VotingEnsemble**和**StackEnsemble** 。 

投票实现使用加权平均值的软投票。 堆栈实现使用两层实现，其中第一层的模型与投票系综相同，第二层模型用于查找第一层模型的最佳组合。 

如果使用的是 ONNX 模型，**或**启用了模型 explainability，则会禁用堆栈，并且仅使用投票。

可以使用 `enable_voting_ensemble` 和布尔参数禁用系综定型 `enable_stack_ensemble` 。

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

若要更改默认的系综行为，可以在对象中提供多个默认参数 `kwargs` `AutoMLConfig` 。

> [!IMPORTANT]
>  以下参数不是 AutoMLConfig 类的显式参数。 

* `ensemble_download_models_timeout_sec`：在 VotingEnsemble 和 StackEnsemble 模型生成期间，会下载来自先前子运行的多个拟合模型。 如果遇到此错误 `AutoMLEnsembleException: Could not find any models for running ensembling`，则可能需要为要下载的模型提供更多时间。 默认值为 300 秒并行下载这些模型，且没有最大超时限制。 如果需要更多时间，请将此参数配置为大于 300 秒的值。 

  > [!NOTE]
  >  如果已超时且下载了模型，则融合会使用它下载的多个模型继续执行。 并不需要下载所有模型才能在超时内完成。

以下参数只应用于 StackEnsemble 模型： 

* `stack_meta_learner_type`：元学习器是针对单个异类模型的输出而训练出来的模型。 默认的元学习器是用于分类任务的 `LogisticRegression`（或为 `LogisticRegressionCV`，如果启用了交叉验证的话），以及用于回归/预测任务的 `ElasticNet`（或为 `ElasticNetCV`，如果启用了交叉验证的话）。 此参数可以是下列字符串之一：`LogisticRegression`、`LogisticRegressionCV`、`LightGBMClassifier`、`ElasticNet`、`ElasticNetCV`、`LightGBMRegressor` 或 `LinearRegression`。

* `stack_meta_learner_train_percentage`：指定为训练元学习器而保留的训练集的比例（选择训练的训练和验证类型时）。 默认值为 `0.2`。 

* `stack_meta_learner_kwargs`：要传递给元学习器的初始值设定项的可选参数。 这些参数和参数类型对来自相应模型构造函数的参数和参数类型进行镜像，然后再转发到模型构造函数。

下面的代码示例展示了如何在 `AutoMLConfig` 对象中指定自定义融合行为。

```python
ensemble_settings = {
    "ensemble_download_models_timeout_sec": 600
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

## <a name="run-experiment"></a>运行试验

对于自动化 ML，可以创建 `Experiment` 对象，这是 `Workspace` 中用于运行实验的命名对象。

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

提交试验以运行和生成模型。 将 `AutoMLConfig` 传递给 `submit` 方法以生成模型。

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>首先在新的计算机上安装依赖项。  最长可能需要在 10 分钟后才会显示输出。
>将 `show_output` 设置为 `True` 可在控制台上显示输出。

 <a name="exit"></a> 

### <a name="exit-criteria"></a>退出条件

有几个选项可供定义来结束实验。

|条件| description
|----|----
无 &nbsp; 条件 | 如果未定义任何退出参数，则试验会继续，直到主要指标没有进一步的进度。
经过 &nbsp; 一 &nbsp; &nbsp; &nbsp; 段时间后| `experiment_timeout_minutes`在设置中使用，定义实验应继续运行的时间（分钟）。 <br><br> 若要帮助避免试验超时失败，最少需要15分钟，如果行的大小超过10000000则为60分钟。
&nbsp; &nbsp; &nbsp; 已 &nbsp; 达到分数| 在 `experiment_exit_score` 达到指定的主要指标评分后，使用完成试验。

## <a name="explore-models-and-metrics"></a>探索模型和指标

如果在笔记本中操作，可以在小组件或内联单元中查看训练结果。 有关更多详细信息，请参阅[跟踪和评估模型](how-to-monitor-view-training-logs.md#monitor-automated-machine-learning-runs)。

请参阅[了解自动化机器学习结果](how-to-understand-automated-ml.md)，了解每个运行的性能图表和指标的定义和示例。 

若要获取特征化摘要并了解哪些功能已添加到特定模型，请参阅[特征化透明度](how-to-configure-auto-features.md#featurization-transparency)。 

## <a name="register-and-deploy-models"></a>注册和部署模型

有关如何下载或注册模型以便部署到 Web 服务的详细信息，请参阅[如何部署模型以及在何处部署模型](how-to-deploy-and-where.md)。


<a name="explain"></a>

## <a name="model-interpretability"></a>模型可解释性

模型可解释性让你可以了解模型进行预测的原因，以及基础特征重要性值。 SDK 包括各种包，这些包用于在训练和推理时间为本地和已部署的模型启用模型可解释性功能。

有关如何在自动化机器学习试验中启用可解释性功能的代码示例，请参阅[操作方法](how-to-machine-learning-interpretability-automl.md)。

有关如何在自动化机器学习之外的其他 SDK 区域中启用模型解释和特征重要性的基本信息，请参阅可解释性方面的[概念](how-to-machine-learning-interpretability.md)文章。

> [!NOTE]
> 解释客户端目前不支持 ForecastTCN 模型。 如果此模型作为最佳模型返回，则不会返回解释仪表板，并且不支持按需解释运行。

## <a name="next-steps"></a>后续步骤

+ 详细了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。

+ 详细了解[如何使用自动化机器学习训练回归模型](tutorial-auto-train-models.md)或[如何使用自动化机器学习对远程资源进行训练](how-to-auto-train-remote.md)。

+ 了解如何在[多个模型解决方案加速器](https://aka.ms/many-models)中用 AutoML 训练多个模型。
