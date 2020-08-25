---
title: 在 ML 管道中使用自动化 ML
titleSuffix: Azure Machine Learning
description: AutoMLStep 允许在管道中使用自动化机器学习。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 06/15/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 7eac92a3d438c6a9ee67ae5d5b06829f3ef77528
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2020
ms.locfileid: "88754917"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>在 Python 的 Azure 机器学习管道中使用自动化 ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 机器学习的自动化 ML 功能可帮助你发现高性能模型，而无需重新实现所有可能的方法。 结合 Azure 机器学习管道，你可以创建可部署的工作流，这些工作流可以快速发现最适合你的数据的算法。 本文将介绍如何有效地将数据准备步骤与自动化 ML 步骤结合起来。 自动化 ML 可以快速发现最适合你的数据的算法，同时让你开始使用 MLOps，并使用管道对生命周期操作进行建模。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* Azure 机器学习工作区。 请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。  

* 基本熟悉 Azure 的[自动化机器学习](concept-automated-ml.md)和[机器学习管道](concept-ml-pipelines.md)设施和 SDK。

## <a name="review-automated-mls-central-classes"></a>查看自动化 ML 的中心类

管道中的自动化 ML 由 `AutoMLStep` 对象表示。 `AutoMLStep` 类是 `PipelineStep` 的子类。 `PipelineStep` 对象的图形定义了 `Pipeline`。

`PipelineStep` 有多个子类。 除了 `AutoMLStep`，本文还将显示一个 `PythonScriptStep` 用于数据准备，另一个用于注册模型。

最初将数据移动到 ML 管道时，首选方法是使用 `Dataset` 对象__。 若要在步骤 _之间_ 移动数据并可能保存运行中的数据输出，首选方法是处理 `OutputFileDatasetConfig` 对象。 有关详细信息，请参阅[来自 ML 管道的输入和输出数据](how-to-move-data-in-out-of-pipelines.md)。

> [!NOTE]
>`OutputFileDatasetConfig`和 `OutputTabularDatasetConfig` 类是试验性预览功能，随时可能会更改。
>
>有关详细信息，请参阅 https://aka.ms/azuremlexperimental。

通过 `AutoMLConfig` 对象配置 `AutoMLStep`。 `AutoMLConfig` 是一个灵活的类，如[使用 Python 配置自动化 ML 试验](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#configure-your-experiment-settings)中所述。 

`Pipeline` 在 `Experiment` 中运行。 对于每个步骤，管道 `Run` 都具有子级 `StepRun`。 自动化 ML `StepRun` 的输出是训练指标和最高性能的模型。

为了具体介绍，本文会创建一个用于分类任务的简单管道。 任务是预测泰坦尼克号的幸存者，但我们只会顺便讨论数据或任务。

## <a name="get-started"></a>入门

### <a name="retrieve-initial-dataset"></a>检索初始数据集

通常，ML 工作流从预先存在的基线数据开始。 对于已注册的数据集，这是一个很好的方案。 数据集在整个工作区中可见，支持版本控制，并且可以以交互方式浏览。 可通过多种方法创建和填充数据集，如[创建 Azure 机器学习数据集](how-to-create-register-datasets.md)中所述。 因为我们将使用 Python SDK 创建管道，所以请使用该 SDK 下载基线数据，并使用名称“titanic_ds”注册它。

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

代码首先登录到 config.json 中定义的 Azure 机器学习工作区（有关说明，请参阅****[教程：开始使用 Python SDK 创建第一个 ML 试验](tutorial-1st-experiment-sdk-setup.md)）。 如果尚未注册名为 `'titanic_ds'` 的数据集，该 SDK 将创建它。 代码从 Web 下载 CSV 数据，使用这些数据实例化 `TabularDataset`，然后将数据集注册到工作区。 最后，函数 `Dataset.get_by_name()` 将 `Dataset` 分配给 `titanic_ds`。 

### <a name="configure-your-storage-and-compute-target"></a>配置存储和计算目标

管道需要的其他资源有存储资源，通常还有 Azure 机器学习计算资源。 

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

可将数据准备和自动化 ML 步骤之间的中间数据存储在工作区的默认数据存储中，因此我们只需要在 `Workspace` 对象上调用 `get_default_datastore()`。 

然后，代码会检查 AML 计算目标 `'cpu-cluster'` 是否已经存在。 如果不是，我们指定需要一个基于 CPU 的小型计算目标。 如果你打算使用自动化 ML 的深度学习功能（例如有 DNN 支持的文本特征化），则应选择具有强大 GPU 支持的计算，如 [GPU 优化虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/sizes-gpu)中所述。 

代码将一直阻止到目标预配完毕，然后打印刚创建的计算目标的某些详细信息。 最后，从工作区检索命名计算目标并将其分配给 `compute_target`。 

### <a name="configure-the-training-run"></a>配置训练运行

下一步是确保远程训练运行包含训练步骤所需的所有依赖项。 通过创建和配置 `RunConfiguration` 对象来设置依赖项和运行时上下文。 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import Environment 

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
aml_run_config.target = compute_target

USE_CURATED_ENV = True
if USE_CURATED_ENV :
    curated_environment = Environment.get(workspace=ws, name="AzureML-Tutorial")
    aml_run_config.environment = curated_environment
else:
    aml_run_config.environment.python.user_managed_dependencies = False
    
    # Add some packages relied on by data prep step
    aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
        conda_packages=['pandas','scikit-learn'], 
        pip_packages=['azureml-sdk[automl,explain]', 'azureml-dataprep[fuse,pandas]'], 
        pin_sdk_version=False)
