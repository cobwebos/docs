---
title: 部署具有 GPU 的推理模型
titleSuffix: Azure Machine Learning service
description: 了解如何将深度学习模型部署为 web 服务使用 GPU 进行推理。 在本文中，Tensorflow 模型部署到的 Azure Kubernetes 服务群集。 该群集使用已启用 GPU 的虚拟机以托管 web 服务和评分推理请求。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: ec71165553a1d65ff133d605bf94255100f74e6e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388921"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>部署用于推断具有 GPU 的深度学习模型

了解如何使用 GPU 推理的机器学习模型部署为 web 服务。 推理，或模型评分，阶段已部署的模型不使用的是用于预测，最常在生产数据。

本文介绍如何使用 Azure 机器学习服务来部署示例 Tensorflow 深度学习模型添加到已启用 GPU 的虚拟机 (VM) 上的 Azure Kubernetes 服务 (AKS) 群集。 当请求被发送到服务时，该模型将使用 GPU 运行推理工作负荷。

Gpu 提供高度可并行化的计算上 Cpu 的性能优于。 已启用 GPU 的 Vm 的极好的用例包括深度学习的模型定型和推断，尤其是对于大型批处理的请求。

此示例演示如何部署 TensorFlow 保存到 Azure 机器学习的模型。 执行以下步骤：

* 创建启用了 GPU 的 AKS 群集
* Tensorflow GPU 模型部署

## <a name="prerequisites"></a>必备组件

* Azure 机器学习服务工作区
* Python 发行版
* 已注册的 Tensorflow 保存模型。 若要了解如何将模型注册，请参阅[部署模型](../service/how-to-deploy-and-where.md#registermodel)。

这篇文章基于 Jupyter notebook[部署到 AKS Tensorflow 模型](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb)。 Jupyter notebook 使用 TensorFlow 保存模型，并将其部署到 AKS 群集。 此外可以应用于任何机器学习框架，通过对评分文件和环境文件进行较小的更改支持 Gpu 的笔记本。  

## <a name="provision-an-aks-cluster-with-gpus"></a>预配配备 Gpu 的 AKS 群集

Azure 有许多不同的 GPU 选项。 您可以使用其中任何一个的推断。 请参阅[的 N 系列 Vm 的列表](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series)有关功能和成本的完整明细。

AKS 使用 Azure 机器学习服务的详细信息，请参阅[如何部署以及在何处](../service/how-to-deploy-and-where.md#deploy-aks)。

```python
# Provision AKS cluster with GPU machine
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

# Create the cluster
aks_target = ComputeTarget.create(
    workspace=ws, name=aks_name, provisioning_configuration=prov_config
)

aks_target.wait_for_deployment()
```

> [!IMPORTANT]
> Azure 将向你收费，只要 AKS 群集预配。 请确保在完成时删除 AKS 群集。

## <a name="write-the-entry-script"></a>编写入口脚本

将以下代码保存到工作目录为`score.py`。 此文件评分映像，因为它们已发送至你的服务。 它加载保存的 TensorFlow 模型，在每个 POST 请求时，传递到 TensorFlow 会话输入的图像，然后返回生成的分数。 其他推断框架需要不同的计分概要文件。

```python
import tensorflow as tf
import numpy as np
import ujson
from azureml.core.model import Model
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    global session
    global input_name
    global output_name
    
    session = tf.Session()

    model_path = Model.get_model_path('resnet50')
    model = tf.saved_model.loader.load(session, ['serve'], model_path)
    if len(model.signature_def['serving_default'].inputs) > 1:
        raise ValueError("This score.py only supports one input")
    input_name = [tensor.name for tensor in model.signature_def['serving_default'].inputs.values()][0]
    output_name = [tensor.name for tensor in model.signature_def['serving_default'].outputs.values()]
    

@rawhttp
def run(request):
    if request.method == 'POST':
        reqBody = request.get_data(False)
        resp = score(reqBody)
        return AMLResponse(resp, 200)
    if request.method == 'GET':
        respBody = str.encode("GET is not supported")
        return AMLResponse(respBody, 405)
    return AMLResponse("bad request", 500)

def score(data):
    result = session.run(output_name, {input_name: [data]})
    return ujson.dumps(result[1])

if __name__ == "__main__":
    init()
    with open("lynx.jpg", 'rb') as f: #load file for testing locally
        content = f.read()
        print(score(content))

```

## <a name="define-the-conda-environment"></a>定义的 conda 环境

创建一个名为的 conda 环境文件`myenv.yml`以指定你的服务的依赖关系。 务必要指定您正在使用`tensorflow-gpu`以获得更快的性能。

```yaml
name: aml-accel-perf
channels:
  - defaults
dependencies:
  - tensorflow-gpu = 1.12
  - numpy
  - ujson
  - pip:
    - azureml-core
    - azureml-contrib-services
```

## <a name="define-the-gpu-inferenceconfig-class"></a>定义 GPU InferenceConfig 类

创建`InferenceConfig`Gpu，并确保 CUDA 装有 Docker 映像的对象。

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='gpu-rn'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"resnet50")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   gpu_enabled=True)
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

## <a name="issue-a-sample-query-to-your-deployed-model"></a>颁发给已部署的模型的示例查询

将测试查询发送到已部署的模型。 当 jpeg 图像发送到模型时，它分数图像。

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
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
