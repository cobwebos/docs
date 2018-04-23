---
title: 将 Git 存储库与 Azure Machine Learning Workbench 项目配合使用 | Microsoft Docs
description: 本文介绍如何将 Git 存储库与 Azure Machine Learning Workbench 项目结合使用。
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: 6d244e990552ac4899361360efb44be140b06fe8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="use-a-git-repo-with-a-machine-learning-workbench-project"></a>将 Git 存储库与 Machine Learning Workbench 项目配合使用
了解有关 Azure Machine Learning Workbench 如何使用 Git 提供版本控制并确保数据科学试验重现能力。 了解如何将项目与云 Git 存储库 (repo) 关联。

Machine Learning Workbench 设计用于 Git 集成。 创建新项目时，项目文件夹自动“Git 初始化”为本地 Git 存储库。 还会创建第二个隐藏的本地 Git 存储库，并且有一个名为 AzureMLHistory/\<project GUID\> 的分支。 此分支会跟踪每个执行的项目文件夹更改。 

将 Azure 机器学习项目与 Git 存储库相关联可在本地或远程实现自动版本控制。 Git 存储库托管在 Visual Studio Team Services (Team Services) 中。 由于机器学习项目与 Git 存储库相关联，有权访问远程存储库的任何人都可以将最新的源代码下载到另一台计算机中（漫游）。  

> [!NOTE]
> Team Services 有自己的访问控制列表 (ACL)，其与 Azure 机器学习试验服务无关。 Git 存储库和机器学习工作区或项目之间的用户访问权限可能有所不同。 可能需要管理访问权限。 
> 
> 无论是希望授予团队成员对机器学习项目的代码级访问权限，还是仅共享工作区，都需要向用户授予访问 Team Services Git 存储库的正确权限。 

若要使用 Git 管理版本控制，可以使用主分支或在存储库中创建其他分支。 也可以使用本地 Git 存储库，然后推送到远程 Git 存储库（如果已预配）。

下图描绘了 Team Services Git 存储库与机器学习项目之间的关系：

![Azure 机器学习 Git](media/using-git-ml-project/aml_git.png)

若要开始使用远程 Git 存储库，请完成以下各节所述的步骤。

> [!NOTE]
> 目前，Azure 机器学习仅支持 Team Services 帐户中的 Git 存储库。 计划在将来针对常规 Git 存储库（如 GitHub）提供支持。

