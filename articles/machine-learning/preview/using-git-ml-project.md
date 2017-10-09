---
title: "将 Git 存储库与 Azure Machine Learning Workbench 项目配合使用 | Microsoft Docs"
description: "本文介绍如何将 Git 存储库与 Azure Machine Learning Workbench 项目结合使用。"
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: hning86
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 59b07c9834904e01256b75344ba2e6892e56438c
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>将 Git 存储库与 Azure Machine Learning Workbench 项目配合使用
本文档提供有关 Azure Machine Learning Workbench 如何使用 Git 确保数据科学试验重现能力的信息。 此外，提供有关如何将项目与云 Git 存储库关联的说明。

## <a name="introduction"></a>介绍
Azure Machine Learning Workbench 在设计上能够完全与 Git 集成。 创建新项目时，项目文件夹会自动“Git 初始化”为本地 Git 存储库 (repo)，同时，会创建包含名为 _AzureMLHistory/<project_GUID>_ 的分支的另一个隐藏本地 Git 存储库，用于跟踪每次执行发生的项目文件夹更改。 

将 Azure 机器学习项目与托管在 Visual Studio Team Service (VSTS) 项目中的 Git 存储库相关联，可在本地和远程启用自动版本控制。 这种关联可让有权访问远程存储库的任何人将最新源代码下载到另一台计算机（漫游）。  

> [!NOTE]
> VSTS 具有自身的、与 Azure 机器学习试验服务无关的访问控制列表。 用户访问权限可能根据 Git 存储库和 Azure 机器学习工作区或项目的不同而异，并可能需要受到管理。 因此，如果想要将 Azure 机器学习项目与团队成员共享（包括代码级访问权限），则除了单纯地共享工作区以外，还需要显式授予该成员对 VSTS Git 存储库的适当访问权限。 

借助 Git，还可以使用主分支或通过在存储库中创建其他分支来显式管理版本控制。 可以只使用本地 Git 存储库，也可以推送到远程 Git 存储库（如果已预配）。

下图描绘了 VSTS Git 存储库与 Azure 机器学习项目之间的关系：

![AML Git](media/using-git-ml-project/aml_git.png)

若要开始使用远程 Git 存储库，请遵照以下基本说明。

> [!NOTE]
> 目前，Azure 机器学习仅支持 VSTS 帐户中的 Git 存储库。 我们已计划在将来支持常规 Git 存储库（例如 GitHub 等）。

