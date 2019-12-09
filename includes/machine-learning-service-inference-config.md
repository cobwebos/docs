---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 52689c585e148c18d16ad627570414a8de444fea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926986"
---
`inferenceconfig.json` 文档中的项将映射到[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)类的参数。 下表描述了 JSON 文档中的实体与方法的参数之间的映射：

| JSON 实体 | 方法参数 | 描述 |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | 包含要为映像运行的代码的本地文件的路径。 |
| `runtime` | `runtime` | 可选。 要用于映像的运行时。 当前支持的运行时 `spark-py` 和 `python`。 如果设置 `environment`，则将忽略此设置。 |
| `condaFile` | `conda_file` | 可选。 本地文件的路径，该文件包含要用于映像的 Conda 环境定义。  如果设置 `environment`，则将忽略此设置。 |
| `extraDockerFileSteps` | `extra_docker_file_steps` | 可选。 本地文件的路径，该文件包含设置映像时要运行的其他 Docker 步骤。  如果设置 `environment`，则将忽略此设置。|
| `sourceDirectory` | `source_directory` | 可选。 包含创建映像的所有文件的文件夹的路径。 |
| `enableGpu` | `enable_gpu` | 可选。 是否在映像中启用 GPU 支持。 GPU 映像必须用于 Azure 服务，如 Azure 容器实例、Azure 机器学习计算、Azure 虚拟机和 Azure Kubernetes 服务。 默认值为 False。 如果设置 `environment`，则将忽略此设置。|
| `baseImage` | `base_image` | 可选。 要用作基础映像的自定义映像。 如果未提供任何基本映像，则该映像将基于提供的运行时参数。 如果设置 `environment`，则将忽略此设置。 |
| `baseImageRegistry` | `base_image_registry` | 可选。 包含基本映像的映像注册表。 如果设置 `environment`，则将忽略此设置。|
| `cudaVersion` | `cuda_version` | 可选。 要为需要 GPU 支持的映像安装的 CUDA 版本。 GPU 映像必须用于 Azure 服务，如 Azure 容器实例、Azure 机器学习计算、Azure 虚拟机和 Azure Kubernetes 服务。 支持的版本为9.0、9.1 和10.0。 如果设置 `enable_gpu`，则默认值为9.1。 如果设置 `environment`，则将忽略此设置。 |
| `description` | `description` | 图像的说明。 如果设置 `environment`，则将忽略此设置。  |
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

以下 JSON 是一个示例推理配置，该配置使用具有特定版本的现有 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)与 CLI 配合使用：

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": "1"
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```

以下 JSON 是一个示例推理配置，该配置使用包含最新版本的现有 Azure 机器学习[环境](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)与 CLI 配合使用：

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": null
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```
