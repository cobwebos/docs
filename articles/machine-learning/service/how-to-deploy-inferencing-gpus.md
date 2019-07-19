---
title: 使用 GPU 为推理部署模型
titleSuffix: Azure Machine Learning service
description: 本文介绍如何使用 Azure 机器学习服务将支持 GPU 的 Tensorflow 深度学习模型部署为 web 服务。服务和评分推理请求。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: eeb1bc35e0438a7e99ea5ed8284f0c8611108da0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326993"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>使用 GPU 为推理部署深度学习模型

本文介绍如何使用 Azure 机器学习服务将支持 GPU 的 Tensorflow 深度学习模型部署为 web 服务。

将模型部署到 Azure Kubernetes 服务 (AKS) 群集, 以便执行启用 GPU 的推断。 推断或模型计分是部署的模型用于预测的阶段。 使用 Gpu 而不是 Cpu, 为高度可并行化计算提供性能优势。

虽然此示例使用 TensorFlow 模型, 但你可以通过对计分文件和环境文件进行少量更改, 将以下步骤应用到支持 Gpu 的任何机器学习框架。 

在本文中，将执行以下步骤：

* 创建启用了 GPU 的 AKS 群集
* 部署 Tensorflow GPU 模型
* 向已部署的模型发出示例查询

## <a name="prerequisites"></a>先决条件

* Azure 机器学习服务工作区。
* Python 发行版。
* 已注册的 Tensorflow 保存的模型。
    * 若要了解如何注册模型, 请参阅[部署模型](../service/how-to-deploy-and-where.md#registermodel)。

可以完成本操作方法系列的第一部分,[如何训练 TensorFlow 模型](how-to-train-tensorflow.md)以实现必需的先决条件。

## <a name="provision-an-aks-cluster-with-gpus"></a>使用 Gpu 预配 AKS 群集

Azure 有很多不同的 GPU 选项。 可以为推断使用其中的任何一个。 若要全面了解功能和成本, 请参阅[N 系列 vm 列表](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)。

有关将 AKS 与 Azure 机器学习服务一起使用的详细信息, 请参阅[如何部署和位置](../service/how-to-deploy-and-where.md#deploy-aks)。

```Python
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
> 只要预配了 AKS 群集, Azure 就会向你收费。 完成后, 请务必删除 AKS 群集。

## <a name="write-the-entry-script"></a>写入条目脚本

将以下代码保存到工作目录`score.py`中的。 此文件将映像发送到你的服务时将其评分。 它加载 TensorFlow 保存的模型, 将输入图像传递到每个 POST 请求上的 TensorFlow 会话, 然后返回生成的分数。 其他推断框架需要不同的计分文件。

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model

def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = Model.get_model_path('tf-dnn-mnist')
    saver = tf.train.import_meta_graph(os.path.join(model_root, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")
    
    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, 'mnist-tf.model'))

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()

```
## <a name="define-the-conda-environment"></a>定义 conda 环境

创建一个名`myenv.yml`为的 conda 环境文件以指定服务的依赖关系。 务必指定你使用`tensorflow-gpu`来实现更快的性能。

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  - azureml-defaults==1.0.43.*
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

## <a name="define-the-gpu-inferenceconfig-class"></a>定义 GPU InferenceConfig 类

创建一个`InferenceConfig`对象, 该对象启用 gpu 并确保 CUDA 与 Docker 映像一起安装。

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='aks-dnn-mnist'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"tf-dnn-mnist")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   enable_gpu=True)
```

有关详细信息，请参阅：

- [InferenceConfig 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [AksServiceDeploymentConfiguration 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>部署模型

将模型部署到 AKS 群集, 并等待其创建服务。

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Azure 机器学习服务不会使用`InferenceConfig`需要将 gpu 启用到没有 gpu 的群集的对象来部署模型。

有关详细信息, 请参阅[模型类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)。

## <a name="issue-a-sample-query-to-your-model"></a>向模型发出示例查询

将测试查询发送到已部署的模型。 当你将 jpeg 图像发送到模型时, 它将对该图像进行评分。 下面的代码示例使用外部实用工具函数来加载图像。 可在[GitHub 上的 Pir TensorFlow 示例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py)中找到相关的代码。 

```python
# Used to test your webservice
from utils import load_data 

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

> [!IMPORTANT]
> 若要最大程度地减少延迟和优化吞吐量, 请确保客户端与终结点位于同一 Azure 区域。 在此示例中, Api 是在美国东部 Azure 区域中创建的。

## <a name="clean-up-the-resources"></a>清理资源

如果专门为此示例创建了 AKS 群集, 请在完成后删除资源。

> [!IMPORTANT]
> Azure 会根据部署 AKS 群集的时间来对你进行计费。 完成后, 请务必将其清除。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>后续步骤

* [在 FPGA 上部署模型](../service/how-to-deploy-fpga-web-service.md)
* [用 ONNX 部署模型](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [定型 Tensorflow DNN 模型](../service/how-to-train-tensorflow.md)
