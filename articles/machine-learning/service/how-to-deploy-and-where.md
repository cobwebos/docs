---
title: 部署模型的方式和位置
titleSuffix: Azure Machine Learning service
description: 了解如何以及在何处部署 Azure 机器学习服务模型，包括：Azure 容器实例、Azure Kubernetes 服务、Azure IoT Edge 和现场可编程门阵列。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/06/2019
ms.custom: seoapril2019
ms.openlocfilehash: 5c0c3ade3fd089a4819b8836b07e249fc32c06e0
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543609"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>使用 Azure 机器学习服务部署模型

了解如何在 Azure 云中将机器学习模型部署为 web 服务, 或将其部署到 IoT Edge 设备。

无论[在何处部署](#target)模型, 工作流都是相似的:

1. 注册模型。
1. 准备部署 (指定资产、使用情况、计算目标)。
1. 将模型部署到计算目标。
1. 测试已部署的模型, 也称为 "web 服务"。

有关部署工作流涉及的概念的详细信息，请参阅[使用 Azure 机器学习服务管理、部署和监视模型](concept-model-management-and-deployment.md)。

## <a name="prerequisites"></a>先决条件

- Azure 机器学习服务工作区。 有关详细信息, 请参阅[创建 Azure 机器学习服务工作区](how-to-manage-workspace.md)。

- 模型。 如果没有训练的模型, 则可以使用该模型 &[本教程](https://aka.ms/azml-deploy-cloud)中提供的依赖项文件。

- [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、 [Azure 机器学习 Python SDK](https://aka.ms/aml-sdk)或[Azure 机器学习 Visual Studio Code 扩展](how-to-vscode-tools.md)。

## <a name="connect-to-your-workspace"></a>连接到你的工作区

下面的代码演示如何使用缓存到本地开发环境中的信息连接到 Azure 机器学习服务工作区:

**使用 SDK**

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

有关使用 SDK 连接到工作区的详细信息, 请参阅适用于[Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace)。

**使用 CLI**

使用 CLI 时, 请使用`-w`或`--workspace-name`参数指定命令的工作区。

**使用 VS Code**

使用 VS Code 时, 使用图形界面选择工作区。 有关详细信息, 请参阅 VS Code 扩展文档中的 "[部署和管理模型](how-to-vscode-tools.md#deploy-and-manage-models)"。

## <a id="registermodel"></a>注册模型

已注册的模型是组成模型的一个或多个文件的逻辑容器。 例如, 如果您有一个存储在多个文件中的模型, 则可以在工作区中将其注册为一个模型。 注册后, 可以下载或部署已注册的模型, 并接收已注册的所有文件。

> [!TIP]
> 注册模型时, 需要提供云位置的路径 (从定型运行) 或本地目录。 此路径只是在注册过程中查找要上载的文件;它不需要与条目脚本中使用的路径匹配。 有关详细信息, 请参阅[什么是 get_model_path](#what-is-get_model_path)。

机器学习模型注册到 Azure 机器学习工作区中。 模型可以来自 Azure 机器学习或可以来自其他位置。 下面的示例演示如何注册模型:

### <a name="register-a-model-from-an-experiment-run"></a>从试验运行注册模型

本节中的代码片段演示如何从定型运行注册模型:

> [!IMPORTANT]
> 这些代码片段假设您之前已经执行了定型运行, 并且有权访问`run`对象 (SDK 示例) 或运行 ID 值 (CLI 示例)。 有关定型模型的详细信息, 请参阅[为模型定型创建和使用计算目标](how-to-set-up-training-targets.md)。

+ **使用 SDK**

  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  `model_path`引用模型的云位置。 在此示例中, 使用单个文件的路径。 若要在模型注册中包含多个文件`model_path` , 请将设置为包含这些文件的目录。

+ **使用 CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  `--asset-path`引用模型的云位置。 在此示例中, 使用单个文件的路径。 若要在模型注册中包含多个文件`--asset-path` , 请将设置为包含这些文件的目录。

+ **使用 VS Code**

  使用具有[VS Code](how-to-vscode-tools.md#deploy-and-manage-models)扩展的任何模型文件或文件夹注册模型。

### <a name="register-a-model-from-a-local-file"></a>从本地文件注册模型

您可以通过提供模型的**本地路径**来注册模型。 可以提供文件夹或单个文件。 您可以使用此方法注册培训了 Azure 机器学习服务的两个模型, 然后将其下载或在 Azure 机器学习外训练的模型。

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **Python SDK 的 ONNX 示例:**

    ```python
    import os
    import urllib.request
    from azureml.core import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  若要在模型注册中包含多个文件`model_path` , 请将设置为包含这些文件的目录。

+ **使用 CLI**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  若要在模型注册中包含多个文件`-p` , 请将设置为包含这些文件的目录。

**时间估计**：大约 10 秒。

有关详细信息，请参阅 [Model 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)的参考文档。

有关使用 Azure 机器学习服务外部训练的模型的详细信息, 请参阅[如何部署现有模型](how-to-deploy-existing-model.md)。

<a name="target"></a>

## <a name="choose-a-compute-target"></a>选择计算目标

以下计算目标或计算资源可用于托管你的 web 服务部署。 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>准备部署

部署模型需要执行以下几项操作:

* 一个__项脚本__。 此脚本接受请求, 使用模型为请求评分, 并返回结果。

    > [!IMPORTANT]
    > 条目脚本特定于模型;它必须了解传入请求数据的格式、模型所需的数据格式, 以及返回给客户端的数据的格式。
    >
    > 如果请求数据的格式不能由您的模型使用, 则该脚本可以将其转换为可接受的格式。 它还可能在将响应返回给客户端之前对其进行转换。

    > [!IMPORTANT]
    > Azure 机器学习 SDK 不为 web 服务或 IoT Edge 部署提供访问数据存储或数据集的方法。 如果需要部署的模型来访问在部署外部存储的数据, 例如在 Azure 存储帐户中, 则必须使用相关的 SDK 开发自定义代码解决方案。 例如,[用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python)。
    >
    > 可能适用于你的方案的另一种方法是[批处理预测](how-to-run-batch-predictions.md), 这在评分时提供对数据存储的访问。

* 运行条目脚本或模型所需的**依赖项**(如帮助程序脚本或 Python/Conda 包)

* 托管已部署模型的计算目标的__部署配置__。 此配置描述运行模型所需的内存和 CPU 需求等因素。

这些实体封装为__推理配置__和__部署配置__。 推理配置引用入口脚本和其他依赖项。 这些配置是在使用 SDK 时以编程方式定义的, 在使用 CLI 执行部署时作为 JSON 文件进行定义。

### <a id="script"></a> 1.& 依赖项定义条目脚本

条目脚本接收提交给已部署 web 服务的数据, 并将其传递给模型。 然后，该脚本接收模型返回的响应，并将该响应返回给客户端。 **该脚本特定于您的模型**;它必须了解模型需要的数据并返回数据。

该脚本包含用于加载和运行模型的两个函数:

* `init()`：此函数通常将模型载入全局对象。 当 web 服务的 Docker 容器启动时, 此函数只运行一次。

* `run(input_data)`：此函数使用模型来基于输入数据预测值。 运行的输入和输出通常使用 JSON 进行序列化和反序列化。 也可以处理原始二进制数据。 可以在将数据发送到模型之前或者返回给客户端之前转换数据。

#### <a name="what-is-get_model_path"></a>什么是 get_model_path？

注册模型时, 提供用于在注册表中管理该模型的模型名称。 将此名称与模型一起使用[。 get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-)可在本地文件系统上检索模型文件的路径。 如果注册文件夹或文件集合, 此 API 将返回包含这些文件的目录的路径。

注册模型时, 请为其指定一个名称, 该名称对应于在本地或在服务部署期间放置模型的位置。

下面的示例将返回名为的单个文件`sklearn_mnist_model.pkl` (使用名称`sklearn_mnist`注册) 的路径:

```python
model_path = Model.get_model_path('sklearn_mnist')
```

#### <a name="optional-automatic-schema-generation"></a>可有可无自动生成架构

若要为 web 服务自动生成架构, 请在构造函数中为一个已定义的类型对象提供输入和/或输出的示例, 并使用类型和示例来自动创建该架构。 然后 Azure 机器学习服务在部署期间为 web 服务创建[OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 规范。

目前支持以下类型:

* `pandas`
* `numpy`
* `pyspark`
* 标准 Python 对象

若要使用架构生成, 请`inference-schema`将包包含在 conda 环境文件中。

##### <a name="example-dependencies-file"></a>示例依赖关系文件

以下 YAML 是用于推理的 Conda 依赖项文件的一个示例:

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

如果要使用自动生成架构, 则输入脚本**必须**导入`inference-schema`包。

定义`input_sample` 和`output_sample`变量中的输入和输出示例格式, 表示 web 服务的请求和响应格式。 在`run()`函数的 input 和 output 函数修饰器中使用这些示例。 下面的 scikit-learn 示例使用架构生成。

##### <a name="example-entry-script"></a>示例条目脚本

下面的示例演示如何接受并返回 JSON 数据:

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

下面的示例演示如何使用数据帧将输入数据定义为`<key: value>`字典。 此方法支持从 Power BI 使用已部署的 web 服务 ([了解有关如何从 Power BI 使用 web 服务的详细信息](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # replace model_name with your actual model name, if needed
    model_path = Model.get_model_path('model_name')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data
    "input_name_2": "value2",
    # This is a integer type sample. Use the data type that reflects this column in your data
    "input_name_3": 3
}])

# This is a integer type sample. Use the data type that reflects the expected result
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

有关更多示例脚本, 请参阅以下示例:

* Pytorch[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>二进制数据

如果模型接受二进制数据（如映像），则必须修改用于部署的 `score.py` 文件以接受原始 HTTP 请求。 若要接受原始数据, 请`AMLRequest`在输入脚本中使用类, 并`@rawhttp`将修饰器`run()`添加到函数。

下面是一个`score.py`接受二进制数据的示例:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> 类位于`azureml.contrib`命名空间中。 `AMLRequest` 此命名空间中的任务在我们努力改进服务时经常发生变化。 因此，此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持。
>
> 如果需要在本地开发环境中对此进行测试, 可以使用以下命令安装组件:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>跨域资源共享 (CORS)

跨域资源共享是一种允许网页上的资源从另一个域请求的方式。 CORS 基于与客户端请求一起发送的 HTTP 标头, 并随服务响应一起返回。 有关 CORS 和有效标头的详细信息, 请参阅维基百科上的[跨域资源共享](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)。

若要配置模型部署以支持 CORS, 请在`AMLResponse`条目脚本中使用类。 此类允许您设置响应对象的标头。

下面的示例从输入`Access-Control-Allow-Origin`脚本中设置响应的标头:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> 类位于`azureml.contrib`命名空间中。 `AMLResponse` 此命名空间中的任务在我们努力改进服务时经常发生变化。 因此，此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持。
>
> 如果需要在本地开发环境中对此进行测试, 可以使用以下命令安装组件:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2.定义 InferenceConfig

推理配置介绍了如何配置模型以便进行预测。 此配置不是你的输入脚本的一部分;它引用您的条目脚本, 并用于查找部署所需的所有资源。 它稍后在实际部署模型时使用。

下面的示例演示如何创建推理配置。 此配置指定运行时、入口脚本和 (可选) conda 环境文件:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

有关详细信息, 请参阅[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)类参考。

有关将自定义 Docker 映像与推理配置配合使用的信息, 请参阅[如何使用自定义 docker 映像部署模型](how-to-deploy-custom-docker-image.md)。

### <a name="cli-example-of-inferenceconfig"></a>InferenceConfig 的 CLI 示例

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

以下命令演示如何使用 CLI 部署模型:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

在此示例中, 配置包含以下项:

* 此模型需要 Python
* 用于处理发送到已部署服务的 web 请求的[条目脚本](#script)
* 描述推理所需的 Python 包的 conda 文件

有关将自定义 Docker 映像与推理配置配合使用的信息, 请参阅[如何使用自定义 docker 映像部署模型](how-to-deploy-custom-docker-image.md)。

### <a name="3-define-your-deployment-configuration"></a>3.定义部署配置

在部署之前, 必须定义部署配置。 __部署配置特定于将托管 web 服务的计算目标__。 例如, 在本地部署时, 必须指定服务接受请求的端口。 部署配置不是你的输入脚本的一部分。 它用于定义将托管模型和条目脚本的计算目标的特征。

你可能还需要创建计算资源。 例如, 如果你还没有与工作区关联的 Azure Kubernetes 服务。

下表提供了为每个计算目标创建部署配置的示例:

| 计算目标 | 部署配置示例 |
| ----- | ----- |
| 本地 | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure 容器实例 | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes 服务 | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

可以从`azureml.core.webservice`以下项导入用于本地、ACI 和 AKS web 服务的每个类:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

> [!TIP]
> 在将模型部署为服务之前, 您可能需要对其进行分析, 以确定最佳的 CPU 和内存要求。 可以使用 SDK 或 CLI 分析模型。 有关详细信息, 请参阅[配置文件 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)和[az ml 模型配置文件](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)参考。
>
> 模型分析结果以对象的`Run`形式发出。 有关详细信息, 请参阅[ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)类参考。

## <a name="deploy-to-target"></a>部署到目标

部署使用推理配置部署配置来部署模型。 不管计算目标如何, 部署过程都是类似的。 部署到 AKS 的方式略有不同, 因为必须提供对 AKS 群集的引用。

### <a id="local"></a>本地部署

若要在本地部署, 你需要在本地计算机上安装 Docker。

#### <a name="using-the-sdk"></a>使用 SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

有关详细信息, 请参阅[LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py)、 [Model ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)和[Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)的参考文档。

#### <a name="using-the-cli"></a>使用 CLI

若要使用 CLI 进行部署, 请使用以下命令。 替换`mymodel:1`为注册的模型的名称和版本:

```azurecli-interactive
az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

有关详细信息, 请参阅[az ml 模型部署](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)参考。

### <a id="notebookvm"></a>NotebookVM web 服务 (开发测试)

请参阅[将模型部署到笔记本 vm](how-to-deploy-local-container-notebook-vm.md)。

### <a id="aci"></a>Azure 容器实例 (开发测试)

请参阅[部署到 Azure 容器实例](how-to-deploy-azure-container-instance.md)。

### <a id="aks"></a>Azure Kubernetes 服务 (开发测试 & 生产)

请参阅[部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)。

## <a name="consume-web-services"></a>使用 Web 服务

每个已部署的 web 服务都提供 REST API, 因此你可以使用各种编程语言来创建客户端应用程序。 如果已为服务启用密钥身份验证, 则需要提供服务密钥作为请求标头中的令牌。
如果已为服务启用令牌身份验证, 则需要提供 Azure 机器学习 JWT 令牌作为请求标头中的持有者令牌。

> [!TIP]
> 部署服务后, 可以检索架构 JSON 文档。 使用部署的 web 服务中的[swagger_uri 属性](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)(如`service.swagger_uri`) 获取本地 web 服务 swagger 文件的 uri。

### <a name="request-response-consumption"></a>请求-响应消耗

下面是如何在 Python 中调用服务的示例:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

有关详细信息，请参阅[创建客户端应用程序以使用 Web 服务](how-to-consume-web-service.md)。

### <a name="web-service-schema-openapi-specification"></a>Web 服务架构 (OpenAPI 规范)

如果在部署中使用了自动生成架构, 则可以使用[swagger_uri 属性](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri)获取服务的 OpenAPI 规范的地址。 例如， `print(service.swagger_uri)` 。 使用 GET 请求 (或在浏览器中打开 URI) 来检索规范。

以下 JSON 文档是为部署生成的架构 (OpenAPI 规范) 的示例:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for the Azure Machine Learning service myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

有关规范的详细信息, 请参阅[开放 API 规范](https://swagger.io/specification/)。

有关可根据规范创建客户端库的实用工具, 请参阅[codegen](https://github.com/swagger-api/swagger-codegen)。

### <a id="azuremlcompute"></a>批处理推理
Azure 机器学习计算目标由 Azure 机器学习服务创建和管理。 它们可用于来自 Azure 机器学习管道的批处理预测。

有关使用 Azure 机器学习计算进行批处理推理的演练, 请参阅[如何运行批预测](how-to-run-batch-predictions.md)一文。

### <a id="iotedge"></a>IoT Edge 推理
对部署到边缘的支持处于预览阶段。 有关详细信息, 请参阅将[Azure 机器学习部署为 IoT Edge 模块一](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)文。


## <a id="update"></a>更新 web 服务

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuous-model-deployment"></a>连续模型部署 

可以使用[Azure DevOps](https://azure.microsoft.com/services/devops/)的机器学习扩展持续部署模型。 使用 Azure DevOps 的机器学习扩展, 可以在 Azure 机器学习服务工作区中注册新的机器学习模型时触发部署管道。 

1. 注册[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), 这会使应用程序持续集成和交付到任何平台/任何可能的云。 Azure Pipelines[不同于 ML 管道](concept-ml-pipelines.md#compare)。 

1. [创建 Azure DevOps 项目。](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. 安装[Azure Pipelines 的机器学习扩展](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) 

1. 使用__服务连接__设置与 Azure 机器学习服务工作区的服务主体连接, 以访问所有项目。 单击 "项目设置", 单击 "服务连接", 并选择 "Azure 资源管理器"。

    ![view-service-connection](media/how-to-deploy-and-where/view-service-connection.png) 

1. 将 AzureMLWorkspace 定义为__作用域级别__, 并填写后续参数。

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. 接下来, 若要使用 Azure Pipelines 连续部署机器学习模型, 请在 "管道" 下选择 "__发布__"。 添加新项目, 选择 "AzureML 模型项目" 和在前面步骤中创建的服务连接。 选择要触发部署的模型和版本。 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. 对模型项目启用模型触发器。 启用触发器后, 每次指定版本 (即 此模型的最新版本) 是在工作区中注册, 将触发 Azure DevOps release 管道。 

    ![enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)

有关更多示例项目和示例, 请参阅以下示例存储库:

* [https://github.com/Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [https://github.com/Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="clean-up-resources"></a>清理资源
若要删除已部署的 Web 服务，请使用 `service.delete()`。
若要删除已注册的模型，请使用 `model.delete()`。

有关详细信息, 请参阅[WebService ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)和[Model ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)的参考文档。

## <a name="next-steps"></a>后续步骤
* [如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)
* [部署故障排除](how-to-troubleshoot-deployment.md)
* [使用 SSL 保护 Azure 机器学习 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)

