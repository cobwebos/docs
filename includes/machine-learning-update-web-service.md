---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: e6a7ee8ed497c87c08f13e09a0022b0835c952cc
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82975081"
---
若要更新 Web 服务，请使用 `update` 方法。 你可以更新 Web 服务，以使用可以在推理配置中指定的新模型、新入口脚本或新依赖项。 有关详细信息，请参阅 [Webservice.update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-) 的文档。

> [!IMPORTANT]
> 创建模型的新版本时，必须手动更新要使用的每个服务。
>
> 不能使用 SDK 来更新从 Azure 机器学习设计器发布的 Web 服务。

**使用 SDK**

下面的代码演示如何使用 SDK 更新 Web 服务的模型、环境和入口脚本：

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

还可以使用 ML CLI 更新 Web 服务。 以下示例演示如何注册新模型，然后更新 Web 服务以使用新模型：

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> 此示例使用 JSON 文档将模型信息从注册命令传递到更新命令。
>
> 若要更新服务以使用新的入口脚本或环境，请创建[推理配置文件](/azure/machine-learning/reference-azure-machine-learning-cli#inference-configuration-schema)并使用 `ic` 参数指定它。

有关详细信息，请参阅 [az ml 服务更新](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update)文档。