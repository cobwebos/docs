---
title: 자동화된 ML 실험 만들기
titleSuffix: Azure Machine Learning
description: 자동화된 기계 학습은 사용자를 위한 알고리즘을 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 기계 학습 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: c1ebedcf93d66c01c80f7f40171a7aa27441488d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722146"
---
# <a name="configure-automated-ml-experiments-in-python"></a>在 Python 中配置自动 ML 试验
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本指南介绍如何通过[AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)定义自动机器学习试验的各种配置设置。 자동화된 Machine Learning은 사용자를 위한 알고리즘과 하이퍼 매개 변수를 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 Machine Learning 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다.

若要查看自动机器学习试验的示例，请参阅[教程：使用自动化机器学习](tutorial-auto-train-models.md)或[云中的自动化机器学习](how-to-auto-train-remote.md)来训练分类模型。

자동화된 기계 학습에서 사용할 수 있는 구성 옵션은 다음과 같습니다.

* 选择试验类型：分类、回归或时序预测
* 데이터 원본, 형식 및 데이터 가져오기
* 컴퓨팅 대상 선택: 로컬 또는 원격
* 자동화된 Machine Learning 실험 설정
* 자동화된 Machine Learning 실험 실행
* 모델 메트릭 탐색
* 모델 등록 및 배포

如果你不愿意，还可以[在 Azure 机器学习 studio 中创建自动化机器学习试验](how-to-create-portal-experiments.md)。

## <a name="select-your-experiment-type"></a>실험 유형 선택

실험을 시작하기 전에 해결하려는 기계 학습 문제의 종류를 결정해야 합니다. 自动机器学习支持分类、回归和预测的任务类型。 详细了解[任务类型](how-to-define-task-type.md)。

자동화된 Machine Learning은 자동화 및 튜닝 프로세스 중에 다음 알고리즘을 지원합니다. 사용자는 알고리즘을 지정할 필요가 없습니다.

