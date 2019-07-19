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
ms.date: 07/08/2019
ms.custom: seoapril2019
ms.openlocfilehash: 796118999041b2bef2d51657901e9e399578e97c
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68327038"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>使用 Azure 机器学习服务部署模型

了解如何在 Azure 云中将机器学习模型部署为 web 服务, 或将其部署到 IoT Edge 设备。 

无论[在何处部署](#target)模型, 工作流都是相似的:

1. 注册模型。
1. 准备部署 (指定资产、使用情况、计算目标)
1. 将模型部署到计算目标。
1. 测试已部署的模型, 也称为 "web 服务"。

有关部署工作流涉及的概念的详细信息，请参阅[使用 Azure 机器学习服务管理、部署和监视模型](concept-model-management-and-deployment.md)。

## <a name="prerequisites"></a>先决条件

- 模型。 如果没有训练的模型, 则可以使用该模型 &[本教程](https://aka.ms/azml-deploy-cloud)中提供的依赖项文件。

- [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、 [Azure 机器学习 Python SDK](https://aka.ms/aml-sdk)或[Azure 机器学习 Visual Studio Code 扩展](how-to-vscode-tools.md)。

## <a id="registermodel"></a>注册模型

构成模型的一个或多个文件的已注册模型逻辑容器。 例如, 如果您有一个存储在多个文件中的模型, 则可以在工作区中将其注册为一个模型。 注册后, 可以下载或部署已注册的模型, 并接收已注册的所有文件。

机器学习模型注册到 Azure 机器学习工作区中。 模型可以来自 Azure 机器学习或可以来自其他位置。 下面的示例演示如何从文件注册模型:

### <a name="register-a-model-from-an-experiment-run"></a>从试验运行注册模型

+ **Scikit-learn-使用 SDK 了解示例**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > 若要在模型注册中包含多个文件`model_path` , 请将设置为包含这些文件的目录。

+ **使用 CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```

  > [!TIP]
  > 若要在模型注册中包含多个文件`--asset-path` , 请将设置为包含这些文件的目录。

+ **使用 VS Code**

  使用具有[VS Code](how-to-vscode-tools.md#deploy-and-manage-models)扩展的任何模型文件或文件夹注册模型。

### <a name="register-an-externally-created-model"></a>注册外部创建的模型

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

您可以通过提供模型的**本地路径**来注册外部创建的模型。 可以提供文件夹或单个文件。

+ **Python SDK 的 ONNX 示例:**
  ```python
  onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
  urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
  !tar xvzf mnist.tar.gz
  
  model = Model.register(workspace = ws,
                         model_path ="mnist/model.onnx",
                         model_name = "onnx_mnist",
                         tags = {"onnx": "demo"},
                         description = "MNIST image classification CNN from ONNX Model Zoo",)
  ```

  > [!TIP]
  > 若要在模型注册中包含多个文件`model_path` , 请将设置为包含这些文件的目录。

+ **使用 CLI**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > 若要在模型注册中包含多个文件`-p` , 请将设置为包含这些文件的目录。

**时间估计**：大约 10 秒。

有关详细信息，请参阅 [Model 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)的参考文档。

有关使用 Azure 机器学习服务外部训练的模型的详细信息, 请参阅[如何部署现有模型](how-to-deploy-existing-model.md)。

<a name="target"></a>

## <a name="choose-a-compute-target"></a>选择计算目标

以下计算目标或计算资源可用于托管你的 web 服务部署。 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>准备部署

若要部署为 web 服务, 您必须创建一个推理配置 (`InferenceConfig`) 和一个部署配置。 推理或模型计分是部署模型用于预测的阶段, 最常见的是生产数据。 在推理配置中, 你指定为模型提供服务所需的脚本和依赖项。 在 "部署配置" 中, 指定如何为计算目标提供模型的详细信息。

> [!IMPORTANT]
> Azure 机器学习 SDK 不为 web 服务或 IoT Edge 部署提供访问数据存储或数据集的方法。 如果需要部署的模型来访问在部署外部存储的数据, 例如在 Azure 存储帐户中, 则必须使用相关的 SDK 开发自定义代码解决方案。 例如,[用于 Python 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-python)。
>
> 可能适用于你的方案的另一种方法是[批处理预测](how-to-run-batch-predictions.md), 这在评分时提供对数据存储的访问。

### <a id="script"></a> 1.& 依赖项定义条目脚本

条目脚本接收提交给已部署 web 服务的数据, 并将其传递给模型。 然后，该脚本接收模型返回的响应，并将该响应返回给客户端。 **该脚本特定于您的模型**;它必须了解模型需要的数据并返回数据。

该脚本包含用于加载和运行模型的两个函数:

* `init()`：此函数通常将模型载入全局对象。 当 web 服务的 Docker 容器启动时, 此函数只运行一次。

* `run(input_data)`：此函数使用模型来基于输入数据预测值。 运行的输入和输出通常使用 JSON 进行序列化和反序列化。 也可以处理原始二进制数据。 可以在将数据发送到模型之前或者返回给客户端之前转换数据。

#### <a name="what-is-getmodelpath"></a>什么是 get_model_path？

注册模型时, 提供用于在注册表中管理该模型的模型名称。 将此名称与模型一起使用[。 get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-)可在本地文件系统上检索模型文件的路径。 如果注册文件夹或文件集合, 此 API 将返回包含这些文件的目录的路径。

注册模型时, 请为其指定一个名称, 该名称对应于在本地或在服务部署期间放置模型的位置。

下面的示例将返回名为的单个文件`sklearn_mnist_model.pkl` (使用名称`sklearn_mnist`注册) 的路径:

```python
model_path = Model.get_model_path('sklearn_mnist')
``` 

#### <a name="optional-automatic-swagger-schema-generation"></a>可有可无自动 Swagger 架构生成

若要为 web 服务自动生成架构, 请在构造函数中为一个已定义的类型对象提供输入和/或输出的示例, 并使用类型和示例来自动创建该架构。 然后 Azure 机器学习服务在部署期间为 web 服务创建[OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 规范。

目前支持以下类型:

* `pandas`
* `numpy`
* `pyspark`
* 标准 Python 对象

若要使用架构生成, 请`inference-schema`将包包含在 conda 环境文件中。 下面的示例使用`[numpy-support]` , 因为入口脚本使用 numpy 参数类型: 

#### <a name="example-dependencies-file"></a>示例依赖关系文件
以下 YAML 是用于推理的 Conda 依赖项文件的一个示例。

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

> [!TIP]
> 部署服务后, 使用`swagger_uri`属性检索架构 JSON 文档。

#### <a name="example-entry-script"></a>示例条目脚本

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

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
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

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>带有字典输入的示例脚本 (支持 Power BI 中使用)

下面的示例演示如何使用数据帧将输入数据定义为 < 键: 值 > 字典。 此方法支持从 Power BI 使用已部署的 web 服务 ([了解有关如何从 Power BI 使用 web 服务的详细信息](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

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
    model_path = Model.get_model_path('model_name')   # replace model_name with your actual model name, if needed
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = pd.DataFrame(data=[{
              "input_name_1": 5.1,         # This is a decimal type sample. Use the data type that reflects this column in your data
              "input_name_2": "value2",    # This is a string type sample. Use the data type that reflects this column in your data
              "input_name_3": 3            # This is a integer type sample. Use the data type that reflects this column in your data
            }])

output_sample = np.array([0])              # This is a integer type sample. Use the data type that reflects the expected result

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
* 对二进制数据进行评分:[如何使用 web 服务](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2.定义 InferenceConfig

推理配置介绍了如何配置模型以便进行预测。 下面的示例演示如何创建推理配置。 此配置指定运行时、入口脚本和 (可选) conda 环境文件:

```python
inference_config = InferenceConfig(runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

有关详细信息, 请参阅[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)类参考。

有关将自定义 Docker 映像与推理配置配合使用的信息, 请参阅[如何使用自定义 docker 映像部署模型](how-to-deploy-custom-docker-image.md)。

### <a name="cli-example-of-inferenceconfig"></a>InferenceConfig 的 CLI 示例

以下 JSON 文档是用于机器学习 CLI 的示例推理配置:

```JSON
{
   "entryScript": "x/y/score.py",
   "runtime": "python",
   "condaFile": "env/myenv.yml",
   "sourceDirectory":"C:/abc",
}
```

以下实体在此文件中有效:

* __entryScript__:包含要为映像运行的代码的本地文件的路径。
* __运行时__:要用于映像的运行时。 当前支持的运行时为 "spark-py" 和 "python"。
* __condaFile__(可选):本地文件的路径, 该文件包含要用于映像的 conda 环境定义。
* __extraDockerFileSteps__(可选):本地文件的路径, 该文件包含在设置映像时要运行的其他 Docker 步骤。
* __sourceDirectory__(可选):包含创建映像的所有文件的文件夹的路径。
* __enableGpu__(可选):是否在映像中启用 GPU 支持。 GPU 映像必须用于 Microsoft Azure 服务, 如 Azure 容器实例、Azure 机器学习计算、Azure 虚拟机和 Azure Kubernetes 服务。 默认值为 False。
* __baseImage__(可选):要用作基础映像的自定义图像。 如果未提供任何基本映像, 则将根据给定的运行时参数使用基本映像。
* __baseImageRegistry__(可选):包含基本映像的映像注册表。
* __cudaVersion__(可选):要为需要 GPU 支持的映像安装的 CUDA 版本。 GPU 映像必须用于 Microsoft Azure 服务, 如 Azure 容器实例、Azure 机器学习计算、Azure 虚拟机和 Azure Kubernetes 服务。 支持的版本为9.0、9.1 和10.0。 如果设置了 "enable_gpu", 默认值为 "9.1"。

这些实体将映射到[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)类的参数。

三个以下命令演示了如何使用 CLI 部署模型:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

在此示例中, 配置包含以下项:

* 包含推理所需资产的目录
* 此模型需要 Python
* 用于处理发送到已部署服务的 web 请求的[条目脚本](#script)
* 描述推理所需的 Python 包的 conda 文件

有关将自定义 Docker 映像与推理配置配合使用的信息, 请参阅[如何使用自定义 docker 映像部署模型](how-to-deploy-custom-docker-image.md)。

### <a name="3-define-your-deployment-configuration"></a>3.定义部署配置

在部署之前, 必须定义部署配置。 部署配置特定于将托管 web 服务的计算目标。 例如, 在本地部署时, 必须指定服务接受请求的端口。

你可能还需要创建计算资源。 例如, 如果你还没有与工作区关联的 Azure Kubernetes 服务。

下表提供了为每个计算目标创建部署配置的示例:

| 计算目标 | 部署配置示例 |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure 容器实例 | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes 服务 | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

以下部分演示如何创建部署配置, 并使用它来部署 web 服务。

### <a name="optional-profile-your-model"></a>可选：分析模型

在将模型部署为服务之前, 可以使用 SDK 或 CLI 对其进行分析, 以确定最佳的 CPU 和内存要求。  模型分析结果以对象的`Run`形式发出。 [可在 API 文档中找到模型配置文件架构](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)的完整详细信息

了解有关[如何使用 SDK 分析模型](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)的详细信息。

若要使用 CLI 分析模型, 请使用[az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)。

## <a name="deploy-to-target"></a>部署到目标

### <a id="local"></a>本地部署

若要在本地部署, 你需要在本地计算机上**安装 Docker** 。

+ **使用 SDK**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **使用 CLI**

    若要使用 CLI 进行部署, 请使用以下命令。 替换`mymodel:1`为注册的模型的名称和版本:

  ```azurecli-interactive
  az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    `deploymentconfig.json`文档中的项将映射到 LocalWebservice 的参数[。](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py) 下表描述了 JSON 文档中的实体与方法的参数之间的映射:

    | JSON 实体 | 方法参数 | 描述 |
    | ----- | ----- | ----- |
    | `computeType` | NA | 计算目标。 对于 local, 该值必须为`local`。 |
    | `port` | `port` | 要在其上公开服务的 HTTP 终结点的本地端口。 |

    以下 JSON 是用于 CLI 的示例部署配置:

    ```json
    {
        "computeType": "local",
        "port": 32267
    }
    ```

### <a id="aci"></a>Azure 容器实例 (开发测试)

如果满足以下一个或多个条件，请使用 Azure 容器实例将模型部署为 Web 服务：
- 你需要快速部署并验证你的模型。
- 正在测试一个开发中的模型。 

若要查看 ACI 的配额和区域可用性, 请参阅[Azure 容器实例的配额和区域可用性](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)一文。

+ **使用 SDK**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **使用 CLI**

    若要使用 CLI 进行部署, 请使用以下命令。 替换`mymodel:1`为注册的模型的名称和版本。 替换`myservice`为要为此服务提供的名称:

    ```azurecli-interactive
    az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
    ```

    `deploymentconfig.json`文档中的项将映射到 AciWebservice 的参数[。](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py) 下表描述了 JSON 文档中的实体与方法的参数之间的映射:

    | JSON 实体 | 方法参数 | 描述 |
    | ----- | ----- | ----- |
    | `computeType` | NA | 计算目标。 对于 ACI, 该值必须为`ACI`。 |
    | `containerResourceRequirements` | NA | 包含为容器分配的 CPU 和内存的配置元素。 |
    | &emsp;&emsp;`cpu` | `cpu_cores` | 要分配给此 web 服务的 CPU 内核数。 出厂`0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | 为此 web 服务分配的内存量 (以 GB 为限)。 缺省值`0.5` |
    | `location` | `location` | 要将此 Webservice 部署到的 Azure 区域。 如果未指定, 将使用工作区位置。 有关可用区域的详细信息, 请参阅:[ACI 区域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
    | `authEnabled` | `auth_enabled` | 是否为此 Webservice 启用身份验证。 默认为 False |
    | `sslEnabled` | `ssl_enabled` | 是否为此 Webservice 启用 SSL。 默认值为 False。 |
    | `appInsightsEnabled` | `enable_app_insights` | 是否为此 Webservice 启用 AppInsights。 默认为 False |
    | `sslCertificate` | `ssl_cert_pem_file` | 启用 SSL 时所需的证书文件 |
    | `sslKey` | `ssl_key_pem_file` | 启用 SSL 时所需的密钥文件 |
    | `cname` | `ssl_cname` | 如果启用了 SSL, 则为 cname |
    | `dnsNameLabel` | `dns_name_label` | 评分终结点的 dns 名称标签。 如果未指定, 则将为评分终结点生成唯一的 dns 名称标签。 |

    以下 JSON 是用于 CLI 的示例部署配置:

    ```json
    {
        "computeType": "aci",
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        },
        "authEnabled": true,
        "sslEnabled": false,
        "appInsightsEnabled": false
    }
    ```

+ **使用 VS Code**

  若要[使用 VS Code 部署模型](how-to-vscode-tools.md#deploy-and-manage-models), 无需事先创建一个 aci 容器来进行测试, 因为会动态创建 aci 容器。

有关详细信息，请参阅 [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) 和 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) 类的参考文档。

### <a id="aks"></a>Azure Kubernetes 服务 (开发测试 & 生产)

可以使用现有 AKS 群集，也可使用 Azure 机器学习 SDK、CLI 或 Azure 门户创建新群集。

<a id="deploy-aks"></a>

如果已附加 AKS 群集, 则可以将其部署到该群集。 如果尚未创建或附加 AKS 群集, 请按照此过程<a href="#create-attach-aks">创建新的 AKS 群集</a>。

+ **使用 SDK**

  ```python
  aks_target = AksCompute(ws,"myaks")
  # If deploying to a cluster configured for dev/test, ensure that it was created with enough
  # cores and memory to handle this deployment configuration. Note that memory is also used by
  # things such as dependencies and AML components.
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **使用 CLI**

    若要使用 CLI 进行部署, 请使用以下命令。 替换`myaks`为 AKS 计算目标的名称。 替换`mymodel:1`为注册的模型的名称和版本。 替换`myservice`为要为此服务提供的名称:

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    `deploymentconfig.json`文档中的项将映射到 AksWebservice 的参数[。](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py) 下表描述了 JSON 文档中的实体与方法的参数之间的映射:

    | JSON 实体 | 方法参数 | 描述 |
    | ----- | ----- | ----- |
    | `computeType` | NA | 计算目标。 对于 AKS, 该值必须为`aks`。 |
    | `autoScaler` | NA | 包含自动缩放的配置元素。 请参阅自动缩放程序表。 |
    | &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | 是否为 web 服务启用自动缩放。 如果`numReplicas`  = 为,`True`则为;`False`否则为。 `0` |
    | &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | 自动缩放此 web 服务时要使用的容器的最小数目。 默认值为。 `1` |
    | &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | 自动缩放此 web 服务时要使用的最大容器数。 默认值为。 `10` |
    | &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | 自动缩放程序尝试缩放此 web 服务的频率。 默认值为。 `1` |
    | &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | 自动缩放程序应为此 web 服务尝试维护的目标利用率 (百分比为 100)。 默认值为。 `70` |
    | `dataCollection` | NA | 包含数据集合的配置元素。 |
    | &emsp;&emsp;`storageEnabled` | `collect_model_data` | 是否为 web 服务启用模型数据收集。 默认值为。 `False` |
    | `authEnabled` | `auth_enabled` | 是否为 web 服务启用身份验证。 默认值为。 `True` |
    | `containerResourceRequirements` | NA | 包含为容器分配的 CPU 和内存的配置元素。 |
    | &emsp;&emsp;`cpu` | `cpu_cores` | 要分配给此 web 服务的 CPU 内核数。 出厂`0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | 为此 web 服务分配的内存量 (以 GB 为限)。 缺省值`0.5` |
    | `appInsightsEnabled` | `enable_app_insights` | 是否为 web 服务启用 Application Insights 日志记录。 默认值为。 `False` |
    | `scoringTimeoutMs` | `scoring_timeout_ms` | 对 web 服务的评分调用强制执行的超时值。 默认值为。 `60000` |
    | `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | 此 web 服务每个节点的最大并发请求数。 默认值为。 `1` |
    | `maxQueueWaitMs` | `max_request_wait_time` | 在返回503错误之前, 请求将在三个队列中停留的最长时间 (毫秒)。 默认值为。 `500` |
    | `numReplicas` | `num_replicas` | 要分配给此 web 服务的容器数。 没有默认值。 如果未设置此参数, 则默认情况下将启用自动缩放程序。 |
    | `keys` | NA | 包含键的配置元素。 |
    | &emsp;&emsp;`primaryKey` | `primary_key` | 要用于此 Webservice 的主要身份验证密钥 |
    | &emsp;&emsp;`secondaryKey` | `secondary_key` | 要用于此 Webservice 的辅助身份验证密钥 |
    | `gpuCores` | `gpu_cores` | 要分配给此 Webservice 的 GPU 核心数。 默认值为1。 |
    | `livenessProbeRequirements` | NA | 包含活动探测要求的配置元素。 |
    | &emsp;&emsp;`periodSeconds` | `period_seconds` | 执行活动探测的频率 (以秒为单位)。 默认值为10秒。 最小值为1。 |
    | &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | 启动活动探测之前启动容器的秒数。 默认值为310 |
    | &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | 活动探测超时前等待的秒数。默认值为2秒。 最小值为1 |
    | &emsp;&emsp;`successThreshold` | `success_threshold` | 在失败后将活动探测视为成功的最小连续尝试次数。 默认值为 1。 最小值为1。 |
    | &emsp;&emsp;`failureThreshold` | `failure_threshold` | 当 Pod 启动并且活动探测失败时, Kubernetes 将在放弃之前尝试 failureThreshold 次。 默认值为3。 最小值为1。 |
    | `namespace` | `namespace` | 将 webservice 部署到的 Kubernetes 命名空间。 最多63小写字母数字 (' a-z ', ' 0 '-' 9 ') 和连字符 ('-') 字符。 第一个和最后一个字符不能是连字符。 |

    以下 JSON 是用于 CLI 的示例部署配置:

    ```json
    {
        "computeType": "aks",
        "autoScaler":
        {
            "autoscaleEnabled": true,
            "minReplicas": 1,
            "maxReplicas": 3,
            "refreshPeriodInSeconds": 1,
            "targetUtilization": 70
        },
        "dataCollection":
        {
            "storageEnabled": true
        },
        "authEnabled": true,
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        }
    }
    ```

+ **使用 VS Code**

  还可以[通过 VS Code 扩展部署到 AKS](how-to-vscode-tools.md#deploy-and-manage-models), 但需要提前配置 AKS 群集。

在[AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice)中了解有关 AKS 部署和自动缩放的详细信息。

#### 创建新的 AKS 群集<a id="create-attach-aks"></a>
**时间估计**：大约 20 分钟。

创建或附加 AKS 群集是工作区的一次过程。 可以将此群集重复用于多个部署。 如果删除群集或包含该群集的资源组, 则下次需要部署时, 必须创建新群集。 可以将多个 AKS 群集附加到工作区。

如果要创建用于开发、验证和测试的 AKS 群集, 请在使用`cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)时设置。 使用此设置创建的群集将只有一个节点。

> [!IMPORTANT]
> 设置`cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`创建的 AKS 群集不适用于处理生产流量。 推理时间可能比为生产创建的群集长。 对于开发/测试群集也不保证容错。
>
> 建议创建用于开发/测试的群集使用至少两个虚拟 Cpu。

以下示例演示了如何创建新的 Azure Kubernetes Service 群集:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksComputee.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

有关在 Azure 机器学习 SDK 外创建 AKS 群集的详细信息, 请参阅以下文章:
* [创建 AKS 群集](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [创建 AKS 群集 (门户)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

有关`cluster_purpose`参数的详细信息, 请参阅[AksCompute. ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) reference。

> [!IMPORTANT]
> 对于 [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)，如果为 agent_count 和 vm_size 选择自定义值，则需要确保 agent_count 乘以 vm_size 的结果大于或等于 12 个虚拟 CPU。 例如，如果对 vm_size 使用“Standard_D3_v2”（有 4 个虚拟 CPU），则应该为 agent_count 选择 3 或更大的数字。
>
> Azure 机器学习 SDK 不支持缩放 AKS 群集。 若要缩放群集中的节点, 请在 Azure 门户中使用 AKS 群集的 UI。 只能更改节点计数, 而不能更改群集的 VM 大小。

#### <a name="attach-an-existing-aks-cluster"></a>附加现有的 AKS 群集
**估计时间:** 大约5分钟。

如果 Azure 订阅中已有 AKS 群集, 并且它是版本 1.12. # #, 则可以使用它来部署映像。

> [!WARNING]
> 将 AKS 群集附加到工作区时, 可以通过设置`cluster_purpose`参数来定义使用群集的方式。
>
> 如果未设置`cluster_purpose`参数或设置`cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, 则群集必须有至少12个可用的虚拟 cpu。
>
> 如果设置`cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`了, 则群集不需要具有12个虚拟 cpu。 但是, 为开发/测试配置的群集不适用于生产级别的流量, 可能会增加推理时间。

以下代码演示了如何将现有的 AKS 1.12. # # 群集附加到工作区:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

有关的详细信息`attack_configuration()`, 请参阅[AksCompute. attach_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)引用。

有关`cluster_purpose`参数的详细信息, 请参阅[AksCompute. ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) reference。

## <a name="consume-web-services"></a>使用 Web 服务

每个已部署的 web 服务都提供 REST API, 因此你可以使用各种编程语言来创建客户端应用程序。 如果为服务启用了身份验证, 则需要提供服务密钥作为请求标头中的令牌。

### <a name="request-response-consumption"></a>请求-响应消耗

下面是如何在 Python 中调用服务的示例:
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

有关详细信息，请参阅[创建客户端应用程序以使用 Web 服务](how-to-consume-web-service.md)。


### <a id="azuremlcompute"></a>批处理推理
Azure 机器学习计算目标由 Azure 机器学习服务创建和管理。 它们可用于来自 Azure 机器学习管道的批处理预测。

有关使用 Azure 机器学习计算进行批处理推理的演练, 请参阅[如何运行批预测](how-to-run-batch-predictions.md)一文。

### <a id="iotedge"></a>IoT Edge 推理
对部署到边缘的支持处于预览阶段。 有关详细信息, 请参阅将[Azure 机器学习部署为 IoT Edge 模块一](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)文。


## <a id="update"></a>更新 web 服务

创建新模型时, 必须手动更新要使用新模型的每个服务。 若要更新 Web 服务，请使用 `update` 方法。 下面的代码演示如何将 web 服务更新为使用新模型:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

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

有关示例项目和示例, 请查看[MLOps 存储库](https://github.com/Microsoft/MLOps)

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

