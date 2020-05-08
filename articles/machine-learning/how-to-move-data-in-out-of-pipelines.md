---
title: 在 ML 管道中移动数据
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习管道中数据的输入 & 输出。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.custom: contperfq4
ms.openlocfilehash: 233361fb238342cde3c692174e85fb57f69979b1
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858465"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>将数据移入 ML 管道和在 ML 管道之间移动数据的步骤 (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文提供用于在 Azure 机器学习管道中的步骤之间导入、转换和移动数据的代码。 有关数据在 Azure 机器学习中的工作原理的概述，请参阅[访问 Azure 存储服务中的数据](how-to-access-data.md)。 有关 Azure 机器学习管道的优点和结构，请参阅[什么是 Azure 机器学习管道？](concept-ml-pipelines.md)。

本文将向你介绍如何：

- 将`Dataset`对象用于预先存在的数据
- 在步骤中访问数据
- 将`Dataset`数据拆分为子集，如定型和验证子集
- 创建`PipelineData`对象以将数据传输到下一管道步骤
- 使用`PipelineData`对象作为管道步骤的输入
- 创建要`Dataset`保存的`PipelineData`新对象

## <a name="prerequisites"></a>先决条件

需要：

- Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

- [适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或 [Azure 机器学习工作室](https://ml.azure.com/)的访问权限。

- Azure 机器学习工作区。
  
  [创建 Azure 机器学习工作区](how-to-manage-workspace.md)，或者通过 Python SDK 使用现有工作区。 导入`Workspace`和`Datastore`类，并使用函数`config.json` `from_config()`从文件中加载订阅信息。 默认情况下，此函数查找当前目录中的 JSON 文件，但你也可以使用`from_config(path="your/file/path")`指定指向该文件的 path 参数。

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- 某些预先存在的数据。 本文简要介绍了如何使用[Azure blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)。

- 可选：现有机器学习管道，如[使用 AZURE 机器学习 SDK 创建和运行机器学习管道](how-to-create-your-first-pipeline.md)中所述的管道。

## <a name="use-dataset-objects-for-pre-existing-data"></a>将`Dataset`对象用于预先存在的数据 

将数据引入管道的首选方法是使用[Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)对象。 `Dataset`对象表示在整个工作区中可用的永久数据。

有多种方法可以创建和注册`Dataset`对象。 表格数据集用于一个或多个文件中可用的分隔数据。 文件数据集用于二进制数据（如图像）或要分析的数据。 创建`Dataset`对象的最简单编程方式是使用工作区存储或公共 url 中的现有 blob：

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

若要详细了解如何创建具有不同选项和不同源的数据集，请在 Azure 机器学习 UI 中注册这些数据集并进行查看，了解数据大小如何与计算容量交互，以及如何对其进行版本控制，请参阅[创建 Azure 机器学习数据集](how-to-create-register-datasets.md)。 

### <a name="pass-datasets-to-your-script"></a>将数据集传递给脚本

若要将数据集的路径传递给你的脚本`Dataset` ，请`as_named_input()`使用对象的方法。 你可以将生成`DatasetConsumptionConfig`的对象作为参数传递给你的脚本，或者，通过使用`inputs`管道脚本的参数，你可以使用`Run.get_context().input_datasets[]`检索数据集。

创建命名输入后，可以选择其访问模式： `as_mount()`或。 `as_download()` 如果你的脚本处理数据集中的所有文件，而计算资源上的磁盘足以容纳数据集，则下载访问模式是更好的选择。 下载访问模式将避免在运行时流式传输数据所产生的开销。 如果你的脚本访问数据集的子集或它对于计算而言太大，请使用装载访问模式。 有关详细信息，请阅读[装载与下载](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

若要将数据集传递到管道步骤：

1. 使用`TabularDataset.as_named_inputs()` or `FileDataset.as_named_input()` （不是 '）来创建`DatasetConsumptionConfig`对象
1. 使用`as_mount()`或`as_download()`设置访问模式
1. 使用`arguments`或`inputs`参数将数据集传递给管道步骤

以下代码片段显示了在`PythonScriptStep`构造函数中组合这些步骤的常见模式： 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

你还可以使用方法（如`random_split()`和`take_sample()` ）创建多个输入或减少传递到管道步骤的数据量：

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

管道步骤脚本的命名输入作为`Run`对象内的字典提供。 使用`Run` `Run.get_context()`检索活动对象，然后使用`input_datasets`检索命名输入的字典。 `DatasetConsumptionConfig`如果使用`arguments`参数而不是`inputs`参数传递对象，请使用`ArgParser`代码访问数据。 以下代码片段演示了这两种方法。

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

还可以访问直接注册`Dataset`的。 由于已注册的数据集是持久性的，并且在工作区之间共享，因此可以直接检索它们：

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>`PipelineData`用于中间数据

当`Dataset`对象表示持久性数据时， [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)对象用于从管道步骤输出的临时数据。 因为`PipelineData`对象的使用时间比单个管道步骤长，所以可以在管道定义脚本中定义。 创建`PipelineData`对象时，必须提供一个名称和数据将驻留的数据存储。 `PythonScriptStep`使用`arguments`和`PipelineData` `outputs` _参数将对象_传递给：

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

您可以选择使用提供即时`PipelineData`上载的访问模式创建对象。 在这种情况下，当你`PipelineData`创建时， `upload_mode`将`"upload"`设置为， `output_path_on_compute`并使用参数指定要将数据写入到的路径：

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>`PipelineData`用作定型步骤的输出

在管道的`PythonScriptStep`中，可以使用程序的参数检索可用输出路径。 如果此步骤是第一个，并将初始化输出数据，则必须在指定的路径创建目录。 然后，可以编写要包含在中的`PipelineData`任何文件。

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

`PipelineData`如果你创建`is_directory`了参数设置为`True`的，则只需执行`os.makedirs()`调用即可，然后就可以随意写入路径所需的任何文件。 有关更多详细信息，请参阅[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)参考文档。

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>作为`PipelineData`非初始步骤的输入读取

在初始管道步骤将一些数据写入`PipelineData`路径并且该步骤成为初始步骤的输出后，可将其用作后续步骤的输入：

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

`PipelineData`输入的值是上一个输出的路径。 如前面所示，第一步写入单个文件，使用它可能如下所示： 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>将`PipelineData`对象转换`Dataset`为 s

如果你想要使你`PipelineData`的可用时间超过运行的持续时间，请使用其`as_dataset()`函数将其转换为。 `Dataset` 然后`Dataset`，你可以注册，使其成为你工作区中的第一类公民。 由于在`PipelineData`每次管道运行时，对象将具有不同的路径，因此强烈建议你在注册`create_new_version`从`True` `PipelineData`对象创建的`Dataset`时将设置为。

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>后续步骤

* [创建 Azure 机器学习数据集](how-to-create-register-datasets.md)
* [使用 Azure 机器学习 SDK 创建和运行机器学习管道](how-to-create-your-first-pipeline.md)