분류 | 회귀 | 时序预测
|-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)|[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[支持矢量分类（SVC）](https://scikit-learn.org/stable/modules/svm.html#classification)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 分类器](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN 回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 线性分类器](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[线性回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[线性回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[Fast 线性回归量](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[自动 ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[联机梯度下降回归量](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
|[平均感知器分类器](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[线性 SVM 分类器](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

使用 `AutoMLConfig` 构造函数中的 `task` 参数来指定实验类型。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>데이터 원본 및 형식

자동화된 Machine Learning은 로컬 데스크톱 또는 Azure Blob Storage와 같은 클라우드의 데이터를 지원합니다. 数据可以读入**Pandas 数据帧**或**Azure 机器学习 TabularDataset**。  [了解有关数据集的详细信息](how-to-create-register-datasets.md)。

定型数据的要求：
- 数据必须为表格格式。
- 要预测的值（目标列）必须位于数据中。

下面的代码示例演示如何以这些格式存储数据。

* TabularDataset
  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* pandas 데이터 프레임

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split

    df = pd.read_csv("your-local-file.csv")
    train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
    label = "label-col-name"
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>원격 컴퓨팅에서 실험을 실행하기 위한 데이터 가져오기

对于远程执行，必须可从远程计算访问定型数据。 SDK 中的类[`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)公开功能：

* 轻松地将数据从静态文件或 URL 源传输到工作区
* 在云计算资源上运行时，使数据可用于训练脚本

有关使用 `Dataset` 类将数据装载到计算目标的示例，请参阅[操作方法](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target)。

## <a name="train-and-validation-data"></a>데이터 학습 및 유효성 검사

您可以直接在 `AutoMLConfig` 构造函数中指定单独的定型集和验证集。

### <a name="k-folds-cross-validation"></a>K 접기 교차 유효성 검사

`n_cross_validations` 설정을 사용하여 교차 유효성 검사의 수를 지정합니다. 학습 데이터 집합은 무작위로 동일한 크기의 `n_cross_validations` 접기로 분할됩니다. 각 교차 유효성 검사 라운드 중에 접기 중 하나는 나머지 접기에 대해 학습된 모델의 유효성 검사에 사용됩니다. 이 프로세스는 각 접기가 유효성 검사 집합으로 한 번 사용될 때까지 `n_cross_validations` 라운드 동안 반복됩니다. 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 세트에 대해 다시 학습됩니다.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo 交叉验证（重复随机子采样）

`validation_size`를 사용하여 유효성 검사에 사용해야 하는 학습 데이터 세트의 비율을 지정하고, `n_cross_validations`를 사용하여 교차 유효성 검사의 수를 지정합니다. 각 교차 유효성 검사 라운드 중에 나머지 데이터에 대해 학습된 모델의 유효성 검사를 위해 `validation_size` 크기의 하위 집합이 무작위로 선택됩니다. 마지막으로, 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 집합에 대해 다시 학습됩니다. Monte Carlo 不支持时序预测。

### <a name="custom-validation-dataset"></a>사용자 지정 유효성 검사 데이터 세트

如果不接受随机拆分，则使用自定义验证数据集，通常为时序数据或不均衡数据。 사용자 고유의 유효성 검사 데이터 세트를 지정할 수 있습니다. 모델은 무작위 데이터 세트 대신 지정된 유효성 검사 데이터 세트에 대해 평가됩니다.

## <a name="compute-to-run-experiment"></a>실험 실행 컴퓨팅

다음으로, 모델을 학습할 위치를 결정합니다. 자동화된 Machine Learning 실험은 다음 컴퓨팅 옵션에서 실행할 수 있습니다.
*   로컬 데스크톱 또는 랩톱과 같은 로컬 머신 - 일반적으로 데이터 세트가 작고 아직 탐색 단계에 있는 경우입니다.
*   클라우드의 원격 머신 - [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute)는 Azure Virtual Machines 클러스터에서 Machine Learning 모델을 학습할 수 있도록 하는 관리형 서비스입니다.

    有关具有本地和远程计算目标的笔记本的示例，请参阅此[GitHub 站点](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)。

*   Azure 订阅中的 Azure Databricks 群集。 可在此处找到更多详细信息-[设置 Azure Databricks 群集以实现自动 ML](how-to-configure-environment.md#azure-databricks)

    请参阅此[GitHub 站点](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)以获取带有 Azure Databricks 的笔记本的示例。

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>실험 설정 구성

자동화된 Machine Learning 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다. 이러한 매개 변수는 `AutoMLConfig` 개체를 인스턴스화하여 설정됩니다. 매개 변수의 전체 목록은 [AutoMLConfig 클래스](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)를 참조하세요.

일부 사례:

1.  使用 AUC 加权作为主要指标的分类试验，其中 "试验超时分钟数" 设置为30分钟，2次交叉验证折叠。

    ```python
    automl_classifier=AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        blacklist_models='XGBoostClassifier',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=2)
    ```
2.  下面是在60分钟之后结束的回归试验集的一个示例，其中包含5次验证交叉折叠。

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        experiment_timeout_minutes=60,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5)
    ```

三个不同的 `task` 参数值（第三个任务类型为 `forecasting`，并使用类似的算法池作为 `regression` 任务）确定要应用的模型的列表。 使用 "`whitelist`" 或 "`blacklist` 参数" 可以进一步修改包含或排除的可用模型的迭代。 支持的模型的列表可以在[SupportedModels 类](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels)中找到。

### <a name="primary-metric"></a>기본 메트릭
主要指标用于确定要在模型定型期间用于优化的指标。 您可以选择的可用指标由您选择的任务类型决定，下表显示了每种任务类型的有效主要指标。

|분류 | 회귀 | 时序预测
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

了解这些指标的特定定义，了解[自动化机器学习结果](how-to-understand-automated-ml.md)。

### <a name="data-featurization"></a>数据特征化

在每个自动机器学习试验中，你的数据将[自动进行缩放和规范化](concept-automated-ml.md#preprocess)，以帮助*特定*的算法对不同规模的功能敏感。  但是，还可以启用其他特征化，例如缺失值插补法、编码和转换。 [详细了解所包含的特征化](how-to-create-portal-experiments.md#preprocess)。

若要启用此特征化，请为[`AutoMLConfig` 类](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)指定 `"featurization": 'auto'`。

> [!NOTE]
> 자동화된 기계 학습 사전 처리 단계(기능 정규화, 누락된 데이터 처리, 텍스트를 숫자로 변환 등)는 기본 모델의 일부가 됩니다. 예측에 모델을 사용하는 경우 학습 중에 적용되는 동일한 전처리 단계가 입력 데이터에 자동으로 적용됩니다.

### <a name="time-series-forecasting"></a>时序预测
时序 `forecasting` 任务需要配置对象中有其他参数：

1. `time_column_name`：必需的参数，用于定义定型数据中包含有效时序的列的名称。
1. `max_horizon`：根据定型数据的周期定义要预测的时间长度。 例如，如果您有使用每日时间粒度的定型数据，则可以定义要在多长时间内为模型定型。
1. `grain_column_names`：定义在定型数据中包含单独时序数据的列的名称。 例如，如果要按商店预测特定品牌的销售额，则可以将商店和品牌列定义为粒度列。 将为每个颗粒/分组创建单独的时间系列和预测。 

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

### <a name="ensemble"></a>系综配置

默认情况下启用系综模型，并在自动机器学习运行中显示为最终的运行迭代。 当前支持的系综方法是投票和堆栈。 投票是使用加权平均值作为软投票实现的，堆栈实现使用两层实现，其中第一层的模型与投票系综相同，第二层模型用于查找的最佳组合第一层中的模型。 如果使用的是 ONNX 模型，**或**启用了模型 explainability，则将禁用堆栈，并且仅使用投票。

有多个默认参数可以作为 `kwargs` 在 `AutoMLConfig` 对象中提供，以更改默认 stack 系综行为。

* `stack_meta_learner_type`：学习器是针对单个异类模型的输出训练的模型。 默认的元学习器 `LogisticRegression` 用于分类任务（或者，如果启用了交叉验证，则 `LogisticRegressionCV`）和 `ElasticNet` 用于回归/预测任务（如果启用了交叉验证，则为 `ElasticNetCV`）。 此参数可以是下列字符串之一： `LogisticRegression`、`LogisticRegressionCV`、`LightGBMClassifier`、`ElasticNet`、`ElasticNetCV`、`LightGBMRegressor`或 `LinearRegression`。
* `stack_meta_learner_train_percentage`：指定为定型学习器而保留定型集的比例（选择定型定型类型时）。 기본값은 `0.2`입니다.
* `stack_meta_learner_kwargs`：要传递给学习器的初始值设定项的可选参数。 这些参数和参数类型从相应的模型构造函数镜像参数和参数类型，并转发到模型构造函数。

下面的代码演示了一个在 `AutoMLConfig` 对象中指定自定义系综行为的示例。

```python
ensemble_settings = {
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

默认情况下，系综训练是启用的，但可以通过使用 `enable_voting_ensemble` 和 `enable_stack_ensemble` 布尔参数来禁用。

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

## <a name="run-experiment"></a>실험 실행

对于自动 ML，你将创建一个 `Experiment` 对象，该对象是用于运行试验的 `Workspace` 中的命名对象。

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

실행하려는 실험을 제출하고 모델을 생성합니다. `AutoMLConfig`를 `submit` 메서드에 전달하여 모델을 생성합니다.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>새 머신에 먼저 종속성이 설치됩니다.  출력이 표시되는 데 최대 10분이 걸릴 수 있습니다.
>`show_output`을 `True`로 설정하면 출력이 콘솔에 표시됩니다.

### <a name="exit-criteria"></a>退出条件
可以定义几个选项来结束实验。
1. 无标准：如果未定义任何退出参数，则试验将继续，直到不会对主要指标进行进一步的处理。
1. 在一段时间后退出：通过使用设置中的 `experiment_timeout_minutes`，你可以定义实验在运行中的运行时间（分钟）。
1. 达到分数后退出：使用 `experiment_exit_score` 将在达到主要指标分数后完成试验。

### <a name="explore-model-metrics"></a>모델 메트릭 탐색

如果在笔记本中，则可以在小组件或内嵌项中查看训练结果。 자세한 내용은 [모델 추적 및 평가](how-to-track-experiments.md#view-run-details)를 참조하세요.

## <a name="understand-automated-ml-models"></a>了解自动 ML 模型

使用自动 ML 生成的任何模型都包括以下步骤：
+ 自动功能设计（如果 `"featurization": 'auto'`）
+ 缩放/规范化和具有超参数值的算法

我们使它成为从自动 ML 的 fitted_model 输出获取此信息的透明方法。

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>自动功能设计

请参阅 `"featurization": 'auto'`时所发生的预处理和[自动功能工程](concept-automated-ml.md#preprocess)的列表。

다음 예를 살펴보세요.
+ 有四种输入功能： A （数值）、B （数值）、C （数值）、D （日期时间）
+ 数值特征 C 被丢弃，因为它是具有所有唯一值的 ID 列
+ 数值特征 A 和 B 的值缺失，因此数据估算的平均值
+ DateTime 功能 D 特征化为11个不同的工程功能

在拟合模型的第一个步骤中使用这两个 Api 来了解更多信息。  请参阅[此示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。

+ API 1： `get_engineered_feature_names()` 返回工程功能名称的列表。

  Usage:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  此列表包括所有工程的功能名称。

  >[!Note]
  >请将 "timeseriestransformer" 用于任务 = "预测"，否则请将 "datatransformer" 用于 "回归" 或 "分类" 任务。

+ API 2： `get_featurization_summary()` 为所有输入功能返回特征化 summary。

  Usage:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >请将 "timeseriestransformer" 用于任务 = "预测"，否则请将 "datatransformer" 用于 "回归" 或 "分类" 任务。

  출력:
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

   장소:

   |출력|정의|
   |----|--------|
   |RawFeatureName|提供的数据集中的输入功能/列名称。|
   |TypeDetected|检测到输入功能的数据类型。|
   |删除|指示输入功能是否已删除或已被使用。|
   |EngineeringFeatureCount|通过自动功能工程转换生成的功能的数量。|
   |변환|应用于输入功能以生成工程功能的转换的列表。|
   
### <a name="customize-feature-engineering"></a>自定义功能设计
若要自定义功能设计，请指定 `"feauturization":FeaturizationConfig`。

支持的自定义项包括：

|사용자 지정|정의|
|--|--|
|列用途更新|重写指定列的功能类型。|
|转换器参数更新 |更新指定转换器的参数。 目前支持 Imputer （平均值、最频繁 & 中值）和 HashOneHotEncoder。|
|删除列 |要从特征化中删除的列。|
|块转换器| 阻止用于特征化进程的转换器。|

使用 API 调用创建 FeaturizationConfig 对象：
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

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>缩放/规范化和具有超参数值的算法：

若要了解管道的缩放/规范化和算法/超参数值，请使用 fitted_model。 [详细了解缩放/规范化](concept-automated-ml.md#preprocess)。 샘플 출력은 다음과 같습니다.

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

若要获取更多详细信息，请使用[此示例笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)中所示的帮助器函数。

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


print_model(fitted_model)
```

下面的示例输出适用于使用特定算法（在本例中为 RobustScalar，在本例中为 LogisticRegression）的管道。

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

使用自动 ML 的生成的模型都具有包装对象，这些对象通过其开源源类来镜像功能。 自动 ML 返回的大多数分类模型包装器对象实现 `predict_proba()` 函数，该函数接受功能的类似数组或稀疏矩阵数据示例（X 值），并返回每个样本及其各自的类概率的 n 维数组。

假设您已使用上述相同的调用检索最佳运行和拟合模型，则可以直接从拟合的模型调用 `predict_proba()`，根据模型类型提供相应格式的 `X_test` 示例。

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

如果基础模型不支持 `predict_proba()` 函数或格式不正确，则将引发特定于模型类的异常。 有关如何针对不同的模型类型实现此函数的示例，请参阅[RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba)和[XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html)参考文档。

<a name="explain"></a>

## <a name="model-interpretability"></a>모델 해석력

模型 interpretability 使您可以了解模型为何进行预测，以及基础特征重要性值。 SDK 包括各种包，用于在定型和推理时间为本地和已部署的模型启用模型 interpretability 功能。

有关如何在自动机器学习试验中启用 interpretability 功能的详细说明，请参阅操作[方法](how-to-machine-learning-interpretability-automl.md)。

有关如何在自动化机器学习之外的其他 SDK 区域中启用模型解释和功能重要性的一般信息，请参阅 interpretability 上的[概念](how-to-machine-learning-interpretability.md)文章。

## <a name="next-steps"></a>다음 단계

[모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.

详细了解[如何使用自动机器学习对回归模型定型](tutorial-auto-train-models.md)，或者[如何在远程资源上使用自动机器学习进行训练](how-to-auto-train-remote.md)。
