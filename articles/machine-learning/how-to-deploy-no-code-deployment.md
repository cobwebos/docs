---
title: '无 (预览版的代码部署) '
titleSuffix: Azure Machine Learning
description: 了解如何在不使用条目脚本的情况下部署模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.openlocfilehash: 9d6e234e1f4c8ac5199b92a09eb12bf7aa41b01b
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185479"
---
# <a name="preview-no-code-model-deployment"></a>（预览）无代码模型部署

无代码模型部署目前处于预览阶段，支持以下机器学习框架：

## <a name="tensorflow-savedmodel-format"></a>TensorFlow SavedModel 格式
需要以**SavedModel 格式**注册 TensorFlow 模型，才能使用无代码模型部署。

请参阅[此链接](https://www.tensorflow.org/guide/saved_model)，了解有关如何创建 SavedModel 的信息。

我们支持[TensorFlow 服务 DockerHub](https://registry.hub.docker.com/r/tensorflow/serving/tags)的 "标记" 下列出的任何 TensorFlow 版本。

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

## <a name="onnx-models"></a>ONNX 模型

任何 ONNX 推理图都支持 ONNX 模型注册和部署。 当前不支持预处理和后续处理步骤。

下面是有关如何注册和部署 MNIST ONNX 模型的示例：

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

若要对模型进行评分，请参阅[使用部署为 web 服务的 Azure 机器学习模型](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service)。 许多 ONNX 项目使用 protobuf 文件来简洁地存储定型和验证数据，这可能会导致难以知道服务所需的数据格式。 作为模型开发人员，您应该为您的开发人员提供文档：

* 输入格式 (JSON 或二进制) 
* 输入数据形状和类型 (例如，形状的一个浮动数组 [100100，3] ) 
* 例如，对于图像、颜色空间、组件顺序以及值是否规范化) ，域信息 (。

如果你使用的是 Pytorch，则[从 Pytorch 将模型导出到 ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)具有有关转换和限制的详细信息。 

## <a name="scikit-learn-models"></a>Scikit-learn 模型

所有内置 scikit-learn 模型类型都支持无代码模型部署。

下面的示例演示如何在不使用额外代码的情况下注册和部署 sklearn 模型：

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

> [!NOTE]
> 默认情况下，支持 predict_proba 的模型将使用该方法。 要重写此内容以使用预测，可以修改 POST 正文，如下所示：

```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

> [!NOTE]
> 这些依赖项包含在预生成的 scikit-learn 推理容器中：

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```
## <a name="next-steps"></a>后续步骤

* [排查失败的部署问题](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)
* [创建使用 web 服务的客户端应用程序](how-to-consume-web-service.md)
* [更新 Web 服务](how-to-deploy-update-web-service.md)
* [如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)
* [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
* [为模型部署创建事件警报和触发器](how-to-use-event-grid.md)
