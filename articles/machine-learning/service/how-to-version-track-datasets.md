---
title: 数据集版本控制
titleSuffix: Azure Machine Learning service
description: 了解如何以最佳方式对数据集进行版本控制，以及如何使用机器学习管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.custom: ''
ms.openlocfilehash: 6940b6ecc231befba908271920e31d4821338baa
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928944"
---
# <a name="version-and-track-datasets-in-experiments"></a>试验中的版本和跟踪数据集
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，你将了解如何为可再现性版本和跟踪 Azure 机器学习数据集。 数据集版本控制是一种将数据的状态做成书签的方式，以便您可以应用特定版本的数据集以供将来试验。

典型版本控制方案：

* 当新数据可用于重新训练时
* 应用不同的数据准备或功能设计方法时

## <a name="prerequisites"></a>必备组件

对于本教程的内容，你需要：

- [已安装适用于 Python AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。 此 SDK 包含[azureml 数据集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py)包。
    
- [Azure 机器学习工作区](concept-workspace.md)。 通过运行以下代码来检索现有的[工作区，或创建一个新的工作区](how-to-manage-workspace.md)。

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- [Azure 机器学习数据集](how-to-create-register-datasets.md)。

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>注册和检索数据集版本

通过注册数据集，可以在试验和同事之间对其进行版本、重复使用和共享。 您可以使用相同的名称注册多个数据集，并按名称和版本号检索特定版本。

### <a name="register-a-dataset-version"></a>注册数据集版本

下面的代码通过将 `create_new_version` 参数设置为 `True`，来注册 `titanic_ds` 数据集的新版本。 如果没有向工作区注册现有 `titanic_ds` 数据集，代码会创建一个名为 `titanic_ds` 的新数据集，并将其版本设置为1。

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>按名称检索数据集

默认情况下，`Dataset` 类上的[get_by_name （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--)方法返回注册到工作区中的最新版本的数据集。 

下面的代码获取 `titanic_ds` dataset 的版本1。

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>版本控制最佳实践

创建数据集版本时，*不会*使用工作区创建额外的数据副本。 由于数据集是对存储服务中的数据的引用，因此，你有一个真实来源，由存储服务管理。

>[!IMPORTANT]
> 如果数据集引用的数据被覆盖或删除，则调用特定版本的数据集*不*会恢复更改。

从数据集加载数据时，始终会加载数据集引用的当前数据内容。 如果要确保每个数据集版本都是可重复的，我们建议您不要修改数据集版本引用的数据内容。 当新数据进入时，将新数据文件保存到单独的数据文件夹中，然后创建新的数据集版本以包含该新文件夹中的数据。

以下图像和示例代码显示了如何构建数据文件夹的结构，并创建引用这些文件夹的数据集版本：

![文件夹结构](media/how-to-version-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>版本管道输出数据集

您可以使用 dataset 作为每个机器学习管道步骤的输入和输出。 重新运行管道时，每个管道步骤的输出将注册为新的数据集版本。

由于机器学习管道会在每次管道重新运行时将每个步骤的输出填充到新文件夹中，因此，版本控制的输出数据集是可重复的。

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>在试验中跟踪数据集

对于每个机器学习试验，可以通过试验 `Run` 对象轻松跟踪用作输入的数据集。

下面的代码使用[`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--)方法跟踪试验运行时所使用的输入数据集：

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

还可以通过使用[Azure 机器学习 Studio](https://ml.azure.com/)查找试验 `input_datasets`。 

下图显示了在何处查找 Azure 机器学习 Studio 中实验的输入数据集。 在此示例中，请参阅**试验**窗格，并打开试验的特定运行的 "**属性**" 选项卡，`keras-mnist`。

![输入数据集](media/how-to-version-datasets/input-datasets.png)

使用以下代码向数据集注册模型：

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

注册后，可以使用 Python 或[Azure 机器学习 Studio](https://ml.azure.com/)来查看向数据集注册的模型列表。 以下视图来自 "**资产**" 下的 "**数据集**" 窗格。 选择数据集，然后选择 "**模型**" 选项卡，获取向数据集注册的模型的列表。 

![输入数据集模型](media/how-to-version-datasets/dataset-models.png)

## <a name="next-steps"></a>后续步骤

* [数据集定型](how-to-train-with-datasets.md)
* [更多示例数据集笔记本](https://aka.ms/dataset-tutorial)
