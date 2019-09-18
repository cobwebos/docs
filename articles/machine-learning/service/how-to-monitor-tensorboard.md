---
title: 使用 TensorBoard 将试验可视化
titleSuffix: Azure Machine Learning
description: 启动 TensorBoard 以直观显示试验运行历史记录，并识别超参数优化和重新训练的潜在区域。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 06/28/2019
ms.openlocfilehash: b96b80a735c0caee8a3aabaf19b04fd0e153ba6b
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034336"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>用 TensorBoard 和 Azure 机器学习可视化试验运行和指标

本文介绍如何在 TensorBoard 中使用[ `tensorboard`包](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py)在主 Azure 机器学习 SDK 中查看试验运行和指标。 检查试验运行后，可以更好地优化和重新训练机器学习模型。

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview)是一套 web 应用程序，用于检查和了解实验结构和性能。

如何在 Azure 机器学习试验中启动 TensorBoard 取决于试验类型：
+ 如果实验以本机方式输出 TensorBoard 可使用的日志文件，例如 PyTorch、Chainer 和 TensorFlow 试验，则可以直接从实验的运行历史记录[启动 TensorBoard](#direct) 。 

+ 对于不以本机方式输出 TensorBoard 可耗用文件（如 scikit-learn）的试验或 Azure 机器学习试验，请[使用`export_to_tensorboard()`方法](#export)将运行历史记录作为 TensorBoard 日志导出，并从该处启动 TensorBoard。 

## <a name="prerequisites"></a>先决条件

* 若要启动 TensorBoard 并查看试验运行历史记录，你的实验需要预先启用日志记录，以跟踪其指标和性能。  

* 此操作方法中的代码可在以下任一环境中运行： 

    * Azure 机器学习笔记本 VM-无需下载或安装

        * 在开始本教程之前完成[教程：设置环境和工作](tutorial-1st-experiment-sdk-setup.md)区，创建随 SDK 和示例存储库预先加载的专用笔记本服务器。

        * 在笔记本服务器上的 "示例" 文件夹中，通过导航到以下目录来查找两个已完成和扩展的笔记本：操作**方法 > 培训-深入了解**。
        * export-run-history-to-run-history.ipynb
        * tensorboard. ipynb

    * 自己的 Juptyer 笔记本服务器
          * 用额外的`tensorboard` [安装 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
          * [创建 Azure 机器学习工作区](how-to-manage-workspace.md)。  
          * [创建工作区配置文件](how-to-configure-environment.md#workspace)。
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>选项 1：直接在 TensorBoard 中查看运行历史记录

此选项适用于本机输出 TensorBoard 所使用的日志文件的试验，如 PyTorch、Chainer 和 TensorFlow 试验。 如果这不是你的实验，请[ `export_to_tensorboard()`改用方法](#export)。

下面的示例代码使用远程计算目标中 TensorFlow 的存储库中的[MNIST 演示试验](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py)Azure 机器学习计算。 接下来，我们使用 SDK 的自定义[TensorFlow 估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)来训练模型，然后针对此 TensorFlow 试验启动 TensorBoard，即本机输出 TensorBoard 事件文件的实验。

### <a name="set-experiment-name-and-create-project-folder"></a>设置试验名称并创建项目文件夹

此处为试验命名，并创建其文件夹。 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>下载 TensorFlow 演示试验代码

TensorFlow 的存储库有一个 MNIST 演示，其中包含广泛的 TensorBoard 检测。 我们不需要更改此演示的任何代码即可使用 Azure 机器学习。 在下面的代码中，我们将下载 MNIST 代码，并将其保存在新创建的试验文件夹中。

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
在 MNIST 代码文件 mnist_with_summaries py 中，可以看到有一些行调用`tf.summary.scalar()`了`tf.summary.FileWriter()` 、 `tf.summary.histogram()`等。这些方法将试验的方法分组、记录和标记为运行历史记录。 在`tf.summary.FileWriter()`从记录的试验指标中序列化数据时，这一点尤其重要，这允许 TensorBoard 生成其视觉对象。

 ### <a name="configure-experiment"></a>配置试验

在下面，我们将配置试验，并设置日志和数据的目录。 这些日志将上传到 TensorBoard 访问的项目服务。

>[!Note]
> 对于此 TensorFlow 示例，你将需要在本地计算机上安装 TensorFlow。 此外，TensorBoard 模块（即包含在 TensorFlow 中的模块）必须可以访问此笔记本的内核，因为本地计算机是运行 TensorBoard 的。

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>为试验创建群集
我们将为此试验创建 AmlCompute 群集，但可以在任何环境中创建你的试验，并且你仍可以根据试验运行历史记录启动 TensorBoard。 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>提交运行 TensorFlow 估计器

TensorFlow 估计器提供了一种简单的方法来启动计算目标上的 TensorFlow 培训作业。 它通过泛型[`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)类实现，可用于支持任何框架。 有关使用泛型估计器定型模型的详细信息，请参阅[使用估计器 Azure 机器学习训练模型](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>启动 TensorBoard

可以在运行时或在完成后启动 TensorBoard。 在下面的示例中，我们将创建一个 TensorBoard `tb`对象实例，该实例使用`run`在中加载的试验运行历史记录， `start()`然后使用方法启动 TensorBoard。 
  
[TensorBoard 构造函数](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py)使用一个运行的数组，因此请确保将其作为单元素数组传入。

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>选项 2：在 TensorBoard 中将历史记录作为日志导出

下面的代码设置一个示例试验，使用 Azure 机器学习运行历史记录 Api 开始日志记录过程，并将试验运行历史记录导出到 TensorBoard 可供可视化的日志。 

### <a name="set-up-experiment"></a>设置试验

下面的代码将设置一个新的实验，并为运行`root_run`目录命名。 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

在这里，我们将加载糖尿病数据集，这是 scikit-learn 提供的内置小数据集，并将其拆分为测试集和定型集。

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>运行试验和日志度量值

在此代码中，我们将在运行历史记录中训练线性回归模型和日志关键`alpha`指标、alpha 系数和`mse`平均方形错误。

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>导出运行到 TensorBoard

通过 SDK 的[export_to_tensorboard （）](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py)方法，我们可以将 Azure 机器学习实验的运行历史记录导出到 tensorboard 日志中，以便我们可以通过 tensorboard 查看这些日志。  

在下面的代码中，我们将在`logdir`当前工作目录中创建文件夹。 在此文件夹中，我们将从`root_run`此处导出试验运行历史记录和日志，然后将其标记为 "已完成"。 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 还可以通过指定运行的名称，将特定的运行导出到 TensorBoard`export_to_tensorboard(run_name, logdir)`

开始和停止 TensorBoard 此试验的运行历史记录导出后，可以用[start （）](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-)方法启动 TensorBoard。 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

完成后，请务必调用 TensorBoard 对象的[stop （）](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--)方法。 否则，在关闭笔记本内核之前，TensorBoard 将继续运行。 

```python
tb.stop()
```

## <a name="next-steps"></a>后续步骤

在本操作指南中，创建了两个试验，并了解了如何针对其运行历史记录启动 TensorBoard，以确定可能的优化和重新训练的区域。 

* 如果你对模型感到满意，请转到我们[如何部署模型](how-to-deploy-and-where.md)一文。 
* 了解有关[超参数优化](how-to-tune-hyperparameters.md)的详细信息。 
