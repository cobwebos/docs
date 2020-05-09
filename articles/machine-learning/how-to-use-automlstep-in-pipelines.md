---
title: 在 ML 管道中使用自动 ML
titleSuffix: Azure Machine Learning
description: AutoMLStep 允许在管道中使用自动机器学习。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 04/28/2020
ms.openlocfilehash: 9bf17512d0b14c7106101d98598e2914020afc7a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857949"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>在 Python 中的 Azure 机器学习管道中使用自动 ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 机器学习的自动 ML 功能可帮助你发现高性能模型，而不会重新实现每种可能的方法。 与 Azure 机器学习管道结合使用，你可以创建可部署的工作流，这些工作流可以快速发现最适合你的数据的算法。 本文介绍如何将数据准备步骤有效地加入到自动 ML 步骤。 自动 ML 可以快速发现最适合你的数据的算法，同时让你 MLOps 和模型生命周期操作化和管道。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* Azure 机器学习工作区。 请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。  

* 基本熟悉 Azure 的[自动化机器学习](concept-automated-ml.md)和[机器学习管道](concept-ml-pipelines.md)设施和 SDK。

## <a name="review-automated-mls-central-classes"></a>查看自动 ML 的中心类

管道中的自动 ML 由`AutoMLStep`对象表示。 `AutoMLStep` 类是 `PipelineStep` 的一个子类。 `PipelineStep`对象的图形定义`Pipeline`。

有多个子类`PipelineStep`。 除了之外`AutoMLStep`，本文还将显示`PythonScriptStep`用于数据准备的，另一种用于注册模型。

最初将数据移动_到_ML 管道的首选方法是使用`Dataset`对象。 若要在步骤_之间_移动数据，首选方法是`PipelineData`处理对象。 要与`AutoMLStep`一起使用，必须`PipelineData`将对象转换为`PipelineOutputTabularDataset`对象。 有关详细信息，请参阅[来自 ML 管道的输入和输出数据](how-to-move-data-in-out-of-pipelines.md)。

