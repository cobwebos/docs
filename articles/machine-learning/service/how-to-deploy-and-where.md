---
title: 如何以及在何处部署模型
titleSuffix: Azure Machine Learning service
description: 了解如何以及在何处部署 Azure 机器学习服务模型，包括：Azure 容器实例、Azure Kubernetes 服务、Azure IoT Edge 和现场可编程门阵列。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 1528b5e92e1952bf85799afd71bd5dac16aedcf4
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878292"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>使用 Azure 机器学习服务部署模型

本文档介绍了如何将模型作为 Web 服务部署在 Azure 云或 IoT Edge 设备中。 

## <a name="compute-targets-for-deployment"></a>计算目标的部署

使用 Azure 机器学习 SDK 将训练的模型部署到以下位置：

| 计算目标 | 部署类型 | 描述 |
| ----- | ----- | ----- |
| [Azure Kubernetes 服务 (AKS)](#aks) | 实时推理 | 非常适合用于大规模生产部署。 提供了自动缩放和快速的响应时间。 |
| [Azure 机器学习计算 (amlcompute)](#azuremlcompute) | 批处理推理 | 在无服务器计算上运行批处理预测。 支持的普通和低优先级 Vm。 |
| [Azure 容器实例 (ACI)](#aci) | 测试 | 适用于开发或测试。 **不适用于生产工作负荷。** |
| [Azure IoT Edge](#iotedge) | （预览版）IoT 模块 | 在 IoT 设备上部署模型。 推断在设备上进行。 |
| [现场可编程门阵列 (FPGA)](#fpga) | （预览版）Web 服务 | 以超低的延迟进行实时推断。 |

## <a name="deployment-workflow"></a>部署工作流

为所有计算目标部署模型的过程类似：

1. 训练并注册模型。
1. 配置并注册使用模型的映像。
1. 将映像部署到计算目标。
1. 测试部署

以下视频演示如何部署到 Azure 容器实例：

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


有关部署工作流涉及的概念的详细信息，请参阅[使用 Azure 机器学习服务管理、部署和监视模型](concept-model-management-and-deployment.md)。

## <a name="prerequisites-for-deployment"></a>部署的先决条件

[!INCLUDE [aml-prereq](../../../includes/aml-prereq.md)]

- 定型的模型。 如果没有已训练的模型，请使用[训练模型](tutorial-train-models-with-aml.md)教程中的步骤训练一个模型，并将其注册到 Azure 机器学习服务。

    > [!NOTE]
    > 尽管 Azure 机器学习服务可以适用于将加载到 Python 3 中的任何泛型模型，此文档中的示例演示如何使用 Python pickle 格式存储的模型。
    >
    > 有关使用 ONNX 模型的详细信息，请参阅 [ONNX 和 Azure 机器学习](how-to-build-deploy-onnx.md)文档。

## <a id="registermodel"></a> 注册已训练的模型

模型注册表是在 Azure 云中存储和组织已训练模型的一种方式。 模型将注册到 Azure 机器学习服务工作区中。 可以使用 Azure 机器学习或其他服务训练模型。 下面的代码演示如何注册文件中的模型，请设置名称、 标记和说明：

```python
from azureml.core.model import Model

model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**时间估计**：大约 10 秒。

注册模型的示例，请参阅[训练的图像分类器](tutorial-train-models-with-aml.md)。

有关详细信息，请参阅 [Model 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)的参考文档。

## <a id="configureimage"></a> 创建并注册映像

部署的模型打包为映像。 该映像包含运行模型所需的依赖项。

对于 **Azure 容器实例**、**Azure Kubernetes 服务**和 **Azure IoT Edge** 部署，将使用 [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) 类来创建映像配置。 然后，使用映像配置创建新的 Docker 映像。

以下代码演示如何创建新的映像配置：

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml"}
                                                 )
```

**时间估计**：大约 10 秒。

下表描述了此示例中的重要参数：

| 参数 | 描述 |
| ----- | ----- |
| `execution_script` | 指定一个 Python 脚本，用于接收提交到服务的请求。 在此示例中，该脚本包含在 `score.py` 文件中。 有关详细信息，请参阅[执行脚本](#script)部分。 |
| `runtime` | 指示映像使用 Python。 另一个选项是将 Python 与 Apache Spark 配合使用的 `spark-py`。 |
| `conda_file` | 用于提供 conda 环境文件。 此文件定义已部署的模型的 conda 环境。 有关创建此文件的详细信息，请参阅[创建环境文件 (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file)。 |

有关创建映像配置的示例，请参阅[部署的图像分类器](tutorial-deploy-models-with-aml.md)。

有关详细信息，请参阅 [ContainerImage 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)的参考文档

### <a id="script"></a> 执行脚本

执行脚本接收提交到已部署映像的数据，并将此数据传递给模型。 然后，该脚本接收模型返回的响应，并将该响应返回给客户端。 **该脚本是特定于您的模型**; 它必须了解模型需要并返回的数据。 适用于图像分类模型的示例脚本，请参阅[部署的图像分类器](tutorial-deploy-models-with-aml.md)。

脚本包含两个函数，加载和运行模型：

* `init()`:此函数通常将模型载入全局对象。 此函数只能在 Docker 容器启动时运行一次。

* `run(input_data)`:此函数使用模型来基于输入数据预测值。 运行的输入和输出通常使用 JSON 进行序列化和反序列化。 也可以处理原始二进制数据。 可以在将数据发送到模型之前或者返回给客户端之前转换数据。

#### <a name="working-with-json-data"></a>处理 JSON 数据

以下示例脚本接受并返回 JSON 数据。 `run` 函数将数据从 JSON 转换为模型所需的格式，然后在返回响应之前将响应转换为 JSON：

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

# load the model
def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

# Passes data to the model and returns the prediction
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

#### <a name="working-with-binary-data"></a>处理二进制数据

如果模型接受__二进制数据__，请使用 `AMLRequest`、`AMLResponse` 和 `rawhttp`。 以下脚本示例接受二进制数据，并返回 POST 请求的反向字节。 对于 GET 请求，它在响应正文中返回完整 URL：

```python
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

# Accept and return binary data
@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    # handle GET requests
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    # handle POST requests
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `azureml.contrib` 命名空间会频繁更改，因为我们正在改进服务。 因此，此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持。
>
> 如果需要在本地开发环境中对此进行测试，可以使用以下命令安装 `contrib` 命名空间中的组件：
> ```shell
> pip install azureml-contrib-services
> ```

### <a id="createimage"></a> 注册映像

创建映像配置后，可以使用它来注册映像。 此映像存储在工作区的容器注册表中。 创建后，可将同一映像部署到多个服务。

```python
# Register the image from the image configuration
image = ContainerImage.create(name = "myimage",
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**时间估计**：大约 3 分钟。

同时注册多个映像时，会自动控制映像的版本。 例如，为注册为 `myimage` 的第一个映像分配 ID `myimage:1`。 下一次将某个映像注册为 `myimage` 时，新映像的 ID 是 `myimage:2`。

有关详细信息，请参阅 [ContainerImage 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)的参考文档。

## <a id="deploy"></a> 将部署为 web 服务

部署过程根据要部署到的计算目标而略有不同。 使用以下部分中的信息了解如何部署到：

| 计算目标 | 部署类型 | 描述 |
| ----- | ----- | ----- |
| [Azure Kubernetes 服务 (AKS)](#aks) | Web 服务 （实时推理）| 非常适合用于大规模生产部署。 提供了自动缩放和快速的响应时间。 |
| [Azure 机器学习计算](#azuremlcompute) | Web 服务 （批处理推理）| 在无服务器计算上运行批处理预测。 支持的普通和低优先级 Vm。 |
| [Azure 容器实例 (ACI)](#aci) | Web 服务 （开发/测试）| 适用于开发或测试。 **不适用于生产工作负荷。** |
| [Azure IoT Edge](#iotedge) | （预览版）IoT 模块 | 在 IoT 设备上部署模型。 推断在设备上进行。 |
| [现场可编程门阵列 (FPGA)](#fpga) | （预览版）Web 服务 | 以超低的延迟进行实时推断。 |

> [!IMPORTANT]
> 将模型部署为 Web 服务时，目前不支持跨域资源共享 (CORS)。

本节在此示例使用[deploy_from_image](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-)，则需要您在执行部署之前注册的模型和图像。 有关其他部署方法的详细信息，请参阅[部署](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-)并[deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-)。

### <a id="aci"></a> 将部署到 Azure 容器实例 （开发测试）

如果满足以下一个或多个条件，请使用 Azure 容器实例将模型部署为 Web 服务：

- 你需要快速部署并验证你的模型。 ACI 部署在 5 分钟内完成。
- 正在测试一个开发中的模型。 若要查看 ACI 的配额和区域可用性，请参阅文档 [Azure 容器实例的配额和区域可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)。

若要部署到 Azure 容器实例，请使用以下步骤：

1. 定义部署配置。 此配置取决于您的模型的要求。 以下示例定义一种使用 1 个 CPU 核心和 1 GB 内存的配置：

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. 若要部署在本文档的[创建映像](#createimage)部分创建的映像，请使用以下代码：

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **时间估计**：大约需要 5 分钟。

有关详细信息，请参阅 [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) 和 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) 类的参考文档。

### <a id="aks"></a> 将部署到 Azure Kubernetes 服务 （生产）

若要将模型部署为大规模生产 Web 服务，请使用 Azure Kubernetes 服务 (AKS)。 可以使用现有 AKS 群集，也可使用 Azure 机器学习 SDK、CLI 或 Azure 门户创建新群集。

对于工作区而言，创建 AKS 群集是一次性过程。 可以将此群集重复用于多个部署。

> [!IMPORTANT]
> 如果删除该群集，则在下次需要进行部署时必须创建新群集。

Azure Kubernetes 服务提供了以下功能：

* 自动缩放
* 日志记录
* 模型数据集合
* Web 服务的快速响应时间
* TLS 终止
* Authentication

#### <a name="autoscaling"></a>自动缩放

可以通过设置控制自动缩放`autoscale_target_utilization`， `autoscale_min_replicas`，和`autoscale_max_replicas`适用于 AKS 的 web 服务。 下面的示例演示如何启用自动缩放：

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

若要增加/减少的决策基于利用当前的容器副本。 处于忙碌状态 （正在处理请求） 的副本数除以总的当前副本数是当前利用率。 如果此数目超过目标利用率，会创建更多副本。 如果是较低，会减少副本。 默认情况下，目标利用率为 70%。

若要添加副本的决策的制作和快速实现 （约 1 秒）。 若要删除副本的决策需要较长 （大约 1 分钟）。 此行为会保留周围的空闲副本一分钟的时间在新的请求到达时，它们可以处理的情况下。

您可以使用以下代码计算所需的副本：

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

有关详细信息设置`autoscale_target_utilization`， `autoscale_max_replicas`，并`autoscale_min_replicas`，请参阅[AksWebservice.deploy_configuration](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-)引用。

#### <a name="create-a-new-cluster"></a>创建新群集

若要创建新的 Azure Kubernetes 服务群集，请使用以下代码：

> [!IMPORTANT]
> 创建 AKS 群集是工作区的一次性过程。 创建后，即可重复使用此群集进行多个部署。 如果删除该群集或包含该群集的资源组，则在下次需要进行部署时必须创建新群集。
> 对于 [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)，如果为 agent_count 和 vm_size 选择自定义值，则需要确保 agent_count 乘以 vm_size 的结果大于或等于 12 个虚拟 CPU。 例如，如果对 vm_size 使用“Standard_D3_v2”（有 4 个虚拟 CPU），则应该为 agent_count 选择 3 或更大的数字。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

**时间估计**：大约 20 分钟。

#### <a name="use-an-existing-cluster"></a>使用现有群集

如果已在 Azure 订阅中有 AKS 群集并且它 1.12 版。 # # 和具有至少为 12 的虚拟 Cpu，可以使用它来部署你的映像。 下面的代码演示如何将附加现有的 AKS 1.12。 # # 为你的工作区的群集：

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**时间估计**：大约 3 分钟。

创建 AKS 群集外部 Azure 机器学习 SDK 的详细信息，请参阅以下文章：

* [创建 AKS 群集](https://docs.microsoft.com/cli/azure/aks?toc=%2Fen-us%2Fazure%2Faks%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [创建 AKS 群集 （门户）](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

#### <a name="deploy-the-image"></a>部署映像

若要将本文档的[创建映像](#createimage)部分中创建的映像部署到 Azure Kubernetes 服务器群集，请使用以下代码：

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
service = Webservice.deploy_from_image(workspace = ws,
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
service.wait_for_deployment(show_output = True)
print(service.state)
```

**时间估计**：大约 3 分钟。

有关详细信息，请参阅 [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) 和 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) 类的参考文档。

### <a id="azuremlcompute"></a> 使用 Azure 机器学习计算的推理

创建和管理 Azure 机器学习服务的 azure 机器学习计算目标。 它们可以用于从 Azure 机器学习管道批处理预测。

使用 Azure 机器学习计算的批处理推理的演练，请阅读[如何运行批预测](how-to-run-batch-predictions.md)文档。


### <a id="fpga"></a>部署到现场可编程门阵列 (FPGA)

使用 Project Brainwave 可以在实时推断请求时实现超低的延迟。 Project Brainwave 可加快部署于 Azure 云中现场可编程门阵列上的深度神经网络 (DNN) 的速度。 常用 DNN 可用作传输学习的特征化器，也可以使用基于你自己的数据训练的权重进行自定义。

有关使用 Project Brainwave 部署模型的演练，请参阅[部署到 FPGA](how-to-deploy-fpga-web-service.md) 文档。

## <a name="define-schema"></a>定义架构

可用于自定义修饰器[OpenAPI](https://swagger.io/docs/specification/about/)规范生成和输入键入操作时部署 web 服务。 在`score.py`文件中，为其中一个已定义的类型对象，提供的输入和/或构造函数中的输出的示例，以及使用类型和示例来自动生成的架构。 目前支持以下类型：

* `pandas`
* `numpy`
* `pyspark`
* 标准 Python

首先确保的必要依赖项`inference-schema`包中包含你`env.yml`conda 环境文件。 此示例使用`numpy`参数类型的架构，因此额外的 pip`[numpy-support]`也会安装。

```python
%%writefile myenv.yml
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

接下来，修改`score.py`要导入文件`inference-schema`包。 定义输入和输出中的示例格式`input_sample`和`output_sample`变量，表示 web 服务的请求和响应格式。 使用这些示例中输入和输出函数的修饰器`run()`函数。

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)


input_sample = np.array([[1.8]])
output_sample = np.array([43638.88])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

按照正常图像注册和 web 服务部署过程使用更新后`score.py`文件中，从服务中检索 Swagger uri。 请求此 uri 将返回`swagger.json`文件。

```python
service.wait_for_deployment(show_output=True)
print(service.swagger_uri)
```



创建新的映像时，必须手动更新要使用新映像的每个服务。 若要更新 Web 服务，请使用 `update` 方法。 以下代码演示如何将 Web 服务更新为使用新映像：

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

有关详细信息，请参阅 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) 类的参考文档。

## <a name="test-web-service-deployments"></a>测试 web 服务部署

若要测试 Web 服务部署，可以使用 Webservice 对象的 `run` 方法。 以下示例将某个 JSON 文档设置为 Web 服务，并显示结果。 发送的数据必须符合模型的预期。 在此示例中，数据格式与糖尿病模型预期的输入相匹配。

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

Web 服务是一个 REST API，因此，可以在各种编程语言中创建客户端应用程序。 有关详细信息，请参阅[创建客户端应用程序以使用 Web 服务](how-to-consume-web-service.md)。

## <a id="update"></a> 更新 Web 服务

创建新的映像时，必须手动更新要使用新映像的每个服务。 若要更新 Web 服务，请使用 `update` 方法。 以下代码演示如何将 Web 服务更新为使用新映像：

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

有关详细信息，请参阅 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) 类的参考文档。

## <a id="iotedge"></a>部署到 Azure IoT Edge

Azure IoT Edge 设备是运行 Azure IoT Edge 运行时的基于 Linux 或 Windows 的设备。 使用 Azure IoT 中心，你可以部署机器学习模型到这些设备作为 IoT Edge 模块。 将模型部署到 IoT Edge 设备允许设备直接使用模型，而不必将数据发送到云进行处理。 你可以获得更快的响应时间，执行更少的数据传输。

Azure IoT Edge 模块将从容器注册表部署到设备。 从模型创建映像时，该映像将存储在工作区的容器注册表中。

> [!IMPORTANT]
> 在本部分中的信息假定你已熟悉 Azure IoT 中心和 Azure IoT Edge 模块。 虽然在本部分中的信息的一些特定于 Azure 机器学习服务，在 Azure IoT 服务中进行大多数进程将部署到 edge 设备。
>
> 如果您不熟悉 Azure IoT，请参阅[Azure IoT 基础](https://docs.microsoft.com/azure/iot-fundamentals/)并[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/)的基本信息。 若要了解有关特定操作的详细信息，然后使用本部分中的其他链接。

### <a name="set-up-your-environment"></a>设置环境

* 开发环境。 有关详细信息，请参阅[如何配置开发环境](how-to-configure-environment.md)文档。

* Azure 订阅中的 [Azure IoT 中心](../../iot-hub/iot-hub-create-through-portal.md)。

* 定型的模型。 有关如何训练模型的示例，请参阅[使用 Azure 机器学习训练图像分类模型](tutorial-train-models-with-aml.md)文档。 [在适用于 Azure IoT Edge GitHub 存储库的 AI 工具包](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial)上提供了预训练模型。

### <a id="getcontainer"></a> 获取容器注册表凭据

若要将 IoT Edge 模块部署到设备，Azure IoT 需要容器注册表（Azure 机器学习服务在其中存储 Docker 映像）的凭据。

可以通过两种方式来获取凭据：

+ **在 Azure 门户中**：

  1. 登录到 [Azure 门户](https://portal.azure.com/signin/index)。

  1. 转到 Azure 机器学习服务工作区并选择“概述”。 要转到容器注册表设置，请选择“注册表”链接。

     ![容器注册表项的图片](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. 进入容器注册表后，选择“访问密钥”，然后启用管理员用户。

     ![访问密钥屏幕的图片](./media/how-to-deploy-and-where/findaccesskey.png)

  1. 保存“登录服务器”、“用户名”和“密码”的值。

+ **使用 Python 脚本**：

  1. 在运行的上述代码后面使用以下 Python 脚本来创建容器：

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. 保存 ContainerURL、servername、username 和 password 的值。

     必须使用这些凭据向 IoT Edge 设备提供对专用容器注册表中映像的访问权限。

### <a name="prepare-the-iot-device"></a>准备 IoT 设备

使用 Azure IoT 中心注册你的设备，然后在设备上安装 IoT Edge 运行时。 如果您不熟悉此过程，请参阅[快速入门：将第一个 IoT Edge 模块部署到 Linux x64 设备](../../iot-edge/quickstart-linux.md)。

注册设备的其他方法包括：

* [Azure 门户](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)
* [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-cli)
* [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-vscode)

### <a name="deploy-the-model-to-the-device"></a>将模型部署到设备

若要将模型部署到设备，请使用收集的注册表信息[获取容器注册表凭据](#getcontainer)模块部署使用的部分将指导的 IoT Edge 模块。 例如，当[从 Azure 门户部署 Azure IoT Edge 模块](../../iot-edge/how-to-deploy-modules-portal.md)，则必须配置__注册表设置__设备。 使用__登录服务器__，__用户名__，并__密码__工作区容器注册表。

您还可以使用部署[Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli)并[Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-vscode)。

## <a name="clean-up"></a>清理

若要删除已部署的 Web 服务，请使用 `service.delete()`。

若要删除映像，请使用 `image.delete()`。

若要删除已注册的模型，请使用 `model.delete()`。

有关详细信息，请参阅 [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)、[Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--) 和 [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--) 的参考文档。

## <a name="next-steps"></a>后续步骤

* [部署故障排除](how-to-troubleshoot-deployment.md)
* [使用 SSL 保护 Azure 机器学习 Web 服务](how-to-secure-web-service.md)
* [使用机器学习模型部署为 web 服务](how-to-consume-web-service.md)
* [如何运行批预测](how-to-run-batch-predictions.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
* [Azure 机器学习服务 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Azure 虚拟网络中使用 Azure 机器学习服务](how-to-enable-virtual-network.md)
* [用于构建推荐系统的最佳实践](https://github.com/Microsoft/Recommenders)
* [在 Azure 上生成实时建议 API](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
