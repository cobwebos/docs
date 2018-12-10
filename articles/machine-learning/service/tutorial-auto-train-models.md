---
title: 教程 2：使用自动化机器学习训练回归模型 - Azure 机器学习服务
description: 了解如何使用自动化机器学习生成 ML 模型。  Azure 机器学习可以通过自动化方式为你执行数据预处理、算法选择和超参数选择操作。 然后，使用 Azure 机器学习服务来部署最终模型。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.openlocfilehash: 0c4b9c31c4ae8a6a7a7044887c9af051966c745e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879525"
---
# <a name="tutorial-2-train-a-regression-model-with-automated-machine-learning"></a>教程 2：使用自动化机器学习训练回归模型

本教程是由两个部分构成的系列教程的第二部分。 在上一教程中，[已准备用于回归建模的纽约市出租车数据](tutorial-data-prep.md)。

现在可以使用 Azure 机器学习服务来生成模型了。 在教程的此部分，需使用准备好的数据自动生成一个回归模型，用于预测出租车费用价格。 使用此服务的自动化 ML 功能，可以定义机器学习目标和约束，启动自动化机器学习过程，然后进行算法选择和超参数优化。 自动化 ML 技术会对算法和超参数的多种组合进行迭代访问，知道找到符合要求的最佳模型。