通过`AutoMLStep` `AutoMLConfig`对象进行配置。 `AutoMLConfig`是一个灵活的类，如在[Python 中配置自动 ML 试验](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#configure-your-experiment-settings)中所述。 

在`Pipeline`中运行`Experiment`。 管道`Run`为每个步骤都提供子`StepRun`。 自动 ML `StepRun`的输出是定型指标和性能最高的模型。

为简单起见，本文为分类任务创建了一个简单的管道。 该任务将预测 Titanic 的生存，但我们不会讨论数据或任务，只是通过传递。

## <a name="get-started"></a>入门

### <a name="retrieve-initial-dataset"></a>检索初始数据集

通常，ML 工作流从预先存在的基线数据开始。 对于已注册的数据集，这是一个很好的方案。 数据集在工作区中可见，支持版本控制，可以交互方式浏览。 有多种方法可以创建和填充数据集，如[创建 Azure 机器学习数据集](how-to-create-register-datasets.md)中所述。 由于我们将使用 Python SDK 来创建管道，请使用 SDK 下载基线数据，并使用名称 "titanic_ds" 注册它。

```python
from azureml.core import Workspace, Dataset

ws = Workspace.from_config()
if not 'titanic_ds' in ws.datasets.keys() :
    # create a TabularDataset from Titanic training data
    web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
                 'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
    titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

    titanic_ds.register(workspace = ws,
                                     name = 'titanic_ds',
                                     description = 'Titanic baseline data',
                                     create_new_version = True)

titanic_ds = Dataset.get_by_name(ws, 'titanic_ds')
```

代码首先登录到**配置**中定义的 Azure 机器学习工作区（有关说明，请参阅[教程：开始使用 Python SDK 创建首个 ML 试验](tutorial-1st-experiment-sdk-setup.md)）。 如果没有名为`'titanic_ds'` "已注册" 的数据集，则会创建一个。 此代码从 Web 下载 CSV 数据，使用这些数据来实例化`TabularDataset` ，然后使用工作区注册该数据集。 最后，函数`Dataset.get_by_name()`将分配`Dataset`给。 `titanic_ds` 

### <a name="configure-your-storage-and-compute-target"></a>配置存储和计算目标

管道需要的其他资源为存储，通常是 Azure 机器学习计算资源。 

```python
from azureml.core import Datastore
from azureml.core.compute import AmlCompute, ComputeTarget

datastore = ws.get_default_datastore()

compute_name = 'cpu-cluster'
if not compute_name in ws.compute_targets :
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                min_nodes=0,
                                                                max_nodes=1)
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # Show the result
    print(compute_target.get_status().serialize())

compute_target = ws.compute_targets[compute_name]
```

数据准备和自动 ML 步骤之间的中间数据可以存储在工作区的默认数据存储中，因此，我们不需要对`get_default_datastore()` `Workspace`对象执行多个调用。 

之后，代码检查 AML 计算目标`'cpu-cluster'`是否已存在。 如果不是，我们会指定需要一个基于 CPU 的小型计算目标。 如果打算使用自动 ML 的深度学习功能（例如，使用 DNN 支持的文本特征化），应选择具有强 GPU 支持的计算，如[gpu 优化虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/sizes-gpu)中所述。 

该代码将一直阻止到预配目标，然后打印刚刚创建的计算目标的某些详细信息。 最后，从工作区中检索命名的计算目标并将其`compute_target`分配给。 

### <a name="configure-the-training-run"></a>配置训练运行

下一步是确保远程定型运行包含定型步骤所需的所有依赖项。 依赖项和运行时上下文通过创建和配置`RunConfiguration`对象进行设置。 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
aml_run_config.target = compute_target
aml_run_config.environment.python.user_managed_dependencies = False

# Add some packages relied on by data prep step
aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
    conda_packages=['pandas','scikit-learn'], 
    pip_packages=['azureml-sdk[automl,explain]', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)
```

## <a name="prepare-data-for-automated-machine-learning"></a>为自动机器学习准备数据

### <a name="write-the-data-preparation-code"></a>编写数据准备代码

基线 Titanic 数据集包含混合数字和文本数据，缺少某些值。 若要为自动化机器学习做好准备，数据准备管道步骤将：

- 使用随机数据或与 "Unknown" 对应的类别填充缺少的数据
- 将分类数据转换为整数
- 删除我们不打算使用的列
- 将数据拆分为定型集和测试集
- 将转换的数据写入`PipelineData`输出路径

```python
%%writefile dataprep.py
from azureml.core import Run

import pandas as pd 
import numpy as np 
import pyarrow as pa
import pyarrow.parquet as pq
import argparse

RANDOM_SEED=42

def prepare_age(df):
    # Fill in missing Age values from distribution of present Age values 
    mean = df["Age"].mean()
    std = df["Age"].std()
    is_null = df["Age"].isnull().sum()
    # compute enough (== is_null().sum()) random numbers between the mean, std
    rand_age = np.random.randint(mean - std, mean + std, size = is_null)
    # fill NaN values in Age column with random values generated
    age_slice = df["Age"].copy()
    age_slice[np.isnan(age_slice)] = rand_age
    df["Age"] = age_slice
    df["Age"] = df["Age"].astype(int)
    
    # Quantize age into 5 classes
    df['Age_Group'] = pd.qcut(df['Age'],5, labels=False)
    df.drop(['Age'], axis=1, inplace=True)
    return df

def prepare_fare(df):
    df['Fare'].fillna(0, inplace=True)
    df['Fare_Group'] = pd.qcut(df['Fare'],5,labels=False)
    df.drop(['Fare'], axis=1, inplace=True)
    return df 

def prepare_genders(df):
    genders = {"male": 0, "female": 1, "unknown": 2}
    df['Sex'] = df['Sex'].map(genders)
    df['Sex'].fillna(2, inplace=True)
    df['Sex'] = df['Sex'].astype(int)
    return df

def prepare_embarked(df):
    df['Embarked'].replace('', 'U', inplace=True)
    df['Embarked'].fillna('U', inplace=True)
    ports = {"S": 0, "C": 1, "Q": 2, "U": 3}
    df['Embarked'] = df['Embarked'].map(ports)
    return df
    
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
    
titanic_ds = Run.get_context().input_datasets['titanic_ds']
df = titanic_ds.to_pandas_dataframe().drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1)
df = prepare_embarked(prepare_genders(prepare_fare(prepare_age(df))))

