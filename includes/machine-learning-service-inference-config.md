---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: f500df6a592769928470d22468ff2fdff18293a5
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88748889"
---
`inferenceconfig.json` 文档中的条目映射到 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 类的参数。 下表描述了 JSON 文档中的实体与方法参数之间的映射：

| JSON 实体 | 方法参数 | 说明 |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | 包含要为映像运行的代码的本地文件路径。 |
| `sourceDirectory` | `source_directory` | 可选。 包含用于创建图像的所有文件的文件夹路径，这样就可以方便地访问此文件夹或子文件夹中的任何文件。 可从本地计算机上传整个文件夹，作为 WebService 的依赖项。 注意：entry_script、conda_file 和 extra_docker_file_steps 路径是 source_directory 路径的相对路径。 |
| `environment` | `environment` | 可选。  Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)。|

可以在推理配置文件中包含 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)的完整规范。 如果工作区中不存在此环境，Azure 机器学习将创建它。 否则，Azure 机器学习将更新环境（如必要）。 以下 JSON 是一个示例：

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn==0.22.1",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

你还可以在单独的 CLI 参数中使用现有的 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)，并从推理配置文件中删除环境键。 使用 -e 代表环境名称，而使用 --ev 代表环境版本。 如果你未指定 --ev，系统将使用最新版本。 下面是该推理配置文件的一个示例：

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

以下命令演示了如何使用以前的推理配置文件（名为 myInferenceConfig.json）来部署模型。 

它还使用现有 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)（名为 AzureML-Minimal）的最新版本。

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
