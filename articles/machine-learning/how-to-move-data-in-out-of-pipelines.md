---
title: 在 ML 管道中移动数据
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习管道中数据的输入和输出。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: a1bd93931f8a94f598952b28fc3db23d33e5783f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329765"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>将数据移入 ML 管道和在 ML 管道之间移动数据的步骤 (Python)



本文提供了用于在 Azure 机器学习管道中的步骤之间导入、转换和移动数据的代码。 有关数据在 Azure 机器学习中的工作原理的概述，请参阅[访问 Azure 存储服务中的数据](how-to-access-data.md)。 有关 Azure 机器学习管道的优点和结构，请参阅[什么是 Azure 机器学习管道？](concept-ml-pipelines.md)。

本文将向你介绍如何：

- 将 `Dataset` 对象用于预先存在的数据
- 在步骤中访问数据
- 将 `Dataset` 数据拆分为子集，例如训练子集和验证子集
- 创建 `PipelineData` 对象来将数据传输到下一管道步骤
- 使用 `PipelineData` 对象作为管道步骤的输入
- 基于 `PipelineData` 创建你要持久保存的新 `Dataset` 对象

> [!TIP]
> 公共预览版类和中提供了在管道步骤之间传递临时数据并在管道运行后保存数据的更好的  [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) 体验 [`OutputTabularDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?view=azure-ml-py&preserve-view=true) 。  这些类是 [试验](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true#&preserve-view=truestable-vs-experimental) 性预览功能，随时可能会更改。


## <a name="prerequisites"></a>先决条件

需要：

- Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

- [适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)，或 [Azure 机器学习工作室](https://ml.azure.com/)的访问权限。

- Azure 机器学习工作区。
  
  [创建 Azure 机器学习工作区](how-to-manage-workspace.md)或通过 Python SDK 使用现有工作区。 导入 `Workspace` 和 `Datastore` 类，并使用函数 `from_config()` 从文件 `config.json` 中加载订阅信息。 默认情况下，此函数会查找当前目录中的 JSON 文件，但你也可以使用 `from_config(path="your/file/path")` 指定一个路径参数，使之指向该文件。

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- 某些预先存在的数据。 本文简要介绍了如何使用 [Azure blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)。

- 可选：现有机器学习管道，例如[使用 Azure 机器学习 SDK 创建和运行机器学习管道](how-to-create-your-first-pipeline.md)中所述的管道。

## <a name="use-dataset-objects-for-pre-existing-data"></a>将 `Dataset` 对象用于预先存在的数据 

将数据引入到管道的首选方法是使用 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true) 对象。 `Dataset` 对象表示在整个工作区中可用的持久性数据。

可以通过许多方法来创建和注册 `Dataset` 对象。 表格数据集用于一个或多个文件中可用的分隔数据。 文件数据集用于二进制数据（例如图像）或你要分析的数据。 创建 `Dataset` 对象的最简单编程方式是使用工作区存储或公共 URL 中的现有 blob：

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

若要详细了解如何使用各种选项以及基于各种源创建数据集、在 Azure 机器学习 UI 中注册数据集并查看它们、了解数据大小如何与计算容量交互，以及如何对数据集进行版本控制，请参阅[创建 Azure 机器学习数据集](how-to-create-register-datasets.md)。 

### <a name="pass-datasets-to-your-script"></a>将数据集传递给脚本

若要将数据集的路径传递给你的脚本，请使用 `Dataset` 对象的 `as_named_input()` 方法。 你可以将生成的 `DatasetConsumptionConfig` 对象作为参数传递给你的脚本，也可以通过使用管道脚本的 `inputs` 参数来使用 `Run.get_context().input_datasets[]` 检索数据集。

创建命名输入后，可以选择其访问模式：`as_mount()` 或 `as_download()`。 如果你的脚本处理数据集中的所有文件，而计算资源上的磁盘足以容纳数据集，则下载访问模式是更好的选择。 下载访问模式可避免在运行时流式传输数据所产生的开销。 如果你的脚本访问数据集的子集或它对于你的计算而言太大，请使用装载访问模式。 有关详细信息，请阅读[装载与下载](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

若要将数据集传递给管道步骤，请执行以下操作：

1. 使用 `TabularDataset.as_named_input()` 或 `FileDataset.as_named_input()`（末尾没有“s”）创建一个 `DatasetConsumptionConfig` 对象
1. 使用 `as_mount()` 或 `as_download()` 设置访问模式
1. 使用 `arguments` 或 `inputs` 参数将数据集传递给管道步骤

以下代码片段显示了在 `PythonScriptStep` 构造函数中组合这些步骤的常见模式： 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_input('iris').as_mount()]
)
```

你还可以使用 `random_split()` 和 `take_sample()` 等方法来创建多个输入或减少传递给管道步骤的数据量：

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_input('train').as_download(), test.as_named_input('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>在脚本中访问数据集

管道步骤脚本的命名输入可以在 `Run` 对象中用作字典。 使用 `Run.get_context()` 检索活动 `Run` 对象，然后使用 `input_datasets` 检索命名输入的字典。 如果你使用 `arguments` 参数而非 `inputs` 参数传递 `DatasetConsumptionConfig` 对象，请使用 `ArgParser` 代码访问数据。 以下代码片段演示了这两种技术。

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_input('train').as_download()]
    inputs=[test.as_named_input('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

传递的值将是数据集文件的路径。

还可以直接访问已注册的 `Dataset`。 由于已注册的数据集是持久性的，并且在整个工作区中共享，因此你可以直接检索它们：

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>将 `PipelineData` 用于中间数据

尽管 `Dataset` 对象表示持久性数据，但 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) 对象用于从管道步骤输出的临时数据。 由于 `PipelineData` 对象的寿命比单个管道步骤长，因此你在管道定义脚本中定义它们。 创建 `PipelineData` 对象时，必须提供一个名称和用于存储数据的数据存储。 同时使用 `arguments` 和 `outputs` 参数将 `PipelineData` 对象传递给 `PythonScriptStep`：

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

你可以选择使用一个提供即时上传功能的访问模式来创建 `PipelineData` 对象。 在这种情况下，当你创建 `PipelineData` 时，请将 `upload_mode` 设置为 `"upload"`，并使用 `output_path_on_compute` 参数指定要将数据写入到其中的路径：

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

> [!TIP]
> 公共预览版类 [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) 改进了在管道步骤之间传递中间数据的体验。 有关使用的代码示例 `OutputFileDatasetConfig` ，请参阅如何 [构建两步 ML 管道](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)。


### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>将 `PipelineData` 用作训练步骤的输出
在管道的 `PythonScriptStep` 中，可以使用程序的参数检索可用输出路径。 如果此步骤是第一个步骤并将初始化输出数据，则必须在指定的路径创建目录。 然后，你可以写入要包含在 `PipelineData` 中的任何文件。

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

如果你已在创建 `PipelineData` 时将 `is_directory` 参数设置为 `True`，则只需执行 `os.makedirs()` 调用即可，然后就可以随意将所需的任何文件写入该路径。 有关更多详细信息，请查看 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) 参考文档。


### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>读取 `PipelineData` 作为非初始步骤的输入

当初始管道步骤将一些数据写入到 `PipelineData` 路径并且该数据成为初始步骤的输出后，可将其用作后面步骤的输入：

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']

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

`PipelineData` 输入的值是上一输出的路径。 

> [!TIP]
> 公共预览版类 [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) 改进了在管道步骤之间传递中间数据的体验。 有关使用的代码示例 `OutputFileDatasetConfig` ，请参阅如何 [构建两步 ML 管道](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)。

如前所示，如果第一个步骤写入了单个文件，则可以如下所示使用它： 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()
with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>将 `PipelineData` 对象转换为 `Dataset`

如果要使 `PipelineData` 的可用时间超过某个运行的持续时间，请使用其 `as_dataset()` 函数将其转换为 `Dataset`。 然后，你可以注册 `Dataset`，使其在你的工作区中处于第一等级。 由于每次运行管道时，`PipelineData` 对象的路径都不同，因此强烈建议你在注册基于 `PipelineData` 对象创建的 `Dataset` 时将 `create_new_version` 设置为 `True`。

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)

```
> [!TIP]
> 公共预览版类提供了用于在管道运行之外持久保存中间数据的体验 [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) 。 有关使用的代码示例 `OutputFileDatasetConfig` ，请参阅如何 [构建两步 ML 管道](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)。

## <a name="next-steps"></a>后续步骤

* [创建 Azure 机器学习数据集](how-to-create-register-datasets.md)
* [使用 Azure 机器学习 SDK 创建和运行机器学习管道](how-to-create-your-first-pipeline.md)
