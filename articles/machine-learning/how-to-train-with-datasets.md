---
title: 使用 azureml-datasets 进行训练
titleSuffix: Azure Machine Learning
description: 了解如何在训练中使用数据集
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/20/2020
ms.openlocfilehash: cd72ce9fed7f821807b8604f68068c64a38293e3
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996660"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>使用 Azure 机器学习中的数据集进行训练
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何在训练试验中处理[Azure 机器学习数据集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)。  您可以使用本地或远程计算目标中的数据集，而无需担心连接字符串或数据路径。

Azure 机器学习数据集提供与 Azure 机器学习定型产品（如[ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py)、[估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)、 [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py)和[Azure 机器学习管道](how-to-create-your-first-pipeline.md)）的无缝集成。

## <a name="prerequisites"></a>先决条件

若要创建数据集并使用数据集进行训练，需要具有以下各项：

* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* [Azure 机器学习工作区](how-to-manage-workspace.md)。

* [已安装适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包含 azureml-datasets 包。

> [!Note]
> 某些数据集类依赖于[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)包。 对于 Linux 用户，仅以下分发版支持这些类： Red Hat Enterprise Linux、Ubuntu、Fedora 和 CentOS。

## <a name="access-and-explore-input-datasets"></a>访问和浏览输入数据集

你可以从工作区上实验的训练脚本访问现有的 TabularDataset，并将该数据集加载到 pandas 数据帧，以便进一步浏览本地环境。

下面的代码使用[`get_context()`]() [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py)类中的方法来访问定型脚本中的现有输入`titanic`TabularDataset。 然后，使用[`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--)方法将该数据集加载到 pandas 数据帧中，以便在定型之前进行进一步的数据浏览和准备。

> [!Note]
> 如果原始数据源包含 NaN、空字符串或空值，则使用 to_pandas_dataframe （）时，这些值将替换为*Null*值。 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

如果需要将已准备的数据从中的 pandas 数据帧加载到一个新数据集，请将数据写入本地文件（例如 parquet），并从该文件创建新的数据集。 你还可以从数据存储中的本地文件或路径创建数据集。 详细了解[如何创建数据集](how-to-create-register-datasets.md)。

## <a name="use-datasets-directly-in-training-scripts"></a>直接在训练脚本中使用数据集

如果你有尚未注册为数据集的结构化数据，请创建一个 TabularDataset 并将其直接用于本地或远程试验的定型脚本中。

在此示例中，将创建一个未注册的[TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) ，并将其用作`estimator`对象的直接输入以进行定型。 如果你想要将此 TabularDataset 与工作区中的其他试验一起使用，请参阅[如何将数据集注册到你的工作区](how-to-create-register-datasets.md#register-datasets)。

### <a name="create-a-tabulardataset"></a>创建 TabularDataset

下面的代码从 Web URL 创建未注册的 TabularDataset。  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset 对象提供将 TabularDataset 中的数据加载到 pandas 或 spark 数据帧中的功能，以便你可以使用熟悉的数据准备和培训库，而无需离开你的笔记本。 若要利用此功能，请参阅[访问和浏览输入数据集](#access-and-explore-input-datasets)。

### <a name="configure-the-estimator"></a>配置估算器

[估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)对象用于提交试验运行。 Azure 机器学习具有适用于常见机器学习框架的预配置估算器和一个通用估算器。

此代码创建一个通用估算器对象 `est`，该对象指定了

* 脚本的脚本目录。 此目录中的所有文件都上传到群集节点以便执行。
* 训练脚本 *train_titanic.py*。
* 用于训练的输入数据集 `titanic_ds`。 `as_named_input()`是必需的，以便在训练脚本中可以通过指定的名称`titanic`引用输入数据集。 
* 试验的计算目标。
* 试验的环境定义。

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```

## <a name="mount-files-to-remote-compute-targets"></a>将文件装载到远程计算目标

如果你有非结构化数据，请创建一个[FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py) ，并装载或下载你的数据文件，使其可供远程计算目标用于培训。 了解何时使用[装载与下载](#mount-vs-download)进行远程训练试验。 

下面的示例创建 FileDataset，并将数据集作为参数传递给计算目标，方法是将其作为估计器中的参数进行定型。 

### <a name="create-a-filedataset"></a>创建 FileDataset

以下示例从 Web URL 创建未注册的 FileDataset。 从其他来源详细了解[如何创建数据集](https://aka.ms/azureml/howto/createdatasets)。

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

### <a name="configure-the-estimator"></a>配置估算器

建议在安装时将该数据集作为参数传递。 除了通过估计器中的`inputs`参数传递数据集之外，还可以通过`script_params`参数传递数据集，并通过参数获取定型脚本中的数据路径（装入点）。 这样，便可以在任何云平台上使用相同的培训脚本进行本地调试和远程培训。

[SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) 估算器对象用于为 scikit-learn 试验提交运行。 提交运行后，`mnist` 数据集引用的数据文件将装载到计算目标。 详细了解使用 [SKlearn 估算器](how-to-train-scikit-learn.md)进行训练。

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
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

# retrieve the 2 arguments configured through script_params in estimator
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


## <a name="mount-vs-download"></a>装载 vs 下载

从 Azure Blob 存储、Azure 文件、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL 数据库和 Azure Database for PostgreSQL 创建的数据集支持装载或下载任意格式的文件。 

装载数据集时，请将数据集引用的文件附加到目录（装入点），并使其在计算目标上可用。 基于 Linux 的计算支持装载，这些计算包括 Azure 机器学习计算、虚拟机和 HDInsight。 

下载数据集时，数据集引用的所有文件都将下载到计算目标。 所有计算类型都支持下载。 

如果你的脚本处理数据集引用的所有文件，并且你的计算磁盘可以适合你的完整数据集，则建议下载，以避免从存储服务传输数据的系统开销。 如果数据大小超出计算磁盘大小，则无法下载。 对于此方案，我们建议装载，因为在处理时只会加载脚本使用的数据文件。

以下代码装载`dataset`到 temp 目录`mounted_path`

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

## <a name="notebook-examples"></a>Notebook 示例

[数据集笔记本](https://aka.ms/dataset-tutorial)演示了本文中的概念并在其基础上进行了扩展。

## <a name="next-steps"></a>后续步骤

* 使用 TabularDataset [自动训练机器学习模型](how-to-auto-train-remote.md)。

* 使用 FileDataset [训练图像分类模型](https://aka.ms/filedataset-samplenotebook)。

* [使用管道对数据集进行定型](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)。
