---
title: 来自 ML 管道的输入和输出数据
titleSuffix: Azure Machine Learning
description: 在 Azure 机器学习管道中准备、使用和生成数据
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 3dd1a82bf7fad1f201f5c0f52af944ef44a3fdf9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879760"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>将数据移动到 ML 管道步骤 （Python） 中和之间

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

数据是机器学习管道的核心。 本文提供了用于在 Azure 机器学习管道中的步骤之间导入、转换和移动数据的代码。 有关数据在 Azure 机器学习中工作原理的概述，请参阅[Azure 存储服务中的访问数据](how-to-access-data.md)。 有关 Azure 机器学习管道的优点和结构，请参阅什么是[Azure 机器学习管道？](concept-ml-pipelines.md)

本文将向你介绍如何：

- 对`Dataset`预先存在的数据使用对象
- 访问步骤中的数据
- 将数据`Dataset`拆分为子集，如训练和验证子集
- 创建`PipelineData`对象以将数据传输到下一个管道步骤
- 使用`PipelineData`对象作为管道步骤的输入
- 从`PipelineData`希望`Dataset`保留创建新对象

## <a name="prerequisites"></a>先决条件

需要：

- Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

- [适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或 [Azure 机器学习工作室](https://ml.azure.com/)的访问权限。

- Azure 机器学习工作区。
  
  [创建 Azure 机器学习工作区](how-to-manage-workspace.md)或通过 Python SDK 使用现有工作区。 导入`Workspace`和`Datastore`类，并使用 函数`config.json``from_config()`从文件加载订阅信息。 默认情况下，此函数查找当前目录中的 JSON 文件，但您也可以指定路径参数以使用 指向`from_config(path="your/file/path")`该文件。

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- 一些预先存在的数据。 本文简要显示了 Azure blob[容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)的使用。

- 可选：现有的机器学习管道，如[使用 Azure 机器学习 SDK 创建和运行机器学习管道](how-to-create-your-first-pipeline.md)中所述管道。

## <a name="use-dataset-objects-for-pre-existing-data"></a>对`Dataset`预先存在的数据使用对象 

将数据引入管道的首选方法是使用[数据集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)对象。 `Dataset`对象表示整个工作区中可用的持久数据。

创建和注册`Dataset`对象的方法有很多种。 表格数据集用于一个或多个文件中可用的分隔数据。 文件数据集用于二进制数据（如图像）或要分析的数据。 创建`Dataset`对象的最简单的编程方法是在工作区存储或公共 URL 中使用现有 blob：

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

有关创建具有不同选项和不同来源的数据集的更多选项、在 Azure 机器学习 UI 中注册数据集并查看它们、了解数据大小如何与计算容量交互以及对其进行版本控制，请参阅[创建 Azure 机器学习数据集](how-to-create-register-datasets.md)。 

### <a name="pass-datasets-to-your-script"></a>将数据集传递到脚本

要将数据集的路径传递给脚本，`Dataset`请使用对象`as_named_input()`的方法。 可以将生成的`DatasetConsumptionConfig`对象作为参数传递给脚本，也可以通过使用`inputs`参数将结果对象传递到管道脚本，也可以使用`Run.get_context().input_datasets[]`检索数据集。

创建命名输入后，可以选择其访问模式：`as_mount()`或`as_download()`。 如果脚本处理数据集中的所有文件，而计算资源上的磁盘足够大，以便数据集使用，则下载访问模式是更好的选择。 下载访问模式将避免在运行时流式传输数据的开销。 如果脚本访问数据集的子集，或者该子集对于计算来说太大，请使用装载访问模式。 有关详细信息，请阅读["安装与下载"](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

要将数据集传递到管道步骤，

1. 使用`TabularDataset.as_named_inputs()``FileDataset.as_named_input()`或 （末尾无's'） 创建`DatasetConsumptionConfig`对象
1. 使用`as_mount()``as_download()`或 设置访问模式
1. 使用 或`arguments``inputs`参数将数据集传递到管道步骤

以下代码段显示了在`PythonScriptStep`构造函数中组合这些步骤的常见模式： 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

您还可以使用 和 等`random_split()``take_sample()`方法创建多个输入或减少传递到管道步骤的数据量：

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>访问脚本中的数据集

管道步骤脚本的命名输入作为`Run`对象中的字典提供。 使用`Run``Run.get_context()`检索活动对象，然后使用`input_datasets`检索命名输入的字典。 如果使用`DatasetConsumptionConfig``arguments`参数而不是参数传递对象，`inputs`请使用`ArgParser`代码访问数据。 这两种技术都体现在以下代码段中。

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

传递的值将是数据集文件的路径。

也可以直接访问已注册的`Dataset`。 由于已注册的数据集是永久性的，并且跨工作区共享，因此可以直接检索它们：

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>用于`PipelineData`中间数据

当`Dataset`对象表示持久性数据时，[管道数据](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)对象用于从管道步骤输出的临时数据。 由于`PipelineData`对象的生命周期比单个管道步骤长，因此在管道定义脚本中定义它们。 创建`PipelineData`对象时，必须提供数据将驻留在其中的名称和数据存储。 `PipelineData``PythonScriptStep`使用`arguments`和`outputs`参数将对象传递给对象_both_：

```python
default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)
```

您可以选择使用提供即时上载的访问`PipelineData`模式创建对象。 在这种情况下，当您创建 的 时`PipelineData`，将`upload_mode`设置`"upload"`和`output_path_on_compute`参数指定要将数据写入数据的路径：

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>用作`PipelineData`训练步骤的输出

在管道中`PythonScriptStep`，可以使用程序的参数检索可用的输出路径。 如果此步骤是第一步，并且将初始化输出数据，则必须在指定的路径上创建目录。 然后，您可以编写您希望包含在 的任何`PipelineData`文件。

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

`PipelineData`如果创建`is_directory`参数设置为`True`，则只需执行`os.makedirs()`调用就足够了，然后您可以自由地将任何您希望写入路径的文件写入。 有关详细信息，请参阅[管道数据](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)参考文档。

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>读取`PipelineData`为非初始步骤的输入

初始管道步骤将一些数据写入`PipelineData`路径，并成为该初始步骤的输出后，它可用作后续步骤的输入：

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

输入的值是前`PipelineData`一个输出的路径。 如果如前所示，第一步编写了单个文件，则使用它可能如下所示： 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>将`PipelineData`对象转换为`Dataset`s

如果要使可用`PipelineData`时间超过运行持续时间，请使用其`as_dataset()`函数将其转换为 。 `Dataset` 然后，您可以注册`Dataset`，使其成为您的工作区中的一流公民。 由于对象`PipelineData`在每次运行管道时都将具有不同的路径，因此强烈建议`create_new_version`您在注册从`True``Dataset``PipelineData`对象创建时设置为 。

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>后续步骤

* [创建 Azure 机器学习数据集](how-to-create-register-datasets.md)
* [使用 Azure 机器学习 SDK 创建和运行机器学习管道](how-to-create-your-first-pipeline.md)
