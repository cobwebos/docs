---
title: 部署具有 GPU 的推理模型
titleSuffix: Azure Machine Learning service
description: 本文介绍如何使用 Azure 机器学习服务来部署深度学习模型作为 web service.service 和分数推理请求启用了 GPU 的 Tensorflow。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: 8086d059913cc61bff0bca31681368bea6d76777
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543799"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>部署用于推断具有 GPU 的深度学习模型

本文介绍如何使用 Azure 机器学习服务来部署深入学习模型作为 web 服务启用了 GPU 的 Tensorflow。

将模型部署到 Azure Kubernetes 服务 (AKS) 群集来执行操作启用了 GPU 的推断。 推断，或模型评分，是的阶段已部署的模型用于预测。 使用 Gpu 而不高度可并行化计算 Cpu 产品/服务性能优势。

虽然此示例使用 TensorFlow 模型，可应用于任何机器学习框架，支持 Gpu 进行评分文件和环境文件较小的更改的以下步骤。 

在本文中，将执行以下步骤：

* 创建启用了 GPU 的 AKS 群集
* Tensorflow GPU 模型部署
* 颁发给已部署的模型的示例查询

## <a name="prerequisites"></a>必备组件

* Azure 机器学习服务工作区。
* Python 发行版。
* 已注册的 Tensorflow 保存模型。
    * 若要了解如何将模型注册，请参阅[部署模型](../service/how-to-deploy-and-where.md#registermodel)。

你可以完成此操作方法系列的第一部分[如何训练 TensorFlow 模型](how-to-train-tensorflow.md)，以满足所需的先决条件。

## <a name="provision-an-aks-cluster-with-gpus"></a>预配配备 Gpu 的 AKS 群集

Azure 有许多不同的 GPU 选项。 您可以使用其中任何一个的推断。 请参阅[的 N 系列 Vm 的列表](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)有关功能和成本的完整明细。

AKS 使用 Azure 机器学习服务的详细信息，请参阅[如何部署以及在何处](../service/how-to-deploy-and-where.md#deploy-aks)。

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    compute_target = ComputeTarget(workspace=ws, name=aks_name)
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
> Azure 将向你收费，只要 AKS 群集预配。 请确保在完成时删除 AKS 群集。

## <a name="write-the-entry-script"></a>编写入口脚本

将以下代码保存到工作目录为`score.py`。 此文件评分映像，因为它们已发送至你的服务。 它加载保存的 TensorFlow 模型，在每个 POST 请求时，传递到 TensorFlow 会话输入的图像，然后返回生成的分数。 其他推断框架需要不同的计分概要文件。

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
## <a name="define-the-conda-environment"></a>定义的 conda 环境

创建一个名为的 conda 环境文件`myenv.yml`以指定你的服务的依赖关系。 务必要指定您正在使用`tensorflow-gpu`以获得更快的性能。

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

创建`InferenceConfig`Gpu，并确保 CUDA 装有 Docker 映像的对象。

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

将模型部署到 AKS 群集，并等待它来创建你的服务。

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
> Azure 机器学习服务不会部署一个拥有模型`InferenceConfig`需要 GPU 来启用到不具有 GPU 群集的对象。

有关详细信息，请参阅[模型类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)。

## <a name="issue-a-sample-query-to-your-model"></a>颁发给您的模型的示例查询

将测试查询发送到已部署的模型。 当 jpeg 图像发送到模型时，它分数图像。 下面的代码示例使用一个外部实用工具函数以加载图像。 您可以在 pir 中找到相关的代码[GitHub 上的 TensorFlow 示例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py)。 

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
> 若要尽量减少延迟并优化吞吐量，请确保你的客户端终结点所在的同一 Azure 区域中。 在此示例中，在美国东部 Azure 区域中创建的 Api。

## <a name="clean-up-the-resources"></a>清理资源

在此示例使用完后，请删除你的资源。

> [!IMPORTANT]
> 基于多长时间部署 AKS 群集的 azure 帐单。 请确保使用它完成后清理。

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>后续步骤

* [部署模型上 FPGA](../service/how-to-deploy-fpga-web-service.md)
* [部署具有 ONNX 模型](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [训练的 DNN Tensorflow 模型](../service/how-to-train-tensorflow.md)
