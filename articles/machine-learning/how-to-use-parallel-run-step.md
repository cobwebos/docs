---
title: 对大数据运行批量预测
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 ParallelRunStep 对大量数据进行异步推理。 ParallelRunStep 提供现成的并行处理功能，可针对大数据用例优化高吞吐量、触发后不理推理。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 01/15/2020
ms.custom: Ignite2019
ms.openlocfilehash: 3d283d1094336b928869aa281b4a640d7a62dd94
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79477181"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>使用 Azure 机器学习对大量数据运行批处理推理
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何使用 Azure 机器学习以异步方式和并行方式处理大量数据。 此处所述的 ParallelRunStep 功能当前为公共预览版。 这是一种用于生成推理和处理数据的高性能和高吞吐量的方式。 它提供现成的异步功能。

通过 ParallelRunStep，可以轻松地将离线推理扩展到生产数据 TB 级的大型计算机群集，从而提高工作效率和优化成本。

本文介绍如何执行以下任务：

> * 创建远程计算资源。
> * 编写自定义推理脚本。
> * 创建[机器学习管道](concept-ml-pipelines.md)，以根据 [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/) 数据集注册预先训练的图像分类模型。 
> * 使用该模型可对 Azure Blob 存储帐户中提供的示例映像运行批处理推理。 

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 试用 [Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。

* 对于引导式快速入门，如果你还没有 Azure 机器学习的工作区或笔记本虚拟机，请完成[安装教程](tutorial-1st-experiment-sdk-setup.md)。 

* 若要管理你自己的环境和依赖项，请参阅关于配置你自己的环境的[操作指南](how-to-configure-environment.md)。 在你的环境中运行 `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` 以下载必要的依赖项。

## <a name="set-up-machine-learning-resources"></a>设置机器学习资源

以下操作设置运行批处理推理管道所需的资源：

- 创建一个数据存储以指向包含要推理的映像的 blob 容器。
- 将数据引用设置为批处理推理管道步骤的输入和输出。
- 设置计算群集以运行批处理推理步骤。

### <a name="create-a-datastore-with-sample-images"></a>使用示例映像创建数据存储

在名为 `pipelinedata` 的帐户上获取公共 blob 容器 `sampledata` 中的 MNIST 评估集。 创建一个指向此容器的名为 `mnist_datastore` 的数据存储。 在对 `register_azure_blob_container` 的以下调用中，将 `overwrite` 标志设置为 `True` 会覆盖以前用该名称创建的所有数据存储。 

可以通过为 `datastore_name`、`container_name` 和 `account_name` 提供自己的值，将此步骤更改为指向 blob 容器。

```python
from azureml.core import Datastore
from azureml.core import Workspace

# Load workspace authorization details from config.json
ws = Workspace.from_config()

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

接下来，将工作区的默认数据存储指定为输出数据存储。 你将使用它进行推理输出。

创建工作区时，会默认将 [Azure 文件存储](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) 和 [Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) 附加到工作区。 Azure 文件存储是工作区的默认数据存储，但你也可以使用 Blob 存储作为数据存储。 有关详细信息，请参阅 [Azure 存储选项](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)。

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>配置数据输入和输出

现在你需要配置数据输入和输出，其中包括：

- 包含输入图像的目录。
- 存储预先训练的模型的目录。
- 包含标签的目录。
- 输出的目录。

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) 是一个用于在 Azure 机器学习中浏览、转换和管理数据的类。 此类有两种类型：[`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) 和 [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py)。 在此示例中，将使用 `FileDataset` 作为批处理推理管道步骤的输入。 

> [!NOTE] 
> 现在，批处理推理中的 `FileDataset` 支持仅限于 Azure Blob 存储。 

你还可以在自定义推理脚本中引用其他数据集。 例如，你可以使用它来访问脚本中的标签，以便使用 `Dataset.register` 和 `Dataset.get_by_name` 来标记映像。

有关 Azure 机器学习数据集的详细信息，请参阅[创建和访问数据集（预览版）](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)。

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 对象用于在管道步骤之间传输中间数据。 在此示例中，将其用于推理输出。

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
registered_mnist_ds = input_mnist_ds.register(ws, mnist_ds_name, create_new_version=True)
named_mnist_ds = registered_mnist_ds.as_named_input(mnist_ds_name)

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

