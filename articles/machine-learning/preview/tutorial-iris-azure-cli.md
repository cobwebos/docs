---
title: "有关 Azure 机器学习预览版功能的教程文章 - 命令行接口 | Microsoft Docs"
description: "本教程逐步讲解通过命令行接口完成端到端鸢尾花分类所要执行的步骤。"
services: machine-learning
author: ahgyger
ms.author: ahgyger, ritbhat
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 453c774c97b77dd7829a50fa5e5668d06f817a1d
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2017
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>教程：使用命令行接口将鸢尾花分类
Azure 机器学习服务（预览版）是一个集成式的端到端数据科学和高级分析解决方案，可让专业数据科学家以云的规模准备数据、开发试验和部署模型。

本教程介绍如何使用 Azure 机器学习预览版功能中的命令行接口 (CLI) 工具来执行以下操作： 
> [!div class="checklist"]
> * 设置试验帐户并创建工作区
> * 创建一个项目
> * 将试验提交到多个计算目标
> * 提升并注册已训练的模型
> * 部署 Web 服务以便为新数据评分

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件
- 需要能够访问 Azure 订阅，并有权在该订阅中创建资源。 
- 需要遵循[安装和创建快速入门](quickstart-installation.md)安装 Azure Machine Learning Workbench 应用程序。 

  >[!NOTE]
  >只需在本地 Azure Machine Learning Workbench。 只需遵循标题为“安装 Azure Machine Learning Workbench”的部分中的步骤，因为创建帐户和创建新项目的步骤将由本文中的命令行完成。
 
## <a name="getting-started"></a>入门
使用 Azure 机器学习命令行接口 (CLI) 可以执行端到端数据科学工作流所需的所有任务。 可通过以下方式访问 CLI 工具：

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>选项 1. 从 Azure ML Workbench 登录对话框启动 Azure 机器学习 CLI
首次启动 Azure ML Workbench 和登录时，如果无权访问试验帐户，则会看到以下屏幕：

![找不到帐户](media/tutorial-iris-azure-cli/no_account_found.png)

在对话框中单击“命令行窗口”链接启动命令行窗口。

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>选项 2. 从 Azure ML Workbench 应用启动 Azure 机器学习 CLI
如果已有权访问试验帐户，则可以成功登录。 然后，可以单击“文件” --> “打开命令提示符”菜单打开命令行窗口。

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>选项 3. 在任意命令行窗口中启用 Azure 机器学习 CLI
也可以在任何命令行窗口中启用 Azure 机器学习 CLI。 只需启动命令窗口并输入以下命令：

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
若要使更改永久生效，可以在 Windows 上使用 `SETX`。 对于 macOS，可以使用 `setenv`。

>[!TIP]
>可通过设置上述环境变量，在偏好的终端窗口中启用 Azure CLI。

## <a name="step-1-log-in-to-azure"></a>步骤 1。 登录 Azure
第一步是从 AMLWorkbench 应用打开 CLI（“文件”>“打开命令提示符”）。 这可以确保使用正确的 Python 环境，并能够使用机器学习 CLI 命令。 

然后，需要在 CLI 中设置正确的上下文来访问和管理 Azure 资源。
 
```azure-cli
# log in
$ az login

# list all subscriptions
$ az account list -o table

# set the current subscription
$ az account set -s <subscription id or name>
```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>步骤 2. 创建新的 Azure 机器学习试验帐户和工作区
首先，创建新的试验帐户和新的工作区。 有关试验帐户和工作区的更多详细信息，请参阅 [Azure 机器学习的概念](overview-general-concepts.md)。

> [!NOTE]
> 试验帐户需要一个用于存储试验运行输出的存储帐户。 该存储帐户的名称必须在 Azure 中全局唯一，因为它有一个关联的 URL。 如果未指定现有的存储帐户，系统会使用试验帐户名称创建新的存储帐户。 请确保使用唯一名称，否则会收到类似于“名为 \<storage_account_name> 的存储帐户已被占用”的错误。 或者，可以使用 `--storage` 参数来提供现有存储帐户。

```azure-cli
# create a resource group 
$ az group create --name <resource group name> --location <supported Azure region>

# create a new experimentation account with a new storage account
$ az ml account experimentation create --name <experimentation account name> --resource-group <resource group name>

# create a new experimentation account with an existing storage account
$ az ml account experimentation create --name <experimentation account name>  --resource-group <resource group name> --storage <storage account Azure Resource ID>

# create a workspace in the experimentation account
az ml workspace create --name <workspace name> --account <experimentation account name> --resource-group <resource group name>
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>步骤 2.a（可选）：与同事共享工作区
现在，我们探讨如何与同事共享对工作区的访问权限。 共享试验帐户的访问权限与共享项目的访问权限的步骤相同。 只需更新获取 Azure 资源 ID 的方式。

```azure-cli
# find the workspace Azure Resource ID
$az ml workspace show --name <workspace name> --account <experimentation account name> --resource-group <resource group name>

