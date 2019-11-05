---
title: 什么是 FPGA-如何部署
titleSuffix: Azure Machine Learning
description: 了解如何使用 FPGA 上运行的模型部署 web 服务 Azure 机器学习，以实现超高延迟推理。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: 5e8dc6181660f0c1545df0688e2749f8f0187027
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496896"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>什么是现场可编程的入口阵列（FPGA）以及如何部署
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍了现场可编程的入口数组（FPGA），并说明了如何使用 Azure 机器学习向 Azure FPGA 部署模型。 

FPGA 包含一组可编程的逻辑块以及由可反复配置的互连组成的层次结构。 这种互连可以在生产之后以不同方式来配置块。 与其他芯片相比，FPGA 既有可编程性，又有很好的性能。

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>Fpga 与 CPU、GPU 和 ASIC

下面的示意图和表将 FPGA 与其他处理器进行了比较。

![Azure 机器学习 FPGA 比较示意图](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|处理器||说明|
|---|:-------:|------|
|特定于应用程序的集成电路|ASIC|自定义电路（例如 Google 的 TensorFlow 处理器单元 (TPU)）的效率最高。 它们不能根据需求的变化重新配置。|
|现场可编程门阵列|FPGA|FPGA（例如 Azure 上提供的那些）提供接近于 ASIC 的性能。 它们也是灵活的，并且可以在一段时间后重新配置以实现新逻辑。|
|图形处理单元|GPU|进行 AI 计算时的常用选择。 GPU 提供并行处理功能，在进行图形渲染时速度快于 CPU。|
|中央处理单元|CPU|常规用途处理器，其性能无法胜任图形和视频处理。|

Azure 上的 Fpga 基于 Intel 的 FPGA 设备，数据科学家和开发人员使用该设备加速实时 AI 计算。 这种启用了 FPGA 的体系结构提供了高性能、灵活性和可伸缩性，可以在 Azure 上使用。

Fpga 可以实现实时推理（或模型评分）请求的低延迟。 不需要使用异步请求（批处理）。 批处理可能导致延迟，因为需要处理更多的数据。 神经处理单元的实现不需要批处理;因此，与 CPU 和 GPU 处理器相比，延迟时间可能会更低一些。

### <a name="reconfigurable-power"></a>可反复配置的功能
可以针对不同类型的机器学习模型反复配置 FPGA。 利用这种灵活性，可以更轻松地根据最优化的数值精度和所用内存模型来加速应用程序。 由于 FPGA 可以反复配置，因此可以跟上快速变化的 AI 算法的要求。

## <a name="whats-supported-on-azure"></a>Azure 支持的操作
Microsoft Azure 是世界上在 FPGA 中的最大云投资。 使用这种支持 FPGA 的体系结构，受训的神经网络就可以快速运行并降低延迟。 Azure 可以在 Fpga 上并行处理预先训练的深层神经网络（DNN），以横向扩展你的服务。 DNNs 可以预先训练，可以作为深度特征化器来传输学习内容，或者使用更新的权重进行优化。

Azure 上的 Fpga 支持：

+ 图像分类和识别方案
+ TensorFlow 部署
+ Intel FPGA 硬件 

目前提供以下 DNN 模型：
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

以下 Azure 区域提供 Fpga：
  - 美国东部
  - 东南亚
  - 欧洲西部
  - 美国西部 2

> [!IMPORTANT]
> 若要优化滞后时间和吞吐量，将数据发送到 FPGA 模型的客户端应位于上述某个区域（即模型部署到的区域）中。

**Azure vm 的 PBS 系列**包含 Intel Arria 10 fpga。 检查 Azure 配额分配时，它将显示为 "标准 PBS 系列个 vcpu"。 PB6 VM 具有六个个 vcpu 和一个 FPGA，并且它将在将模型部署到 FPGA 时由 Azure ML 自动进行设置。 它仅与 Azure ML 一起使用，并且不能运行任意轨道。 例如，你将无法通过轨道将 FPGA 与进行刷新，以进行加密、编码等。

### <a name="scenarios-and-applications"></a>方案和应用

Azure Fpga 与 Azure 机器学习集成。 Microsoft 使用 FPGA 进行 DNN 评估、必应搜索排名、软件定义网络 (SDN) 加速，以便将 CPU 解放出来完成其他任务，同时降低延迟。

以下方案使用 Fpga：
+ [自动化光学检查系统](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [地表覆盖制图](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)



## <a name="example-deploy-models-on-fpgas"></a>示例：在 Fpga 上部署模型 

你可以使用 Azure 机器学习硬件加速模型将模型部署为 Fpga 上的 web 服务。 使用 Fpga 可提供超高延迟推理，即使只使用一批。 推理或模型计分是部署模型用于预测的阶段，最常见的是生产数据。


### <a name="prerequisites"></a>必备组件

- Azure 订阅。  如果没有，请在开始前创建一个免费帐户。 立即试用[Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。

- FPGA 配额。 使用 Azure CLI 检查是否有配额：

    ```shell
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > 其他可能的位置为 ``southeastasia``、``westeurope``和 ``westus2``。

    此命令返回类似于下面的文本：

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    请确保在__CurrentValue__下至少有6个个 vcpu。

    如果没有配额，则提交[https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)的请求。

- 已安装 Azure 机器学习工作区以及用于 Python 的 Azure 机器学习 SDK。 有关详细信息，请参阅[创建工作区](how-to-manage-workspace.md)。
 
- 用于硬件加速模型的 Python SDK：

    ```shell
    pip install --upgrade azureml-accel-models
    ```


## <a name="1-create-and-containerize-models"></a>1. 创建模型并容器化

本文档将介绍如何创建 TensorFlow 图来预处理输入图像、如何在 FPGA 上使用 ResNet 50 使其成为特征化器，然后通过针对 ImageNet 数据集定型的分类器来运行这些功能。

遵照说明操作：

* 定义 TensorFlow 模型
* 转换模型
* 部署模型
* 使用已部署的模型
* 删除已部署的服务

使用[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 创建服务定义。 服务定义是一个文件，用于根据 TensorFlow 来描述图形（输入、特征化器、分类器）的管道。 部署命令会自动将定义和图压缩到 ZIP 文件中，然后将 ZIP 文件上传到 Azure Blob 存储。 DNN 已部署为在 FPGA 上运行。

### <a name="load-azure-ml-workspace"></a>加载 Azure ML 工作区

加载 Azure ML 工作区。

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>预处理图像

Web 服务的输入为 JPEG 图像。  第一步是解码 JPEG 图像并对其进行预处理。  JPEG 图像被视为字符串，结果为 tensors，这将是 ResNet 50 模型的输入。

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Load 特征化器

初始化模型并下载 ResNet50 的量化版本的 TensorFlow 检查点以用作特征化器。  您可以通过导入其他深层神经网络，将以下代码片段中的 "QuantizedResnet50" 替换为：

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>添加分类器

已在 ImageNet 数据集上训练该分类器。  [示例笔记本](https://aka.ms/aml-notebooks)集中提供了用于传输学习和培训自定义权重的示例。

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>保存模型

现在已加载预处理器、ResNet 50 特征化器和分类器，请将图形和关联的变量保存为模型。

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                               inputs={'images': in_images},
                               outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>保存输入和输出 tensors
对于模型转换和推理，将需要在预处理和分类器步骤期间创建的输入和输出 tensors。

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> 保存输入和输出 tensors，因为对于模型转换和推理请求，将需要它们。

可用模型和相应的默认分类器输出 tensors 如下所示，如果您使用了默认分类器，则可用于推理。

+ Resnet50, QuantizedResnet50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, QuantizedDensenet121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, QuantizedVgg16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, QuantizedSsdVgg
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>注册模型

在 Azure Blob 存储中结合使用 SDK 和 ZIP 文件来[注册](./concept-model-management-and-deployment.md)模型。 添加标记和有关模型的其他元数据有助于跟踪定型模型。

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

如果已注册某一模型并想要加载它，则可以检索它。

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>转换模型

将 TensorFlow 图形转换为开放式神经网络交换格式（[ONNX](https://onnx.ai/)）。  你将需要提供输入和输出 tensors 的名称，并且你的客户端将在你使用 web 服务时使用这些名称。

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>创建 Docker 映像

转换后的模型和所有依赖项都将添加到 Docker 映像。  然后，可以部署并实例化此 Docker 映像。  支持的部署目标包括云中的 AKS 或边缘设备，如[Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)。  你还可以为已注册的 Docker 映像添加标记和说明。

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

按标记列出图像并获取详细日志进行任何调试。

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. 部署到云或边缘

### <a name="deploy-to-the-cloud"></a>部署到云

若要将模型部署为大规模生产 Web 服务，请使用 Azure Kubernetes 服务 (AKS)。 你可以使用 Azure 机器学习 SDK、CLI 或[Azure 机器学习 studio](https://ml.azure.com)创建一个新的。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2”. If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

AKS 部署可能需要大约15分钟。  检查部署是否成功。

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

将容器部署到 AKS 群集。
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>测试云服务
Docker 映像支持 gRPC 和 TensorFlow 服务 "预测" API。  使用示例客户端调入 Docker 映像，从模型中获取预测。  示例客户端代码可用：
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

如果要使用 TensorFlow 服务，则可以[下载示例客户端](https://www.tensorflow.org/serving/setup)。

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize AzureML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

由于此分类器是在[ImageNet](http://www.image-net.org/)数据集上定型的，因此将这些类映射到用户可读的标签。

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>清理服务
删除 web 服务、图像和模型（必须按此顺序完成，因为存在依赖关系）。

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>部署到本地边缘服务器

所有[Azure Data Box Edge 设备](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
)都包含用于运行模型的 FPGA。  在 FPGA 上，一次只能运行一个模型。  若要运行不同的模型，只需部署一个新的容器。 有关说明和示例代码，请参阅[此 Azure 示例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)。

## <a name="secure-fpga-web-services"></a>保护 FPGA Web 服务

若要保护 FPGA web 服务，请参阅[安全 web 服务](how-to-secure-web-service.md)文档。

## <a name="next-steps"></a>后续步骤

查看以下笔记本、视频和博客：

+ 几个[示例笔记本](https://aka.ms/aml-accel-models-notebooks)。

+ [Hyperscale hardware: ML at scale on top of Azure + FPGA : Build 2018 (video)](https://channel9.msdn.com/events/Build/2018/BRK3202)（超大规模硬件：基于 Azure + FPGA：内部版本 2018 的大规模机器学习（视频））

+ [Inside the Microsoft FPGA-based configurable cloud (video)](https://channel9.msdn.com/Events/Build/2017/B8063)（深入了解基于 Microsoft FPGA 的可配置云（视频））

+ [用于实时 AI 的 Project Brainwave：项目主页](https://www.microsoft.com/research/project/project-brainwave/)