## <a name="step-1-create-a-machine-learning-experimentation-account"></a>步骤 1。 创建机器学习试验帐户
创建机器学习试验帐户并安装 Azure Machine Learning Workbench 应用。 有关详细信息，请参阅[安装 和创建快速入门](../service/quickstart-installation.md)。

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>步骤 2. 创建团队项目，或使用现有的团队项目
在 [Azure 门户](https://portal.azure.com/)中，创建新的团队项目：
1. 选择 +。
2. 搜索“团队项目”。
3. 输入所需信息：
    - **名称**：团队名称。
    - **版本控制**：选择 Git。
    - **订阅**：选择拥有机器学习试验帐户的订阅。
    - **位置**：最好选择靠近机器学习试验资源的区域。
4. 选择**创建**。 

![在 Azure 门户创建团队项目](media/using-git-ml-project/create_vsts_team.png)

请确保使用访问 Machine Learning Workbench 时所用的相同 Azure Active Directory (Azure AD) 帐户登录。 否则，系统无法使用 Azure AD 凭据访问 Machine Learning Workbench。 不过，如果使用命令行来创建机器学习项目，并提供个人访问令牌来访问 Git 存储库，则是一种例外情况。 我们会在文章后面部分详细讨论这一点。

若要直接转到所创建的团队项目，请使用 URL https://\<team project name\>.visualstudio.com。

## <a name="step-3-set-up-a-machine-learning-project-and-git-repo"></a>步骤 3. 设置机器学习项目和 Git 存储库

有两种方法可设置机器学习项目：
- 创建具有远程 Git 存储库的机器学习项目
- 将现有机器学习项目与 Team Services Git 存储库关联起来

### <a name="create-a-machine-learning-project-that-has-a-remote-git-repo"></a>创建具有远程 Git 存储库的机器学习项目
打开 Machine Learning Workbench 并创建新项目。 在“Git 存储库”框中，输入步骤 2 中的 Team Services Git 存储库 URL。 该 URL 通常类似于：https://\<Team Services account name\>.visualstudio.com/_git/\<project name\>

![创建具有 Git 存储库的机器学习项目](media/using-git-ml-project/create_project_with_git_rep.png)

也可以使用 Azure 命令行工具 (Azure CLI) 来创建此项目。 可选择输入个人访问令牌。 机器学习可以使用此令牌来访问 Git 存储库，而不使用 Azure AD 凭据：

```
# Create a new project that has a Git repo by using a personal access token.
$ az ml project create -a <Experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <Team Services personal access token>
```

> [!IMPORTANT]
> 如果选择空白项目模板，选择使用的 Git 存储库可能已有主分支。 机器学习只需在本地克隆此主分支即可。 这会将 aml_config 文件夹和其他项目元数据文件添加到本地项目文件夹。 
>
> 如果选择任何其他项目模板，Git 存储库中则不能有主分支。 否则，便会出现错误。 另一种方法是使用 `az ml project create` 命令行创建具有 `--force` 开关的项目。 这会删除原始主分支中的文件，并将其替换为所选模板中的新文件。

启用了远程 Git 存储库集成的新机器学习项目便已创建完成。 项目文件夹始终 Git 初始化为本地 Git 存储库。 Git 远程功能设置为远程 Team Services Git 存储库，因此可将提交内容推送到远程 Git 存储库。

### <a name="associate-an-existing-machine-learning-project-with-a-team-services-git-repo"></a>将现有机器学习项目与 Team Services Git 存储库关联起来
还可以创建不带 Team Services Git 存储库的机器学习项目，并依赖本地 Git 存储库生成运行历史记录快照。 随后，可以使用以下命令将 Team Services Git 存储库与此现有机器学习项目关联起来：

```azurecli
# Ensure that you are in the project path so Azure CLI has the context of your current project.
$ az ml project update --repo https://<Team Services account name>.visualstudio.com/_git/<project name>
```

> [!NOTE] 
> 只能对未关联 Git 存储库的机器学习项目执行更新存储库操作。 另外，将 Git 存储库与机器学习相关联后，便无法删除它。

## <a name="step-4-capture-a-project-snapshot-in-the-git-repo"></a>步骤 4. 捕获 Git 存储库中的项目快照
可在项目中执行几次脚本运行操作，并在运行操作之间做出一些更改。 可以在桌面应用或者在 Azure CLI 中使用 `az ml experiment submit` 命令执行此操作。 有关详细信息，请参阅 [Iris 分类教程](tutorial-classifying-iris-part-1.md)。 对于每次运行，如果项目文件夹中的任何文件发生任何更改，则会将整个项目文件夹的快照提交并推送到远程 Git 存储库中名为 AzureMLHistory/\<project GUID\> 的分支下。 若要查看这些分支和提交内容（包括 AzureMLHistory/\<project GUID\> 分支），请转到 Team Services Git 存储库 URL。 

> [!NOTE] 
> 仅在执行脚本之前提交快照。 当前，数据准备执行或 Notebook 单元执行不会触发快照。

![运行历史记录分支](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> 最好不要在历史记录分支中使用 Git 命令进行操作。 因为它可能会妨碍运行历史记录。 应使用主分支或为自己的 Git 操作创建其他分支。

## <a name="step-5-restore-a-previous-project-snapshot"></a>步骤 5。 还原以前的项目快照 
要将整个项目文件夹恢复到之前的运行历史记录快照状态，请在 Machine Learning Workbench 中执行以下操作：
1. 在活动栏（沙漏图标）中，选择“运行”。
2. 在“运行列表”视图中，选择要还原的运行。
3. 在“运行详细信息”视图中，选择“还原”。

![运行历史记录分支](media/using-git-ml-project/restore_project.png)

（可选）可以在 Machine Learning Workbench 的 Azure CLI 窗口中使用以下命令：

```azurecli
# Discover the run I want to restore a snapshot from.
$ az ml history list -o table

# Restore the snapshot from a specific run.
$ az ml project restore --run-id <run ID>
```

运行此命令时务必小心。 执行此命令会将整个项目文件夹覆盖为启动特定的运行时创建的快照。 但项目会保持在当前分支上。 这意味着，会丢失当前项目文件夹中所有的更改。  

执行此操作之前，可能要使用 Git 将更改提交到当前分支。

## <a name="step-6-use-the-master-branch"></a>步骤 6. 使用主分支
避免意外丢失当前项目状态的一种方法是将该项目提交到 Git 存储库的主分支（或自行创建的任何分支）。 可以从命令行或者其他喜欢的 Git 客户端工具使用 Git 来对主分支进行操作。 例如：

```sh
# Check status to make sure you are on the master branch (or branch of your choice).
$ git status

# Stage all changes.
$ git add -A

# Commit all changes locally on the master branch.
$ git commit -m 'these are my updates so far'

# Push changes to the remote Team Services Git repo master branch.
$ git push origin master
```

现在，可以通过完成步骤 5 来将项目安全还原到先前的快照。 可随时返回到刚刚在主分支上进行的提交操作。

## <a name="authentication"></a>身份验证
如果只是依赖于机器学习中的运行历史记录功能来创建项目快照及还原快照，则不需要考虑 Git 存储库身份验证的问题。 由机器学习试验服务层处理身份验证。

但是，如果使用自己的 Git 工具来管理版本控制，则需要针对 Team Services 中的远程 Git 存储库处理身份验证。 在机器学习中，使用 HTTPS 协议，将远程 Git 存储库作为 Git 远程添加到本地存储库。 这意味着在向此远程发出 Git 命令（例如推送或拉取）时，需要提供用户名、密码或个人访问令牌。 若要在 Team Services Git 存储库中创建个人访问令牌，请按照[使用个人访问令牌进行身份验证](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate)中的说明进行操作。

## <a name="next-steps"></a>后续步骤
- 了解如何[使用 Team Data Science Process 来组织项目结构](how-to-use-tdsp-in-azure-ml.md)。