os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
pq.write_table(pa.Table.from_pandas(df), args.output_path)

print(f"Wrote test to {args.output_path} and train to {args.output_path}")
```

上面的代码段是 Titanic 数据的完整、最少的数据准备示例。 代码段以 Jupyter "魔术 command" 开头，以将代码输出到文件中。 如果未使用 Jupyter 笔记本，请删除该行，并手动创建文件。

上述代码`prepare_`段中的各种函数修改输入数据集中的相关列。 这些函数在数据更改为 Pandas `DataFrame`对象后处理数据。 在每种情况下，丢失的数据都是用代表的随机数据或分类数据填充的，表示 "未知"。 基于文本的分类数据映射到整数。 不再需要的列将被覆盖或删除。 

在代码定义数据准备函数后，代码分析输入参数，该参数是我们要将数据写入到的路径。 （这些值将由`PipelineData`将在下一步中讨论的对象决定。）此代码检索已注册`'titanic_cs'` `Dataset`的，将其转换为`DataFrame`Pandas，并调用各种数据准备函数。 

由于是`output_path`完全限定的，因此该`os.makedirs()`函数用于准备目录结构。 此时，你可以使用`DataFrame.to_csv()`来写入输出数据，但 Parquet 文件的效率更高。 这种效率可能与此类小型数据集无关，但使用**PyArrow**包`from_pandas()`和`write_table()`函数只是比`to_csv()`更多的键击。

下面讨论的自动 ML 步骤本身支持 Parquet 文件，因此不需要进行任何特殊处理。 

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>编写数据准备管道步骤（`PythonScriptStep`）

上面所述的数据准备代码必须与要用于`PythonScripStep`管道的对象相关联。 Parquet 数据准备输出所写入到的路径由`PipelineData`对象生成。 之前准备的资源（例如`ComputeTarget` `RunConfig`、和`'titanic_ds' Dataset` ）用于完成规范。

```python
from azureml.pipeline.core import PipelineData
from azureml.pipeline.steps import PythonScriptStep

