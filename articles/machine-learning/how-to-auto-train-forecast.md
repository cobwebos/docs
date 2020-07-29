---
title: 自动训练时序预测模型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习通过自动化机器学习来训练时序预测回归模型。
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/09/2020
ms.openlocfilehash: 4f27fc9542d6c4e9027c7a1a0d4daeb7cb079e81
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321541"
---
# <a name="auto-train-a-time-series-forecast-model"></a>自动训练时序预测模型
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，你将了解如何在 [Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py) 中使用自动化机器学习来配置和训练时序预测回归模型。 

有关低代码体验，请参阅[教程：使用自动化机器学习预测需求](tutorial-automated-ml-forecast.md)，里面有关于在 [Azure 机器学习工作室](https://ml.azure.com/)中使用自动化机器学习的时序预测示例。

配置预测模型与使用自动化机器学习设置标准回归模型很相似，但要处理时序数据，需执行一些配置选项和预处理步骤。 

例如，你可[配置](#config)预测应延伸至未来的多久时间（预测时间范围），以及预测滞后等等。 自动化 ML 会针对数据集和预测时间范围内的所有项目，习得通常有内部分支的单个模型。 这样就可以使用更多的数据来估计模型参数，使得未知系列的泛化成为可能。

下面示例演示了如何：

* 准备用于时序建模的数据
* 在 [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 对象中配置特定的时序参数
* 使用时序数据运行预测

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

与经典的时序方法不同，在自动化 ML 中，将“透视”过去的时序值，使其成为回归器与其他预测器的附加维度。 此方法会在训练过程中，将多个上下文变量及其关系彼此整合。 影响预测的因素有很多，因此该方法将自身与真实的预测场景很好地协调起来。 例如，在预测销售额时，历史趋势、汇率和价格相互作用，共同推动着销售结果。 

从训练数据中提取的特征扮演着重要的角色。 而且，自动化 ML 会执行标准预处理步骤并生成额外的时序特征，以捕捉季节性影响和最大程度提高预测准确性

## <a name="time-series-and-deep-learning-models"></a>时序和深度学习模型

通过自动化 ML 的深度学习，可预测单变量和多变量时序数据。

深度学习模型具有三个固有功能：
1. 可以从任意输入到输出映射进行学习
1. 支持多个输入和输出
1. 它们可自动提取横跨较长序列的输入数据中的模式

对于较大数据，深度学习模型（例如 Microsoft ForecastTCN）可提高生成的模型的分数。 了解如何[配置试验进行深度学习](#configure-a-dnn-enable-forecasting-experiment)。

自动化 ML 作为推荐系统的一部分向用户提供原生时序和深度学习模型。 

模型| 说明 | 优点
----|----|---
Prophet（预览版）|Prophet 最适合用于受季节影响大且包含多个季节历史数据的时序。 若要利用此模型，请使用 `pip install fbprophet` 在本地安装它。 | 准确、快速、可靠地反应时序中的离群值、缺失数据和巨大变化。
Auto-ARIMA（预览版）|自动回归集成移动平均 (ARIMA) 在数据静态时性能最佳。 这意味着其统计属性（例如平均值和方差）在整个集中保持不变。 例如，如果你掷一枚硬币，那么无论是今天掷、明天掷还是明年掷，正面朝上的可能性都是 50%。| 适用于单变量系列，这是因为使用过去的值来预测未来的值。
ForecastTCN（预览版）| ForecastTCN 是一种神经网络模型，旨在处理最苛刻的预测任务，从而捕获数据中的非线性本地和全局趋势以及时序之间的关系。|可利用数据中的复杂趋势并轻松扩展到最大型的数据集。

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 若要创建工作区，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
* 本文假设读者基本熟悉如何设置自动化机器学习试验。 遵循[教程](tutorial-auto-train-models.md)或[操作方法](how-to-configure-auto-train.md)，了解基本的自动化机器学习试验设计模式。

## <a name="preparing-data"></a>准备数据

自动化机器学习中预测回归任务类型和回归任务类型之间最重要的区别在于，前者包含数据中表示有效时序的一项特征。 常规时序具有明确定义的一致频率，并且在连续时间范围内的每个采样点上都有一个值。 将以下快照看作 `sample.csv` 文件。

```output
day_datetime,store,sales_quantity,week_of_year
9/3/2018,A,2000,36
9/3/2018,B,600,36
9/4/2018,A,2300,36
9/4/2018,B,550,36
9/5/2018,A,2100,36
9/5/2018,B,650,36
9/6/2018,A,2400,36
9/6/2018,B,700,36
9/7/2018,A,2450,36
9/7/2018,B,650,36
```

此数据集是某公司每日销售数据的简单示例，该公司拥有两个不同的商店（A 和 B）。此外，它还包含一个 `week_of_year` 特征，使模型可以检测以周为单位的季节性。 字段 `day_datetime` 表示以天为频率的洁净时序，字段 `sales_quantity` 是运行预测的目标列。 将数据读取到 Pandas 数据帧中，然后使用 `to_datetime` 函数确保该时序的类型为 `datetime`。

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

在这种情况下，数据已按时间字段 `day_datetime` 降序排序。 但在设置试验时，请确保所需的时间列按升序进行排序，从而生成有效的时序。 假设数据包含 1,000 条记录，并在数据中进行确定性拆分以创建训练和测试数据集。 确定标签列名称并将其设置为“标签”。 在本例中，标签将为 `sales_quantity`。 然后，从 `test_data` 分离出标签字段，形成 `test_target` 集。

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> 在训练用于预测未来值的模型时，请确保在针对预期范围运行预测时可使用训练中用到的所有特征。 例如，在创建需求预测时，包含当前股票价格的特征可能大幅提升训练准确度。 但是，如果你打算使用较长的时间范围进行预测，则可能没法准确预测与未来的时序点相对应的未来股价值，模型准确性也会受到影响。

<a name="config"></a>

## <a name="train-and-validation-data"></a>训练和验证数据
可直接在 `AutoMLConfig` 构造函数中指定单独的训练集和验证集。

### <a name="rolling-origin-cross-validation"></a>滚动原点交叉验证
对于时序预测，滚动原点交叉验证 (ROCV) 用于按暂时一致的方式拆分时序。 ROCV 使用原始时间点将时序分成训练数据和验证数据。 在时间内滑动原点会生成交叉验证折叠。  

![替换文字](./media/how-to-auto-train-forecast/ROCV.svg)

此策略将保留时序数据完整性并消除数据泄露的风险。 ROCV 自动用于预测任务，方式是同时传递训练数据和验证数据，并使用 `n_cross_validations` 设置交叉验证折叠数。 详细了解 AutoML 如何应用交叉验证来[防止过度拟合模型](concept-manage-ml-pitfalls.md#prevent-over-fitting)。

```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```
详细了解 [AutoMLConfig](#configure-and-run-experiment)。

## <a name="configure-and-run-experiment"></a>配置并运行试验

对于预测任务，自动化机器学习使用特定于时序数据的预处理和估计步骤。 将执行下列预处理步骤：

* 检测时序采样频率（例如每小时、每天、每周），并为缺失的时间点创建新记录以使序列连续。
* 通过向前填充估算目标列中缺少的值，通过列值中位数估算特征列中缺少的值
* 创建基于粒度的特征，使其对不同序列具有固定效果
* 创建基于时间的特征，以帮助学习季节性模式
* 将分类变量编码为数值数量

[`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) 对象定义自动化机器学习任务所需的设置和数据。 与回归问题类似，你要定义标准训练参数，例如任务类型、迭代次数、训练数据和交叉验证次数。 对于预测任务，还必须设置对试验有影响的其他参数。 下表解释了每个参数及其用法。

| 参数&nbsp;名称 | 说明 | 必须 |
|-------|-------|-------|
|`time_column_name`|用于指定输入数据中用于生成时序的日期时间列并推断其频率。|✓|
|`grain_column_names`|定义输入数据中各个序列组的名称。 如果未定义粒度，则假定数据集为一个时序。||
|`max_horizon`|定义必需预测时间范围上限（以时序频率为单位）。 单位基于预测器应预测出的训练数据的时间间隔，例如每月、每周。|✓|
|`target_lags`|要根据数据频率滞后目标值的行数。 此滞后表示为一个列表或整数。 默认情况下，在独立变量和依赖变量之间的关系不匹配或关联时，应使用滞后。 例如，在尝试预测某产品的需求时，任何月份的需求可能取决于之前 3 个月特定商品的价格。 在此示例中，可将目标（需求）的滞后负 3 个月，以便针对正确的关系训练模型。||
|`target_rolling_window_size`|要用于生成预测值的 *n* 个历史时间段，该值小于或等于训练集大小。 如果省略，则 *n* 为完整训练集大小。 如果训练模型时只想考虑一定量的历史记录，请指定此参数。||
|`enable_dnn`|启用预测 DNN。||

请参阅[参考文档](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)以了解详细信息。

将时序设置创建为字典对象。 将 `time_column_name` 设置为数据集中的 `day_datetime` 字段。 定义 `grain_column_names` 参数，确保为数据创建两个单独的时序组（门店 A 和 B 各一个）。最后，将 `max_horizon` 设置为 50，以预测整个测试集****。 使用 `target_rolling_window_size` 将预测时段设置为 10 个时段，并使用 `target_lags` 参数指定目标值滞后两个时段。 建议将 `max_horizon`、`target_rolling_window_size` 和 `target_lags` 设置为“auto”，然后就会自动检测这些值。 在下面的示例中，“auto”设置已用于这些参数。 

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": "auto",
    "target_lags": "auto",
    "target_rolling_window_size": "auto",
    "preprocess": True,
}
```

> [!NOTE]
> 自动机器学习预处理步骤（特征规范化、处理缺失数据，将文本转换为数字等）成为基础模型的一部分。 使用模型进行预测时，训练期间应用的相同预处理步骤将自动应用于输入数据。

通过在上述代码片段中定义 `grain_column_names`，AutoML 将创建两个单独的时序组，也称为多时序。 如果未定义任何粒度，AutoML 将假定数据集是单个时序。 要详细了解单个时序，请查看 [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。

现在创建一个标准 `AutoMLConfig` 对象，指定 `forecasting` 任务类型，然后提交试验。 模型完成后，检索最佳的运行迭代。

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

请参阅[预测示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)，了解高级预测配置的详细代码示例，其中包括：

* [假日检测和特征化](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [滚动原点交叉验证](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [可配置滞后](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [滚动窗口聚合特征](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>配置“DNN 启用预测”试验

> [!NOTE]
> DNN 对自动机器学习的预测支持处于预览状态，不支持本地运行。

要使用 DNN 进行预测，需要将 AutoMLConfig 中的 `enable_dnn` 参数设置为 true。 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
详细了解 [AutoMLConfig](#configure-and-run-experiment)。

或者，可在工作室中选择 `Enable deep learning` 选项。
![替换文字](./media/how-to-auto-train-forecast/enable_dnn.png)

建议将 AML 计算群集与 GPU SKU 一起使用，并将至少两个节点用作计算目标。 为了留出足够的时间让 DNN 训练完成，我们建议至少将试验超时值设为几小时。
有关包含 GPU 的 AML 计算和 VM 大小的详细信息，请参阅 [AML 计算文档](how-to-set-up-training-targets.md#amlcompute)和 [GPU 优化的虚拟机大小文档](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)。

查看[饮料制造预测笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)，获取使用 DNN 的详细代码示例。

### <a name="target-rolling-window-aggregation"></a>目标滚动窗口聚合
通常，目标的最新值是预测程序能具有的最佳信息。 创建目标的累计统计信息可能会提高预测的准确性。 通过目标滚动窗口聚合，可将数据值的滚动聚合添加为特征。 要启动目标滚动窗口，请将 `target_rolling_window_size` 设置为所需的整数窗口大小。 

预测能源需求时可看到此项的示例。 可添加一个滚动窗口特征（3 天）来解释供暖空间的热变化。 在下例中，我们通过在 `AutoMLConfig` 构造函数中设置 `target_rolling_window_size=3`，将此窗口设置为大小 3。 下表显示了在应用窗口聚合后发生的特征工程。 根据定义的设置针对滑动窗口 3 生成表示最小值、最大值和总和的列。 每一行有计算得出的一个新特征；如果时间戳为 2017 年 9 月 8 日凌晨 4:00，则使用 2017 年 9 月 8 日凌晨 1:00 至 3:00 的需求值计算最小值、最大值和总和值。 3 这个窗口将移位填充其余行的数据。

![替换文字](./media/how-to-auto-train-forecast/target-roll.svg)

通过生成和使用这些附加特征作为额外的上下文数据，可帮助提高训练模型的准确性。

请查看使用[目标滚动窗口聚合特征](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)的 Python 代码示例。

### <a name="view-feature-engineering-summary"></a>查看特性工程摘要

对于自动化机器学习中的时序任务类型，可以通过特征工程过程查看详细信息。 以下代码显示每个原始特征以及以下属性：

* 原始特征名称
* 由此原始特征形成的工程特征的数量
* 检测到的类型
* 特征是否已被丢弃
* 原始特征的特征转换列表

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>用最佳模型进行预测

使用最佳模型迭代来预测测试数据集的值。

应使用 `forecast()` 而不是 `predict()`，这样就可指定预测的开始时间。 在下例中，先将 `y_pred` 中的所有值替换为 `NaN`。 在本例中，预测原点将位于训练数据的末尾，这是使用 `predict()` 时的常态。 但是，如果只将 `y_pred` 的后半部分替换为 `NaN`，则函数不会修改前半部分的数值，而会在后半部分预测 `NaN` 值。 函数将返回预测值和对齐的特征。

还可以在 `forecast()` 函数中使用 `forecast_destination` 参数，预测到指定日期为止的值。

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

计算 `actual_labels` 实际值和 `predict_labels` 中预测值之间的 RMSE（均方根误差）。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

现在确定了模型的整体准确性，最现实的下一步是使用模型来预测未知的未来值。 提供与测试集 `test_data` 具有相同格式但具有未来日期时间的数据集，生成的预测集就是每个时序步骤的预测值。 假设数据集中最后的时序记录针对的是 2018/12/31。 若要预测次日的需求（或者小于或等于 `max_horizon` 的待预测时间段），请为每个商店创建 2019/01/01 的一条时序记录。

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

重复执行必要的步骤，将此未来数据加载到数据帧，然后运行 `best_run.predict(test_data)` 以预测未来值。

> [!NOTE]
> 不能预测大于 `max_horizon` 的时间段数的值。 必须使用更大的时间范围对模型进行重新训练，才能预测当前时间范围之外的未来值。

## <a name="next-steps"></a>后续步骤

* 遵循[教程](tutorial-auto-train-models.md)了解如何通过自动化机器学习创建试验。
* 查看[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 参考文档。
