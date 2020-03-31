---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159399"
---
`inferenceconfig.json` 文档中的条目映射到 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 类的参数。 下表描述了 JSON 文档中的实体与方法参数之间的映射：

| JSON 实体 | 方法参数 | 描述 |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | 包含要为映像运行的代码的本地文件路径。 |
| `sourceDirectory` | `source_directory` | 可选。 包含创建映像的所有文件的文件夹的路径，从而轻松访问此文件夹或子文件夹中的任何文件。 可以从本地计算机上载整个文件夹作为 Web 服务的依赖项。 注意：entry_script、conda_file和extra_docker_file_steps路径是source_directory路径的相对路径。 |
| `environment` | `environment` | 可选。  Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)。|

您可以在推理配置文件中包括 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)的完整规范。 如果工作区中不存在此环境，Azure 机器学习将创建它。 否则，Azure 机器学习将在必要时更新环境。 以下 JSON 就是一个示例：

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
                            "scikit-learn",
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

还可以在分离的 CLI 参数中使用现有的 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)，并从推理配置文件中删除"环境"密钥。 将 -e 用于环境名称，对于环境版本使用 -ev。 如果不指定 --ev，将使用最新版本。 下面是推理配置文件的示例：

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

以下命令演示如何使用以前的推理配置文件（名为 myinferenceConfig.json）部署模型。 

它还使用现有 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)的最新版本（称为 AzureML-最小）。

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