# add Bob to this workspace as a new owner
$az role assignment create --assignee bob@contoso.com --role owner --scope <workspace Azure Resource ID>
```

> [!TIP]
> 上述命令中的 `bob@contoso.com` 必须是当前订阅所属的目录中的有效 Azure AD 标识。

## <a name="step-3-create-a-new-project"></a>步骤 3. 创建新项目
下一步是创建新项目。 可通过多种方法开始使用新项目。

### <a name="create-a-new-blank-project"></a>创建新的空白项目

```azure-cli
# create a new project
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path>
```

### <a name="create-a-new-project-with-a-default-project-template"></a>使用默认项目模板创建新项目
可以使用默认模板创建新项目。

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>创建与云 Git 存储库关联的新项目
可以创建与 VSTS (Visual Studio Team Service) Git 存储库关联的新项目。 每次提交试验时，都会将整个项目文件夹的快照提交到远程 Git 存储库。 有关更多详细信息，请参阅[将 Git 存储库与 Azure Machine Learning Workbench 项目配合使用](using-git-ml-project.md)。

> [!NOTE]
> Azure 机器学习仅支持在 VSTS 中创建的空 Git 存储库。

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --repo <VSTS repo URL>
```
> [!TIP]
> 如果收到错误“存储库 URL 可能无效，或者用户可能没有访问权限”，可以在 VSTS 中创建安全令牌（“安全性”下的“添加个人访问令牌”菜单），并在创建项目时使用 `--vststoken` 参数。 

### <a name="sample_create"></a>从示例创建新项目
此示例将示例项目用作模板来创建新项目。

```azure-cli
# List the project samples, find the Classifying Iris sample
$ az ml project sample list

# Create a new project from the sample
az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```
创建项目后，使用 `cd` 命令进入项目目录。

## <a name="step-4-run-the-training-experiment"></a>步骤 4：运行训练试验 
以下步骤假设已创建一个包含鸢尾花样本的项目（请参阅[从在线示例创建新项目](#sample_create)）。

### <a name="prepare-your-environment"></a>准备环境 
对于鸢尾花示例，需要安装 matplotlib。

```azure-cli
$ pip install matplotlib
```

###  <a name="submit-the-experiment"></a>提交试验

```azure-cli
# Execute the file
$ az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>使用递减的正则化率迭代试验
结合一定的创造力，只需将用于提交试验的 Python 脚本与不同的正则化率结合在一起。 （可能需要编辑文件，以便指向正确的项目路径。）

```azure-cli
$ python run.py
```

## <a name="step-5-view-run-history"></a>步骤 5. 查看运行历史记录
以下命令列出以前执行的所有运行。 

```azure-cli
$ az ml history list -o table
```
运行以上命令会显示属于此项目的所有运行的列表。 还可以看到列出了准确性和正则化率指标。 这样就可以轻松地从列表中识别最佳运行。

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>步骤 5.a：查看给定运行创建的附件 
若要查看与给定运行关联的附件，可以使用运行历史记录的 info 命令。 从上述列表中找到特定运行的运行 ID。

```azure-cli
$ az ml history info --run <run id> --artifact driver_log
```

若要从运行下载项目，可使用以下命令：

```azure-cli
# Stream a given attachment 
$ az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>步骤 6. 提升运行的项目 
执行的某个运行具有更好的 AUC，因此，我们希望使用它来创建评分 Web 服务，以部署到生产环境。 为此，首先需要将项目提升到资产。

```azure-cli
$ az ml history promote --run <run id> --artifact-path outputs/model.pkl --name model.pkl
```

这会在项目目录中创建一个包含 `model.pkl.link` 文件的 `assets` 文件夹。 此链接文件用于引用提升的资产。

## <a name="step-7-download-the-files-to-be-operationalized"></a>步骤 7. 下载要操作化的文件
现在需要下载提升的模型，以便可以使用这些文件创建预测 Web 服务。 

```azure-cli
$ az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-setup-your-model-management-environment"></a>步骤 8。 设置模型管理环境 
创建环境用于部署 Web 服务。 可以使用 Docker 在本地计算机上运行该 Web 服务。 或者将其部署到 ACS 群集进行大规模操作。 

```azure-cli
# Create new local operationalization environment
$ az ml env setup -l <supported Azure region> -n <env name>
# Once setup is complete, set your environment for current context
$ az ml env set -g <resource group name> -n <env name>
```

## <a name="step-9-create-a-model-management-account"></a>步骤 9. 创建模型管理帐户 
需要使用模型管理帐户在生产环境中部署和跟踪模型。 

```azure-cli
$ az ml account modelmanagement create -n <model management account name> -g <resource group name> -l <supported Azure region>
```

## <a name="step-10-create-a-web-service"></a>步骤 10. 创建 Web 服务
然后，使用部署的模型创建一个可返回预测数据的 Web 服务。 

```azure-cli
$ az ml service create realtime -m asset_download/model.pkl -f score.py -r python –n <web service name>
```

## <a name="step-10-run-the-web-service"></a>步骤 10. 运行 Web 服务
使用前一步骤的输出中的 Web 服务 ID，可以调用该 Web 服务并对其进行测试。 

```azure-cli
# Get web service usage infomration
$ az ml service usage realtime -i <web service id>

# Call the web service with the run command:
$ az ml service run realtime -i <web service id> -d <input data>
```

## <a name="deleting-all-the-resources"></a>删除所有资源 
在本教程的最后，让我们删除已创建的所有资源，除非想要继续使用！ 

为此，只需删除包含所有资源的资源组即可。 

```azure-cli
az group delete --name <resource group name>
```

## <a name="next-steps"></a>后续步骤
本教程已介绍如何使用 Azure 机器学习预览版功能来执行以下操作 
> [!div class="checklist"]
> * 设置试验帐户并创建工作区
> * 创建项目
> * 将试验提交到多个计算目标
> * 提升并注册已训练的模型
> * 创建模型管理帐户用于模型管理
> * 创建环境用于部署 Web 服务
> * 部署 Web 服务并使用新数据评分