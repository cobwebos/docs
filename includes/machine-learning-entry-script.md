---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 2b4f768b25917e712380ca4a7f8ac58cb6140777
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542756"
---
入口脚本接收提交到已部署 Web 服务的数据，并将此数据传递给模型。 然后，该脚本接收模型返回的响应，并将该响应返回给客户端。 该脚本特定于你的模型**。 它必须能够识别模型需要和返回的数据。

该脚本包含两个用于加载和运行模型的函数：

* `init()`：此函数通常将模型载入全局对象。 此函数只能在 Web 服务的 Docker 容器启动时运行一次。

* `run(input_data)`：此函数使用模型来基于输入数据预测值。 运行的输入和输出通常使用 JSON 进行序列化和反序列化。 你也可以使用原始二进制数据。 可以先转换数据，然后再将其发送给模型，或将其返回给客户端。

REST API 预期请求正文是采用以下结构的 JSON 文档：

```json
{
    "data":
        [
            <model-specific-data-structure>
        ]
}
```

下面的示例演示如何加载已注册的 scikit-learn 模型并使用 numpy 数据对其进行评分：

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

若要查看更多示例，请参阅以下脚本：

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Binary Data](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#binary-data)