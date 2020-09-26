---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 4975bb2a8ad384b8abc28f1d1835c2c9e98b8c54
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91315417"
---
入口脚本接收提交到已部署 Web 服务的数据，并将此数据传递给模型。 然后，该脚本接收模型返回的响应，并将该响应返回给客户端。 该脚本特定于你的模型**。 它必须能够识别模型需要和返回的数据。

你需要在输入脚本中完成以下两项操作：

1. 使用名为) 的函数加载模型 (`init()`
1. 使用名为) 的函数对输入数据运行模型 (`run()`

下面将详细介绍这些步骤。

### <a name="writing-init"></a>写入 init ( # A1 

#### <a name="loading-a-registered-model"></a>加载已注册的模型

已注册的模型存储在名为的环境变量指向的路径上 `AZUREML_MODEL_DIR` 。 有关确切目录结构的详细信息，请参阅在 [条目脚本中查找模型文件](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)

#### <a name="loading-a-local-model"></a>加载本地模型

如果你选择不注册模型并将模型作为源目录的一部分传递，则可以通过将模型的路径与计分脚本相对应，在本地读取它，就像在本地一样。 例如，如果你的目录结构如下：

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

可以通过以下 Python 代码加载模型：

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

#### <a name="writing-run"></a>正在编写运行 ( # A1

`run()` 每次您的模型收到计分请求，并期望请求的主体为具有以下结构的 JSON 文档时执行：

```json
{
    "data": <model-specific-data-structure>
}

```

的输入 `run()` 是一个 Python 字符串，其中包含 "数据" 键后面的任何内容。

以下示例演示如何加载已注册的 scikit-learn 模型并使用 numpy 数据对其进行评分：

```python
import json
import numpy as np
import os
from sklearn.externals import joblib


def init():
    global model
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(data):
    try:
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

有关更高级的示例，包括自动 Swagger 架构生成和二进制 (（即映像) 数据），请阅读 [有关高级条目脚本创作的文章](../articles/machine-learning/how-to-deploy-advanced-entry-script.md)