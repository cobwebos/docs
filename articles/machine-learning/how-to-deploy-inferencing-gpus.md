---
title: 部署支持 GPU 推理的模型
titleSuffix: Azure Machine Learning
description: 本文介绍如何使用 Azure 机器学习将启用 GPU 的 Tensorflow 深度学习模型部署为 web 服务。服务和评分推理请求。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: e4a8c84426ca03b9e111b8ece89f48a97f31072f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328354"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>使用 GPU 为推理部署深度学习模型


本文介绍如何使用 Azure 机器学习将启用 GPU 的模型部署为 Web 服务。 本文中的信息基于 Azure Kubernetes 服务 (AKS) 上的模型部署。 AKS 群集提供模型用于推理的 GPU 资源。

推理（模型评分）是使用部署的模型进行预测的阶段。 使用 GPU 代替 CPU 可为高度并行化的计算提供性能优势。

> [!IMPORTANT]
> 对于 Web 服务部署，只有 Azure Kubernetes 服务支持 GPU 推理。 对于使用机器学习管道进行的推理，只有 Azure 机器学习计算支持 GPU。 有关使用机器学习管道的详细信息，请参阅[运行批量预测](how-to-use-parallel-run-step.md)。 

> [!TIP]
> 尽管本文中的代码片段使用了 TensorFlow 模型，但你可以将这些信息应用于任何支持 GPU 的机器学习框架。

> [!NOTE]
> 本文中的信息基于[如何部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)一文中的信息。 那篇文章总体上说的是在 AKS 上部署，本文介绍的是特定于 GPU 的部署。

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* 安装了 Azure 机器学习 SDK 的 Python 开发环境。 有关详细信息，请参阅 [Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。  

* 使用 GPU 的已注册的模型。

    * 若要了解如何注册模型，请参阅[部署模型](how-to-deploy-and-where.md#registermodel)。

    * 若要创建并注册此文档创建时所用的 Tensorflow 模型，请参阅[如何训练 TensorFlow 模型](how-to-train-tensorflow.md)。

* 大致了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。

## <a name="connect-to-your-workspace"></a>连接到工作区

若要连接到现有工作区，请使用以下代码：

> [!IMPORTANT]
> 此代码片段预期工作区配置被保存到当前目录或其父项。 有关创建工作区的详细信息，请参阅[创建和管理 Azure 机器学习工作区](how-to-manage-workspace.md)。   有关将配置保存到文件的详细信息，请参阅[创建工作区配置文件](how-to-configure-environment.md#workspace)。

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>使用 GPU 创建 Kubernetes 群集

Azure Kubernetes 服务提供了许多不同的 GPU 选项。 可以将其中任何一个用于模型推理。 若要全面了解其功能和成本，请参阅 [N 系列 VM 列表](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)。

以下代码演示如何为工作区创建新的 AKS 群集：

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> 只要存在 AKS 群集，Azure 就会向你收费。 请务必在使用完 AKS 群集后将其删除。

有关将 AKS 与 Azure 机器学习配合使用的详细信息，请参阅[如何部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)。

## <a name="write-the-entry-script"></a>编写入口脚本

入口脚本接收提交给 Web 服务的数据，将其传递给模型，并返回计分结果。 下面的脚本将在启动时加载 Tensorflow 模型，然后使用该模型对数据进行评分。

> [!TIP]
> 入口脚本特定于你的模型。 例如，脚本必须知道要与模型、数据格式等一起使用的框架。

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

此文件的名称为 `score.py`。 有关入口脚本的详细信息，请参阅[如何以及在何处部署](how-to-deploy-and-where.md)。

## <a name="define-the-conda-environment"></a>定义 Conda 环境

Conda 环境文件指定服务的依赖项。 它包括模型和入口脚本都需要的依赖项。 请注意，必须将版本为 1.0.45 或更高版本的 azureml-defaults 指示为 pip 依赖项，因为它包含将模型托管为 Web 服务所需的功能。 以下 YAML 定义 Tensorflow 模型的环境。 它指定 `tensorflow-gpu`，后者会使用在此部署中使用的 GPU：

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
  - numpy
  - tensorflow-gpu=1.12
channels:
- conda-forge
```

在此示例中，该文件将保存为 `myenv.yml`。

## <a name="define-the-deployment-configuration"></a>定义部署配置

> [!IMPORTANT]
> AKS 不允许 Pod 共享 GPU，支持 GPU 的 Web 服务的副本数只能与群集中的 GPU 数相同。

部署配置定义用于运行 web 服务的 Azure Kubernetes 服务环境：

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

有关详细信息，请参阅 [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) 的参考文档。

## <a name="define-the-inference-configuration"></a>定义推理配置

推理配置指向入口脚本和环境对象，后者使用具有 GPU 支持的 docker 映像。 请注意，用于环境定义的 YAML 文件必须将版本为 1.0.45 或更高版本的 azureml-defaults 列为 pip 依赖项，因为它包含将模型托管为 Web 服务所需的功能。

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

有关环境的详细信息，请参阅[创建和管理用于训练和部署的环境](how-to-use-environments.md)。
有关详细信息，请参阅 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py&preserve-view=true) 的参考文档。

## <a name="deploy-the-model"></a>部署模型

将模型部署到 AKS 群集，并等待其创建服务。

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

有关详细信息，请参阅[模型](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true)的参考文档。

## <a name="issue-a-sample-query-to-your-service"></a>向服务发出示例查询

将测试查询发送到已部署的模型。 将 jpeg 图像发送到模型时，它会对该图像进行评分。 下面的代码示例下载测试数据，然后选择要发送到服务的随机测试映像。

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

有关创建客户端应用程序的详细信息，请参阅[创建客户端以使用部署的 Web 服务](how-to-consume-web-service.md)。

## <a name="clean-up-the-resources"></a>清理资源

如果专门为此示例创建了 AKS 群集，请在使用完后删除资源。

> [!IMPORTANT]
> Azure 会根据 AKS 群集的部署时长来收费。 使用完后，请务必将其清除。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>后续步骤

* [在 FPGA 上部署模型](how-to-deploy-fpga-web-service.md)
* [用 ONNX 部署模型](concept-onnx.md#deploy-onnx-models-in-azure)
* [训练 Tensorflow DNN 模型](how-to-train-tensorflow.md)
