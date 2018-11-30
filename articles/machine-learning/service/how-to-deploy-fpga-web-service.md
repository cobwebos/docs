---
title: 使用 Azure 机器学习将模型部署为 FPGA 上的 Web 服务
description: 了解如何使用 Azure 机器学习通过在 FPGA 上运行模型部署 Web 服务。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 10/29/2018
ms.openlocfilehash: c6bf3c50958085f45c52ec2fa3cbdfba8adbe35f
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711712"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>使用 Azure 机器学习将模型部署为 FPGA 上的 Web 服务

可在[现场可编程门阵列 (FPGA)](concept-accelerate-with-fpgas.md) 上将模型部署为 Web 服务。  使用 FPGA 可实现超低的延迟推断，即使只有一个批数据大小。   

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://aka.ms/AMLfree)。

- 已安装 Azure 机器学习服务工作区，以及适用于 Python 的 Azure 机器学习 SDK。 了解如何通过[如何配置开发环境](how-to-configure-environment.md)一文满足这些先决条件。
 
  - 工作区需要位于美国东部 2 区域。

  - 安装附加 contrib：

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>创建和部署模型
创建一个管道来预处理输入图像，在 FPGA 上使用 ResNet 50 对其进行特征化，然后通过在 ImageNet 数据集上训练的分类器来运行这些功能。

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
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
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
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>创建服务定义
已经定义了在服务上运行的图像预处理、特征化器和分类器，现可创建服务定义。 服务定义是从部署到 FPGA 服务的模型生成的一组文件。 服务定义包括管道。 管道是一系列按顺序运行的阶段。  支持 TensorFlow 阶段、Keras 阶段和 BrainWave 阶段。  这些阶段在服务上按顺序运行，将每个阶段的输出输入到下个阶段。

要创建 TensorFlow 阶段，请指定包含图形的会话（在本例中使用默认图形）以及此阶段的输入和输出张量。  此信息用于保存图表，以便在服务上运行。

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
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
    service.wait_for_deployment(true)
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

在 FPGA 上运行的 Azure 机器学习模型提供 SSL 支持和基于密钥的身份验证。 这样可以限制对服务的访问权限并保护由客户端提交的数据。 [了解如何保护 Web 服务](how-to-secure-web-service.md)。


## <a name="sample-notebook"></a>示例 Notebook

本文中的概念在 [project-brainwave/project-brainwave-quickstart.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/project-brainwave/project-brainwave-quickstart.ipynb) 笔记本中演示。

获取此笔记本：

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

了解如何[使用部署为 Web 服务的 ML 模型](how-to-consume-web-service.md)。