## <a name="step-1-create-an-azure-ml-experimentation-account"></a>步骤 1。 创建 Azure 机器学习试验帐户
如果尚未这样做，请创建 Azure 机器学习试验帐户并安装 Azure ML Workbench 应用。 请参阅[安装和创建快速入门](quickstart-installation.md)中的更多详细信息。

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>步骤 2. 创建团队项目，或使用现有的团队项目
在 [Azure 门户](https://portal.azure.com/)中创建新的**团队项目**。
1. 单击 **+**
2. 搜索“团队项目”
3. 输入所需的信息。
    - 名称：团队名称。
    - 版本控制：**Git**
    - 订阅：具有 Azure 机器学习试验帐户的订阅。
    - 位置：最好是位于靠近 Azure 机器学习试验资源的区域。
4. 单击“创建” 。 

![通过 Azure 门户创建团队项目](media/using-git-ml-project/create_vsts_team.png)


> [!TIP]
> 请确保使用访问 Azure Machine Learning Workbench 时所用的 Azure Active Directory (AAD) 帐户登录。 否则，新团队项目最终可能使用错误的租户 ID，并且 Azure 机器学习可能找不到它。 在这种情况下，必须使用命令行接口并提供 VSTS 令牌。

创建团队项目后，可以转到下一步。

若要直接导航到刚刚创建的团队项目，请使用 URL `https://<team_project_name>.visualstudio.com`。

> [!NOTE]
> 目前，Azure 机器学习仅支持不带任何主分支的空 Git 存储库。 在命令行接口中，可以使用 --force 参数首先删除主分支。 

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>步骤 3. 创建包含远程 Git 存储库的新 Azure 机器学习项目
启动 Azure ML Workbench 并创建新项目。 在 Git 存储库文本框中填充执行步骤 2 时获取的 VSTS Git 存储库 URL。 该 URL 通常类似于：http://<VSTS 帐户名>.visualstudio.com/_git/<项目名称>

![创建包含 Git 存储库的 Azure 机器学习项目](media/using-git-ml-project/create_project_with_git_rep.png)

现已创建启用了远程 Git 存储库集成的新 Azure 机器学习项目，可以继续下一步。 项目文件夹始终 Git 初始化为本地 Git 存储库。 Git“远程”功能设置为远程 VSTS Git 存储库，因此可将提交内容推送到远程 Git 存储库。

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>步骤 3.a：将现有的 Azure 机器学习项目与 VSTS Git 存储库相关联
（可选）还可以创建不带 VSTS Git 存储库的 Azure 机器学习项目，并只依赖于使用本地 Git 存储库生成运行历史记录快照。 稍后可使用以下命令将 VSTS Git 存储库与此现有 Azure 机器学习项目相关联：

```azurecli
# make sure you are in the project path so CLI has context of your current project
az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name
```

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>步骤 4。 捕获 Git 存储库中的项目快照
现在，可在项目中执行几次运行，使每次运行都发生一些更改。 可以从桌面应用或者在 CLI 中使用 `az ml experiment submit` 命令执行此操作。 有关更多详细信息，请遵循[鸢尾花分类教程](tutorial-classifying-iris-part-1.md)。 对于每次运行，如果项目文件夹中的任何文件发生任何更改，则会将整个项目文件夹的快照提交并推送到远程 Git 存储库。 可以通过浏览到 VSTS Git 存储库 URL 查看分支和提交内容。

![运行历史记录分支](media/using-git-ml-project/run_history_branch.png)

## <a name="step-5-restore-a-previous-project-snapshot"></a>步骤 5. 还原以前的项目快照 
通过 AML Workbench 将整个项目文件夹还原到以前的运行历史记录项目快照状态。
1. 在活动栏中单击“运行”（沙漏图标）。
2. 在“运行列表”视图中，单击要还原的运行。
3. 在“运行详细信息”视图中，单击“还原”。

![运行历史记录分支](media/using-git-ml-project/restore_project.png)

或者，可以通过 Azure ML Workbench CLI 窗口使用以下命令。

```azurecli
# discover the run I want to restore snapshot from:
az ml history list -o table

# restore the snapshot from a particular run
az ml project restore --run-id <run_id>
```

执行此命令会将整个项目文件夹覆盖为启动特定的运行时创建的快照。 这意味着，会**丢失当前项目文件夹中的所有更改**。 因此，运行此命令时请多加小心。

## <a name="step-6-use-the-master-branch"></a>步骤 6. 使用主分支
避免意外丢失当前项目状态的一种方法是将该项目提交到 Git 存储库的主分支。 可以直接从命令行（或者偏好的其他所选 Git 客户端工具）使用 Git 来操作主分支。 例如：

```
# make sure you are on the master branch
git checkout master

# stage all changes
git add -A

# commit all changes locally on the master branch
git commit -m 'this is my updates so far'

# push changes into the remote VSTS Git repo master branch.
git push origin master
```

现在，在确认始终可以恢复主分支提交内容的前提下，可以遵循步骤 5，安全地将项目还原到以前的快照。

## <a name="authentication"></a>身份验证
如果只是依赖于使用 Azure 机器学习中的运行历史记录功能来创建项目快照和还原快照，则不需要考虑 Git 存储库身份验证。 试验服务层会处理身份验证。

但是，如果使用自己的 Git 工具来管理版本控制，则需要针对 VSTS 上的远程 Git 存储库正确处理身份验证。 也就是说，在针对该远程 Git 存储库发出 Git 命令之前，需要先在本地计算机上设置对 Git 存储库的身份验证。 

进行此项设置的最简单方法是创建 SSH 密钥对，并将公钥部分上传到 Git 存储库安全设置。

### <a name="generate-ssh-key"></a>生成 SSH 密钥 
首先，让我们在计算机上生成 SSH 密钥对。

#### <a name="on-windows"></a>在 Windows 上：
在 Windows 上启动 Git GUI 桌面应用，在“帮助”菜单下，单击“显示 SSH 密钥”。

![SSH 密钥](media/using-git-ml-project/git_gui.png)

将 SSH 复制到剪贴板。

#### <a name="on-macos"></a>在 macOS 上：
通过命令外壳执行的快速步骤：
```
# generate the SSH key
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# start the SSH agent in the background
$ eval "$(ssh-agent -s)"

# add newly generated SSH key to the SSH agent
$ ssh-add -K ~/.ssh/id_rsa

# display the public key so you can copy it.
$ more ~/.ssh/id_rsa.pub
```
在[此 GitHub 文章](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)中可以找到更详细的步骤。

### <a name="upload-public-key-to-git-repo"></a>将公钥上传到 Git 存储库
通过 https://<VSTS 帐户名>.visualstudio.com 转到 Visual Studio 帐户主页并登录，在虚拟形象下面单击“安全性”。

添加 SSH 公钥，粘贴在上一步骤中获取的 SSH 公钥，并为其命名。 可在此处添加多个密钥。

现在，可在本地针对删除的存储库发出 Git 命令，不需要进一步执行显式身份验证。

### <a name="read-more"></a>了解详细信息
请参阅以下两篇文章（其中任何一种方法都可行），更详细地了解如何针对 VSTS 中的远程 Git 存储库启用本地身份验证。
- [使用 SSH 密钥身份验证](https://www.visualstudio.com/en-us/docs/git/use-ssh-keys-to-authenticate)
- [使用 Git 凭据管理器](https://www.visualstudio.com/en-us/docs/git/set-up-credential-managers)


## <a name="next-steps"></a>后续步骤
参阅[使用 TDSP 将项目结构化](how-to-use-tdsp-in-azure-ml.md)，了解如何使用 Team Data Science Process 来组织项目结构

