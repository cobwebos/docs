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
ms.date: 03/09/2020
ms.openlocfilehash: 05d658c052c5bc12f49d957bb29ad085c269c57b
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137344"
---
# <a name="auto-train-a-time-series-forecast-model"></a>自动训练时序预测模型
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何使用 Azure 机器学习中的自动化机器学习来配置和训练时序预测回归模型。 

配置预测模型类似于使用自动化机器学习设置标准回归模型，但存在某些用于处理时序数据的配置选项和预处理步骤。 

例如，您可以[配置](#config)未来预测应扩展的时间（预测范围）以及滞后时间和更多时间。 自动化 ML 会针对数据集和预测时间范围内的所有项目，习得通常有内部分支的单个模型。 这样就可以使用更多的数据来估计模型参数，使得未知系列的泛化成为可能。

以下示例介绍如何：

* 准备用于时序建模的数据
* 在[`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)对象中配置特定的时序参数
* 使用时序数据运行预测

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

与传统的时序方法不同，在自动 ML 过去的时间序列值中，"透视" 将成为回归量与其他预测值的附加维度。 此方法在定型过程中合并多个上下文变量以及它们之间的关系。 由于多个因素可能会影响预测，此方法会将其自身与真实的预测方案协调起来。 例如，在预测销售时，历史趋势、汇率和价格相互作用，共同促成了销售结果。 

从训练数据中提取的特征扮演着关键的角色。 而且，自动 ML 会执行标准预处理步骤并生成附加的时序功能，以捕获季节性效果并最大程度地提高预测准确性

## <a name="time-series-and-deep-learning-models"></a>时序和深度学习模型


通过自动化 ML 的深度学习，可预测单变量和多变量时序数据。

深度学习模型具有三个固有功能：
1. 可以从任意输入到输出映射进行学习
1. 支持多个输入和输出
1. 可以从跨越较长序列的输入数据中自动提取模式

对于较大的数据，深度学习模型（如 Microsoft 的 ForecastTCN）可以改进生成的模型的分数。 了解如何[配置试验深度学习](#configure-a-dnn-enable-forecasting-experiment)。

自动化 ML 包含推荐系统，可为用户提供本机时序和深度学习模型。 


模型| 说明 | 优势
----|----|---
Prophet （预览版）|Prophet 最适合用于受季节影响大且包含多个季节历史数据的时序。 | 准确 & 快速、强健、离群值、丢失数据以及在时序方面产生巨大的变化。
自动 ARIMA （预览版）|当数据为静止时，自动回归集成移动平均线（ARIMA）的性能最佳。 这意味着，其统计属性（如平均值和方差）在整个集中都是固定的。 例如，如果您翻了一硬币，那么您将获得机头的概率为50%，而不考虑您是今天、明天还是下一年。| 适用于单变量系列，因为过去的值用于预测未来值。
ForecastTCN （预览版）| ForecastTCN 是一个神经网络模型，旨在应对最苛刻的预测任务，捕获数据中的非线性本地和全局趋势以及时序之间的关系。|能够利用数据的复杂趋势，并可以轻松地扩展到最大的数据集。

## <a name="prerequisites"></a>必备条件

* Azure 机器学习工作区。 若要创建工作区，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。
* 本文假设读者基本熟悉如何设置自动化机器学习试验。 遵循[教程](tutorial-auto-train-models.md)或[操作方法](how-to-configure-auto-train.md)，了解基本的自动化机器学习试验设计模式。

## <a name="preparing-data"></a>准备数据

自动化机器学习中预测回归任务类型和回归任务类型之间最重要的区别在于，前者包含数据中表示有效时序的一项特征。 常规时序具有明确定义的一致频率，并且在连续时间范围内的每个采样点上都有一个值。 将以下快照看作 `sample.csv` 文件。

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

此数据集是某公司每日销售数据的简单示例，该公司拥有两个不同的商店（A 和 B）。此外，它还包含一个 `week_of_year` 特征，使模型可以检测以周为单位的季节性。 字段 `day_datetime` 表示以天为频率的洁净时序，字段 `sales_quantity` 是运行预测的目标列。 将数据读入 Pandas 数据帧，然后使用 `to_datetime` 函数确保时序为 `datetime` 类型。

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

在这种情况下，数据已按时间字段`day_datetime`升序排序。 但是，在设置试验时，请确保将所需时间列按升序排列以生成有效的时序。 假设数据包含 1,000 条记录，并在数据中进行确定性拆分以创建训练和测试数据集。 标识标签列名，并将其设置为标签。 在此示例中，标签将为`sales_quantity`。 然后，从 `test_data` 中分离出标签字段，以形成 `test_target` 集。

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> 在训练用于预测未来值的模型时，请确保在针对预期范围运行预测时可使用训练中用到的所有特征。 例如，在创建需求预测时，包含当前股票价格的特征可能大幅提升训练准确度。 但是，如果打算预测较长时间范围内的情况，则可能无法准确预测与未来的时序点相对应的未来股价值，并且模型准确性可能会受到影响。

<a name="config"></a>

## <a name="train-and-validation-data"></a>训练和验证数据
可以直接在 `AutoMLConfig` 构造函数中指定单独的训练集和验证集。

### <a name="rolling-origin-cross-validation"></a>滚动起源交叉验证
对于时序预测滚动起源交叉验证（ROCV），用于以暂时一致的方式拆分时序。 ROCV 使用源时间点将系列分成定型数据和验证数据。 在时间内滑动源会生成交叉验证折叠。  

![替换文字](./media/how-to-auto-train-forecast/ROCV.svg)

此策略将保留时序数据完整性并消除数据泄露的风险。 ROCV 会自动用于预测任务，方法是将定型和验证数据传递给一起，并使用`n_cross_validations`设置交叉验证折叠数。 

```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```
了解有关[AutoMLConfig](#configure-and-run-experiment)的详细信息。

## <a name="configure-and-run-experiment"></a>配置并运行试验

对于预测任务，自动化机器学习使用特定于时序数据的预处理和估计步骤。 将执行以下预处理步骤：

* 检测时序采样频率（例如，每小时、每天、每周），并创建不存在时间点的新记录，以使序列持续。
* 通过向前填充补充目标中缺失的值，使用列中值补充特征列中缺失的值
* 创建基于粒度的特征，使其对不同序列具有固定效果
* 创建基于时间的特征，以帮助学习季节性模式
* 将分类变量编码为数值量

[`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)对象定义自动机器学习任务所需的设置和数据。 与回归问题类似，需定义标准训练参数，如任务类型、迭代数、训练数据和交叉验证次数。 对于预测任务，还必须设置对试验有影响的其他参数。 下表阐释了每个参数及其用途。

| 参数&nbsp;名称 | 说明 | 必需 |
|-------|-------|-------|
|`time_column_name`|用于指定输入数据中用于生成时序的日期时间列并推断其频率。|✓|
|`grain_column_names`|定义输入数据中各个序列组的名称。 如果未定义粒度，则假定数据集为一个时序。||
|`max_horizon`|定义以时序频率为单位的最大理想预测时间范围。 单位基于定型数据的时间间隔（例如，每月、每周预测应预测）。|✓|
|`target_lags`|基于数据频率使目标值滞后的行数。 Lag 表示为一个列表或单个整数。 默认情况下，在独立变量和依赖变量之间的关系不匹配或关联时，应使用 Lag。 例如，在尝试预测某个产品的需求时，任意月份的需求可能依赖于之前 3 个月内特定商品的价格。 在此示例中，可将目标（需求）的滞后负 3 个月，以便针对正确的关系训练模型。||
|`target_rolling_window_size`|要用于生成预测值的 *n* 个历史时间段，该值小于或等于训练集大小。 如果省略，则 *n* 为完整训练集大小。 如果训练模型时只想考虑一定量的历史记录，请指定此参数。||
|`enable_dnn`|启用预测 DNN。||

请参阅[参考文档](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)以了解详细信息。

将时序设置创建为字典对象。 将 `time_column_name` 设置为数据集中的 `day_datetime` 字段。 定义 `grain_column_names` 参数以确保为数据创建两个单独的时序组****，分别用于商店 A 和 B。最后，将 `max_horizon` 设置为 50 以便预测整个测试集。 将预测时段设置为10个周期`target_rolling_window_size`，并为该`target_lags`参数指定两个句点的目标值的单个 lag。 建议将设置为， `max_horizon`并`target_rolling_window_size` `target_lags`将设置为 "自动"，这会自动检测这些值。 在下面的示例中，"自动" 设置已用于这些参数。 

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

通过在上述代码片段中定义 `grain_column_names`，AutoML 将创建两个单独的时序组，也称为多时序。 如果未定义粒度，AutoML 将假定该数据集为单时序。 若要详细了解单个时间系列，请参阅[energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。

现在，创建标准 `AutoMLConfig` 对象，指定 `forecasting` 任务类型，然后提交试验。 模型完成后，检索最佳的运行迭代。

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

有关高级预测配置的详细代码示例，请参阅[预测示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)，其中包括：

* [假日检测和特征化](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [滚动原点交叉验证](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [可配置滞后](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [滚动窗口聚合特征](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>配置启用 DNN 的预测试验

> [!NOTE]
> 自动机器学习中预测的 DNN 支持为预览版，不支持本地运行。

为了利用 DNN 进行预测，需要将 AutoMLConfig 中的 `enable_dnn` 参数设置为 true。 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
了解有关[AutoMLConfig 的](#configure-and-run-experiment)详细信息。

或者，你可以选择工作室`Enable deep learning`中的选项。
![替换文字](./media/how-to-auto-train-forecast/enable_dnn.png)

建议使用 GPU Sku 和至少两个节点作为计算目标的 AML 计算群集。 若要留出足够的时间让 DNN 培训完成，建议将试验超时设置为最少几小时。
有关 AML 计算和包含 GPU 的 VM 大小的详细信息，请参阅[AML 计算文档](how-to-set-up-training-targets.md#amlcompute)和[GPU 优化虚拟机大小文档](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)。

查看[饮料生产预测笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)以获取利用 dnn 的详细代码示例。

### <a name="target-rolling-window-aggregation"></a>目标滚动窗口聚合
通常，预测可以具有的最佳信息是目标的最新值。 创建目标的累计统计信息可以提高预测的准确性。 目标滚动窗口聚合允许添加数据值的滚动聚合作为功能。 若要启用目标滚动窗口， `target_rolling_window_size`请将设置为所需的整数窗口大小。 

预测能源需求时，可以查看此示例。 你可以添加三天滚动窗口功能，以用于对加热空间的热变化进行计算。 在下面的示例中，我们通过在`target_rolling_window_size=3` `AutoMLConfig`构造函数中设置，创建了大小为3的此窗口。 该表显示在应用窗口聚合时发生的功能工程。 最小值、最大值和总和列基于定义的设置在三个滑动窗口上生成。 每一行都有一个新的计算功能，在2017年9月8日的时间戳的情况下： 00 (，最大值、最小值和求和值使用9月8日的需求值计算，2017 1： 00 (： 00 (。 此窗口的三个移位将为其余行填充数据。

![替换文字](./media/how-to-auto-train-forecast/target-roll.svg)

生成并使用这些附加功能作为额外的上下文数据，有助于提高定型模式的准确性。

查看利用[目标滚动窗口聚合功能](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)的 Python 代码示例。

### <a name="view-feature-engineering-summary"></a>查看特征工程摘要

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

使用最佳模型迭代预测测试数据集的值。

应`forecast()`使用函数而不是`predict()`，这将允许指定预测应开始的时间。 在下面的示例中，首先将 `y_pred` 中的所有值替换为 `NaN`。 在本例中，预测原点将位于训练数据的末尾，这是使用 `predict()` 时的常态。 但是，如果只将 `y_pred` 的后半部分替换为 `NaN`，则函数不会修改前半部分的数值，而会在后半部分预测 `NaN` 值。 函数将返回预测值和对齐的特征。

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

既已确定整体模型准确性，最现实的下一步是使用该模型预测未知的未来值。 提供与测试集`test_data`相同的格式的数据集，但对于未来的 datetime，生成的预测集是每个时序步骤的预测值。 假设数据集中的最后一条时序记录为 2018/12/31 的记录。 若要预测次日的需求（或者小于或等于 `max_horizon` 的待预测时间段），请为每个商店创建 2019/01/01 的一条时序记录。

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

重复执行必要的步骤，将此未来数据加载到数据帧，然后运行 `best_run.predict(test_data)` 以预测未来值。

> [!NOTE]
> 不能预测大于 `max_horizon` 的时间段数的值。 必须使用更大的时间范围对模型进行重新训练，才能预测当前时间范围之外的未来值。

## <a name="next-steps"></a>后续步骤

* 遵循[教程](tutorial-auto-train-models.md)了解如何通过自动化机器学习创建试验。
* 查看[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 参考文档。