![流程图](./media/tutorial-auto-train-models/flow2.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置 Python 环境并导入 SDK 包
> * 配置 Azure 机器学习服务工作区
> * 自动训练回归模型
> * 使用自定义参数在本地运行模型
> * 浏览结果
> * 注册最佳模型

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://aka.ms/AMLfree)。

>[!NOTE]
> 本文中的代码已使用 Azure 机器学习 SDK 版本 1.0.0 进行测试


## <a name="prerequisites"></a>先决条件

> * [运行数据准备教程](tutorial-data-prep.md)。
> * 自动化机器学习配置的环境，例如 Azure Notebooks、本地 Python 环境或 Data Science Virtual Machine。 [设置](samples-notebooks.md)自动化机器学习。

## <a name="get-the-notebook"></a>获取 Notebook

为方便起见，本教程以 [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/regression-part2-automated-ml.ipynb) 的形式提供。 在 Azure Notebooks 或你自己的 Jupyter Notebook 服务器中运行 `regression-part2-automated-ml.ipynb` Notebook。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="import-packages"></a>导入包
导入需要在本教程中使用的 Python 包。


```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
from azureml.train.automl.run import AutoMLRun
import time
import logging

```

## <a name="configure-workspace"></a>配置工作区

从现有工作区创建工作区对象。 `Workspace` 是一个类，可接受 Azure 订阅和资源信息，并创建云资源来监视和跟踪模型运行。 `Workspace.from_config()` 读取 **aml_config/config.json** 文件并将详细信息加载到名为 `ws` 的对象中。  在本教程中，`ws` 在代码的其余部分使用。

有了工作区对象以后，请为试验指定一个名称，然后通过工作区创建并注册本地目录。 所有运行的历史记录都记录在指定的试验下和 [Azure 门户](https://portal.azure.com)中。


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

import os

output = {}
output['SDK version'] = azureml.core.VERSION
output['Subscription ID'] = ws.subscription_id
output['Workspace'] = ws.name
output['Resource Group'] = ws.resource_group
output['Location'] = ws.location
output['Project Directory'] = project_folder
pd.set_option('display.max_colwidth', -1)
pd.DataFrame(data=output, index=['']).T
```

## <a name="explore-data"></a>浏览数据

利用在上一教程中创建的数据流对象。 打开并执行数据流，然后查看结果。


```python
import azureml.dataprep as dprep
package_saved = dprep.Package.open(".\dflow")
dflow_prepared = package_saved.dataflows[0]
dflow_prepared.get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>类型</th>
      <th>Min</th>
      <th>Max</th>
      <th>Count</th>
      <th>缺失计数</th>
      <th>非缺失计数</th>
      <th>缺失百分比</th>
      <th>错误计数</th>
      <th>空计数</th>
      <th>0.1% 分位</th>
      <th>1% 分位</th>
      <th>5% 分位</th>
      <th>25% 分位</th>
      <th>50% 分位</th>
      <th>75% 分位</th>
      <th>95% 分位</th>
      <th>99% 分位</th>
      <th>99.9% 分位</th>
      <th>平均值</th>
      <th>标准偏差</th>
      <th>Variance</th>
      <th>倾斜</th>
      <th>峰度</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>vendor</th>
      <td>FieldType.STRING</td>
      <td>1</td>
      <td>VTS</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_weekday</th>
      <td>FieldType.STRING</td>
      <td>星期五</td>
      <td>星期三</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>3.57523</td>
      <td>3</td>
      <td>9.91106</td>
      <td>15.9327</td>
      <td>19</td>
      <td>22.0225</td>
      <td>23</td>
      <td>23</td>
      <td>14.2326</td>
      <td>6.34926</td>
      <td>40.3131</td>
      <td>-0.693335</td>
      <td>-0.459336</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.32313</td>
      <td>4.92308</td>
      <td>14.2214</td>
      <td>29.5244</td>
      <td>44.6436</td>
      <td>56.3767</td>
      <td>58.9798</td>
      <td>59</td>
      <td>29.4635</td>
      <td>17.4396</td>
      <td>304.14</td>
      <td>0.00440324</td>
      <td>-1.20458</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>4.99286</td>
      <td>4.91954</td>
      <td>14.6121</td>
      <td>29.9239</td>
      <td>44.5221</td>
      <td>56.6792</td>
      <td>59</td>
      <td>59</td>
      <td>29.6225</td>
      <td>17.3868</td>
      <td>302.302</td>
      <td>-0.0227466</td>
      <td>-1.19409</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>星期五</td>
      <td>星期三</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>dropoff_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>3.23217</td>
      <td>2.93333</td>
      <td>9.92334</td>
      <td>15.9135</td>
      <td>19</td>
      <td>22.2739</td>
      <td>23</td>
      <td>23</td>
      <td>14.1815</td>
      <td>6.45578</td>
      <td>41.677</td>
      <td>-0.691001</td>
      <td>-0.500215</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.1064</td>
      <td>5</td>
      <td>14.2051</td>
      <td>29.079</td>
      <td>44.2937</td>
      <td>56.6338</td>
      <td>58.9984</td>
      <td>59</td>
      <td>29.353</td>
      <td>17.4241</td>
      <td>303.598</td>
      <td>0.0142562</td>
      <td>-1.21531</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.03373</td>
      <td>5</td>
      <td>14.7471</td>
      <td>29.598</td>
      <td>45.3216</td>
      <td>56.1044</td>
      <td>58.9728</td>
      <td>59</td>
      <td>29.7923</td>
      <td>17.481</td>
      <td>305.585</td>
      <td>-0.0281313</td>
      <td>-1.21965</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>Y</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0782</td>
      <td>-73.7365</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0763</td>
      <td>-73.9625</td>
      <td>-73.9629</td>
      <td>-73.949</td>
      <td>-73.9279</td>
      <td>-73.8667</td>
      <td>-73.8304</td>
      <td>-73.8232</td>
      <td>-73.7698</td>
      <td>-73.9139</td>
      <td>0.0487111</td>
      <td>0.00237277</td>
      <td>0.402697</td>
      <td>-0.613516</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5755</td>
      <td>40.8799</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.6329</td>
      <td>40.7131</td>
      <td>40.7116</td>
      <td>40.7214</td>
      <td>40.7581</td>
      <td>40.8051</td>
      <td>40.8489</td>
      <td>40.8676</td>
      <td>40.8777</td>
      <td>40.7652</td>
      <td>0.0483485</td>
      <td>0.00233758</td>
      <td>0.228088</td>
      <td>-0.598862</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0857</td>
      <td>-73.7209</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0788</td>
      <td>-73.9856</td>
      <td>-73.9858</td>
      <td>-73.959</td>
      <td>-73.9367</td>
      <td>-73.8848</td>
      <td>-73.8155</td>
      <td>-73.7767</td>
      <td>-73.7335</td>
      <td>-73.9207</td>
      <td>0.055961</td>
      <td>0.00313163</td>
      <td>0.648649</td>
      <td>0.0229141</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5835</td>
      <td>40.8797</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.5977</td>
      <td>40.6954</td>
      <td>40.6951</td>
      <td>40.7275</td>
      <td>40.7582</td>
      <td>40.7884</td>
      <td>40.8504</td>
      <td>40.868</td>
      <td>40.8786</td>
      <td>40.7595</td>
      <td>0.0504621</td>
      <td>0.00254642</td>
      <td>0.0484179</td>
      <td>-0.0368799</td>
    </tr>
    <tr>
      <th>passengers</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>5</td>
      <td>5</td>
      <td>6</td>
      <td>6</td>
      <td>2.32979</td>
      <td>1.79978</td>
      <td>3.2392</td>
      <td>0.834099</td>
      <td>-1.11111</td>
    </tr>
    <tr>
      <th>cost</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>444</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>3.01808</td>
      <td>3.0125</td>
      <td>5.91545</td>
      <td>9.49055</td>
      <td>16.5816</td>
      <td>33.5638</td>
      <td>51.9924</td>
      <td>81.1368</td>
      <td>12.9112</td>
      <td>11.6447</td>
      <td>135.599</td>
      <td>8.6842</td>
      <td>269.818</td>
    </tr>
  </tbody>
</table>

为试验准备数据时，请将列添加到 `dflow_x`，使之成为创建模型所需的特性。 将 `dflow_y` 定义为预测值：cost。

```python
dflow_X = dflow_prepared.keep_columns(['pickup_weekday', 'dropoff_latitude', 'dropoff_longitude','pickup_hour','pickup_longitude','pickup_latitude','passengers'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-data-into-train-and-test-sets"></a>将数据拆分为训练集和测试集

现在请使用 `sklearn` 库中的 `train_test_split` 函数将数据拆分为训练集和测试集。 该函数将数据分成用于模型训练的 x（特性）数据集和用于测试的 y（用于预测的值）数据集。 `test_size` 参数决定了分配用于测试的数据的百分比。 `random_state` 参数设置随机数生成器的种子，因此进行的训练-测试拆分始终具有确定性。

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=123)
# flatten y_train to 1d array
y_train.values.flatten()
```

现在已有所需的包和数据，可以针对模型进行自动训练了。

## <a name="automatically-train-a-model"></a>自动训练模型

若要自动训练模型，请执行以下操作：
1. 定义试验运行的设置
1. 提交用于模型优化的试验

### <a name="define-settings-for-autogeneration-and-tuning"></a>定义自动生成和优化的设置

定义自动生成和优化的试验参数和模型设置。 查看[设置](how-to-configure-auto-train.md)的完整列表。


|属性| 本教程中的值 |Description|
|----|----|---|
|**iteration_timeout_minutes**|10|每个迭代的时间限制（分钟）|
|**迭代**|30|迭代次数。 在每个迭代中，模型通过特定管道使用数据进行训练。|
|**primary_metric**| spearman_correlation | 要优化的指标。|
|**preprocess**| True | 如果为 True，则试验可以基于输入执行预处理。|
|**verbosity**| logging.INFO | 控制日志记录的级别。|
|**n_cross_validationss**|5|交叉验证拆分数



```python
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 30,
    "primary_metric" : 'spearman_correlation',
    "preprocess" : True,
    "verbosity" : logging.INFO,
    "n_cross_validations": 5
}
```


```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task = 'regression',
                             debug_log = 'automated_ml_errors.log',
                             path = project_folder,
                             X = x_train.values,
                             y = y_train.values.flatten(),
                             **automl_settings)
```

### <a name="train-the-automatic-regression-model"></a>训练自动回归模型

启动要在本地运行的试验。 将定义的 `automated_ml_config` 对象传递至试验，并将输出设置为 `true`，以便查看试验过程中的进度。


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

    Parent Run ID: AutoML_83117da4-07e3-473a-b83e-99471bfa9e09
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************
    
     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:21       0.6498    0.6498
             1   MinMaxScaler GradientBoosting                  0:00:22       0.6624    0.6624
             2   StandardScalerWrapper KNN                      0:00:18       0.7267    0.7267
             3   StandardScalerWrapper GradientBoosting         0:00:18       0.5003    0.7267
             4    Ensemble                                      0:00:38       0.6659    0.7267
    

## <a name="explore-the-results"></a>浏览结果

通过 Jupyter 小组件或者通过检查试验历史记录来浏览自动训练的结果。

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>选项 1：添加查看结果所需的 Jupyter 小组件

如果使用 Jupyter Notebook，则使用此 Jupyter Notebook 小组件可以看到一个包含所有结果的图形和表。


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter 小组件运行详细信息](./media/tutorial-auto-train-models/jup-widget-auto.png)

### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>选项 2：获取并检查 Python 中的所有运行迭代

也可检索每个试验的历史记录，并浏览每个迭代运行的单个指标。

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

import pandas as pd
rundata = pd.DataFrame(metricslist).sort_index(1)
rundata
```




<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0.113810</td>
      <td>0.093514</td>
      <td>-0.010248</td>
      <td>0.005867</td>
      <td>0.108187</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>7.004893</td>
      <td>6.348354</td>
      <td>6.493000</td>
      <td>7.045597</td>
      <td>6.646850</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>4.834063</td>
      <td>3.503244</td>
      <td>3.321553</td>
      <td>4.349547</td>
      <td>4.389995</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error</th>
      <td>0.077832</td>
      <td>0.070537</td>
      <td>0.072144</td>
      <td>0.078284</td>
      <td>0.073854</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0.053712</td>
      <td>0.038925</td>
      <td>0.036906</td>
      <td>0.048328</td>
      <td>0.048778</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error</th>
      <td>0.117819</td>
      <td>0.120518</td>
      <td>0.126141</td>
      <td>0.124289</td>
      <td>0.118340</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error</th>
      <td>0.177689</td>
      <td>0.163360</td>
      <td>0.168101</td>
      <td>0.178250</td>
      <td>0.168685</td>
    </tr>
    <tr>
      <th>r2_score</th>
      <td>0.104661</td>
      <td>0.064075</td>
      <td>-0.036158</td>
      <td>-0.004403</td>
      <td>0.096976</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>10.603744</td>
      <td>10.846632</td>
      <td>11.352731</td>
      <td>11.185972</td>
      <td>10.650593</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0.801531</td>
      <td>0.736896</td>
      <td>0.758279</td>
      <td>0.804062</td>
      <td>0.760913</td>
    </tr>
    <tr>
      <th>spearman_correlation</th>
      <td>0.549825</td>
      <td>0.562435</td>
      <td>0.526702</td>
      <td>0.500302</td>
      <td>0.565857</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0.549825</td>
      <td>0.562435</td>
      <td>0.562435</td>
      <td>0.562435</td>
      <td>0.565857</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="retrieve-the-best-model"></a>检索最佳模型

从迭代中选择最佳管道。 `automl_classifier` 上的 `get_output` 方法针对上次拟合调用返回最佳运行和拟合的模型。 `get_output` 上有重载，因此可以针对任何记录的指标或特定的迭代来检索最佳运行和拟合的模型。

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="register-the-model"></a>注册模型

在 Azure 机器学习服务工作区中注册模型。


```python
description = 'Automated Machine Learning Model'
tags = None
local_run.register_model(description=description, tags=tags)
local_run.model_id # Use this id to deploy the model as a web service in Azure
```

## <a name="test-the-best-model-accuracy"></a>测试最佳模型的准确性

使用最佳模型针对测试数据集运行预测。 函数 `predict` 使用最佳模型根据 `x_test` 数据集预测 y（行程费用）的值。 输出 `y_predict` 中头 10 个预测的费用值。

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

将预测的费用值与实际费用值进行比较。 使用 `y_test` dataframe，将其转换为一个列表，以便与预测的值进行比较。 函数 `mean_absolute_error` 接受两个数组的值，计算两个数组之间的平均绝对值误差。 在此示例中，平均绝对误差为 3.5 意味着平均说来，此模型预测的费用与实际值的误差在上下 3.5 以内。

```python
from sklearn.metrics import mean_absolute_error

y_actual = y_test.values.flatten().tolist()
mean_absolute_error(y_actual, y_predict)
```

    [ 3.16213051 ]

运行以下代码，使用完整的 `y_actual` 和 `y_predict` 数据集来计算 MAPE（平均绝对百分比误差）。 此指标计算每个预测值和实际值之间的绝对差值，将所有差值求和，然后将该和表示为实际值总和的百分比。

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.22424976634422172

    Model Accuracy:
    0.7757502336557782

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

本自动化机器学习教程介绍了以下内容：

> [!div class="checklist"]
> * 配置工作区并准备试验数据
> * 通过自动化回归模型在本地使用自定义参数进行训练
> * 浏览和查看训练结果
> * 注册最佳模型

使用 Azure 机器学习来[部署模型](tutorial-deploy-models-with-aml.md)。