prepped_data_path = PipelineData("titanic_train", datastore).as_dataset()
prepped_data_path = PipelineData("titanic_train", datastore).as_dataset()

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=["--output_path", prepped_data_path],
    inputs=[titanic_ds.as_named_input("titanic_ds")],
    outputs=[prepped_data_path],
    allow_reuse=True
)
```

`prepped_data_path`对象的类型`PipelineOutputFileDataset`为。 请注意，它是在`arguments`和`outputs`参数中指定的。 如果查看上一步，你会看到在数据准备代码中，参数`'--output_path'`的值为写入 Parquet 文件的文件路径。 

## <a name="train-with-automlstep"></a>AutoMLStep 定型

配置自动 ML 管道步骤是通过`AutoMLConfig`类完成的。 在[Python 中配置自动 ML 试验](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train)中介绍了此灵活的类。 数据输入和输出是配置的唯一方面，需要在 ML 管道中特别注意。 下面将详细讨论`AutoMLConfig`中管道的输入和输出。 除了数据以外，ML 管道的优点是能够在不同步骤中使用不同的计算目标。 您可以选择仅为自动 ML 过程`ComputeTarget`使用更强大的功能。 这样做非常简单，就像将更强大`RunConfiguration`的`AutoMLConfig`对象赋给`run_configuration`对象的参数一样。

### <a name="send-data-to-automlstep"></a>将数据发送到`AutoMLStep`

在 ML 管道中，输入数据必须是`Dataset`对象。 最高性能的方法是以`PipelineOutputTabularDataset`对象的形式提供输入数据。 `parse_parquet_files()`您可以使用`parse_delimited_files()`或在`PipelineOutputFileDataset`中创建该类型的对象，例如`prepped_data_path`对象。

```python
# type(prepped_data_path) == PipelineOutputFileDataset
# type(prepped_data) == PipelineOutputTabularDataset
prepped_data = prepped_data_path.parse_parquet_files(file_extension=None)
```

上面的代码段从数据准备步骤`PipelineOutputTabularDataset`的`PipelineOutputFileDataset`输出中创建一个高性能的。

另一种方法是`Dataset`使用工作区中注册的对象：

```python
prepped_data = Dataset.get_by_name(ws, 'Data_prepared')
```

比较这两种方法：

| 方法 |  | 
|-|-|
|`PipelineOutputTabularDataset`| 提高性能 | 
|| 自然路由来源`PipelineData` | 
|| 管道运行后数据不会保留 |
|| [显示`PipelineOutputTabularDataset`技术的笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) |
| 注册`Dataset` | 降低性能 |
| | 可以通过多种方式生成 | 
| | 数据在工作区中保持不变 |
| | [显示注册`Dataset`方法的笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/continuous-retraining/auto-ml-continuous-retraining.ipynb)

### <a name="specify-automated-ml-outputs"></a>指定自动 ML 输出

的输出`AutoMLStep`是性能较高的模型的最终度量分数和该模型本身。 若要在后续管道中使用这些输出， `PipelineData`请准备要接收它们的对象。

```python
from azureml.pipeline.core import TrainingOutput

metrics_data = PipelineData(name='metrics_data',
                           datastore=datastore,
                           pipeline_output_name='metrics_output',
                           training_output=TrainingOutput(type='Metrics'))
model_data = PipelineData(name='best_model_data',
                           datastore=datastore,
                           pipeline_output_name='model_output',
                           training_output=TrainingOutput(type='Model'))
```

上面的代码片段为指标`PipelineData`和模型输出创建两个对象。 每个都命名为，并分配给之前检索到的默认数据存储， `type`并`TrainingOutput`与中`AutoMLStep`的特定数据关联。 由于我们将`pipeline_output_name`对这些`PipelineData`对象进行分配，因此，它们的值将不仅可用于单个管道步骤，还可以从整个管道中获得，这将在 "检查管道结果" 一节中讨论。 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>配置和创建自动 ML 管道步骤

定义输入和输出后，就可以创建`AutoMLConfig`和。 `AutoMLStep` 配置的详细信息将取决于你的任务，如在[Python 中配置自动 ML 试验中](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train)所述。 对于 Titanic 生存分类任务，以下代码片段演示了一个简单的配置。

```python
from azureml.train.automl import AutoMLConfig
from azureml.pipeline.steps import AutoMLStep

# Change iterations to a reasonable number (50) to get better accuracy
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 2,
    "experiment_timeout_hours" : 0.25,
    "primary_metric" : 'AUC_weighted'
}

automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)

train_step = AutoMLStep(name='AutoML_Classification',
    automl_config=automl_config,
    passthru_automl_config=False,
    outputs=[metrics_data,model_data],
    allow_reuse=True)
