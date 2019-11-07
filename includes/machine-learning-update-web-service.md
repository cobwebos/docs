---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 694d7e6afbf7bd4b219d52254159dcf2e02f8769
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682093"
---
若要更新 web 服务，请使用 `update` 方法。 您可以更新 web 服务以使用新的模型、新的输入脚本或可在推理配置中指定的新依赖项。 有关详细信息，请参阅 Webservice 的文档[。](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-)

> [!IMPORTANT]
> 创建模型的新版本时，必须手动更新要使用的每个服务。

**使用 SDK**

下面的代码演示如何使用 SDK 更新 web 服务的模型、环境和条目脚本：

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**使用 CLI**

还可以使用 ML CLI 更新 web 服务。 下面的示例演示如何注册新模型，然后更新 web 服务以使用新模型：

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> 在此示例中，使用 JSON 文档将模型信息从注册命令传递到 update 命令。
>
> 若要将服务更新为使用新的条目脚本或环境，请创建[推理配置文件](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema)并使用 `ic` 参数指定它。

有关详细信息，请参阅[az ml 服务更新](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update)文档。