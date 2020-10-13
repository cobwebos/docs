---
title: 为高级方案创作入口脚本
titleSuffix: Azure Machine Learning entry script authoring
description: 了解如何在部署期间编写用于预处理和后处理的 Azure 机器学习条目脚本。
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: gopalv
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 3bd4953812ec88f28ac16956a85c95afc5bb8a38
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999219"
---
# <a name="advanced-entry-script-authoring"></a>高级入口脚本创作

本文介绍如何编写用于专用用例的入口脚本。

## <a name="prerequisites"></a>必备条件

本文假设你已有一个要使用 Azure 机器学习进行部署的经过训练的机器学习模型。 若要详细了解模型部署，请参阅[此教程](how-to-deploy-and-where.md)。

## <a name="automatically-generate-a-swagger-schema"></a>自动生成 Swagger 架构

若要为 Web 服务自动生成架构，请在一个已定义的类型对象的构造函数中提供输入和/或输出的示例。 该类型和示例用于自动创建架构。 Azure 机器学习随后会在部署过程中创建 Web 服务的 [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) 规范。

当前支持以下类型：

* `pandas`
* `numpy`
* `pyspark`
* 标准 Python 对象

若要使用架构生成，请 `inference-schema` 在依赖项文件中包括开源包版本1.1.0 或更高版本。 若要详细了解此包，请参阅 [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema)。 为了生成相容的 swagger 自动 web 服务使用，计分脚本运行 ( # A1 函数必须具有以下 API 形状：
* 名为 "StandardPythonParameterType" 的第一个参数，名为 "输入"，嵌套的包含 PandasDataframeParameterTypes。
* 名为 GlobalParameter 的可选的第二个名为 "StandardPythonParameterType" 的参数，该参数不是嵌套类型。
* 返回 "StandardPythonParameterType" 类型的字典，该字典可能嵌套了包含 PandasDataFrameParameterTypes。
定义 `input_sample` 和 `output_sample` 变量中的输入和输出示例格式，它们表示 Web 服务的请求和响应格式。 在 `run()` 函数的输入和输出函数修饰器中使用这些示例。 以下 scikit-learn 示例使用架构生成功能。



```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')

    # If your model were stored in the same directory as your score.py, you could also use the following:
    # model_path = os.path.abspath(os.path.join(os.path.dirname(__file_), 'sklearn_mnist_model.pkl')

    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

## <a name="power-bi-compatible-endpoint"></a>Power BI 兼容终结点 

下面的示例演示如何根据以上说明定义 API 形状。 此方法支持使用 Power BI 中已部署的 Web 服务。 （[详细了解如何使用 Power BI 中的 Web 服务](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。）

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)

# providing 3 sample inputs for schema generation
numpy_sample_input = NumpyParameterType(np.array([[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]],dtype='float64'))
pandas_sample_input = PandasParameterType(pd.DataFrame({'name': ['Sarah', 'John'], 'age': [25, 26]}))
standard_sample_input = StandardPythonParameterType(0.0)

# This is a nested input sample, any item wrapped by `ParameterType` will be described by schema
sample_input = StandardPythonParameterType({'input1': numpy_sample_input, 
                                            'input2': pandas_sample_input, 
                                            'input3': standard_sample_input})

sample_global_parameters = StandardPythonParameterType(1.0) #this is optional
sample_output = StandardPythonParameterType([1.0, 1.0])

@input_schema('inputs', sample_input)
@input_schema('global_parameters', sample_global_parameters) #this is optional
@output_schema(sample_output)
def run(inputs, global_parameters):
    try:
        data = inputs['input1']
        # data will be convert to target format
        assert isinstance(data, np.ndarray)
        result = model.predict(data)
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```


## <a name="binary-ie-image-data"></a><a id="binary-data"></a> 二进制（即图像）数据

如果模型接受二进制数据（如映像），则必须修改用于部署的 `score.py` 文件以接受原始 HTTP 请求。 若要接受原始数据，请在入口脚本中使用 `AMLRequest` 类，并向 `run()` 函数添加 `@rawhttp` 修饰器。

下面是接受二进制数据的 `score.py` 的示例：

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
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLRequest` 类位于 `azureml.contrib` 命名空间中。 此命名空间中的实体会频繁更改，因为我们正在改进服务。 此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持这些内容。
>
> 如果需要在本地开发环境中对此进行测试，可以使用以下命令安装这些组件：
>
> ```shell
> pip install azureml-contrib-services
> ```

`AMLRequest` 类仅允许访问 score.py 中的原始发布数据，没有客户端组件。 从客户端中，像往常一样发布数据。 例如，以下 Python 代码读取图像文件并发布数据：

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = requests.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

## <a name="cross-origin-resource-sharing-cors"></a>跨域资源共享 (CORS)

通过跨源资源共享 (CORS) 可以从其他域请求网页上的资源。 CORS 通过 HTTP 标头工作，这些标头通过客户端请求发送并随服务响应返回。 若要详细了解 CORS 和有效标头，请参阅维基百科上的[跨域资源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)。

若要配置模型部署以支持 CORS，请在入口脚本中使用 `AMLResponse` 类。 使用此类，可设置响应对象的标头。

以下示例在入口脚本中设置响应的 `Access-Control-Allow-Origin` 标头：

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
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse` 类位于 `azureml.contrib` 命名空间中。 此命名空间中的实体会频繁更改，因为我们正在改进服务。 此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持这些内容。
>
> 如果需要在本地开发环境中对此进行测试，可以使用以下命令安装这些组件：
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure 机器学习仅将 POST 和 GET 请求路由到运行评分服务的容器。 这可能导致错误，因为浏览器使用 OPTIONS 请求预检 CORS 请求。
> 


## <a name="load-registered-models"></a>加载已注册的模型

可以通过两种方法在入口脚本中查找模型：
* `AZUREML_MODEL_DIR`：一个包含模型位置路径的环境变量。
* `Model.get_model_path`：一个 API，使用注册的模型名称返回指向模型文件的路径。

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR 是在服务部署过程中创建的环境变量。 可以使用此环境变量来查找部署的模型的位置。

下表描述了 AZUREML_MODEL_DIR 的值，它的值取决于部署的模型数：

| 部署 | 环境变量值 |
| ----- | ----- |
| 单个模型 | 包含模型的文件夹的路径。 |
| 多个模型 | 包含所有模型的文件夹的路径。 各个模型按名称和版本放置在此文件夹中 (`$MODEL_NAME/$VERSION`) |

在模型注册和部署过程中，会将模型放置在 AZUREML_MODEL_DIR 路径中，并保留它们的原始文件名。

若要在入口脚本中获取某个模型文件的路径，请将此环境变量与要查找的文件路径组合在一起。

**单个模型示例**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**多个模型示例**

在此方案中，向工作区注册两个模型：

* `my_first_model`：包含一个文件 (`my_first_model.pkl`)，并且只有一个版本 (`1`)。
* `my_second_model`：包含一个文件 (`my_second_model.pkl`)，有两个版本；`1` 和 `2`。

部署服务后，部署操作中将同时提供两种模型：

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

在托管服务的 Docker 映像中，`AZUREML_MODEL_DIR` 环境变量包含模型所在的目录。
在此目录中，每个模型都位于 `MODEL_NAME/VERSION` 的目录路径中。 其中 `MODEL_NAME` 是已注册的模型的名称，`VERSION` 是模型的版本。 构成已注册的模型的文件存储在这些目录中。

在此示例中，路径将是 `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` 和 `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl`。


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

### <a name="get_model_path"></a>get_model_path

注册模型时，请提供用于在注册表中管理该模型的模型名称。 将此名称与 [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) 方法结合使用，以检索本地文件系统上一个或多个模型文件的路径。 如果注册文件夹或文件集合，此 API 会返回包含这些文件的目录的路径。

注册模型时，请为其指定一个名称。 该名称对应于模型的放置位置（本地位置或在服务部署过程中指定的位置）。

## <a name="framework-specific-examples"></a>特定于框架的示例

有关特定机器学习用例的更多条目脚本示例，请参阅以下内容：

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.yml)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

## <a name="next-steps"></a>后续步骤

* [排查部署失败的问题](how-to-troubleshoot-deployment.md)
* [部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md)
* [创建客户端应用程序以使用 Web 服务](how-to-consume-web-service.md)
* [更新 Web 服务](how-to-deploy-update-web-service.md)
* [如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)
* [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
* [为模型部署创建事件警报和触发器](how-to-use-event-grid.md)
