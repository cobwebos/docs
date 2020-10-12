---
title: 使用 TensorBoard 将试验可视化
titleSuffix: Azure Machine Learning
description: 启动 TensorBoard 以可视化试验运行历史记录，并识别可在哪些方面进行超参数优化和重新训练。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: minxia
ms.author: minxia
ms.date: 02/27/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 2ed8910db8b903dab3b81d9db6c9b5798d2b6b69
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542047"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>使用 TensorBoard 和 Azure 机器学习可视化试验运行与指标


本文介绍如何使用主要 Azure 机器学习 SDK 中的 [`tensorboard` 包](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py&preserve-view=true)，在 TensorBoard 中查看试验运行和指标。 检查试验运行后，可以更好地优化和重新训练机器学习模型。

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) 是一套 Web 应用程序，用于检查和了解试验的结构与性能。

如何在 Azure 机器学习试验中启动 TensorBoard 取决于试验类型：
+ 如果试验（例如 PyTorch、Chainer 和 TensorFlow 试验）原生可以输出可供 TensorBoard 使用的日志文件，则你可以从试验的运行历史记录[直接启动 TensorBoard](#launch-tensorboard)。 

+ 对于原生无法输出 TensorBoard 可用文件的试验（例如 Scikit-learn 或 Azure 机器学习试验），请使用 [`export_to_tensorboard()` 方法](#export)将运行历史记录导出为 TensorBoard 日志，并从日志启动 TensorBoard。 

> [!TIP]
> 本文档中的信息主要面向希望监视模型训练过程的数据科学家和开发人员。 如果你是一名管理员，希望监视 Azure 机器学习的资源使用情况和事件，例如配额、已完成的训练运行或已完成的模型部署，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。

## <a name="prerequisites"></a>先决条件

* 若要启动 TensorBoard 并查看试验运行历史记录，你的实验需要预先启用日志记录，以跟踪其指标和性能。  
* 本文档中的代码可在以下任一环境中运行： 
    * Azure 机器学习计算实例 - 无需下载或安装
        * 在开始本教程之前完成[教程：设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)创建预先加载了 SDK 和示例存储库的专用笔记本服务器。
        * 在笔记本服务器上的 samples 文件夹中，导航到以下目录找到两个已完成的和已扩展的笔记本：
            * **《如何使用-azureml > 跟踪和监视-试验 > tensorboard > 导出-tensorboard > 导出-运行-历史记录-tensorboard. ipynb**
            * **使用情况-azureml > 跟踪和监视-试验 > tensorboard > tensorboard > tensorboard ipynb**
    * 你自己的 Jupyter 笔记本服务器
       * 使用 `tensorboard` 附加项[安装 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
        * [创建 Azure 机器学习工作区](how-to-manage-workspace.md)。  
        * [创建工作区配置文件](how-to-configure-environment.md#workspace)。

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>选项 1：直接在 TensorBoard 中查看运行历史记录

此选项适用于原生可以输出可供 TensorBoard 使用的日志文件的试验，例如 PyTorch、Chainer 和 TensorFlow 试验。 如果你的试验无此功能，请改用 [`export_to_tensorboard()` 方法](#export)。

以下示例代码在远程计算目标“Azure 机器学习计算”上使用 TensorFlow 存储库中的 [MNIST 演示试验](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py)。 接下来，我们将配置并启动一个运行以训练 TensorFlow 模型，然后针对此 TensorFlow 试验启动 TensorBoard。

### <a name="set-experiment-name-and-create-project-folder"></a>设置试验名称并创建项目文件夹

在此处为试验命名，并创建其文件夹。 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>下载 TensorFlow 演示试验代码

TensorFlow 的存储库包含 MNIST 演示以及丰富的 TensorBoard 检测工具。 我们不会也不需要更改此演示代码的任何部分，即可使其与 Azure 机器学习配合运行。 在以下代码中，我们将下载 MNIST 代码，并将其保存到新建的试验文件夹中。

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
在整个 MNIST 代码文件 mnist_with_summaries.py 中，请注意一些调用 `tf.summary.scalar()`、`tf.summary.histogram()`、`tf.summary.FileWriter()` 等的行。这些方法将试验的关键指标分组、记录和标记到运行历史记录中。 `tf.summary.FileWriter()` 特别重要，因为它序列化所记录的试验指标中的数据，使 TensorBoard 能够基于这些数据生成可视化效果。

 ### <a name="configure-experiment"></a>配置试验

下面，我们将配置试验，并设置日志和数据的目录。 这些日志将上传到运行历史记录，TensorBoard 将在以后访问。

> [!Note]
> 对于此 TensorFlow 示例，需要在本地计算机上安装 TensorFlow。 此外，TensorBoard 模块（即 TensorFlow 包含的模块）必须可供此笔记本的内核访问，因为 TensorBoard 在本地计算机上运行。

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

# Writing logs to ./logs results in their being uploaded to the run history,
# and thus, made accessible to our TensorBoard instance.
args = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>为试验创建群集
我们将为此试验创建 AmlCompute 群集，但是，可以在任何环境中创建试验，并且仍可以针对试验运行历史记录启动 TensorBoard。 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpu-cluster"

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="configure-and-submit-training-run"></a>配置并提交定型运行

通过创建 ScriptRunConfig 对象来配置训练作业。

```Python
from azureml.core import ScriptRunConfig
from azureml.core import Environment

# Here we will use the TensorFlow 2.2 curated environment
tf_env = Environment.get(ws, 'AzureML-TensorFlow-2.2-GPU')

src = ScriptRunConfig(source_directory=exp_dir,
                      script='mnist_with_summaries.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
run = exp.submit(src)
```

### <a name="launch-tensorboard"></a>启动 TensorBoard

可以在运行期间或者在运行完成后启动 TensorBoard。 下面，我们将创建一个 TensorBoard 对象实例 `tb`，该实例采用 `run` 中加载的试验运行历史记录，然后使用 `start()` 方法启动 TensorBoard。 
  
[TensorBoard 构造函数](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py&preserve-view=true)采用运行数组，因此请确保将其作为单元素数组传入。

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

> [!Note]
> 虽然此示例使用 TensorFlow，但 TensorBoard 可以轻松地与 PyTorch 或 Chainer 一起使用。 TensorFlow 必须在运行 TensorBoard 的计算机上可用，但在执行 PyTorch 或 Chainer 计算的计算机上不是必需的。 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>选项2：以日志形式导出历史记录，以在 TensorBoard 中查看

以下代码设置一个示例试验，使用 Azure 机器学习运行历史记录 API 开始日志记录过程，然后将试验运行历史记录导出到 TensorBoard 可用的日志中，以进行可视化。 

### <a name="set-up-experiment"></a>设置试验

以下代码设置新的试验，并将运行目录命名为 `root_run`。 

```python
from azureml.core import Workspace, Experiment
import azureml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

此处我们将加载糖尿病数据集（scikit-learn 随附的内置小型数据集），并将其拆分为测试集和训练集。

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

### <a name="run-experiment-and-log-metrics"></a>运行试验并记录指标

对于此代码，我们将训练一个线性回归模型，并在运行历史记录中记录关键指标、alpha 系数 `alpha` 和均方误差 `mse`。

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run:
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>将运行导出到 TensorBoard

使用 SDK 的 [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py&preserve-view=true) 方法，可将 Azure 机器学习试验的运行历史记录导出到 TensorBoard 日志中，以便可以通过 TensorBoard 查看。  

在以下代码中，我们将在当前工作目录中创建 `logdir` 文件夹。 我们将在此文件夹中从 `root_run` 导出试验运行历史记录和日志，然后将该运行标记为已完成。 

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

> [!Note]
> 还可以通过指定运行的名称 (`export_to_tensorboard(run_name, logdir)`)，将特定的运行导出到 TensorBoard

### <a name="start-and-stop-tensorboard"></a>启动和停止 TensorBoard
导出此试验的运行历史记录后，可以使用 [start()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py&preserve-view=true#&preserve-view=truestart-start-browser-false-) 方法启动 TensorBoard。 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

完成后，请务必调用 TensorBoard 对象的 [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py&preserve-view=true#&preserve-view=truestop--) 方法。 否则，在关闭笔记本内核之前，TensorBoard 将继续运行。 

```python
tb.stop()
```

## <a name="next-steps"></a>后续步骤

在本操作指南中，你已创建两个试验，并已了解如何针对这些试验的运行历史记录启动 TensorBoard，以识别在哪些方面可以进行优化和重新训练。 

* 如果你对自己的模型感到满意，请继续阅读[如何部署模型](how-to-deploy-and-where.md)一文。 
* 详细了解[超参数优化](how-to-tune-hyperparameters.md)。 
