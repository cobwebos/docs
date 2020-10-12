---
title: 将 ML 模型部署到 Fpga
titleSuffix: Azure Machine Learning
description: 了解字段可编程的入口数组。 你可以使用 Azure 机器学习在 FPGA 中部署 web 服务，以实现超低延迟推理。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 09/24/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2, devx-track-python
ms.openlocfilehash: 5d7956b5538b272454f3f55bcda84188c946e978
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328422"
---
# <a name="deploy-ml-models-to-field-programmable-gate-arrays-fpgas-with-azure-machine-learning"></a>向现场可编程的入口阵列部署 ML 模型 (Fpga) 与 Azure 机器学习 

本文介绍了 Fpga 以及如何使用[Azure 机器学习](overview-what-is-azure-ml.md)中的[硬件加速模型 PYTHON 包](https://docs.microsoft.com/python/api/azureml-accel-models/azureml.accel?view=azure-ml-py&preserve-view=true)将 ML 模型部署到 Azure FPGA。

## <a name="what-are-fpgas"></a>什么是 Fpga？
FPGA 包含一组可编程的逻辑块以及由可反复配置的互连组成的层次结构。 这种互连可以在生产之后以不同方式来配置块。 与其他芯片相比，FPGA 既有可编程性，又有很好的性能。 

可以通过 FPGA 确保实时推理（或模型评分）请求的低延迟。 不需要使用异步请求（批处理）。 批处理可能导致延迟，因为需要处理更多的数据。 实现神经处理单元不需批处理，因此与 CPU 和 GPU 处理器相比，其延迟降低很多倍。

可以针对不同类型的机器学习模型反复配置 FPGA。 利用这种灵活性，可以更轻松地根据最优化的数值精度和所用内存模型来加速应用程序。 由于 FPGA 可以反复配置，因此可以跟上快速变化的 AI 算法的要求。

![Azure 机器学习 FPGA 比较示意图](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|处理器| 缩写 |说明|
|---|:-------:|------|
|特定于应用程序的集成电路|ASIC|自定义电路（例如 Google 的 TensorFlow 处理器单元 (TPU)）的效率最高。 它们不能根据需求的变化重新配置。|
|现场可编程门阵列|FPGA|FPGA（例如 Azure 上提供的那些）提供接近于 ASIC 的性能。 它们也是灵活的，并且可以在一段时间后重新配置以实现新逻辑。|
|图形处理单元|GPU|进行 AI 计算时的常用选择。 GPU 提供并行处理功能，在进行图形渲染时速度快于 CPU。|
|中央处理单元|CPU|常规用途处理器，其性能无法胜任图形和视频处理。|

## <a name="fpga-support-in-azure"></a>Azure 中的 FPGA 支持

Microsoft Azure 是世界上在 FPGA 中的最大云投资。 Microsoft 使用 Fpga 进行深层神经网络 (DNN) 评估、必应搜索排名和软件定义的网络 (SDN) 加速以减少延迟，同时释放 Cpu 用于其他任务。

Azure 上的 FPGA 基于 Intel 的 FPGA 设备，数据科学家和开发人员使用这种设备来加快实时 AI 计算速度。 这种启用了 FPGA 的体系结构提供了高性能、灵活性和可伸缩性，可以在 Azure 上使用。

Azure FPGA 与 Azure 机器学习集成。 Azure 可以在 Fpga 上并行处理预先训练的 DNN，以横向扩展你的服务。 DNNs 可以预先训练，可以作为深度特征化器来传输学习内容，或者使用更新的权重进行优化。

|Azure 上 & 配置的方案|支持的 DNN 模型|区域支持|
|--------------------------|--------------------|----------------|
|+ 映像分类和识别方案<br/>+ TensorFlow 部署 (需要 Tensorflow 1.x) <br/>+ Intel FPGA 硬件|-ResNet 50<br/>-ResNet 152<br/>-DenseNet-121<br/>-VGG-16<br/>-SSD-VGG|-美国东部<br/>-东南亚<br/> - 西欧<br/>-美国西部2|

若要优化延迟和吞吐量，将数据发送到 FPGA 模型的客户端应位于上述某一区域（即部署模型的区域）。

Azure VM 的 PBS 系列包含 Intel Arria 10 FPGA****。 查看 Azure 配额分配时，它将显示为“标准 PBS 系列 vCPU”。 PB6 VM 有六个个 vcpu 和一个 FPGA。 在将模型部署到 FPGA 期间，Azure 机器学习会自动预配 PB6 VM。 它仅可用于 Azure ML，无法运行任意位流。 例如，你将无法使用位流刷新 FPGA 以进行加密、编码等。

## <a name="deploy-models-on-fpgas"></a>在 FPGA 上部署模型

你可以使用 [Azure 机器学习硬件加速模型](https://docs.microsoft.com/python/api/azureml-accel-models/azureml.accel?view=azure-ml-py&preserve-view=true)将模型部署为 fpga 上的 web 服务。 使用 FPGA 可实现超低的延迟推理，即使只有一个批数据大小。 

在此示例中，你将创建一个 TensorFlow 图来预处理输入图像，使其成为 FPGA 上使用 ResNet 50 的特征化器，然后通过针对 ImageNet 数据集定型的分类器来运行这些功能。 然后，将模型部署到 AKS 群集。

### <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有，请创建即 [用即付](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) 帐户， (免费 Azure 帐户不符合 FPGA 配额) 的要求。

- 已安装 Azure 机器学习工作区和 Python 的 Azure 机器学习 SDK，如 [创建工作区](how-to-manage-workspace.md)中所述。
 
- 硬件加速模型包：  `pip install --upgrade azureml-accel-models[cpu]`    
    
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)

- FPGA 配额。 提交 [配额请求](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2nac9-PZhBDnNSV2ITz0LNUN0U5S0hXRkNITk85QURTWk9ZUUFUWkkyTC4u)，或运行此 CLI 命令以检查配额： 

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

   请确保在返回的 __CurrentValue__ 下至少有6个个 vcpu。  

### <a name="define-the-tensorflow-model"></a>定义 TensorFlow 模型

首先，使用 [适用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) 来创建服务定义。 服务定义是一个文件，用于根据 TensorFlow 来描述图形（输入、特征化器、分类器）的管道。 部署命令将定义和图形压缩为 ZIP 文件，并将 ZIP 上传到 Azure Blob 存储。 DNN 已经部署，可以在 FPGA 上运行。

1. 加载 Azure 机器学习工作区

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. 预处理映像。 Web 服务的输入为 JPEG 图像。  第一步是解码 JPEG 图像并对其进行预处理。  JPEG 图像被视为字符串，结果为张量，这些张量将是 ResNet 50 模型的输入。

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Load 特征化器。 初始化模型并下载 ResNet50 的量化版本的 TensorFlow 检查点以用作特征化器。  替换代码段中的 "QuantizedResnet50" 以导入其他深层神经网络：

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

1. 添加分类器。 此分类器已在 ImageNet 数据集上定型。

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

1. 保存输入和输出 tensors， **因为将用于模型转换和推理请求**。 

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   如果使用了默认分类器，则可使用以下模型的分类器输出 tensors 进行推断。

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

### <a name="convert-the-model-to-the-open-neural-network-exchange-format-onnx"></a>将模型转换为开放式神经网络交换格式 (ONNX) 

在可以部署到 Fpga 之前，请将模型转换为 [ONNX](https://onnx.ai/) 格式。

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

1. 将 TensorFlow 图形转换为 ONNX 格式。  必须提供输入和输出 tensors 的名称，以便客户端在使用 web 服务时可以使用它们。

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

### <a name="containerize-and-deploy-the-model"></a>容器化和部署模型

接下来，从转换后的模型和所有依赖项创建 Docker 映像。  然后，可以部署并实例化此 Docker 映像。  支持的部署目标包括云中的 Azure Kubernetes 服务 (AKS) ，或边缘设备（如 [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)）。  还可以为已注册的 Docker 映像添加标记和说明。

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

#### <a name="deploy-to-an-azure-kubernetes-service-cluster"></a>部署到 Azure Kubernetes 服务群集

1. 若要将模型部署为大规模生产 web 服务，请使用 AKS。 可以使用 Azure 机器学习 SDK、CLI 或 [Azure 机器学习工作室](https://ml.azure.com)新建一个。

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

1. 将容器部署到 AKS 群集。

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

#### <a name="deploy-to-a-local-edge-server"></a>部署到本地边缘服务器

所有 [Azure Data Box Edge 设备](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
)都包含用于运行模型的 FPGA。  在 FPGA 上，一次只能运行一个模型。  若要运行另一模型，只需部署一个新容器。 有关说明和示例代码，请参阅[此 Azure 示例](https://github.com/Azure-Samples/aml-hardware-accelerated-models)。

### <a name="consume-the-deployed-model"></a>使用已部署的模型

最后，使用示例客户端调入 Docker 映像，从模型中获取预测。  示例客户端代码可用：
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Docker 映像支持 gRPC 和 TensorFlow Serving“预测”API。

你还可以下载 TensorFlow 服务的示例客户端。

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

### <a name="clean-up-resources"></a>清理资源

若要避免不必要的成本，请 **按以下顺序**清理资源： web 服务，然后是图像，然后是模型。

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="next-steps"></a>后续步骤

+ 了解如何 [保护 web 服务](how-to-secure-web-service.md) 文档。

+ [超大规模硬件：大规模在 Azure + FPGA 上的 ML：生成 2018 (视频) ](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [基于 Microsoft FPGA 的可配置云 (视频) ](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [用于实时 AI 的项目脑电波](https://www.microsoft.com/research/project/project-brainwave/)

+ [自动光纤检查系统](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
