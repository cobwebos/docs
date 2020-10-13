---
title: 使用 azureml-datasets 进行训练
titleSuffix: Azure Machine Learning
description: 了解如何在训练中使用数据集
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: aa1ba4aa07ee4cdc097bd4ed3e6e4d7563360a5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331788"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>使用 Azure 机器学习中的数据集进行训练


本文介绍如何在训练实验中使用 [Azure 机器学习数据集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true)。  可以在本地或远程计算目标中使用数据集，而不必考虑连接字符串或数据路径。

Azure 机器学习数据集提供与 [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true)、 [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py&preserve-view=true) 和 [Azure 机器学习管道](how-to-create-your-first-pipeline.md)等 Azure 机器学习定型功能的无缝集成。

## <a name="prerequisites"></a>先决条件

若要创建数据集并使用数据集进行训练，需要具有以下各项：

* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* [Azure 机器学习工作区](how-to-manage-workspace.md)。

* [安装的 Python AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) ( # B0 = 1.13.0) ，其中包含 azureml 数据集包。

> [!Note]
> 某些数据集类依赖于 [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py&preserve-view=true) 包。 对于 Linux 用户，只有以下分发版支持这些类：Red Hat Enterprise Linux、Ubuntu、Fedora 和 CentOS。

## <a name="use-datasets-directly-in-training-scripts"></a>直接在训练脚本中使用数据集

如果有尚未注册为数据集的结构化数据，请创建一个 TabularDataset，并在训练脚本中直接使用它进行本地或远程实验。

在此示例中，将创建一个未注册的 [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) ，并将其指定为 ScriptRunConfig 对象中用于定型的脚本参数。 如果要在工作区中的其他实验中重用此 TabularDataset，请参见[如何将数据集注册到工作区](how-to-create-register-datasets.md#register-datasets)。

### <a name="create-a-tabulardataset"></a>创建 TabularDataset

下面的代码从 Web URL 创建未注册的 TabularDataset。  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset 对象提供将 TabularDataset 中的数据加载到 pandas 或 Spark 数据帧中的功能，以便你可以使用熟悉的数据准备和训练库，且无需离开笔记本。

### <a name="access-dataset-in-training-script"></a>训练脚本中的访问数据集

以下代码配置一个脚本参数 `--input-data` ，该参数将在配置训练运行时指定 (请参阅下一部分) 。 如果以参数值的形式传递表格数据集，则 Azure ML 会将其解析为数据集的 ID，然后，可以使用该数据集访问定型脚本中的数据集， (而无需在脚本) 中对数据集的名称或 ID 进行硬编码。 然后，它使用 [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 方法将该数据集加载到 pandas 数据帧中，以便在定型之前进行进一步的数据浏览和准备。

> [!Note]
> 如果原始数据源包含 NaN、空字符串或空白值，则在使用时， `to_pandas_dataframe()` 这些值将替换为 *Null* 值。

如果需要将已准备的数据从内存中的 pandas 数据帧加载到新数据集，请将数据写入本地文件（例如 parquet），并从该文件创建新的数据集。 你还可以从数据存储中的本地文件或路径创建数据集。 详细了解[如何创建数据集](how-to-create-register-datasets.md)。

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>配置训练运行
[ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py&preserve-view=true)对象用于配置和提交定型运行。

此代码创建一个 ScriptRunConfig 对象， `src` 该对象指定

* 脚本的脚本目录。 此目录中的所有文件都上传到群集节点以便执行。
* 训练脚本 *train_titanic.py*。
* 用于定型的输入数据集， `titanic_ds` 作为脚本参数。 将此数据传递到脚本时，Azure ML 会将其解析为相应的数据集 ID。
* 运行的计算目标。
* 运行的环境。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-dataset', titanic_ds],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>将文件装载到远程计算目标

如果你有非结构化数据，请创建一个 [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py&preserve-view=true)，然后装载或下载数据文件，使它们可在训练中用于远程计算目标。 了解何时使用[装载与下载](#mount-vs-download)进行远程训练实验。 

下面的示例创建 FileDataset，并将数据集作为参数传递到定型脚本，从而将该数据集装载到计算目标。 

> [!Note]
> 如果使用的是自定义 Docker 基础映像，则需要通过 `apt-get install -y fuse` 安装 fuse，作为让数据集装载正常工作所需的依赖项。 了解如何[生成自定义生成映像](how-to-deploy-custom-docker-image.md#build-a-custom-base-image)。

### <a name="create-a-filedataset"></a>创建 FileDataset

以下示例从 Web URL 创建未注册的 FileDataset。 从其他来源详细了解[如何创建数据集](how-to-create-register-datasets.md)。

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-training-run"></a>配置训练运行
建议在通过构造函数的参数装载时，将 dataset 作为参数传递 `arguments` `ScriptRunConfig` 。 这样，你就可以通过参数在训练脚本中 (装入点) 的数据路径。 这样，可以在任何云平台上使用相同的训练脚本进行本地调试和远程训练。

下面的示例创建一个通过在 FileDataset 中传递的 ScriptRunConfig `arguments` 。 提交运行后，数据集引用的数据文件 `mnist_ds` 将装载到计算目标。

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_mnist.py',
                      # the dataset will be mounted on the remote compute and the mounted path passed as an argument to the script
                      arguments=['--data-folder', mnist_ds.as_mount(), '--regularization', 0.5],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>在训练脚本中检索数据

下面的代码展示了如何在脚本中检索数据。

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through `arguments` in the ScriptRunConfig
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="mount-vs-download"></a>装载和下载

对于从 Azure Blob 存储、Azure 文件存储、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL 数据库和 Azure Database for PostgreSQL 创建的数据集，可以装载或下载任何格式的文件。 

装载数据集时，请将数据集引用的文件附加到目录（装入点），并使其在计算目标上可用。 基于 Linux 的计算支持装载，这些计算包括 Azure 机器学习计算、虚拟机和 HDInsight。 

下载数据集时，数据集引用的所有文件都将下载到计算目标。 所有计算类型都支持下载。 

如果脚本处理数据集引用的所有文件，并且计算磁盘可以容纳整个数据集，则建议下载，以避免从存储服务流式传输数据的开销。 如果数据大小超出计算磁盘大小，则无法下载。 对于此方案，我们建议装载，因为在处理时只会加载脚本使用的数据文件。

以下代码将 `dataset` 装载到 `mounted_path` 的临时目录

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="directly-access-datasets-in-your-script"></a>直接访问脚本中的数据集

可以在本地以及在 Azure 机器学习计算等计算群集上远程访问已注册的数据集。 若要跨试验访问已注册的数据集，请使用以下代码按名称访问工作区和已注册的数据集。 默认情况下，`Dataset` 类中的 [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-by-name-workspace--name--version--latest--) 方法返回已注册到工作区的数据集的最新版本。

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="accessing-source-code-during-training"></a>在训练期间访问源代码

Azure Blob 存储具有比 Azure 文件共享更快的吞吐速度，并将扩展到大量并行启动的作业。 出于此原因，我们建议配置运行以使用 Blob 存储来传输源代码文件。

下面的代码示例在运行配置中指定用于源代码传输的 Blob 数据存储。

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Notebook 示例

[数据集笔记本](https://aka.ms/dataset-tutorial)演示了本文中的概念并在其基础上进行了扩展。

## <a name="next-steps"></a>后续步骤

* 使用 TabularDataset [自动训练机器学习模型](how-to-auto-train-remote.md)。

* 使用 FileDataset [训练图像分类模型](https://aka.ms/filedataset-samplenotebook)。

* [通过管道使用数据集进行训练](how-to-create-your-first-pipeline.md)。
