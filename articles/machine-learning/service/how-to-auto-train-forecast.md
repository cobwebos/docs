---
title: 自动训练时序预测模型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习通过自动化机器学习来训练时序预测回归模型。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: eb13e6d279ffd8efc0cdb5ce675b77aac5be9c18
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436627"
---
# <a name="auto-train-a-time-series-forecast-model"></a>自动训练时序预测模型

本文介绍如何使用 Azure 机器学习中的自动化机器学习来训练时序预测回归模型。 配置预测模型类似于使用自动机器学习设置标准回归模型，但存在某些配置选项和预处理步骤来处理时序数据。 下面的示例演示如何执行以下操作：

* 准备时序建模的数据
* 在[`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig)对象中配置特定的时序参数
* 运行带有时序数据的预测

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

你可以使用自动 ML 来合并技术和方法，并获得推荐的高质量时序预测。 自动时间系列实验被视为多元回归问题。 过去的时间序列值将 "透视" 成为回归量与其他预测值的其他维度。

与传统时序方法不同，这种方法的优点是，在定型过程中自然包含多个上下文变量以及它们之间的关系。 在实际预测应用程序中，多个因素可能会影响预测。 例如，在预测销售额时，历史趋势的交互、汇率和价格都相互合作推动了销售结果。 另一个好处是，回归模型中的所有最新创新都立即适用于预测。

您可以[配置](#config)未来预测应扩展的时间（预测范围）以及滞后时间和更多时间。 自动 ML 会为数据集中的所有项目学习单个但通常在内部分支的模型，并预测视野。 这样就可以使用更多的数据来估计模型参数，并使其成为不可见系列。

从定型数据中提取的功能扮演着重要的角色。 而且，自动 ML 会执行标准预处理步骤并生成附加的时序功能，以捕获季节性效果并最大程度地提高预测准确性。

## <a name="prerequisites"></a>必备组件

* Azure 机器学习工作区。 若要创建工作区，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
* 本文假设基本熟悉如何设置自动化机器学习试验。 按照[教程](tutorial-auto-train-models.md)或操作[方法](how-to-configure-auto-train.md)，查看基本的自动化机器学习试验设计模式。

## <a name="preparing-data"></a>准备数据

自动机器学习中预测回归任务类型和回归任务类型之间最重要的区别在于，数据中包含的一项功能是表示有效时序的一项功能。 一个固定时间系列具有明确定义且一致的频率，并在连续时间范围内的每个采样点上都有一个值。 请考虑以下文件快照 `sample.csv`。

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

此数据集是具有两个不同商店（A 和 B）的公司的每日销售数据的简单示例。此外，还有一个 `week_of_year` 的功能，该功能将允许模型检测每周季节性。 字段 `day_datetime` 表示具有每日频率的清除时间系列，而字段 `sales_quantity` 是用于运行预测的目标列。 将数据读入 Pandas 数据帧，然后使用 `to_datetime` 函数确保时序为 @no__t 类型。

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

在这种情况下，数据已按时间字段升序排序 `day_datetime`。 但是，在设置试验时，请确保将所需时间列按升序排序以生成有效的时序。 假设数据中包含1000条记录，并在数据中作出确定性拆分，以创建定型和测试数据集。 标识标签列名，并将其设置为 "标签"。 在此示例中，标签将 `sales_quantity`。 然后，将标签字段从 `test_data` 分隔以构成 @no__t 集。

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> 在为预测未来值的模型定型时，请确保在针对预期范围运行预测时，可以使用在定型中使用的所有功能。 例如，在创建需求预测时，包括当前股票价格的功能可能会大幅提高定型准确度。 但是，如果您打算使用较长的时间段进行预测，则您可能无法准确预测与未来的时序点相对应的未来股价值，并且模型准确性可能会受到影响。

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>配置和运行试验

对于预测任务，自动机器学习使用特定于时序数据的预处理和估计步骤。 将执行以下预处理步骤：

* 检测时序采样频率（例如每小时、每天、每周），并创建不存在时间点的新记录，以使序列持续。
* 归结在目标中缺少值（通过向前填充）和功能列（使用中间列值）
* 创建基于粒度的功能，以实现跨不同序列的固定效果
* 创建基于时间的功能，以帮助学习季节性模式
* 将分类变量编码为数值数量

@No__t 0 对象定义自动机器学习任务所需的设置和数据。 与回归问题类似，定义了标准定型参数，如任务类型、迭代数、定型数据和交叉验证次数。 对于预测任务，还必须设置其他参数，这些参数会影响试验。 下表说明了每个参数及其用法。

| Param | 描述 | 需要 |
|-------|-------|-------|
|`time_column_name`|用于指定输入数据中的日期时间列，这些数据用于生成时序并推断其频率。|✓|
|`grain_column_names`|定义输入数据中各个序列组的名称。 如果未定义颗粒，则假定数据集为一系列时间。||
|`max_horizon`|定义以时间序列频率为单位的最大预期预测范围。 单位基于定型数据的时间间隔（例如每月、每周预测应预测）。|✓|
|`target_lags`|要基于数据频率延迟目标值的行数。 这表示为一个列表或单个整数。 默认情况下，在独立变量和依赖变量之间的关系不匹配或关联时，应使用 Lag。 例如，在尝试预测某个产品的需求时，任意月份的需求可能会依赖于之前3个月的特定商品价格。 在此示例中，您可能希望将目标（需求）的滞后时间降低3个月，使模型是针对正确关系的定型。||
|`target_rolling_window_size`|*n*用于生成预测值的历史时间段，< = 定型集大小。 如果省略，则*n*为完全定型集的大小。 如果只想在训练模型时考虑一定数量的历史记录，请指定此参数。||

有关详细信息，请参阅[参考文档](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)。

将时间序列设置创建为字典对象。 将 @no__t 0 设置为数据集中的 @no__t 字段。 定义 `grain_column_names` 参数以确保为数据创建**两个单独的时序组**;第一种是存储 A，将 @no__t 设置为50，以便预测整个测试集。 将预测时段设置为10个 `target_rolling_window_size` 的周期，并为 `target_lags` 参数提前2个句点的目标值指定一个滞后时间。

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10,
    "preprocess": True,
}
```

> [!NOTE]
> 自动机器学习预处理步骤（特征规范化、处理缺失数据，将文本转换为数字等）成为基础模型的一部分。 使用模型进行预测时，训练期间应用的相同预处理步骤将自动应用于输入数据。

通过在上述代码片段中定义 `grain_column_names`，AutoML 将创建两个单独的时序组，也称为多个时间序列。 如果未定义任何颗粒，AutoML 将假定该数据集是单个时间系列。 若要了解有关单个时间系列的详细信息，请参阅[energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。

现在，创建标准 @no__t 0 对象，指定 @no__t 1 任务类型，并提交试验。 模型完成后，检索最佳的运行迭代。

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
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

有关高级预测配置的详细代码示例，请参阅[能源需求笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)，其中包括：

* 假日检测和特征化
* 滚动-原点交叉验证
* 可配置滞后
* 滚动窗口聚合功能

### <a name="view-feature-engineering-summary"></a>查看功能设计摘要

对于自动机器学习中的时序任务类型，您可以从功能工程过程中查看详细信息。 以下代码显示每个原始功能以及以下属性：

* 原始功能名称
* 此原始功能中形成的工程特征数量
* 检测到的类型
* 功能是否已被删除
* Raw 功能的功能转换列表

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>最佳模型预测

使用最佳模型迭代预测测试数据集的值。

```python
predict_labels = fitted_model.predict(test_data)
actual_labels = test_labels.flatten()
```

或者，您可以使用 `forecast()` 函数，而不是 `predict()`，这将允许指定预测应开始的时间。 在下面的示例中，首先将 `y_pred` 中的所有值替换 @no__t 为-1。 在这种情况下，预测源将位于定型数据的末尾，因为使用 `predict()` 时通常会出现这种情况。 但是，如果仅将 `y_pred` 的后半部分替换为 `NaN`，则函数会将数字值保留在前一半未修改，但会在第二部分中预测 @no__t 2 值。 函数同时返回预测值和对齐功能。

你还可以使用 `forecast()` 函数中的 `forecast_destination` 参数将值向上预测到指定的日期。

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

计算 `actual_labels` 实际值和 `predict_labels` 中预测值之间的 RMSE （根本均值误差）。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_lables, predict_labels))
rmse
```

现在，确定了整体模型准确性，最现实的下一步是使用该模型预测未知的未来值。 只需以与测试集相同的格式提供数据集 `test_data` 但在将来 datetime 时，生成的预测集是每个时序步骤的预测值。 假设数据集中的最后一条时序记录为12/31/2018。 若要预测下一天的需求（或者需要预测的时间段，< = `max_horizon`），请为每个商店创建一个时序记录01/01/2019。

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

重复执行必要的步骤，将此将来的数据加载到数据帧，然后运行 `best_run.predict(test_data)` 以预测未来值。

> [!NOTE]
> 不能预测大于 @no__t 的时间段数的值。 必须使用较大的范围重新对模型进行重新定型，以便预测当前范围之外的未来值。

## <a name="next-steps"></a>后续步骤

* 遵循[教程](tutorial-auto-train-models.md)，了解如何通过自动化机器学习创建试验。
* 查看[适用于 Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)参考文档的 Azure 机器学习 SDK。
