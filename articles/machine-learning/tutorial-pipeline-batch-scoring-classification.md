---
title: 教程：用于批量评分的 ML 管道
titleSuffix: Azure Machine Learning
description: 本教程介绍如何生成机器学习管道，用于对图像分类模型执行批量评分。 Azure 机器学习使你能够将工作重心放在机器学习上，而不必关注基础设施和自动化。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: laobri
ms.date: 03/11/2020
ms.custom: contperfq4
ms.openlocfilehash: 5b6b58cb205c769feeed011c0a2ba2ec569d667a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857762"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>教程：生成用于批量评分的 Azure 机器学习管道

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

此高级教程介绍如何在 Azure 机器学习中构建管道来运行批量评分作业。 机器学习管道可以优化工作流以提高其速度、可移植性和可重用性，使你能够将工作重心放在机器学习上，而不必关注基础结构和自动化。 生成并发布管道后，你将配置一个 REST 终结点，用于从任何平台上的任何 HTTP 库触发该管道。 

本示例使用 Tensorflow 中实现的预先训练的 [Inception-V3](https://arxiv.org/abs/1512.00567) 卷积神经网络模型来对不带标签的图像进行分类。 [详细了解机器学习管道](concept-ml-pipelines.md)。

在本教程中，请完成以下任务：

> [!div class="checklist"]
> * 配置工作区 
> * 下载并存储示例数据
> * 创建用于提取和输出数据的数据集对象
> * 下载、准备模型并将其注册到工作区中
> * 预配计算目标并创建评分脚本
> * 使用 `ParallelRunStep` 类进行异步批处理评分
> * 生成、运行并发布管道
> * 为管道启用 REST 终结点

如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

## <a name="prerequisites"></a>先决条件

* 如果你没有 Azure 机器学习工作区或笔记本虚拟机，请完成[设置教程的第 1 部分](tutorial-1st-experiment-sdk-setup.md)。
* 完成设置教程后，使用同一笔记本服务器打开 tutorials/machine-learning-pipelines-advanced/tutorial-pipeline-batch-scoring-classification.ipynb  笔记本。

如果要在自己的[本地环境](how-to-configure-environment.md#local)中运行设置教程，可以访问 [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) 上的教程。 运行 `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps pandas requests` 以获取所需的包。

## <a name="configure-workspace-and-create-a-datastore"></a>配置工作区并创建数据存储

从现有的 Azure 机器学习工作区创建工作区对象。

- [工作区](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)是可接受 Azure 订阅和资源信息的类。 工作区还可创建用于监视和跟踪模型运行的云资源。 
- `Workspace.from_config()` 读取 `config.json` 文件并将身份验证详细信息加载到名为 `ws` 的对象中。 本教程中的代码使用 `ws` 对象。

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

## <a name="create-a-datastore-for-sample-images"></a>为示例图像创建数据存储

在 `pipelinedata` 帐户中，从 `sampledata` 公共 Blob 容器获取 ImageNet 评估公共数据示例。 调用 `register_azure_blob_container()` 可使数据可用于名为 `images_datastore` 的工作区。 然后，将工作区的默认数据存储设置为输出数据存储。 使用输出数据存储在管道中为输出评分。

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>创建数据集对象

生成管道时，将使用 `Dataset` 对象从工作区数据存储读取数据，并使用 `PipelineData` 对象在管道步骤之间传输中间数据。

> [!Important]
> 本教程中的批量评分示例只使用一个管道步骤。 在包含多个步骤的用例中，典型流包括以下步骤：
>
> 1. 使用 `Dataset` 对象作为提取原始数据的输入，执行某种转换，然后输出 `PipelineData` 对象。  
>
> 2. 使用上一步骤中的 `PipelineData` 输出对象作为输入对象   。 针对后续步骤重复此过程。

在此场景中，你将创建与输入图像和分类标签（y-test 值）的数据存储目录相对应的 `Dataset` 对象。 此外，将为批量评分输出数据创建一个 `PipelineData` 对象。

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/*.txt"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

接下来，将数据集注册到工作区。

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>下载并注册模型

下载预先训练的 Tensorflow 模型用于管道中的批量评分。 首先创建一个用于存储模型的本地目录。 然后下载并提取该模型。

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

接下来，将模型注册到工作区，以便能够在管道流程中轻松检索该模型。 在 `register()` 静态函数中，`model_name` 参数是用于在整个 SDK 中查找模型的键。

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>创建并附加远程计算目标

机器学习管道无法在本地运行，因此你需要在云资源或远程计算目标上运行这些管道。  远程计算目标是可重用的虚拟计算环境，可在其中运行试验和机器学习工作流。 

运行以下代码创建支持 GPU 的 [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) 目标，并将其附加到工作区。 有关计算目标的详细信息，请参阅[概念文章](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)。


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>编写评分脚本

若要执行评分，请创建名为 `batch_scoring.py` 的批量评分脚本，并将其写入当前目录。 该脚本将提取输入图像，应用分类模型，然后将预测结果输出到结果文件中。

`batch_scoring.py` 脚本采用以下参数，这些参数将从稍后创建的 `ParallelRunStep` 传递：

- `--model_name`设置用户帐户 ：所用模型的名称。
- `--labels_name`设置用户帐户 ：保存 `labels.txt` 文件的 `Dataset` 的名称。

管道基础结构使用 `ArgumentParser` 类将参数传入管道步骤。 例如，在以下代码中，为第一个参数 `--model_name` 指定了属性标识符 `model_name`。 在 `init()` 函数中，使用 `Model.get_model_path(args.model_name)` 访问此属性。


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict():
    label = []
    proto_as_ascii_lines = tf.gfile.GFile("labels.txt").readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_name', dest="labels_name", required=True)
    args, _ = parser.parse_known_args()

    workspace = Run.get_context(allow_offline=False).experiment.workspace
    label_ds = Dataset.get_by_name(workspace=workspace, name=args.labels_name)
    label_ds.download(target_path='.', overwrite=True)

    label_dict = get_class_label_dict()
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> 本教程中的管道只有一个步骤，它会将输出写入某个文件。 对于多步骤管道，你也可以使用 `ArgumentParser` 来定义要将输出数据写入到的目录，以便将其输入到后续步骤。 有关使用 `ArgumentParser` 设计模式在多个管道步骤之间传递数据的示例，请参阅[笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb)。

## <a name="build-the-pipeline"></a>构建管道

在运行管道之前，请创建一个用于定义 Python 环境的对象，并创建 `batch_scoring.py` 脚本所需的依赖项。 所需的主要依赖项是 Tensorflow，但你还需要为后台进程安装 `azureml-defaults`。 使用依赖项创建 `RunConfiguration` 对象。 另外，指定 Docker 和 Docker-GPU 支持。

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>创建用于包装脚本的配置

使用脚本、环境配置和参数创建管道步骤。 指定已附加到工作区的计算目标。

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>创建管道步骤

管道步骤是一个对象，用于封装运行管道所需的任何内容，其中包括：

* 环境和依赖项设置
* 要在其上运行管道的计算资源
* 输入和输出数据，以及任何自定义参数
* 对执行步骤期间要运行的脚本或 SDK 逻辑的引用

有多个类继承自父类 [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py)。 你可以选择适当的类，以使用特定的框架和堆栈生成步骤。 在此示例中，将通过自定义 Python 脚本使用 `ParallelRunStep` 类定义步骤逻辑。 如果脚本的某个自变量是步骤的输入或步骤的输出，则必须分别在 `arguments` 数组以及 `input` 或 `output` 参数中定义该自变量。   

如果存在多个步骤，`outputs` 数组中的某个对象引用可用作后续管道步骤的输入。 

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

batch_score_step = ParallelRunStep(
    name="parallel-step-test",
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    models=[model],
    arguments=["--model_name", "inception",
               "--labels_name", "label_ds"],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

有关可对不同步骤类型使用的所有类的列表，请参阅[步骤包](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)。

## <a name="submit-the-pipeline"></a>提交管道

现在请运行管道。 首先，使用工作区引用和创建的管道步骤创建一个 `Pipeline` 对象。 `steps` 参数是步骤数组。 在本例中，批量评分只有一个步骤。 若要生成包含多个步骤的管道，请将步骤按顺序放入此数组。

接下来，使用 `Experiment.submit()` 函数提交管道以供执行。 还可以指定自定义参数 `param_batch_size`。 `wait_for_completion` 函数将在管道生成过程中输出日志。 可以使用日志来查看当前进度。

> [!IMPORTANT]
> 首次管道运行需要大约 15 分钟。  必须下载所有依赖项、创建 Docker 映像，并预配和创建 Python 环境。 再次运行管道所花费的时间会大幅减少，因为会重复使用这些资源，而无需再次创建。 但是，管道的总运行时间取决于脚本的工作负荷，以及每个管道步骤中运行的进程数。

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>下载并查看输出

运行以下代码下载通过 `batch_scoring.py` 脚本创建的输出文件。 然后浏览评分结果。

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>从 REST 终结点发布和运行

运行以下代码，将管道发布到工作区。 在 Azure 机器学习工作室的工作区中，可以看到管道的元数据，包括运行历史记录和持续时间。 还可以从工作室手动运行管道。

发布管道会启用一个 REST 终结点，用于从任何平台上的任何 HTTP 库重新运行该管道。

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

若要从 REST 终结点运行管道，需要获取 OAuth2 Bearer-type 身份验证标头。 以下示例使用交互式身份验证（用于演示目的），但对于大多数需要自动身份验证或无头身份验证的生产方案，请使用服务主体身份验证，如[此文中所述](how-to-setup-authentication.md)。

服务主体身份验证涉及到在 *Azure Active Directory* 中创建应用注册。  首先生成客户端机密，然后为服务主体授予对机器学习工作区的角色访问权限。  使用 [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) 类来管理身份验证流。 

[`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py) 和 `ServicePrincipalAuthentication` 均继承自 `AbstractAuthentication`。 在这两种情况下，请以相同的方式使用 [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py#get-authentication-header--) 函数来提取标头：

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

从已发布的管道对象的 `endpoint` 属性获取 REST URL。 也可以在 Azure 机器学习工作室的工作区中找到该 REST URL。 

对终结点生成 HTTP POST 请求。 在请求中指定身份验证标头。 添加包含试验名称和批大小参数的 JSON 有效负载对象。 如本教程前面所述，`param_batch_size` 将传递到 `batch_scoring.py` 脚本，因为已在步骤配置中将其定义为 `PipelineParameter` 对象。

发出触发运行的请求。 包含相应的代码用于访问响应字典中的 `Id` 密钥以获取运行 ID 的值。

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

使用运行 ID 监视新运行的状态。 新的运行需要花费 10-15 分钟来完成。 

新的运行类似于在本教程中前面运行的管道。 可以选择不查看完整输出。

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>清理资源

如果你打算运行其他 Azure 机器学习教程，请不要完成本部分。

### <a name="stop-the-compute-instance"></a>停止计算实例

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>删除所有内容

如果不打算使用已创建的资源，请删除它们，以免产生任何费用：

1. 在 Azure 门户的左侧菜单中选择“资源组”  。
1. 在资源组列表中，选择创建的资源组。
1. 选择“删除资源组”  。
1. 输入资源组名称。 然后选择“删除”  。

还可保留资源组，但请删除单个工作区。 显示工作区属性，然后选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本机器学习管道教程中，你已完成以下任务：

> [!div class="checklist"]
> * 使用环境依赖项生成了一个要在远程 GPU 计算资源上运行的管道。
> * 使用预先训练的 Tensorflow 模型创建了一个用于运行批量预测的评分脚本。
> * 发布了管道，并使其从 REST 终结点运行。

有关演示如何使用机器学习 SDK 生成管道的更多示例，请参阅[笔记本存储库](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)。
