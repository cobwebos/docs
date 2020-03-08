---
title: Azure 机器学习的 Git 集成
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习如何与本地 Git 存储库集成。 在从属于 Git 存储库的本地目录提交训练运行时，将在运行期间跟踪有关存储库、分支和当前提交的信息。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402818"
---
# <a name="git-integration-for-azure-machine-learning"></a>Azure 机器学习的 Git 集成

[Git](https://git-scm.com/)是一种常用的版本控制系统，可用于对项目进行共享和协作。 

Azure 机器学习完全支持用于跟踪工作的 Git 存储库-可以直接将存储库克隆到共享工作区文件系统、在本地工作站上使用 Git，或使用 CI/CD 管道中的 Git。

将作业提交到 Azure 机器学习时，如果源文件存储在本地 git 存储库中，则会在训练过程中跟踪有关存储库的信息。

由于 Azure 机器学习从本地 git 存储库跟踪信息，因此它不会绑定到任何特定的中心存储库。 可以从 GitHub、GitLab、Bitbucket、Azure DevOps 或任何其他 git 兼容服务克隆你的存储库。

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>将 Git 存储库克隆到你的工作区文件系统
Azure 机器学习为工作区中的所有用户提供共享文件系统。
若要将 Git 存储库克隆到此文件共享，我们建议你创建一个计算实例，& 打开终端。
打开终端后，可以访问完整的 Git 客户端，并可以通过 Git CLI 体验来克隆和使用 Git。

建议您将存储库克隆到您的用户目录中，这样其他人就不会直接在工作分支上进行冲突。

可以克隆任何可进行身份验证的 Git 存储库（GitHub、Azure Repos、BitBucket 等）

有关如何使用 Git CLI 的指南，请参阅[此处。](https://guides.github.com/introduction/git-handbook/)

## <a name="track-code-that-comes-from-git-repositories"></a>跟踪 Git 存储库中的代码

当你从 Python SDK 或机器学习 CLI 提交定型运行时，训练模型所需的文件将上载到你的工作区。 如果 `git` 命令在你的开发环境中可用，则上传过程将使用它来检查文件是否存储在 git 存储库中。 如果是这样，则您的 git 存储库中的信息也将作为训练运行的一部分上传。 此信息存储在定型运行的以下属性中：

| properties | 用于获取值的 Git 命令 | 说明 |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | 从中克隆存储库的 URI。 |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | 从中克隆存储库的 URI。 |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | 提交运行时的活动分支。 |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | 提交运行时的活动分支。 |
| `azureml.git.commit` | `git rev-parse HEAD` | 提交给运行的代码的提交哈希。 |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | 提交给运行的代码的提交哈希。 |
| `azureml.git.dirty` | `git status --porcelain .` | 如果分支/提交处于脏，则为 `True`;否则，`false`。 |

此信息将为使用估计器、机器学习管道或脚本运行的运行发送。

如果你的培训文件未位于开发环境中的 git 存储库中，或者 `git` 命令不可用，则不会跟踪与 git 相关的信息。

> [!TIP]
> 若要检查 git 命令在你的开发环境中是否可用，请打开 shell 会话、命令提示符、PowerShell 或其他命令行接口，并键入以下命令：
>
> ```
> git --version
> ```
>
> 如果已安装，并且在路径中，会收到类似于 `git version 2.4.1`的响应。 有关在开发环境中安装 git 的详细信息，请参阅[git 网站](https://git-scm.com/)。

## <a name="view-the-logged-information"></a>查看记录的信息

Git 信息存储在定型运行的属性中。 可以使用 Azure 门户、Python SDK 和 CLI 查看此信息。 

### <a name="azure-portal"></a>Azure 门户

1. 在[Azure 门户](https://portal.azure.com)中，选择工作区。
1. 选择 "__试验__"，然后选择一个试验。
1. 从 "__运行号码__" 列中选择一个运行。
1. 选择 "__日志__"，然后展开 "__日志__" 和 " __azureml__ " 条目。 选择以 __###\_azure__开头的链接。

    ![门户中的 # # #_azure 条目](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

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

提交定型运行后，返回[run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)对象。 此对象的 `properties` 属性包含已记录的 git 信息。 例如，下面的代码检索 commit 哈希：

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

`az ml run` CLI 命令可用于从运行中检索属性。 例如，以下命令将返回名为 `train-on-amlcompute`的试验中最后一次运行的属性：

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

有关详细信息，请参阅[az ml run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest)参考文档。

## <a name="next-steps"></a>后续步骤

* [设置并使用模型定型的计算目标](how-to-set-up-training-targets.md)