```
此代码段显示了通常与`AutoMLConfig`一起使用的用法。 更流畅（超参数-路子）的参数在单独的字典中指定，而不太可能更改的值是直接在`AutoMLConfig`构造函数中指定的。 在这种情况下`automl_settings` ，指定 brief 运行：仅在2次迭代或15分钟后停止运行，以先达到的条件为准。

`automl_settings`字典将作为 kwargs 传递到`AutoMLConfig`构造函数。 其他参数不太复杂：

- `task`对于此示例`classification` ，设置为。 其他有效值为`regression`和`forecasting`
- `path`和`debug_log`描述项目的路径和将写入调试信息的本地文件 
- `compute_target`是之前定义`compute_target`的，在此示例中，是基于 CPU 的廉价计算机。 如果你使用的是 AutoML 的深度学习工具，则需要将计算目标更改为基于 GPU
- `featurization` 设置为 `auto`。 有关更多详细信息，请参阅自动 ML 配置文档的[Data 特征化](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#data-featurization)部分 
- `training_data`设置为从数据`PipelineOutputTabularDataset`准备步骤的输出中进行的对象 
- `label_column_name`指示要预测的列 

`AutoMLStep`本身采用`AutoMLConfig`和作为输出，创建用于保存指标和`PipelineData`模型数据的对象。 

>[!Important]
> 如果`AutoMLStep`正在使用`PipelineOutputTabularDataset`对象`False`进行输入，则必须将设置`passthru_automl_config`为。

在此示例中，自动 ML 进程将在上执行交叉验证`training_data`。 您可以控制`n_cross_validations`参数的交叉验证次数。 如果已将定型数据拆分为数据准备步骤的一部分，则可以将其设置`validation_data`为其自身。 `Dataset`

有时，你可能会看到`X`数据功能和`y`数据标签的使用。 此方法已弃用，你应将`training_data`其用于输入。 

## <a name="register-the-model-generated-by-automated-ml"></a>注册由自动 ML 生成的模型 

基本 ML 管道中的最后一个步骤是注册创建的模型。 通过将模型添加到工作区的模型注册表中，它将在门户中提供，并且可以进行版本控制。 若要注册模型，请编写`PythonScriptStep`一个采用的`model_data`输出的另`AutoMLStep`一个。

### <a name="write-the-code-to-register-the-model"></a>编写用于注册模型的代码

模型在中注册`Workspace`。 您可能很熟悉如何使用`Workspace.from_config()`登录到本地计算机上的工作区，但有另一种方法可以从正在运行的 ML 管道中获取工作区。 `Run.get_context()`检索活动`Run`。 此`run`对象提供对许多重要对象（包括此处使用`Workspace`的）的访问权限。

```python
%%writefile register_model.py
from azureml.core.model import Model, Dataset
from azureml.core.run import Run, _OfflineRun
from azureml.core import Workspace
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--model_name", required=True)
parser.add_argument("--model_path", required=True)
args = parser.parse_args()

print(f"model_name : {args.model_name}")
print(f"model_path: {args.model_path}")

run = Run.get_context()
ws = Workspace.from_config() if type(run) == _OfflineRun else run.experiment.workspace

model = Model.register(workspace=ws,
                       model_path=args.model_path,
                       model_name=args.model_name)

print("Registered version {0} of model {1}".format(model.version, model.name))
```

### <a name="write-the-pythonscriptstep-code"></a>写入 PythonScriptStep 代码

模型注册`PythonScriptStep`使用`PipelineParameter`作为其参数之一。 管道参数是可在运行时轻松设置的管道的参数。 声明后，它们将作为普通参数传递。 

```python

from azureml.pipeline.core.graph import PipelineParameter

# The model name with which to register the trained model in the workspace.
model_name = PipelineParameter("model_name", default_value="TitanicSurvivalInitial")

register_step = PythonScriptStep(script_name="register_model.py",
                                       name="register_model",
                                       allow_reuse=False,
                                       arguments=["--model_name", model_name, "--model_path", model_data],
                                       inputs=[model_data],
                                       compute_target=compute_target,
                                       runconfig=aml_run_config)
