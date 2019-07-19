---
title: 对大数据运行批量预测
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务对大量数据进行异步批量预测。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 07/12/2019
ms.openlocfilehash: c233c44625779d6b070ccce1795a84f264d4764b
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868801"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-service"></a>使用 Azure 机器学习服务对大型数据集运行批量预测

本文介绍如何使用 Azure 机器学习服务异步对大量数据进行预测。

批量预测（或批量评分）可以针对异步应用程序空前未有的吞吐量进行经济有效的推理。 批量预测管道可以进行缩放，以便在数 TB 生产数据的基础上进行推理。 批量预测针对高吞吐量的即发即弃预测进行优化，适用于大量的数据。

>[!TIP]
> 如果系统要求低延迟处理（以便快速处理单个文档或少量文档），则请使用[实时评分](how-to-consume-web-service.md)而不是批量预测。

在以下步骤中, 你将创建一个[机器学习管道](concept-ml-pipelines.md)来注册预先训练的计算机视觉模型 ([开始-V3](https://arxiv.org/abs/1512.00567))。 然后使用预先训练的模型对 Azure Blob 存储帐户中提供的映像执行批处理评分。 这些用于评分的图像是 [ImageNet](http://image-net.org/) 数据集中的未标记图像。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

- 配置安装 Azure 机器学习 SDK 所需的开发环境。 有关详细信息，请参阅[配置 Azure 机器学习的开发环境](how-to-configure-environment.md)。

- 创建用于保存所有管道资源的 Azure 机器学习工作区。 可以使用以下代码；如需更多选项，请参阅[创建工作区配置文件](how-to-configure-environment.md#workspace)。

  ```python
  from azureml.core import Workspace
  ws = Workspace.create(name = '<workspace-name>',
                        subscription_id = '<subscription-id>',
                        resource_group = '<resource-group>',
                        location = '<workspace_region>',
                        exist_ok = True
                        )
  ```

## <a name="set-up-machine-learning-resources"></a>设置机器学习资源

以下步骤会设置运行管道所需的资源：

- 访问数据存储，该数据存储已经有用于评分的预先训练的模型、输入标签和图像（已经为你设置好）。
- 设置用于存储输出的数据存储。
- 配置 `DataReference` 对象，使之指向前述数据存储中的数据。
- 设置将在其中运行管道步骤的计算机或计算群集。

### <a name="access-the-datastores"></a>访问数据存储

首先，访问包含模型、标签和图像的数据存储。

使用名为*sampledata*的公共 blob 容器, 该容器  包含 ImageNet 评估集中的图像。 此公共容器的数据存储名称为 *images_datastore*。 将此数据存储注册到工作区：

```python
from azureml.core import Datastore

account_name = "pipelinedata"
datastore_name="images_datastore"
container_name="sampledata"

batchscore_blob = Datastore.register_azure_blob_container(ws,
                      datastore_name=datastore_name,
                      container_name= container_name,
                      account_name=account_name,
                      overwrite=True)
```

接下来进行设置，以便使用输出的默认数据存储。

创建工作区时，会默认将 [Azure 文件存储](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) 和 [Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) 附加到工作区。 Azure 文件存储是工作区的默认数据存储，但你也可以使用 Blob 存储作为数据存储。 有关详细信息，请参阅 [Azure 存储选项](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)。

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>配置数据引用

现在，请将管道中的数据作为管道步骤的输入引入。

管道中的数据源由 [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) 对象表示。  `DataReference` 对象指向驻留在数据存储中的或者可从数据存储访问的数据。 不管是用于输入图像的目录、用于存储预先训练的模型的目录、用于标签的目录还是输出目录，都需要 `DataReference` 对象。

```python
from azureml.data.data_reference import DataReference

input_images = DataReference(datastore=batchscore_blob,
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download")

model_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_model",
                          path_on_datastore="batchscoring/models",
                          mode="download")

label_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download")

output_dir = PipelineData(name="scores",
                          datastore=def_data_store,
                          output_path_on_compute="batchscoring/results")
```

### <a name="set-up-compute-target"></a>设置计算目标

在 Azure 机器学习中，*计算*（或*计算目标*）是指在机器学习管道中执行计算步骤的计算机或群集。 例如，可以创建 `Azure Machine Learning compute`。

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

compute_name = "gpucluster"
compute_min_nodes = 0
compute_max_nodes = 4
vm_size = "STANDARD_NC6"

if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target. just use it. ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(
                     vm_size = vm_size, # NC6 is GPU-enabled
                     vm_priority = 'lowpriority', # optional
                     min_nodes = compute_min_nodes,
                     max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws,
                        compute_name,
                        provisioning_config)

    compute_target.wait_for_completion(
                     show_output=True,
                     min_node_count=None,
                     timeout_in_minutes=20)
```

## <a name="prepare-the-model"></a>准备模型

在使用预先训练的模型之前，需下载该模型并将其注册到工作区。

### <a name="download-the-pretrained-model"></a>下载预先训练的模型

从 <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz> 下载预先训练的计算机视觉模型 (InceptionV3)。 然后将其提取到 `models` 子文件夹。

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>注册模型

下面展示了如何注册模型：

```python
import shutil
from azureml.core.model import Model

# register downloaded model
model = Model.register(
        model_path = "models/inception_v3.ckpt",
        model_name = "inception", # This is the name of the registered model
        tags = {'pretrained': "inception"},
        description = "Imagenet trained tensorflow inception",
        workspace = ws)
```

## <a name="write-your-scoring-script"></a>编写评分脚本

>[!Warning]
>以下代码只是一个示例，说明了在[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb)使用的 [batch_score.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py) 中包含了什么内容。 你需要根据具体情况创建自己的评分脚本。

`batch_score.py` 脚本采用 *dataset_path* 中的输入图像和 *model_dir* 中的预先训练的模型，并将 *results-label.txt* 输出到 *output_dir*。

```python
# Snippets from a sample scoring script
# Refer to the accompanying batch-scoring Notebook
# https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb
# for the implementation script

# Get labels
def get_class_label_dict(label_file):
  label = []
  proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
  for l in proto_as_ascii_lines:
    label.append(l.rstrip())
  return label

class DataIterator:
  # Definition of the DataIterator here

def main(_):
    # Refer to batch-scoring Notebook for implementation.
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)
    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                        num_classes=classes_num,
                                                        is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")

        # copy the file to artifacts
        shutil.copy(out_filename, "./outputs/")
```

## <a name="build-and-run-the-batch-scoring-pipeline"></a>生成并运行批量评分管道

### <a name="prepare-the-run-environment"></a>准备运行环境

指定脚本的 conda 依赖项。 稍后创建管道步骤时，需要此对象。

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>指定管道的参数

使用带默认值的 [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) 对象创建一个管道参数。

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(
                    name="param_batch_size",
                    default_value=20)
```

### <a name="create-the-pipeline-step"></a>创建管道步骤

使用脚本、环境配置和参数创建管道步骤。 将已附加到工作区的计算目标指定为执行脚本时的目标。 使用 [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) 创建管道步骤。

```python
from azureml.pipeline.steps import PythonScriptStep
inception_model_name = "inception_v3.ckpt"

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_score.py",
    arguments=["--dataset_path", input_images,
               "--model_name", "inception",
               "--label_dir", label_dir,
               "--output_dir", output_dir,
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config,
    source_directory=scripts_folder
```

### <a name="run-the-pipeline"></a>运行管道

现在请运行管道并检查其生成的输出。 输出会有一个与每个输入图像相对应的分数。

```python
from azureml.pipeline.core import Pipeline

# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

import pandas as pd
df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>发布管道

对运行结果满意以后，请发布管道，以便以后使用不同的输入值来运行它。 发布管道时，你会获得一个 REST 终结点。 此终结点允许使用已通过 [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) 纳入的参数集来调用管道。

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring",
    description="Batch scoring using Inception v3 model",
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>使用 REST 终结点重新运行管道

若要重新运行管道，需要一个 Azure Active Directory 身份验证标头令牌，如 [AzureCliAuthentication 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py)中所述。

```python
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint,
        headers=aad_token,
        json={"ExperimentName": "batch_scoring",
               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
from azureml.pipeline.core.run import PipelineRun
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>后续步骤

若要了解此方面的端到端运行机制，请尝试 [GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines) 中的批量评分笔记本。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

