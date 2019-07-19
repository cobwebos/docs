---
title: 优化模型的超参数
titleSuffix: Azure Machine Learning service
description: 使用 Azure 机器学习服务，有效地优化深度学习/机器学习模型的超参数。 本文将介绍如何定义参数搜索空间、指定要优化的主要指标，以及提前终止性能不佳的运行。
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/08/2019
ms.custom: seodec18
ms.openlocfilehash: 730f39bf0b05ef33bbbca150532f96f1e495a9ed
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302353"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning-service"></a>使用 Azure 机器学习服务优化模型的超参数

使用 Azure 机器学习服务有效地优化模型的超参数。  超参数优化包括以下步骤：

* 定义参数搜索空间
* 指定要优化的主要指标  
* 指定提前终止性能不佳运行的条件
* 分配资源用于优化超参数
* 使用上述配置启动试验
* 将训练运行可视化
* 为模型选择最佳性能配置

## <a name="what-are-hyperparameters"></a>什么是超参数？

超参数是所选的可调整参数，用于训练控制训练过程本身的模型。 例如，要想训练深度神经网络，需要在训练模型前确定网络中隐藏层的数量，以及每个层中的节点数量。 这些值通常在训练过程中保持恒定。

在深度学习/机器学习方案中，模型性能在很大程度上取决于选择的超参数值。 超参数探索的目标是搜索不同的超参数配置，从而找到可以提供最佳性能的配置。 如果搜索空间庞大并且评估每个配置所需的开销很大，则超参数探索过程通常是一个繁琐的手动过程。

使用 Azure 机器学习可以有效地将超参数探索过程自动化，从而节省大量时间和资源。 指定超参数值的范围及训练运行的最大数量。 然后，系统会自动启动多个具有不同参数配置且同时进行的运行，并找到可以提供最佳性能的配置（由你选择的指标来度量）。 性能不佳的训练运行会自动提前终止，从而可以减少计算资源的浪费。 这些资源可以用来探索其他超参数配置。


## <a name="define-search-space"></a>定义搜索空间

通过探索针对每个超参数定义的值范围来自动优化超参数。

### <a name="types-of-hyperparameters"></a>超参数的类型

每个超参数可以是离散的，也可以是连续的。

#### <a name="discrete-hyperparameters"></a>离散超参数 

离散超参数将指定为离散值中的一个 `choice`。 `choice` 可以是：

* 一个或多个逗号分隔值
* `range` 对象
* 任意 `list` 对象


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

在这种情况下，`batch_size` 采用 [16、32、64、128] 中的一个值，`number_of_hidden_layers` 采用 [1、2、3、4] 中的一个值。

也可以使用分布来指定高级离散超参数。 支持以下分布：

* `quniform(low, high, q)` - 返回类似于 round(uniform(low, high) / q) * q 的值
* `qloguniform(low, high, q)` - 返回类似于 round(exp(uniform(low, high)) / q) * q 的值
* `qnormal(mu, sigma, q)` - 返回类似于 round(normal(mu, sigma) / q) * q 的值
* `qlognormal(mu, sigma, q)` - 返回类似于 round(exp(normal(mu, sigma)) / q) * q 的值

#### <a name="continuous-hyperparameters"></a>连续超参数 

将连续超参数指定为一个连续值范围内的分布。 支持的分布包括：

* `uniform(low, high)` - 返回高低之间的均匀分布值
* `loguniform(low, high)` - 返回根据 exp(uniform(low, high)) 绘制的值，使返回值的对数均匀分布
* `normal(mu, sigma)` - 返回正态分布的实际值，包括平均值 μ 和标准方差 σ
* `lognormal(mu, sigma)` - 返回根据 exp(normal(mu, sigma)) 绘制的值，使返回值的对数呈正态分布

参数空间定义的示例：

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

此代码定义具有两个参数（`learning_rate` 和 `keep_probability`）的搜索空间。 `learning_rate` 包含平均值为 10、标准偏差为 3 的正态分布。 `keep_probability` 包含最小值为 0.05、最大值为 0.1 的均匀分布。

### <a name="sampling-the-hyperparameter-space"></a>超参数空间采样

还可以指定参数采样方法来取代超参数空间定义。 Azure 机器学习服务支持随机采样、网格采样和贝叶斯采样。

#### <a name="random-sampling"></a>随机采样

在随机采样中，超参数值是从定义的搜索空间中随机选择的。 [随机采样](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py)允许搜索空间同时包含离散的和连续的超参数。

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>网格采样

[网格采样](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py)对定义的搜索空间中的所有可行值执行简单的网格搜索。 只能对使用 `choice` 指定的超参数使用此方法。 例如，以下空间总共包含 6 个样本：

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>贝叶斯采样

[Bayesian 采样](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py)基于 Bayesian 优化算法, 并使超参数值上的智能选择成为接下来的示例。 它会根据以前的采样方式选取样本，使新样本能够改进报告的主要指标。

使用贝叶斯采样时，并发运行的数目会影响优化效果。 通常情况下，数目较小的并发运行可能带来更好的采样收敛，因为较小的并行度会增加可从先前完成的运行中获益的运行的数量。

