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
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: seodec18
ms.openlocfilehash: 09f0e0f47ecd94c6db67b3973218cc1323bccde3
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83736103"
---
# <a name="configure-automated-ml-experiments-in-python"></a>使用 Python 配置自动化 ML 实验
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本指南介绍了如何使用 [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 定义自动化机器学习实验的各种配置设置。 自动化机器学习将自动选择算法和超参数，并生成随时可用于部署的模型。 可以使用多个选项来配置自动化机器学习试验。

若要查看自动化机器学习实验的示例，请参阅[教程：使用自动化机器学习训练分类模型](tutorial-auto-train-models.md)或[使用云中的自动化机器学习训练模型](how-to-auto-train-remote.md)。

自动化机器学习提供的配置选项：

* 选择试验类型：分类、回归或时序预测
* 数据源、格式和提取数据
* 选择计算目标：本地或远程
* 自动化机器学习试验设置
* 运行自动化机器学习试验
* 探索模型指标
* 注册和部署模型

如果首选无代码实验，也可以[在 Azure 机器学习工作室中创建自动化机器学习实验](how-to-use-automated-ml-for-ml-models.md)。

## <a name="select-your-experiment-type"></a>选择试验类型

在开始试验之前，应确定要解决的机器学习问题类型。 自动化机器学习支持分类、回归和预测任务类型。 详细了解[任务类型](how-to-define-task-type.md)。

在自动化和优化过程中，自动化机器学习支持以下算法。 用户不需要指定算法。

> [!NOTE]
> 如果你计划将自动化 ML 创建的模型导出为 [ONNX 模型](concept-onnx.md)，只有标有 * 的算法才能转换为 ONNX 格式。 详细了解[如何将模型转换为 ONNX](concept-automated-ml.md#use-with-onnx)。 <br> <br> 另请注意，ONNX 目前只支持分类和回归任务。 

分类 | 回归 | 时序预测
|-- |-- |--
[逻辑回归](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)* | [弹性网络](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)* | [弹性网络](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)* |[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)*|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[梯度提升](https://scikit-learn.org/stable/modules/ensemble.html#classification)* |[梯度提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)* |[渐进提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[决策树](https://scikit-learn.org/stable/modules/tree.html#decision-trees)* |[决策树](https://scikit-learn.org/stable/modules/tree.html#regression)* |[决策树](https://scikit-learn.org/stable/modules/tree.html#regression)
[K 最近的邻域](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K 最近的邻域](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)* |[K 近邻](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[线性 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)* |[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[支持矢量分类 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)* |[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)* |[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)* |[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)* |[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* |[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)* | [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 分类器](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier) |[DNN 回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 线性分类器](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[线性回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor) |[线性回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)* |[快速线性回归量](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[Auto-ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)* |[联机梯度下降回归量](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
|[平均感知器分类器](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[线性 SVM 分类器](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)* ||

使用 `AutoMLConfig` 构造函数中的 `task` 参数来指定实验类型。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>数据源和格式

自动化机器学习支持驻留在本地桌面上或云中（例如 Azure Blob 存储）的数据。 可以将数据读入 Pandas DataFrame 或 Azure 机器学习 TabularDataset 中。  [详细了解数据集](how-to-create-register-datasets.md)。

数据训练要求：
- 数据必须为表格格式。
- 要预测的值（目标列）必须在数据中。

下面的代码示例展示了如何以这些格式存储数据。

* TabularDataset

  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* Pandas 数据帧

  ```python
  import pandas as pd
  from sklearn.model_selection import train_test_split

  df = pd.read_csv("your-local-file.csv")
  train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
  label = "label-col-name"
  ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>在远程计算中提取用于运行试验的数据

对于远程执行，训练数据必须是可以从远程计算访问的。 SDK 中的类 [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) 公开功能，以便于能够：

* 轻松地将数据从静态文件或 URL 源传输到工作区
* 让数据可用于在云计算资源上运行的训练脚本

有关使用 `Dataset` 类将数据装载到计算目标的示例，请参阅[操作指南](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets)。

## <a name="train-and-validation-data"></a>训练和验证数据

可以直接在 `AutoMLConfig` 构造函数中指定单独的训练集和验证集。

### <a name="k-folds-cross-validation"></a>K 折交叉验证

使用 `n_cross_validations` 设置指定交叉验证的数目。 训练数据集将随机拆分为大小相等的 `n_cross_validations` 折。 在每个交叉验证轮次，某个折将用于验证剩余折上训练的模型。 重复此过程 `n_cross_validations` 次，直到每个折作为验证集使用了一次。 将报告在所有 `n_cross_validations` 轮次中获得的平均评分，并基于整个训练数据集重新训练相应的模型。

详细了解 autoML 如何应用交叉验证来[防止过度拟合模型](concept-manage-ml-pitfalls.md#prevent-over-fitting)。
### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo 交叉验证（重复随机子采样）

使用 `validation_size` 指定应该用于验证的训练数据集百分比，并使用 `n_cross_validations` 指定交叉验证的数目。 在每个交叉验证轮次，将随机选择 `validation_size` 大小的子集来验证基于剩余数据训练的模型。 最后，将报告在所有 `n_cross_validations` 轮次中获得的平均评分，并基于整个训练数据集重新训练相应的模型。 时序预测不支持 Monte Carlo。

### <a name="custom-validation-dataset"></a>自定义验证数据集

如果随机拆分不可接受（通常是时序数据或不平衡的数据），请使用自定义验证数据集。 可以指定自己的验证数据集。 将会根据指定的验证数据集而不是随机数据集来评估模型。

## <a name="compute-to-run-experiment"></a>用于运行试验的计算环境

接下来，确定要在何处训练模型。 自动化机器学习训练试验可在以下计算选项中运行：
* 本地台式机或便携式计算机等本地计算机  – 如果数据集较小，并且仍处于探索阶段，则通常使用此选项。
* 云中的远程计算机 – [Azure 机器学习托管计算](concept-compute-target.md#amlcompute)是一个托管服务，可用于在 Azure 虚拟机群集上训练机器学习模型。 

  有关包含本地和远程计算目标的笔记本示例，请访问此 [GitHub 站点](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)。

* Azure 订阅中的 Azure Databricks 群集。 如需了解更多详情，请参阅[为自动化 ML 设置 Azure Databricks 群集](how-to-configure-environment.md#azure-databricks)

  有关包含 Azure Databricks 的笔记本示例，请访问此 [GitHub 站点](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)。

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
       blacklist_models=['XGBoostClassifier'],
       training_data=train_data,
       label_column_name=label,
       n_cross_validations=2)
   ```
2. 下面是设置为在 60 分钟后结束的回归实验示例，其中包含 5 折交叉验证。

   ```python
      automl_regressor = AutoMLConfig(
      task='regression',
      experiment_timeout_minutes=60,
      whitelist_models=['KNN'],
      primary_metric='r2_score',
      training_data=train_data,
      label_column_name=label,
      n_cross_validations=5)
   ```

三个不同的 `task` 参数值（第三个任务类型为 `forecasting`，并使用类似的算法池作为 `regression` 任务）确定要应用的模型的列表。 使用 `whitelist` 或 `blacklist` 参数通过要包含或排除的可用模型来进一步修改迭代。 可以在[分类](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification)、[预测](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)和[回归](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression)的 [SupportedModels 类](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels)中找到受支持的模型列表。

为了帮助避免出现实验超时故障，自动化 ML 的验证服务要求将 `experiment_timeout_minutes` 设置为至少 15 分钟，或在按列排列的行的大小超过 10,000,000 时设置为 60 分钟。

### <a name="primary-metric"></a>主要指标
主要指标确定要在模型训练期间用于优化的指标。 可选择的可用指标由你选择的任务类型决定，下表显示了每种任务类型的有效主要指标。

|分类 | 回归 | 时序预测
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

若要了解这些指标的具体定义，请参阅[了解自动化机器学习结果](how-to-understand-automated-ml.md)。

### <a name="data-featurization"></a>数据特征化

在每个自动化机器学习实验中，数据都是[自动缩放和规范化](concept-automated-ml.md#preprocess)，以帮助对不同规模上的特征敏感的某些算法。  不过，还可以启用其他特征化，如缺失值插补、编码和转换。 [详细了解包含了什么特征化](how-to-use-automated-ml-for-ml-models.md#featurization)。

配置实验时，可以启用高级设置 `featurization`。 下表列出了 [AutoMLConfig 类](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)中的特征化的已接受设置。

|特征化配置 | 说明 |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 指明应使用自定义的特征化步骤。 [了解如何自定义特征化](how-to-configure-auto-train.md#customize-feature-engineering)。|
|`"featurization": 'off'`| 指明不得自动完成特征化步骤。|
|`"featurization": 'auto'`| 指明在预处理过程中自动执行[数据护栏和特征化步骤](how-to-use-automated-ml-for-ml-models.md#advanced-featurization-options)。|

> [!NOTE]
> 自动化机器学习特征化步骤（特征规范化、处理缺失数据、将文本转换为数字等）成为基础模型的一部分。 当你使用模型进行预测时，在训练期间应用的相同特征化步骤会自动应用于输入数据。

### <a name="time-series-forecasting"></a>时序预测
时序 `forecasting` 任务需要在配置对象中使用其他参数：

1. `time_column_name`：必需参数，用于定义训练数据中包含有效时序的列的名称。
1. `max_horizon`：根据训练数据的周期性定义要预测的时间长度。 例如，如果你有使用每日时间粒度的训练数据，则可以定义要训练模型的天数。
1. `grain_column_names`：定义训练数据中包含单独时序数据的列的名称。 例如，若要按店铺预测特定品牌的销售额，则可以将店铺列和品牌列定义为粒度列。 将会为每个粒度/分组创建单独的时序和预测。 

有关下面使用的设置的示例，请参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)。

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
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

### <a name="ensemble-configuration"></a><a name="ensemble"></a> 融合配置

融合模型默认启用，并显示为自动化机器学习运行中的最终运行迭代。 目前支持的融合方法是投票和堆叠。 投票实现为使用加权平均数的软投票，堆叠实现使用的是两层实现，其中第一层包含与投票融合相同的模型，第二层模型用于从第一层中查找模型的最佳组合。 如果你使用的是 ONNX 模型，或启用了模型可解释性，那么堆叠会被禁用，只会使用投票。

有多个默认参数可以作为 `kwargs` 在 `AutoMLConfig` 对象中提供，用于更改默认融合行为。

* `ensemble_download_models_timeout_sec`：在 VotingEnsemble 和 StackEnsemble 模型生成期间，会下载来自先前子运行的多个拟合模型。 如果遇到此错误 `AutoMLEnsembleException: Could not find any models for running ensembling`，则可能需要为要下载的模型提供更多时间。 默认值为 300 秒并行下载这些模型，且没有最大超时限制。 如果需要更多时间，请将此参数配置为大于 300 秒的值。 

  > [!NOTE]
  >  如果已超时且下载了模型，则融合会使用它下载的多个模型继续执行。 并不需要下载所有模型才能在超时内完成。

以下参数只应用于 StackEnsemble 模型： 

* `stack_meta_learner_type`：元学习器是针对单个异类模型的输出而训练出来的模型。 默认的元学习器是用于分类任务的 `LogisticRegression`（或为 `LogisticRegressionCV`，如果启用了交叉验证的话），以及用于回归/预测任务的 `ElasticNet`（或为 `ElasticNetCV`，如果启用了交叉验证的话）。 此参数可以是下列字符串之一：`LogisticRegression`、`LogisticRegressionCV`、`LightGBMClassifier`、`ElasticNet`、`ElasticNetCV`、`LightGBMRegressor` 或 `LinearRegression`。

* `stack_meta_learner_train_percentage`：指定（在选择训练的训练类型和验证类型时）要为训练元学习器预留的训练集的比例。 默认值为 `0.2`。 

* `stack_meta_learner_kwargs`：要传递给元学习器的初始值设定项的可选参数。 这些参数和参数类型与相应模型构造函数中的参数和参数类型形成镜像，并转发到模型构造函数。

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

融合训练默认启用，但可以使用 `enable_voting_ensemble` 和 `enable_stack_ensemble` 布尔参数来禁用。

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

### <a name="exit-criteria"></a><a name="exit"></a> 退出条件

有几个选项可供定义来结束实验。
1. 无条件：如果你没有定义任何退出参数，实验会继续，直到主要指标没有进一步的进展。
1. 在一段时间后退出：在设置中使用 `experiment_timeout_minutes`，可以定义实验继续运行多久（以分钟为单位）。
1. 在达到分数后退出：使用 `experiment_exit_score` 会在达到主要指标分数后完成实验。

### <a name="explore-model-metrics"></a>探索模型指标

可以在小组件中查看训练结果，也可以内联查看（如果没有离开笔记本的话）。 有关更多详细信息，请参阅[跟踪和评估模型](how-to-track-experiments.md#view-run-details)。

## <a name="understand-automated-ml-models"></a>了解自动化 ML 模型

任何使用自动化 ML 生成的模型都包括以下步骤：
+ 自动化特征工程（如果 `"featurization": 'auto'`）
+ 缩放/规范化和包含超参数值的算法

我们使从自动化 ML 的 fitted_model 输出中获取此信息变得透明。

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>自动化特征工程

查看在 `"featurization": 'auto'` 时发生的预处理和[自动化特征工程](concept-automated-ml.md#preprocess)的列表。

请看以下示例：
+ 有四种输入特征：A（数值）、B（数值）、C（数值）、D（日期/时间）
+ 数值特征 C 被删除，因为它是包含所有唯一值的 ID 列
+ 数值特征 A 和 B 包含缺失值，因此由平均值进行插补
+ 日期/时间特征 D 特征化为 11 个不同的工程特征

若要了解更多信息，请在拟合模型的第一个步骤中使用下面这两个 API。  请参阅[此示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。

+ API 1：`get_engineered_feature_names()` 返回工程特征名称的列表。

  用法：
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  此列表包括所有工程特征的名称。

  >[!Note]
  >对于“预测”任务，请使用“timeseriestransformer”；对于其他“回归”或“分类”任务，请使用“datatransformer”。

+ API 2：`get_featurization_summary()` 返回所有输入特征的特征化摘要。

  用法：
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >对于“预测”任务，请使用“timeseriestransformer”；对于其他“回归”或“分类”任务，请使用“datatransformer”。

  输出：
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   其中：

   |输出|定义|
   |----|--------|
   |RawFeatureName|所提供的数据集中的输入特征/列名称。|
   |TypeDetected|检测到的输入特征数据类型。|
   |Dropped|指明输入特征是被删除了还是被使用了。|
   |EngineeringFeatureCount|通过自动化特征工程转换生成的特征数。|
   |转换|应用于输入特征以生成工程特征的转换的列表。|
   
### <a name="customize-feature-engineering"></a>自定义特征工程
若要自定义特征工程，请指定  `"featurization": FeaturizationConfig`。

支持的自定义包括：

|自定义|定义|
|--|--|
|列用途更新|重写指定列的特征类型。|
|转换器参数更新 |更新指定转换器的参数。 目前支持缺失值处理器（插补平均值、出现频率最大的值和中值）和 HashOneHotEncoder。|
|删除列 |要从特征化中删除的列。|
|块转换器| 用于特征化进程的块转换器。|

使用 API 调用来创建 FeaturizationConfig 对象：
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

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>缩放/规范化和包含超参数值的算法：

若要了解管道的缩放/规范化和算法/超参数值，请使用 fitted_model.steps。 [详细了解缩放/规范化](concept-automated-ml.md#preprocess)。 下面是示例输出：

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

若要获取更多详细信息，请使用此帮助程序函数： 

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(model)
```

下面的示例输出用于使用特定算法（在此示例中为包含 RobustScalar 的 LogisticRegression）的管道。

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>预测类概率

使用自动化 ML 的生成的模型都包含包装器对象，这些对象与开放源代码原始类中的功能形成镜像。 自动化 ML 返回的大多数分类模型包装器对象实现 `predict_proba()` 函数，此函数接受特征（X 值）的类数组或稀疏矩阵数据示例，并返回每个示例及其各自的类概率的 n 维数组。

假设你已使用上面相同的调用检索了最佳运行和拟合模型，可以直接从拟合模型调用 `predict_proba()`，同时以相应格式（因模型类型而异）提供 `X_test` 示例。

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

如果基础模型不支持 `predict_proba()` 函数或格式不正确，则会抛出特定于模型类的异常。 有关如何针对不同的模型类型实现此函数的示例，请参阅 [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) 和 [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) 参考文档。

<a name="explain"></a>

## <a name="model-interpretability"></a>模型可解释性

借助模型可解释性，可以了解模型为什么进行预测，以及基础特征重要性值。 SDK 包括各种包，用于在训练和推理时为本地和已部署的模型启用模型可解释性特征。

有关如何在自动化机器学习实验中专门启用可解释性特征的代码示例，请参阅[操作指南](how-to-machine-learning-interpretability-automl.md)。

有关如何在自动化机器学习之外的 SDK 其他区域中启用模型解释和特征重要性的一般信息，请参阅这篇关于可解释性的[概念](how-to-machine-learning-interpretability.md)文章。

## <a name="next-steps"></a>后续步骤

+ 详细了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。

+ 详细了解[如何使用自动化机器学习训练回归模型](tutorial-auto-train-models.md)或[如何使用自动化机器学习对远程资源进行训练](how-to-auto-train-remote.md)。
+ 了解如何在[多模型解决方案加速器](https://aka.ms/many-models)中使用 autoML 训练多个模型。