```

以上代码显示了处理依赖项的两个选项。 如前所述，当 `USE_CURATED_ENV = True`，配置基于特选环境。 特选环境中“预先准备”有常见的互依赖库，可以大大加快联机速度。 特选环境在 [Microsoft 容器注册表](https://hub.docker.com/publishers/microsoftowner)中具有预先生成的 Docker 映像。 将 `USE_CURATED_ENV` 更改为 `False` 所采用的路径显示了显式设置依赖项的模式。 在这种情况下，将在资源组内的 Azure 容器注册表中创建和注册新的自定义 Docker 映像（请参阅 [Azure 中的专用 Docker 容器注册表简介](https://docs.microsoft.com/azure/container-registry/container-registry-intro)）。 创建和注册此映像可能需要几分钟的时间。 

## <a name="prepare-data-for-automated-machine-learning"></a>为自动化机器学习准备数据

### <a name="write-the-data-preparation-code"></a>编写数据准备代码

基线泰坦尼克号数据集由混合的数字和文本数据组成，且缺少一些值。 若要使其为自动化机器学习做好准备，数据准备管道步骤将：

- 使用随机数据或与“未知”对应的类别填充缺少的数据
- 将分类数据转换为整数
- 删除不打算使用的列
- 将数据分为训练集和测试集
- 将转换的数据写入 `OutputFileDatasetConfig` 输出路径

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

上面的代码片段是对泰坦尼克号数据进行数据准备的完整的极简示例。 代码片段以 Jupyter“magic 命令”开头，该命令将代码输出到文件中。 如果未使用 Jupyter 笔记本，请删除该行并手动创建文件。

上述代码片段中的各种 `prepare_` 函数修改输入数据集中的相关列。 将数据更改为 Pandas `DataFrame` 对象后，这些函数就会对其进行处理。 在每种情况下，缺少的数据要么用代表性随机数据填充，要么用表示“未知”的分类数据填充。 基于文本的分类数据映射到整数。 将覆盖或删除不再需要的列。 

代码定义了数据准备函数之后，会解析输入参数，这是我们要写入数据的路径。 （这些值将由 `OutputFileDatasetConfig` 对象确定，并将在下一步中进行讨论。）代码会检索已注册的 `'titanic_cs'` `Dataset`，将其转换为 Pandas `DataFrame`，并调用各种数据准备函数。 

由于 `output_path` 是完全限定的，因此使用函数 `os.makedirs()` 来准备目录结构。 此时，你可以使用 `DataFrame.to_csv()` 来写入输出数据，但 Parquet 文件的效率更高。 对于此类小型数据集，这种效率可能无关紧要，但使用 PyArrow 包的 `from_pandas()` 和 `write_table()` 函数与使用 `to_csv()` 相比只是多按几次键盘而已****。

下面讨论的自动化 ML 步骤本机支持 Parquet 文件，因此不需要进行特殊处理即可使用它们。 

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>编写数据准备管道步骤 (`PythonScriptStep`)

上述数据准备代码必须与 `PythonScripStep` 对象相关联才能用于管道。 将 Parquet 数据准备输出写入到的路径由 `OutputFileDatasetConfig` 对象生成。 前面准备的资源（如 `ComputeTarget`、`RunConfig` 和 `'titanic_ds' Dataset`）用于补全规范。

```python
from azureml.data import OutputFileDatasetConfig
from azureml.pipeline.steps import PythonScriptStep