贝叶斯采样仅支持搜索空间中的 `choice` 和 `uniform` 分布。 

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> 贝叶斯采样不支持任何提前终止策略（请参阅[指定提前终止策略](#specify-early-termination-policy)）。 使用贝叶斯参数采样时，请设置 `early_termination_policy = None`，或不使用 `early_termination_policy` 参数。

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>指定主要指标

指定想要超参数优化试验优化的[主要指标](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py)。 将根据此主要指标评估每个训练运行。 性能不佳的运行（其主要指标不符合提前终止策略设置的条件）将会终止。 除了主要指标名称以外，还需指定优化的目标是要最大化还是最小化主要指标。

* `primary_metric_name`：要优化的主要指标的名称。 主要指标的名称需要与训练脚本记录的指标名称完全匹配。 请参阅[记录用于超参数优化的指标](#log-metrics-for-hyperparameter-tuning)。
* `primary_metric_goal`：可以是 `PrimaryMetricGoal.MAXIMIZE` 或 `PrimaryMetricGoal.MINIMIZE`，确定在评估运行时是要将主要指标最大化还是最小化。 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

优化运行以最大化“准确性”。  确保在训练脚本中记录此值。

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>记录用于超参数优化的指标

模型的训练脚本必须在模型训练过程中记录相关指标。 配置超参数优化时，指定要用于评估运行性能的主要指标。 （请参阅[指定要优化的主要指标](#specify-primary-metric-to-optimize)。）必须在训练脚本中记录此指标，以便将其用于超参数优化过程。

使用以下示例代码片段将此指标记录在训练脚本中：

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

训练脚本将计算 `val_accuracy`，并将其记录为“准确性”，它会用作主要指标。 每次记录指标时，超参数优化服务都将收到该指标。 由模型开发人员确定报告此指标的频率。

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>指定提前终止策略

使用 [提前终止策略, 自动终止性能不佳的运行。 终止可以减少资源浪费，并将这些资源用于探索其他参数配置。

使用提前终止策略时，可以配置以下参数来控制何时应用该策略：

* `evaluation_interval`：应用策略的频率。 每次训练脚本都会将主要指标计数记录为一个间隔。 因此，如果 `evaluation_interval` 为 1，则训练脚本每次报告主要指标时，都会应用策略。 如果 `evaluation_interval` 为 2，则训练脚本每两次报告主要指标时会应用策略。 如果未指定，则默认将 `evaluation_interval` 设置为 1。
* `delay_evaluation`：将第一个策略评估延迟指定的间隔数。 这是一个可选参数，可让所有配置运行初始设置的最小间隔数，避免训练运行过早终止。 如果已指定，则每隔大于或等于 delay_evaluation 的 evaluation_interval 倍数应用策略。

Azure 机器学习服务支持以下提前终止策略。

### <a name="bandit-policy"></a>老虎机策略

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition)是基于可宽延时间系数/时差和评估间隔的终止策略。 如果主要指标不在与最佳性能训练运行相关的指定松驰因子/松驰数量范围内，则此策略会提前终止任何运行。 此策略采用以下配置参数：

* `slack_factor` 或 `slack_amount`：在最佳性能训练运行方面允许的松驰。 `slack_factor` 以比率的形式指定允许的松驰。 `slack_amount` 以绝对数量（而不是比率）的形式指定允许的松驰。

    例如，假设以间隔 10 应用老虎机策略。 另外，性能最佳的运行以间隔 10 报告了主要指标 0.8，目标是最大化主要指标。 如果为策略指定的 `slack_factor` 为 0.2，则间隔为 10 时其最佳指标小于 0.66 (0.8/(1+`slack_factor`)) 的任何训练运行将被终止。 如果为策略指定的 `slack_amount` 为 0.2，则间隔为 10 时其最佳指标小于 0.6 (0.8-`slack_amount`) 的任何训练运行将被终止。
* `evaluation_interval`：应用策略的频率（可选参数）。
* `delay_evaluation`：将第一个策略评估延迟指定的间隔数（可选参数）。


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

在此示例中，指标报告时将在每个间隔应用提前终止策略，从评估间隔 5 开始。 其最佳指标小于最佳性能运行的 (1/(1+0.1) 或 91% 的任何运行将被终止。

### <a name="median-stopping-policy"></a>中间值停止策略

[中间停止](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py)是基于运行时所报告的主要指标的运行平均值的早期终止策略。 此策略计算所有训练运行的运行平均值，并终止其性能比运行平均值的中间值更差的运行。 此策略采用以下配置参数：
* `evaluation_interval`：应用策略的频率（可选参数）。
* `delay_evaluation`：将第一个策略评估延迟指定的间隔数（可选参数）。


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

在此示例中，将在每个间隔应用提前终止策略，从评估间隔 5 开始。 如果某个运行的最佳主要指标比所有训练运行中间隔 1:5 的运行平均值的中间值更差，则在间隔 5 处终止该运行。

### <a name="truncation-selection-policy"></a>截断选择策略

[截断选择](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py)将取消每个评估时间间隔内最低执行运行的给定百分比。 根据运行的性能和主要指标比较运行，并终止 X% 的性能最低的运行。 此策略采用以下配置参数：

* `truncation_percentage`：要在每个评估间隔终止的性能最低的运行百分比。 指定一个介于 1 到 99 之间的整数值。
* `evaluation_interval`：应用策略的频率（可选参数）。
* `delay_evaluation`：将第一个策略评估延迟指定的间隔数（可选参数）。


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

在此示例中，将在每个间隔应用提前终止策略，从评估间隔 5 开始。 如果某个运行在间隔 5 处的性能，在间隔 5 的所有运行中处于 20% 的性能最低的运行范围内，则会终止该运行。

### <a name="no-termination-policy"></a>无终止策略

如果希望所有训练运行一直运行到完成，请将策略设置为“无”。 这样就不会应用任何提前终止策略。

```Python
policy=None
```

### <a name="default-policy"></a>默认策略

如果未指定策略，则超参数优化服务将让所有训练运行一直运行到完成。

>[!NOTE] 
>如果你正在寻找既可以节省成本又不会终止有前景的作业的保守策略，则可以使用 `evaluation_interval` 为 1 且 `delay_evaluation` 为 5 的中间值停止策略。 这属于保守的设置，可以提供大约 25%-35% 的节省，且不会造成主要指标损失（基于我们的评估数据）。

## <a name="allocate-resources"></a>分配资源

通过指定训练运行的最大总数，控制超参数优化试验的资源预算。  （可选）指定超参数优化试验的最长持续时间。

* `max_total_runs`：要创建的训练运行的最大总数。 上限 - 例如，如果超参数空间有限并且样本较少，则运行数也会较少。 必须是介于 1 和 1000 之间的数字。
* `max_duration_minutes`：超参数优化试验的最长持续时间（分钟）。 此参数可选。如果已指定，则会自动取消在此持续时间后运行的所有运行。

>[!NOTE] 
>如果同时指定了 `max_total_runs` 和 `max_duration_minutes`，在达到其中的第一个阈值时，会终止超参数优化试验。

此外，指定在超参数优化搜索期间要并行运行的最大训练运行数。

* `max_concurrent_runs`：在任意给定时刻要并行运行的最大运行数。 如果未指定，将并行启动所有 `max_total_runs`。 如果要指定，值必须是介于 1 和 100 之间的数字。

>[!NOTE] 
>并发运行数根据指定计算目标中的可用资源进行限制。 因此，需要确保计算目标能够为所需的并发性提供足够的可用资源。

分配用于优化超参数的资源：

```Python
max_total_runs=20,
max_concurrent_runs=4
```

此代码会将超参数优化试验配置为总共最多使用 20 个运行，每次运行 4 个配置。

## <a name="configure-experiment"></a>配置试验

使用上述部分中定义的超参数搜索空间、提前终止策略、主要指标和资源分配来[配置超参数优化](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py)试验。 此外，提供将结合采样的超参数调用的 `estimator`。 `estimator` 描述运行的训练脚本、每个作业的资源（单个或多个 GPU）以及要使用的计算目标。 由于超参数优化试验的并发性受可用资源限制，所以请确保 `estimator` 中指定的计算目标能够为所需的并发性提供足够的资源。 （有关估算器的详细信息，请参阅[如何训练模型](how-to-train-ml-models.md)。）

配置超参数优化试验：

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>提交试验

定义超参数优化配置后, 请[提交试验](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name` 是要分配给超参数优化试验的名称，`workspace` 是要在其中创建试验的工作区（有关试验的详细信息，请参阅 [Azure 机器学习的工作原理](concept-azure-machine-learning-architecture.md)）

## <a name="visualize-experiment"></a>将实验可视化

Azure 机器学习 SDK 提供了一[小的笔记本小组件](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py), 可直观显示定型运行进度。 以下代码片段可在 Jupyter 笔记本中的一个位置可视化所有的超参数优化运行：

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

此代码会显示一个表格，其中详细描述了每个超参数配置的训练运行。

![超参数优化表](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

还可以将每个运行的性能可视化为训练进度。 

![超参数优化绘图](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

此外，可以使用“并行坐标绘图”来直观地识别各个超参数的性能与值之间的关联。 

![超参数优化并行坐标](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)

还可将 Azure Web 门户中的所有超参数优化运行可视化。 若要详细了解如何在 Web 门户中查看试验，请参阅[如何跟踪试验](how-to-track-experiments.md#view-the-experiment-in-the-web-portal)。

![超参数优化门户](media/how-to-tune-hyperparameters/HyperparameterTuningPortal.png)

## <a name="find-the-best-model"></a>找到最佳模型

所有超参数优化运行完成后,[确定最佳的执行配置](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true-)和相应的超参数值:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>示例 Notebook
请参阅以下文件夹中的超参数-* 笔记本:
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤
* [跟踪试验](how-to-track-experiments.md)
* [部署定型的模型](how-to-deploy-and-where.md)
