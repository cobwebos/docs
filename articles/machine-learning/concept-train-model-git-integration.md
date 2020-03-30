---
title: Azure 机器学习的 Git 集成
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习如何与本地 Git 存储库集成。 当从本地目录（Git 存储库）提交训练运行时，系统会将有关存储库、分支和当前提交的信息作为运行的一部分进行跟踪。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402818"
---
# <a name="git-integration-for-azure-machine-learning"></a>Azure 机器学习的 Git 集成

[Git](https://git-scm.com/)是一个流行的版本控制系统，允许您共享和协作您的项目。 

Azure 机器学习完全支持 Git 存储库以进行跟踪工作 - 您可以将存储库直接克隆到共享工作区文件系统上，在本地工作站上使用 Git，或者从 CI/CD 管道中使用 Git。

将作业提交到 Azure 机器学习时，如果源文件存储在本地 git 存储库中，则有关存储库的信息将作为培训过程的一部分进行跟踪。

由于 Azure 机器学习会跟踪来自本地 git 存储库的信息，因此它不会绑定到任何特定的中心存储库。 可以从 GitHub、GitLab、Bitbucket、Azure DevOps 或任何其他与 git 兼容的服务克隆存储库。

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>将 Git 存储库克隆到工作区文件系统中
Azure 机器学习为工作区中的所有用户提供了共享文件系统。
要将 Git 存储库克隆到此文件共享中，我们建议您创建计算实例&打开终端。
打开终端后，您可以访问完整的 Git 客户端，并可通过 Git CLI 体验克隆和使用 Git。

我们建议您将存储库克隆到用户目录中，以便其他人不会直接在您的工作分支上发生冲突。

您可以克隆可以验证的任何 Git 存储库（GitHub、Azure 存储库、Bit Bucket 等）

有关如何使用 Git CLI 的指南，[请阅读此处](https://guides.github.com/introduction/git-handbook/)。

## <a name="track-code-that-comes-from-git-repositories"></a>跟踪来自 Git 存储库的代码

从 Python SDK 或机器学习 CLI 提交训练运行时，训练模型所需的文件将上传到工作区。 如果可在开发环境中使用 `git` 命令，则上传过程会使用该命令检查文件是否存储在 git 存储库中。 如果是，那么 git 存储库中的信息也会作为训练运行的一部分上传。 此信息存储在训练运行的以下属性中：

| properties | 用于获取值的 Git 命令 | 描述 |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | 从中克隆存储库的 URI。 |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | 从中克隆存储库的 URI。 |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | 提交运行时的活动分支。 |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | 提交运行时的活动分支。 |
| `azureml.git.commit` | `git rev-parse HEAD` | 为运行提交的代码的提交哈希。 |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | 为运行提交的代码的提交哈希。 |
| `azureml.git.dirty` | `git status --porcelain .` | 如果分支/提交异常，则为 `True`；否则为 `false`。 |

对于使用估计器、机器学习管道或脚本运行的运行，将发送此信息。

如果训练文件不在开发环境的 git 存储库中，或者 `git` 命令不可用，则不会跟踪与 git 相关的信息。

> [!TIP]
> 要检查 git 命令在开发环境中是否可用，请打开 shell 会话、命令提示符、PowerShell 或其他命令行接口并键入以下命令：
>
> ```
> git --version
> ```
>
> 如果已安装，并且在路径中，您将收到类似于`git version 2.4.1`的响应。 有关在开发环境中安装 git 的详细信息，请参阅[Git 网站](https://git-scm.com/)。

## <a name="view-the-logged-information"></a>查看记录的信息

Git 信息存储在训练运行的属性中。 可以使用 Azure 门户、Python SDK 和 CLI 查看此信息。 

### <a name="azure-portal"></a>Azure 门户

1. 在 [Azure 门户](https://portal.azure.com)中，选择工作区。
1. 选择“试验”，然后选择一个试验____。
1. 从“运行号”列中选择一个运行____。
1. 选择“日志”，然后展开“日志”和“azureml”条目____________。 选择以__###\_azure__开头的链接。

    ![门户中的 ###_azure 条目](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

记录的信息包含类似于以下 JSON 的文本：

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

提交训练运行后，将返回 [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 对象。 此对象的 `properties` 属性包含记录的 git 信息。 例如，以下代码可检索提交哈希：

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

`az ml run` CLI命令可用于从运行中检索属性。 例如，以下命令返回名为 `train-on-amlcompute` 的试验中最后一次运行的属性：

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

有关详细信息，请参阅 [az ml run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) 参考文档。

## <a name="next-steps"></a>后续步骤

* [设置和使用计算目标进行模型培训](how-to-set-up-training-targets.md)