prepped_data_path = OutputFileDatasetConfig(name="titanic_train", (destination=(datastore, 'outputdataset')))

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=[titanic_ds.as_named_input('titanic_ds').as_mount(), prepped_data_path],
    allow_reuse=True
)
```

`prepped_data_path`对象的类型为指向 `OutputFileDatasetConfig` 目录的类型。  请注意，它是在参数中指定的 `arguments` 。 

## <a name="train-with-automlstep"></a>通过 AutoMLStep 训练

使用 `AutoMLConfig` 类配置自动化 ML 管道步骤。 此灵活的类，如[使用 Python 配置自动化 ML 试验](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train)中所述。 在 ML 管道中，配置时需要特别注意的只有数据输入和输出。 下面将详细讨论管道中 `AutoMLConfig` 的输入和输出。 除了数据之外，ML 管道的一个优点是能够为不同的步骤使用不同的计算目标。 你可以选择只对自动化 ML 进程使用更强大的 `ComputeTarget`。 这样做很简单，只需将功能更强大的 `RunConfiguration` 分配给 `AutoMLConfig` 对象的 `run_configuration` 参数即可。

### <a name="send-data-to-automlstep"></a>将数据发送到 `AutoMLStep`

在 ML 管道中，输入数据必须是 `Dataset` 对象。 性能最高的方法是以 `OutputTabularDatasetConfig` 对象的形式提供输入数据。 使用在上创建该类型的对象 `read_delimited_files()` `OutputFileDatasetConfig` ，如 `prepped_data_path` 对象。

```python
# type(prepped_data_path) == OutputFileDatasetConfig
# type(prepped_data) == OutputTabularDatasetConfig
prepped_data = prepped_data_path.read_delimited_files()
```

以上代码片段会从数据准备步骤的 `OutputFileDatasetConfig` 输出创建一个高性能的 `OutputTabularDatasetConfig`。

### <a name="specify-automated-ml-outputs"></a>指定自动化 ML 输出

`AutoMLStep` 的输出是高性能模型和该模型本身的最终指标分数。 若要在后续管道步骤中使用这些输出，请准备 `OutputFileDatasetConfig` 对象来接收它们。

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

上面的代码片段为指标和模型输出创建了两个 `PipelineData` 对象。 为这两个对象命名，将其分配给先前检索到的默认数据存储，并与 `AutoMLStep` 中 `TrainingOutput` 的特定 `type` 相关联。 由于我们在这些 `PipelineData` 对象上分配 `pipeline_output_name`，因此它们的值不仅可用于单个管道步骤，还可用于整个管道，如以下“检查管道结果”部分中所述。 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>配置和创建自动化 ML 管道步骤

定义输入和输出后，就可以创建 `AutoMLConfig` 和 `AutoMLStep` 了。 配置的详细信息将取决于你的任务，如[使用 Python 配置自动化 ML 试验](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train)中所述。 对于泰坦尼克号幸存者分类任务，以下代码片段演示了一个简单的配置。

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
    outputs=[metrics_data,model_data],
    enable_default_model_output=False,
    enable_default_metrics_output=False,
    allow_reuse=True)
```
此片段显示了通常与 `AutoMLConfig` 一起使用的习语。 在单独的字典中指定更灵活的参数（类似超参数），并直接在 `AutoMLConfig` 构造函数中指定不太可能更改的值。 在本例中，`automl_settings` 指定一个简短的运行：运行将在 2 次迭代或 15 分钟后停止，以先达到的条件为准。

`automl_settings` 字典作为 kwargs 传递给 `AutoMLConfig` 构造函数。 其他参数并不复杂：