### <a name="set-up-a-compute-target"></a>设置计算目标

在 Azure 机器学习中，*计算*（或*计算目标*）是指在机器学习管道中执行计算步骤的计算机或群集。 运行以下代码以创建基于 CPU 的 [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) 目标。

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

## <a name="prepare-the-model"></a>准备模型

[下载预先训练的映像分类模型](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)，然后将其解压缩到 `models` 目录。

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

然后，将该模型注册到你的工作区，使其可用于你的远程计算资源。

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>编写推理脚本

>[!Warning]
>下面的代码只是[示例笔记本](https://aka.ms/batch-inference-notebooks)使用的示例。 你需要为方案创建自己的脚本。

脚本必须包含  两个函数：
- `init()`设置用户帐户 ：此函数适用于后续推理的任何成本高昂或常见的准备工作。 例如，使用它将模型加载到全局对象。 此函数将在进程开始时调用一次。
-  `run(mini_batch)`设置用户帐户 ：将针对每个 `mini_batch` 实例运行此函数。
    -  `mini_batch`设置用户帐户 ：并行运行步骤将调用 run 方法，并将列表或 Pandas 数据帧作为参数传递给该方法。 min_batch 中的每个条目是文件路径（如果输入是 FileDataset）或 Pandas 数据帧（如果输入是 TabularDataset）。
    -  `response`：run() 方法应返回 Pandas 数据帧或数组。 对于 append_row output_action，这些返回的元素将追加到公共输出文件中。 对于 summary_only，将忽略元素的内容。 对于所有的输出操作，每个返回的输出元素都指示输入微型批处理中输入元素的一次成功运行。 你应确保运行结果中包含足够的数据，以便将输入映射到运行结果。 运行输出将写入输出文件中，并且不保证按顺序写入，你应使用输出中的某个键将其映射到输入。

```python
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

### <a name="how-to-access-other-files-in-source-directory-in-entry_script"></a>如何在 entry_script 中访问源目录中的其他文件

如果入口脚本所在的同一目录中包含另一个文件或文件夹，可以通过查找当前工作目录来引用此文件或文件夹。

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>构建并运行包含 ParallelRunStep 的管道

现在，你有了生成管道所需的一切。

### <a name="prepare-the-run-environment"></a>准备运行环境

首先，指定脚本的依赖项。 稍后创建管道步骤时，将使用此对象。

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>为批处理推理管道步骤指定参数

`ParallelRunConfig` 是新引入的批处理推理 `ParallelRunStep` 实例在 Azure 机器学习管道中的主要配置。 使用它来包装脚本并配置所需的参数，包括以下所有参数：
- `entry_script`设置用户帐户 ：作为将在多个节点上并行运行的本地文件路径的用户脚本。 如果 `source_directory` 存在，则使用相对路径。 否则，请使用计算机上可访问的任何路径。
- `mini_batch_size`设置用户帐户 ：传递给单个 `run()` 调用的微型批处理的大小。 （可选；对于 FileDataset，默认值为 `10` 文件，对于 TabularDataset，默认值为 `1MB`。）
    - 对于 `FileDataset`，它是最小值为 `1` 的文件数。 可以将多个文件合并成一个微型批处理。
    - 对于 `TabularDataset`，它是数据的大小。 示例值为 `1024`、`1024KB`、`10MB` 和 `1GB`。 建议值为 `1MB`。 `TabularDataset` 中的微批永远不会跨越文件边界。 例如，如果你有各种大小的 .csv 文件，最小的文件为 100 KB，最大的文件为 10 MB。 如果设置 `mini_batch_size = 1MB`，则大小小于 1 MB 的文件将被视为一个微型批处理。 大小大于 1 MB 的文件将被拆分为多个微型批处理。
- `error_threshold`设置用户帐户 ：在处理过程中应忽略的 `TabularDataset` 记录失败数和 `FileDataset` 文件失败数。 如果整个输入的错误计数超出此值，则作业将中止。 错误阈值适用于整个输入，而非适用于发送给 `run()` 方法的单个微型批处理。 范围为 `[-1, int.max]`。 `-1` 部分指示在处理过程中忽略所有失败。
- `output_action`设置用户帐户 ：以下值之一指示将如何组织输出：
    - `summary_only`设置用户帐户 ：用户脚本将存储输出。 `ParallelRunStep` 仅将输出用于错误阈值计算。
    - `append_row`设置用户帐户 ：对于所有输入文件，将只在输出文件夹中创建一个文件，以追加所有按行分隔的输出。 文件名将是 `parallel_run_step.txt`。
- `source_directory`设置用户帐户 ：文件夹的路径，这些文件夹包含要在计算目标上执行的所有文件（可选）。
- `compute_target`设置用户帐户 ：仅支持 `AmlCompute`。
- `node_count`设置用户帐户 ：用于运行用户脚本的计算节点数。
- `process_count_per_node`设置用户帐户 ：每个节点的进程数。
- `environment`设置用户帐户 ：Python 环境定义。 你可以将其配置为使用现有的 Python 环境进行试验，或设置临时环境进行试验。 定义还负责设置所需的应用程序依赖项（可选）。
- `logging_level`设置用户帐户 ：日志详细程度。 递增详细程度的值为：`WARNING`、`INFO` 和 `DEBUG`。 （可选；默认值为 `INFO`）
- `run_invocation_timeout`设置用户帐户 ：`run()` 方法调用超时（以秒为单位）。 （可选；默认值为 `60`）

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size="5",
    error_threshold=10,
    output_action="append_row",
    environment=batch_env,
    compute_target=compute_target,
    node_count=4)
```

### <a name="create-the-pipeline-step"></a>创建管道步骤

使用脚本、环境配置和参数创建管道步骤。 将已附加到工作区的计算目标指定为脚本的执行目标。 使用 `ParallelRunStep` 创建批处理推理管道步骤，该步骤采用以下所有参数：
- `name`设置用户帐户 ：步骤的名称，但具有以下命名限制：唯一、3-32 个字符和正则表达式 ^\[a-z\]([-a-z0-9]*[a-z0-9])?$。
- `models`设置用户帐户 ：在 Azure 机器学习模型注册表中已注册的零个或多个模型名称。
- `parallel_run_config`设置用户帐户 ：`ParallelRunConfig` 对象，如前文所述。
- `inputs`设置用户帐户 ：一个或多个单类型 Azure 机器学习数据集。
- `output`设置用户帐户 ：与输出目录相对应的 `PipelineData` 对象。
- `arguments`设置用户帐户 ：传递给用户脚本的参数列表（可选）。
- `allow_reuse`设置用户帐户 ：当使用相同的设置/输入运行时，该步骤是否应重用以前的结果。 如果此参数为 `False`，则在管道执行过程中将始终为此步骤生成新的运行。 （可选；默认值为 `True`。）

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="batch-mnist",
    models=[model],
    parallel_run_config=parallel_run_config,
    inputs=[named_mnist_ds],
    output=output_dir,
    arguments=[],
    allow_reuse=True
)
```

>[!Note]
> 上一步依赖于 `azureml-contrib-pipeline-steps`，如[必备组件](#prerequisites)中所述。 

### <a name="submit-the-pipeline"></a>提交管道

现在请运行管道。 首先，使用工作区引用和创建的管道步骤创建一个 [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) 对象。 `steps` 参数是步骤数组。 在本例中，批量评分只有一个步骤。 若要生成包含多个步骤的管道，请将步骤按顺序放入此数组。

接下来，使用 `Experiment.submit()` 函数提交管道以供执行。

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-parallel-run-job"></a>监视并行运行作业

批处理推理作业可能需要很长时间才能完成。 此示例使用 Jupyter 小组件监视进度。 你还可以使用以下项来管理作业的进度：

* Azure 机器学习工作室。 
* 来自 [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) 对象的控制台输出。

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>后续步骤

若要了解此过程的端到端运行机制，请尝试[批处理推理笔记本](https://aka.ms/batch-inference-notebooks)。 

有关 ParallelRunStep 的调试和故障排除指导，请参阅[操作指南](how-to-debug-parallel-run-step.md)。

有关管道的调试和故障排除指南，请参阅[操作指南](how-to-debug-pipelines.md)。

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