```

## <a name="create-and-run-your-automated-ml-pipeline"></a>创建并运行自动 ML 管道

创建和运行包含的`AutoMLStep`管道与正常管道并无区别。 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

上面的代码将数据准备、自动 ML 和模型注册步骤结合到一个`Pipeline`对象中。 然后创建`Experiment`对象。 如果`Experiment`存在命名试验，则构造函数将检索它，如果需要，则创建它。 它将提交`Pipeline`到`Experiment`，从而创建一个`Run`将异步运行管道的对象。 `wait_for_completion()`函数会被阻止，直到运行完成。

### <a name="examine-pipeline-results"></a>检查管道结果 

`run`完成后，您可以检索`PipelineData`已分配的`pipeline_output_name`对象。 您可以下载结果并加载它们以供进一步处理。  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

下载的文件将写入子目录`azureml/{run.id}/`。 度量值文件是 JSON 格式的文件，可以转换为 Pandas 数据帧以进行检查。

对于本地处理，可能需要安装相关程序包，如 Pandas、Pickle、AzureML SDK 等。 在此示例中，可能由自动 ML 找到的最佳模型将取决于 XGBoost。

```bash
!pip install xgboost==0.90
```

```python
import pandas as pd
import json

metrics_filename = metrics_output._path_on_datastore
# metrics_filename = path to downloaded file
with open(metrics_filename) as f:
   metrics_output_result = f.read()
   
deserialized_metrics_output = json.loads(metrics_output_result)
df = pd.DataFrame(deserialized_metrics_output)
df
```

上面的代码片段显示了从 Azure 数据存储上的位置加载的指标文件。 你还可以从下载的文件中加载它，如注释中所示。 反序列化并将其转换为 Pandas 数据帧后，可以查看自动 ML 步骤的每个迭代的详细指标。

可以将模型文件反序列化为可`Model`用于推断的对象、进一步的指标分析等。 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

有关加载和使用现有模型的详细信息，请参阅[将现有模型与 Azure 机器学习配合使用](how-to-deploy-existing-model.md)。

### <a name="download-the-results-of-an-automated-ml-run"></a>下载自动 ML 运行的结果

如果已与文章结合使用，则会获得一个实例化`run`对象。 但您也可以通过`Run` `Workspace` `Experiment`对象的方式检索已完成的对象。

工作区包含所有试验和运行的完整记录。 可以使用门户查找并下载试验的输出或使用代码。 若要从历史运行中访问记录，请使用 Azure 机器学习查找你感兴趣的运行 ID。 使用该 ID，你可以通过`run` `Workspace`和`Experiment`选择特定的。

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

您必须将以上代码中的字符串更改为历史运行的详细信息。 上面的代码段假定您已分配`ws`到与 normal `Workspace` `from_config()`相关的。 直接检索感兴趣的实验，然后代码通过匹配`Run` `run.id`值查找相关的。

获得`Run`对象后，可以下载指标和模型。 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

每`Run`个对象`StepRun`都包含对象，这些对象包含有关各个管道步骤运行的信息。 `run`搜索的`StepRun`对象`AutoMLStep`。 使用其默认名称检索度量值和模型，即使未将对象传递`PipelineData`到的`outputs`参数，它们也可用。 `AutoMLStep` 

最后，实际指标和模型将下载到本地计算机，如上面的 "检查管道结果" 部分所述。

## <a name="next-steps"></a>后续步骤

- 运行此 Jupyter 笔记本，其中显示了使用回归预测出租车费用的[管道中自动 ML 的完整示例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb)
- [无需编写代码即可创建自动 ML 试验](how-to-use-automated-ml-for-ml-models.md)
- 探索[演示自动 ML 的各种 Jupyter 笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
- 了解将管道集成到[端到端 MLOps](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment#automate-the-ml-lifecycle)或调查[MLOps GitHub 存储库](https://github.com/Microsoft/MLOpspython) 
