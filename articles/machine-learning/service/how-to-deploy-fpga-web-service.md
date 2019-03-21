---
title: 在 FPGA 上部署模型
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务通过在 FPGA 上运行模型部署 Web 服务来实现超低延迟推断。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 1/29/2019
ms.custom: seodec18
ms.openlocfilehash: 7aa0e11ed47219829830369d17b300270d3fbffb
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259408"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>使用 Azure 机器学习服务将模型部署为 FPGA 上的 Web 服务

可在[现场可编程门阵列 (FPGA)](concept-accelerate-with-fpgas.md) 上将模型部署为 Web 服务。  使用 FPGA 可实现超低的延迟推断，即使只有一个批数据大小。  当前提供了以下模型：
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16   

## <a name="prerequisites"></a>必备组件

- 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

- 已安装 Azure 机器学习服务工作区，以及适用于 Python 的 Azure 机器学习 SDK。 了解如何通过[如何配置开发环境](how-to-configure-environment.md)一文满足这些先决条件。
 
  - 工作区需要位于美国东部 2 区域。

  - 安装附加 contrib：

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```

  - 当前仅支持 tensorflow 版本<=1.10，因此，在所有其他安装完成后安装它：

    ```shell
    pip install "tensorflow==1.10"
    ```

## <a name="create-and-deploy-your-model"></a>创建和部署模型
创建管道，以便预处理输入的图像，使其使用 ResNet 50 上 FPGA 的功能，然后运行通过 ImageNet 数据集上训练的分类器的功能。

遵照说明操作：

* 定义模型管道
* 部署模型
* 使用已部署的模型
* 删除已部署的服务

> [!IMPORTANT]
> 为优化延迟和吞吐量，客户端应位于终结点所在的同一 Azure 区域中。  目前是在美国东部 Azure 区域创建 API。



### <a name="preprocess-image"></a>预处理图像
管道的第一个阶段是预处理图像。

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="add-featurizer"></a>添加特征化器
初始化模型并下载 ResNet50 的量化版本的 TensorFlow 检查点以用作特征化器。

```python
from azureml.contrib.brainwave.models import QuantizedResnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>添加分类器
已在 ImageNet 数据集上训练该分类器。

```python
classifier_output = model.get_default_classifier(feature_tensor)
```

### <a name="create-service-definition"></a>创建服务定义
已经定义了在服务上运行的图像预处理、特征化器和分类器，现可创建服务定义。 服务定义是从部署到 FPGA 服务的模型生成的一组文件。 服务定义包括管道。 管道是一系列按顺序运行的阶段。  支持 TensorFlow 阶段、Keras 阶段和 BrainWave 阶段。  这些阶段在服务上按顺序运行，同时每个阶段的输出变为下个阶段的输入。

要创建 TensorFlow 阶段，请指定包含图形的会话（在本例中使用默认图形）以及此阶段的输入和输出张量。  此信息用于保存图表，以便在服务上运行。

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'model_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, feature_tensor, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>部署模型
通过服务定义创建服务。  工作区需要位于美国东部 2 位置。

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(True)
```

### <a name="test-the-service"></a>测试服务
要将图像发送到 API 并测试响应，请将映射从输出类 ID 添加到 ImageNet 类名称。

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

调用服务，并将下面的“your-image.jpg”文件名替换为计算机的图像。 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>清理服务
删除服务。

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>保护 FPGA Web 服务

有关如何保护 FPGA Web 服务的信息，请参阅[保护 Web 服务](how-to-secure-web-service.md)文档。


## <a name="next-steps"></a>后续步骤

了解如何[使用部署为 Web 服务的 ML 模型](how-to-consume-web-service.md)。
