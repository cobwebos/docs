---
title: 什么是 FPGA - 如何部署
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习通过在 FPGA 上运行模型部署 Web 服务来实现超低延迟推理。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 8569f4751c54d7b37aa15737a9b3f7f394c7e26e
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983578"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>什么是现场可编程门阵列 (FPGA) 以及如何部署
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍了现场可编程的入口数组（FPGA），并说明了如何使用[Azure 机器学习](overview-what-is-azure-ml.md)向 Azure FPGA 部署模型。

FPGA 包含一组可编程的逻辑块以及由可反复配置的互连组成的层次结构。 这种互连可以在生产之后以不同方式来配置块。 与其他芯片相比，FPGA 既有可编程性，又有很好的性能。

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>Fpga 与 CPU、GPU 和 ASIC

下面的示意图和表将 FPGA 与其他处理器进行了比较。

![Azure 机器学习 FPGA 比较示意图](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|处理器||说明|
|---|:-------:|------|
|特定于应用程序的集成电路|ASIC|自定义电路（例如 Google 的 TensorFlow 处理器单元 (TPU)）的效率最高。 它们不能根据需求的变化重新配置。|
|现场可编程门阵列|FPGA|FPGA（例如 Azure 上提供的那些）提供接近于 ASIC 的性能。 它们也是灵活的，并且可以在一段时间后重新配置以实现新逻辑。|
|图形处理单元|GPU|进行 AI 计算时的常用选择。 GPU 提供并行处理功能，在进行图形渲染时速度快于 CPU。|
|中央处理单元|CPU|常规用途处理器，其性能无法胜任图形和视频处理。|

Azure 上的 FPGA 基于 Intel 的 FPGA 设备，数据科学家和开发人员使用这种设备来加快实时 AI 计算速度。 这种启用了 FPGA 的体系结构提供了高性能、灵活性和可伸缩性，可以在 Azure 上使用。

可以通过 FPGA 确保实时推理（或模型评分）请求的低延迟。 不需要使用异步请求（批处理）。 批处理可能导致延迟，因为需要处理更多的数据。 实现神经处理单元不需批处理，因此与 CPU 和 GPU 处理器相比，其延迟降低很多倍。

### <a name="reconfigurable-power"></a>可反复配置的功能
可以针对不同类型的机器学习模型反复配置 FPGA。 利用这种灵活性，可以更轻松地根据最优化的数值精度和所用内存模型来加速应用程序。 由于 FPGA 可以反复配置，因此可以跟上快速变化的 AI 算法的要求。

## <a name="whats-supported-on-azure"></a>Azure 支持的操作
Microsoft Azure 是世界上在 FPGA 中的最大云投资。 使用这种支持 FPGA 的体系结构，受训的神经网络就可以快速运行并降低延迟。 Azure 可以在 FPGA 之间并行化预先训练的深度神经网络 (DNN) 来横向扩展服务。 DNNs 可以预先训练，可以作为深度特征化器来传输学习内容，或者使用更新的权重进行优化。

Azure 上的 FPGA 支持：

+ 图像分类和识别方案
+ TensorFlow 部署（需要 Tensorflow 1.x）
+ Intel FPGA 硬件

当前提供了以下 DNN 模型：
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

以下 Azure 区域提供 Fpga：
  - 美国东部
  - 东南亚
  - 西欧
  - 美国西部 2

> [!IMPORTANT]
> 若要优化延迟和吞吐量，将数据发送到 FPGA 模型的客户端应位于上述某一区域（即部署模型的区域）。

Azure VM 的 PBS 系列包含 Intel Arria 10 FPGA****。 查看 Azure 配额分配时，它将显示为“标准 PBS 系列 vCPU”。 PB6 VM 具有 6 个 vCPU 和 1 个 FPGA，并且向 FPGA 部署模型时它将由 Azure ML 自动预配。 它仅可用于 Azure ML，无法运行任意位流。 例如，你将无法使用位流刷新 FPGA 以进行加密、编码等。

### <a name="scenarios-and-applications"></a>方案和应用

Azure FPGA 与 Azure 机器学习集成。 Microsoft 使用 FPGA 进行 DNN 评估、必应搜索排名、软件定义网络 (SDN) 加速，以便将 CPU 解放出来完成其他任务，同时降低延迟。

在以下情况下，请使用 FPGA：
+ [自动光纤检查系统](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [土地覆盖贴图](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-models-on-fpgas"></a>在 FPGA 上部署模型

你可以使用[Azure 机器学习硬件加速模型](https://docs.microsoft.com/python/api/azureml-accel-models/azureml.accel?view=azure-ml-py)将模型部署为 fpga 上的 web 服务。 使用 FPGA 可实现超低的延迟推理，即使只有一个批数据大小。 推理或模型评分是将部署的模型用于预测（通常针对生产数据）的阶段。

### <a name="prerequisites"></a>先决条件

- Azure 订阅。  如果还没有该订阅，可以在开始前创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

- FPGA 配额。 使用 Azure CLI 检查是否有配额：

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > 其他可能的位置为``southeastasia``、 ``westeurope``和``westus2``。

    此命令会返回如下所示的文本：

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    确保在 CurrentValue 下至少有 6 个 vCPU____。

    如果没有配额，则提交请求[https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)。

- 已安装 Azure 机器学习工作区以及用于 Python 的 Azure 机器学习 SDK。 有关详细信息，请参阅[创建工作区](how-to-manage-workspace.md)。
 
- 用于硬件加速模型的 Python SDK：

    ```bash
    pip install --upgrade azureml-accel-models[cpu]
    ```

### <a name="1-create-and-containerize-models"></a>1. 创建模型并容器化

本文档将介绍如何创建 TensorFlow 图来预处理输入图像，在 FPGA 上使用 ResNet 50 对其进行特征化，然后通过在 ImageNet 数据集上训练的分类器来运行这些功能。

遵照说明操作：

* 定义 TensorFlow 模型
* 转换模型
* 部署模型
* 使用已部署的模型
* 删除已部署的服务

使用[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 创建服务定义。 服务定义是一个文件，用于根据 TensorFlow 来描述图形（输入、特征化器、分类器）的管道。 部署命令会自动将定义和图压缩到 ZIP 文件中，然后将 ZIP 文件上传到 Azure Blob 存储。 DNN 已经部署，可以在 FPGA 上运行。

1. 加载 Azure 机器学习工作区

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

2. 预处理映像。 Web 服务的输入为 JPEG 图像。  第一步是解码 JPEG 图像并对其进行预处理。  JPEG 图像被视为字符串，结果为张量，这些张量将是 ResNet 50 模型的输入。

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Load 特征化器。 初始化模型并下载 ResNet50 的量化版本的 TensorFlow 检查点以用作特征化器。  可以通过导入其他深度神经网络，将代码片段中的“QuantizedResnet50”替换为以下项：

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

1. 添加分类器。 已在 ImageNet 数据集上训练该分类器。  [示例笔记本](https://aka.ms/aml-notebooks)集中提供了用于转移学习和训练自定义权重的多个示例。

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. 保存模型。 现已加载预处理器、ResNet 50 特征化器和分类器，请将图形和关联变量保存为模型。

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

1. 保存输入和输出 tensors。 模型转换和推理将需要预处理和分类器步骤期间创建的输入和输出张量。

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   > [!IMPORTANT]
   > 保存输入和输出张量，因为模型转换和推理请求时需要它们。

   可用模型和相应的默认分类器输出张量如下所示，如果使用了默认分类器，则以下可用于推理。

   + Resnet50、QuantizedResnet50
     ```python
     output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
     ```
   + Resnet152、QuantizedResnet152
     ```python
     output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
     ```
   + Densenet121、QuantizedDensenet121
     ```python
     output_tensors = "classifier/densenet121/predictions/Softmax:0"
     ```
   + Vgg16、QuantizedVgg16
     ```python
     output_tensors = "classifier/vgg_16/fc8/squeezed:0"
     ```
   + SsdVgg、QuantizedSsdVgg
     ```python
     output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
     ```

1. 在 Azure Blob 存储中结合使用 SDK 和 ZIP 文件来[注册](concept-model-management-and-deployment.md)模型。 添加模型的标记和其他元数据有助于跟踪已训练的模型。

   ```python
   from azureml.core.model import Model

   registered_model = Model.register(workspace=ws,
                                     model_path=model_save_path,
                                     model_name=model_name)

   print("Successfully registered: ", registered_model.name,
         registered_model.description, registered_model.version, sep='\t')
   ```

   如果已注册某一模型并想将其加载，可以检索它。

   ```python
   from azureml.core.model import Model
   model_name = "resnet50"
   # By default, the latest version is retrieved. You can specify the version, i.e. version=1
   registered_model = Model(ws, name="resnet50")
   print(registered_model.name, registered_model.description,
         registered_model.version, sep='\t')
   ```

1. 将 TensorFlow 图形转换为 Open Neural Network Exchange 格式 ([ONNX](https://onnx.ai/))。  需要提供输入和输出张量的名称，并且在你使用 Web 服务时，客户端将使用这些名称。

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

1. 从转换后的模型和所有依赖项创建 Docker 映像。  然后，可以部署并实例化此 Docker 映像。  支持的部署目标包括云中的 AKS 或 [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) 等边缘设备。  还可以为已注册的 Docker 映像添加标记和说明。

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

   按标记列出映像并获取任意调试的详细日志。

   ```python
   for i in Image.list(workspace=ws):
       print('{}(v.{} [{}]) stored at {} with build log {}'.format(
           i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
   ```

### <a name="2-deploy-to-cloud-or-edge"></a>2. 部署到云或边缘

若要将模型部署为大规模生产 Web 服务，请使用 Azure Kubernetes 服务 (AKS)。 可以使用 Azure 机器学习 SDK、CLI 或 [Azure 机器学习工作室](https://ml.azure.com)新建一个。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

AKS 部署可能需要大约 15 分钟。  检查部署是否成功。

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
Docker 映像支持 gRPC 和 TensorFlow Serving“预测”API。  使用示例客户端调入 Docker 映像，从模型中获取预测。  示例客户端代码可用：
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

如果要使用 TensorFlow Serving，可以[下载示例客户端](https://www.tensorflow.org/serving/setup)。

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

由于此分类器是在 [ImageNet](http://www.image-net.org/) 数据集上训练的，因此将这些类映射到可读标签。

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
删除 Web 服务、映像和模型（由于存在依赖项，因而必须按此顺序完成）。

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>部署到本地边缘服务器

所有 [Azure Data Box Edge 设备](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
)都包含用于运行模型的 FPGA。  在 FPGA 上，一次只能运行一个模型。  若要运行另一模型，只需部署一个新容器。 有关说明和示例代码，请参阅[此 Azure 示例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)。

## <a name="secure-fpga-web-services"></a>保护 FPGA Web 服务

若要保护 FPGA Web 服务，请参阅[保护 Web 服务](how-to-secure-web-service.md)文档。

## <a name="next-steps"></a>后续步骤

请访问以下笔记本、视频和博客：

+ 若干[示例笔记本](https://aka.ms/aml-accel-models-notebooks)

+ [超大规模硬件：大规模在 Azure + FPGA 之上的 ML：内部版本2018（视频）](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Inside the Microsoft FPGA-based configurable cloud (video)](https://channel9.msdn.com/Events/Build/2017/B8063)（深入了解基于 Microsoft FPGA 的可配置云（视频））

+ [用于实时 AI 的 Project Brainwave：项目主页](https://www.microsoft.com/research/project/project-brainwave/)