- `task` 在本例中设置为 `classification`。 其他有效值为 `regression` 和 `forecasting`
- `path` 以及 `debug_log` 描述项目的路径和要将调试信息写入的本地文件 
- `compute_target` 是先前定义的 `compute_target`，在本例中，它是一个基于 CPU、价格便宜的计算机。 如果你使用 AutoML 的深度学习工具，则需要将计算目标更改为基于 GPU
- `featurization` 设置为 `auto`。 可以在自动化 ML 配置文档的[数据特征化](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#data-featurization)部分找到更多详细信息 
- `training_data` 设置为从数据准备步骤的输出生成的 `OutputTabularDatasetConfig` 对象 
- `label_column_name` 指示要预测的列 

`AutoMLStep` 本身接受 `AutoMLConfig`，并创建用于保存指标和模型数据的 `PipelineData` 对象作为输出。 

>[!Important]
> 仅当使用时，才能将 `enable_default_model_output` 和设置 `enable_default_metrics_output`  为 `True`  `AutoMLStepRun` 。

在本例中，自动化 ML 进程将对 `training_data` 执行交叉验证。 可以使用 `n_cross_validations` 参数控制交叉验证次数。 如果已经在数据准备步骤中包含拆分训练数据的过程，可将 `validation_data` 设置为其自身的 `Dataset`。

你可能偶尔会看到使用 `X` 指示数据功能，使用 `y` 指示数据标签的情况。 此方法已弃用，应使用 `training_data` 作为输入。 

## <a name="register-the-model-generated-by-automated-ml"></a>注册由自动化 ML 生成的模型 

基本 ML 管道的最后一步是注册创建的模型。 将模型添加到工作区的模型注册表中，即可在门户中使用它们，并且可对其进行版本控制。 若要注册模型，请编写另一个采用 `AutoMLStep` 的 `model_data` 输出的 `PythonScriptStep`。

### <a name="write-the-code-to-register-the-model"></a>编写用于注册模型的代码

在 `Workspace` 中注册模型。 你可能熟悉如何在本地计算机上使用 `Workspace.from_config()` 登录工作区，但还可通过另一种方法从正在运行的 ML 管道获取工作区。 `Run.get_context()` 检索活动 `Run`。 此 `run` 对象提供对许多重要对象的访问，包括此处使用的 `Workspace`。

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

注册 `PythonScriptStep` 的模型使用 `PipelineParameter` 作为其参数之一。 管道参数即管道的参数，可以在运行提交时轻松设置。 声明后，它们将作为普通参数传递。 

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

## <a name="create-and-run-your-automated-ml-pipeline"></a>创建并运行自动化 ML 管道

创建并运行包含 `AutoMLStep` 的管道与创建和运行普通管道相同。 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

以上代码将数据准备、自动化 ML 和模型注册步骤组合到 `Pipeline` 对象中。 然后，创建一个 `Experiment` 对象。 `Experiment` 构造函数将检索已命名的试验是否存在，如果不存在则根据需要创建它。 它将 `Pipeline` 提交给 `Experiment`，创建 `Run` 对象，该对象将异步运行管道。 `wait_for_completion()` 函数将会阻止，直到运行完成。

### <a name="examine-pipeline-results"></a>检查管道结果 

`run` 完成后，可检索分配到 `pipeline_output_name` 的 `PipelineData` 对象。 可下载并加载结果以便进一步处理。  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

下载的文件将写入子目录 `azureml/{run.id}/`。 指标文件是 JSON 格式的，可以转换为 Pandas 数据框帧进行检查。

对于本地处理，可能需要安装相关的包，如 Pandas、Pickle、AzureML SDK 等。 在此示例中，自动化 ML 找到的最佳模型很可能依赖于 XGBoost。

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

以上代码片段显示了从 Azure 数据存储上的位置加载指标文件的情况。 你还可以从下载的文件加载它，如注释中所示。 将其反序列化并转换为 Pandas 数据帧后，就可以看到自动化 ML 步骤的每个迭代的详细指标。

模型文件可以反序列化为 `Model` 对象，可将其用于推断和进一步的指标分析等。 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

有关加载和使用现有模型的详细信息，请参阅[将现有模型用于 Azure 机器学习](how-to-deploy-existing-model.md)。

### <a name="download-the-results-of-an-automated-ml-run"></a>下载自动化 ML 运行的结果

如果你始终跟随文章进行操作，则你现在将拥有一个实例化的 `run` 对象。 但也可以通过 `Experiment` 对象从 `Workspace` 检索已完成的 `Run` 对象。

工作区包含所有试验和运行的完整记录。 可以使用门户来查找和下载试验的输出，也可以使用代码。 若要要访问来自历史运行的记录，请使用 Azure 机器学习查找你感兴趣的运行的 ID。 使用该 ID，可以通过 `Workspace` 和 `Experiment` 选择特定 `run`。

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

必须根据历史运行的具体情况更改上述代码中的字符串。 以上代码片段假设你已将 `ws` 分配给了具有普通 `from_config()` 的相关 `Workspace`。 直接检索感兴趣的试验，然后代码会通过匹配 `run.id` 值来查找你感兴趣的 `Run`。

有 `Run` 对象，就可以下载指标和模型了。 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

每个 `Run` 对象都包含 `StepRun` 对象，这些对象包含有关单个管道步骤运行的信息。 在 `run` 中搜索 `AutoMLStep` 的 `StepRun` 对象。 使用指标和模型的默认名称检索它们，即使不将 `PipelineData` 对象传递给 `AutoMLStep` 的 `outputs` 参数，也可使用这些名称。 

最后，会将实际指标和模型下载到本地计算机，如上面“检查管道结果”部分所述。

## <a name="next-steps"></a>后续步骤

- 运行此 Jupyter 笔记本，它显示了[管道中自动化 ML 的完整示例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb)，该示例使用回归来预测出租车费用
- [无需编写代码即可创建自动化 ML 试验](how-to-use-automated-ml-for-ml-models.md)
- 浏览各种[演示自动化 ML 的 Jupyter 笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
- 了解如何将管道集成到[端到端 MLOps](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment#automate-the-ml-lifecycle) 或调查 [MLOps GitHub 存储库](https://github.com/Microsoft/MLOpspython) 
