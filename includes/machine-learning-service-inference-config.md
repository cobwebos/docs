---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 6970732f53ffa99849e20f279c8bdf4160c30a0a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845136"
---
`inferenceconfig.json` 文档中的项将映射到[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)类的参数。 下表描述了 JSON 文档中的实体与方法的参数之间的映射：

| JSON 实体 | 方法参数 | Description |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | 包含要为映像运行的代码的本地文件的路径。 |
| `runtime` | `runtime` | 可选。 要用于映像的运行时。 支持的运行时 `spark-py` 和 `python`。 如果设置 `environment`，则忽略此项。 |
| `condaFile` | `conda_file` | 可选。 本地文件的路径，该文件包含要用于映像的 Conda 环境定义。  如果设置 `environment`，则忽略此项。 |
| `extraDockerFileSteps` | `extra_docker_file_steps` | 可选。 本地文件的路径，该文件包含设置映像时要运行的其他 Docker 步骤。  如果设置 `environment`，则忽略此项。|
| `sourceDirectory` | `source_directory` | 可选。 包含创建映像的所有文件的文件夹的路径，这样就可以轻松地访问此文件夹或子文件夹中的任何文件。 你可以从本地计算机上传整个文件夹，作为 Webservice 的依赖项。 注意： entry_script、conda_file 和 extra_docker_file_steps 路径是 source_directory 路径的相对路径。 |
| `enableGpu` | `enable_gpu` | 可选。 是否在映像中启用 GPU 支持。 GPU 映像必须用于 Azure 服务，如 Azure 容器实例。 例如，Azure 机器学习计算、Azure 虚拟机和 Azure Kubernetes 服务。 默认值为 False。 如果设置 `environment`，则忽略此项。|
| `baseImage` | `base_image` | 可选。 要用作基础映像的自定义映像。 如果未提供任何基本映像，则该映像将基于提供的运行时参数。 如果设置 `environment`，则忽略此项。 |
| `baseImageRegistry` | `base_image_registry` | 可选。 包含基本映像的映像注册表。 如果设置 `environment`，则忽略此项。|
| `cudaVersion` | `cuda_version` | 可选。 要为需要 GPU 支持的映像安装的 CUDA 版本。 必须在 Azure 服务中使用 GPU 映像。 例如，Azure 容器实例、Azure 机器学习计算、Azure 虚拟机和 Azure Kubernetes 服务。 支持的版本为9.0、9.1 和10.0。 如果设置 `enable_gpu`，则默认值为9.1。 如果设置 `environment`，则忽略此项。 |
| `description` | `description` | 图像的说明。 如果设置 `environment`，则忽略此项。  |
| `environment` | `environment` | 可选。  Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)。|

以下 JSON 是用于 CLI 的示例推理配置：

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```

可以在推理配置文件中包含 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)的完整规范。 如果工作区中不存在此环境，Azure 机器学习将创建它。 否则，Azure 机器学习将更新环境（如有必要）。 下面的 JSON 是一个示例：

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

你还可以在单独的 CLI 参数中使用现有 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)，并从推理配置文件中删除 "环境" 密钥。 将-e 用于环境名称，将--ev 用于环境版本。 如果未指定--ev，将使用最新版本。 下面是推理配置文件的示例：

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

以下命令演示了如何使用以前的推理配置文件（名为 myInferenceConfig）来部署模型。 

它还使用最新版本的现有 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)（名为 AzureML-最小）。

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
