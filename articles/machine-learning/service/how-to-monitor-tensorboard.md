---
title: 可视化使用 TensorBoard 和 Azure 机器学习服务的试验
description: 启动 TensorBoard 可视化试验运行历史记录，并确定潜在的超参数优化和重新训练的领域。
services: machine-learning
author: maxluk
ms.author: maxluk
ms.reviewer: nibaccam
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: babd4cdf8b7ed9e04b4bd975d840688b27439c4f
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560855"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>可视化试验运行和使用 TensorBoard 和 Azure 机器学习的指标

在本文中，您将学习如何在使用 TensorBoard 中查看你的试验运行和指标[`tensorboard`包](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py)在主 Azure 机器学习服务 SDK。 一旦已检查试验运行，可以更好地优化和重新训练机器学习模型。

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview)是一套用于检查和了解您的实验结构和性能的 web 应用程序。

如何使用 Azure 机器学习试验启动 TensorBoard 取决于实验的类型：
+ 如果实验本机输出日志文件，可以由 TensorBoard，如 PyTorch、 Chainer 和 TensorFlow 试验，然后，你可以[直接启动 TensorBoard](#direct)从实验的运行历史记录。 

+ 对于不以本机方式输出 TensorBoard 可使用文件，例如如 scikit-learn 或 Azure 机器学习试验，试验使用[`export_to_tensorboard()`方法](#export)TensorBoard 日志导出运行历史记录和启动从那里 TensorBoard。 

## <a name="prerequisites"></a>必备组件

* 若要运行的历史记录在试验中启动 TensorBoard 和视图，在实验需要以前启用了日志记录以跟踪其度量值和性能。  

* 本操作指南中的代码可以在任一以下环境中运行： 

    * Azure 机器学习 Notebook VM-无下载或安装有必要

        * 完成[基于云的笔记本快速入门](quickstart-run-cloud-notebook.md#create-notebook)来使用 SDK 和示例存储库创建的专用的笔记本服务器预加载。

        * Notebook 服务器上的 samples 文件夹中找到两个已完成和展开笔记本通过导航到此目录：**说明-到-使用-azureml > 培训使用深度学习**。
        * export-run-history-to-run-history.ipynb
        * tensorboard.ipynb

    * Juptyer notebook 服务器
      * 使用[创建工作区文章](setup-create-workspace.md)到
          * [安装 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)与`tensorboard`额外
          * 创建一个工作区和其配置文件 (config.json)
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>选项 1：直接在 TensorBoard 中运行历史记录视图

此选项适用于本机输出日志文件可使用由 TensorBoard，如 PyTorch，链接器和 TensorFlow 试验的试验。 如果这不是实验的情况下，使用[`export_to_tensorboard()`方法](#export)相反。

下面的代码示例使用[MNIST 演示实验](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py)TensorFlow 的远程计算目标，Azure 机器学习计算中的存储库中。 接下来，我们还为使用 SDK 的自定义我们模型定型[TensorFlow 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)，然后启动 TensorBoard 针对此 TensorFlow 试验中，即本机输出 TensorBoard 事件文件的实验。

### <a name="set-experiment-name-and-create-project-folder"></a>设置试验名称，然后创建项目文件夹

此处我们命名试验，并创建其文件夹。 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>下载 TensorFlow 演示试验代码

TensorFlow 的存储库具有广泛 TensorBoard 检测 MNIST 演示。 我们不这样做，也不需要更改任何本演示的代码，使其能够使用 Azure 机器学习服务。 在下面的代码中，我们下载 MNIST 代码并将其保存我们新创建的试验文件夹中。

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
在整个 MNIST 代码文件中，mnist_with_summaries.py，请注意，存在行调用`tf.summary.scalar()`， `tf.summary.histogram()`，`tf.summary.FileWriter()`等。这些方法组、 日志和标记的运行历史记录到在实验的关键指标。 `tf.summary.FileWriter()`是相当重要，因为其序列化 TensorBoard 生成可视化效果从它们允许您记录的实验指标的数据。

 ### <a name="configure-experiment"></a>配置试验

在下面的示例，我们配置实验并设置日志和数据的目录。 这些日志将上载到该项目服务，它 TensorBoard 访问更高版本。

>[!Note]
> 对于此 TensorFlow 示例，需要在本地计算机上安装 TensorFlow。 此外，因为本地计算机是什么运行 TensorBoard，TensorBoard 模块 （即，其中附带 TensorFlow） 必须能够访问此笔记本的内核。

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
我们创建对于此试验，请 AmlCompute 群集，但在任何环境中创建试验，并且仍能够启动 TensorBoard 针对试验运行历史记录。 

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

### <a name="submit-run-with-tensorflow-estimator"></a>使用 TensorFlow 估算器提交运行

TensorFlow 估算器提供了简单的方法启动的计算目标上的 TensorFlow 培训作业。 实现通过通用[ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py)类，该类可用于支持任何框架。 训练模型使用的泛型估计器的详细信息，请参阅[与使用估计器的 Azure 机器学习训练模型](how-to-train-ml-models.md)

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

在运行或在它完成后，可以启动 TensorBoard。 在下面的示例，我们创建 TensorBoard 对象实例， `tb`，将试验运行历史记录中加载`run`，然后启动使用 TensorBoard`start()`方法。 
  
[TensorBoard 构造函数](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py)将数组的运行，因此务必对，将其作为单个元素数组。

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>选项 2：为日志以查看在 TensorBoard 中导出历史记录

下面的代码设置的示例实验、 开始使用 Azure 机器学习的运行历史记录的 Api，日志记录进程并将导出运行历史记录到日志可使用 TensorBoard 可视化效果的实验。 

### <a name="set-up-experiment"></a>设置了实验

下面的代码设置了新的实验并运行的目录`root_run`。 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

此处我们加载 diabetes 数据集-一个内置的小型数据集所带 scikit 的了解，并将其拆分为测试集和定型集。

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

### <a name="run-experiment-and-log-metrics"></a>运行实验并记录指标

此代码中，为我们训练线性回归模型和日志关键指标，alpha 系数`alpha`和均方根误差`mse`，在运行历史记录。

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

### <a name="export-runs-to-tensorboard"></a>导出运行 TensorBoard

使用 SDK [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py)方法中，我们可以将导出到 TensorBoard 日志中，Azure 机器学习实验的运行历史记录以便我们可以通过 TensorBoard 中查看它们。  

在下面的代码中，我们创建该文件夹`logdir`我们当前工作目录中。 此文件夹是，我们将导出我们试验运行历史记录和记录从`root_run`，然后将标记为已完成运行。 

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
 您还可以导出特定运行 TensorBoard 到通过指定运行的名称  `export_to_tensorboard(run_name, logdir)`

启动和停止 TensorBoard 一次导出对于此试验我们运行历史记录，我们可以启动使用 TensorBoard [start （)](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-)方法。 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

完成后，请务必调用[stop （)](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) TensorBoard 对象的方法。 否则，TensorBoard 将继续运行，直到关闭 notebook 内核。 

```python
tb.stop()
```

## <a name="next-steps"></a>后续步骤

在本操作方法，创建两个试验并学习了如何启动 TensorBoard 针对其运行历史记录，以识别潜在的优化和重新训练需要的方面。 

* 如果您对模型感到满意，请转到我们[如何将模型部署](how-to-deploy-and-where.md)一文。 
* 详细了解如何[超参数优化](how-to-tune-hyperparameters.md)。 
