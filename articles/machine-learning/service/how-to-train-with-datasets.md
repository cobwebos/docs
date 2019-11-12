---
title: 为 azureml 数据集定型
titleSuffix: Azure Machine Learning
description: 了解如何在定型中使用数据集
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/25/2019
ms.openlocfilehash: d68a8d35d07da4c61abffad6658419ada62f2f39
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931000"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Azure 机器学习中的数据集定型
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍两种使用远程试验训练运行中的[Azure 机器学习数据集](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)的方式，无需担心连接字符串或数据路径。

- 选项1：如果有结构化数据，请创建 TabularDataset，并直接在训练脚本中使用它。

- 选项2：如果使用非结构化数据，请创建 FileDataset，并将文件装载或下载到远程计算进行培训。

Azure 机器学习数据集提供与[ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py)、[估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py)和[HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py)等 Azure 机器学习训练产品的无缝集成。

## <a name="prerequisites"></a>先决条件

若要创建和训练数据集，需要：

* Azure 订阅。 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* [Azure 机器学习工作区](how-to-manage-workspace.md)。

* [安装的适用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包含 azureml 数据集包。

> [!Note]
> 某些数据集类依赖于[dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)包。 对于 Linux 用户，仅以下分发版支持这些类： Red Hat Enterprise Linux、Ubuntu、Fedora 和 CentOS。

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>选项1：直接在定型脚本中使用数据集

在此示例中，将创建一个[TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) ，并将其用作用于定型的 `estimator` 对象的直接输入。 

### <a name="create-a-tabulardataset"></a>创建 TabularDataset

下面的代码从 web url 创建未注册的 TabularDataset。 还可以从数据存储中的本地文件或路径创建数据集。 详细了解[如何创建数据集](https://aka.ms/azureml/howto/createdatasets)。

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>访问定型脚本中的输入数据集

TabularDataset 对象提供将数据加载到 pandas 或 spark 数据帧中的功能，以便你可以使用熟悉的数据准备和定型库。 若要利用此功能，可以在定型配置中传递 TabularDataset 作为输入，然后在脚本中检索它。

为此，请通过训练脚本中的[`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py)对象访问输入数据集，并使用[`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe--)方法。 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic_ds']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>配置估计器

[估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)对象用于提交试验运行。 Azure 机器学习为常见机器学习框架以及一般估计器预配置了估算。

此代码创建一个 `est`的泛型估计器对象，该对象指定

* 脚本的脚本目录。 此目录中的所有文件都上传到群集节点以便执行。
* 训练脚本， *train_titanic py*。
* 用于定型的输入数据集，`titanic`。
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

## <a name="option-2--mount-files-to-a-remote-compute-target"></a>选项2：将文件装载到远程计算目标

如果要将数据文件提供给计算目标以便进行培训，请使用[FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py)来装载或下载它所引用的文件。

装载文件系统时，会将该文件系统附加到目录（装入点），并使其在计算目标上可用。 装载是瞬时的，因为文件仅在处理时加载。 对于基于 Linux 的计算（包括 Azure 机器学习计算、虚拟机和 HDInsight），支持装入。 对于基于非 Linux 的计算，仅支持下载。  

>[!WARNING]
> 如果数据大小超出了计算目标的存储限制，则下载将失败。

### <a name="create-a-filedataset"></a>创建 FileDataset

以下示例从 web url 创建未注册的 FileDataset。 了解有关如何从其他源[创建数据集](https://aka.ms/azureml/howto/createdatasets)的详细信息。

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

### <a name="configure-the-estimator"></a>配置估计器

你还可以通过 `script_params` 将数据集传递给估计器，而不是 `inputs` 通过参数传递数据集，并通过参数获取定型脚本中的数据路径（装入点）。 这样，你就可以访问数据并使用现有训练脚本。

[Spark-sklearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)估计器对象用于提交 scikit-learn 学习试验的运行。 详细了解[spark-sklearn 估计器](how-to-train-scikit-learn.md)的培训。

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

### <a name="retrieve-the-data-in-your-training-script"></a>检索训练脚本中的数据

提交运行后，`mnist` 数据集引用的数据文件将装载到计算目标。 下面的代码演示如何在脚本中检索数据。

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

## <a name="notebook-examples"></a>Notebook 示例

[数据集笔记本](https://aka.ms/dataset-tutorial)演示并扩展本文中的概念。 

## <a name="next-steps"></a>后续步骤

* 用 TabularDatasets[自动训练机器学习模型](how-to-auto-train-remote.md)。

* 用 FileDatasets[训练图像分类模型](https://aka.ms/filedataset-samplenotebook)。

