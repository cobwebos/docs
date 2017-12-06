---
title: "Azure Machine Learning Workbench 中的漫游和协作 | Microsoft Docs"
description: "已知问题的列表和帮助进行故障排除的指南"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 50f48fb096cb907e050769a8a4159689eb25418c
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench 中的漫游和协作
本文档逐步讲解如何借助 Azure Machine Learning Workbench 在不同的计算机上实现项目漫游，并实现与团队成员的协作。 

创建包含远程 Git 存储库 (repo) 链接的 Azure 机器学习项目时，项目元数据和快照将存储在云中。 使用云链接可从不同的计算机访问该项目（漫游）。 还可以向同事授予访问权限，从而实现协作。 

## <a name="prerequisites"></a>先决条件
首先，安装可以访问试验帐户的 Azure Machine Learning Workbench。 有关更多详细信息，请参阅[安装指南](quickstart-installation.md)。

其次，访问 [Visual Studio Team System](https://www.visualstudio.com)，并创建要将项目链接到的存储库。 有关 Git 的详细信息，请参阅[将 Git 存储库与 Azure Machine Learning Workbench 项目配合使用](using-git-ml-project.md)一文。

## <a name="create-a-new-azure-machine-learning-project"></a>创建新的 Azure 机器学习项目
启动 Azure Machine Learning Workbench，并创建新项目（例如 _iris_）。 在“Visualstudio.com GIT 存储库 URL”文本框中填充有效的 VSTS Git 存储库 URL。 

> [!IMPORTANT]
> 如果选择空白项目模板，但选择的 Git 存储库已有主分支，也是可以的。 Azure 机器学习只需在本地克隆主分支，并将 `aml_config` 文件夹和其他项目元数据文件添加到本地项目文件夹中。 但是，如果选择任何其他项目模板，则 Git 存储库一定不能拥有主分支，否则会出现错误。 替代方法是使用 `az ml project create` 命令行工具创建项目，并提供 `--force` 开关。 这将删除原始主分支上的文件，并将其替换为所选模板中的新文件。

创建项目后，通过项目中的任何脚本提交几个运行。 此操作会将项目状态提交到远程 Git 存储库的运行历史记录分支。 

> [!NOTE] 
> 仅脚本运行会触发到运行历史记录分支的提交。 数据准备执行或笔记本运行不会触发运行历史记录分支上的项目快照。

如果已设置 Git 身份验证，则还可以在主分支中显式操作，或创建新分支。 

示例： 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>漫游
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>在另一台计算机上打开 Azure Machine Learning Workbench
将 VSTS Git 存储库链接到项目后，可以从装有 Azure Machine Learning Workbench 的任何计算机访问 _iris_ 项目。 

若要在另一台计算机上访问 iris 项目，需要使用创建项目时所用的相同凭据登录到应用。 此外，需要导航到相同的试验帐户和工作区。 _iris_ 项目连同其他项目按字母顺序列在工作区中。 

### <a name="download-project-on-second-machine"></a>在第二台计算机上下载项目
在第二台计算机上打开工作区后，_iris_ 项目旁边的图标不同于典型的文件夹图标。 下载图标表示项目的内容在云中，需要下载到当前计算机。 

![创建项目](./media/roaming-and-collaboration/downloadable-project.png)

单击 _iris_ 项目启动下载操作。 花费片刻时间完成下载后，便可以在第二台计算机上访问该项目。 

在 Windows 上，访问路径为 `C:\Users\<username>\Documents\AzureML`

在 macOS 上，访问路径为 `/home/<username>/Documents/AzureML`

我们已计划在将来的版本中增强此功能，以允许选择目标文件夹。 

> [!NOTE]
> 如果 Azure 机器学习目录中恰好有一个与项目完全同名的文件夹，则下载将会失败。 目前，需要重命名现有文件夹才能解决此问题。


### <a name="work-on-the-downloaded-project"></a>处理下载的项目 
新下载的项目反映最后一次在项目中执行运行时的项目状态。 每次提交运行时，项目状态的快照会自动提交到 VSTS Git 存储库中的运行历史记录分支。 在第二台计算机上实例化项目时，我们使用与最新运行关联的快照。 
 

## <a name="collaboration"></a>协作
可与团队成员针对已链接到 VSTS Git 存储库的项目展开协作。 可将试验帐户、工作区和项目的权限分配给用户。 此时，可以使用 Azure CLI 执行 Azure 资源管理器命令。 也可以使用 [Azure 门户](https://portal.azure.com)。 请参阅[以下部分](#portal)。    

### <a name="using-command-line-to-add-users"></a>使用命令行添加用户
我们举个例子。 假设 Alice 是 _Iris_ project 项目的所有者，她希望与 Bob 共享访问权限。 

Alice 单击“文件”菜单，选择“命令提示符”菜单项启动配置为在 _iris_ 项目中运行的命令提示符。 然后，Alice 可通过执行以下命令，确定要向 Bob 授予哪种访问级别。  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the Workspace by inheritance. And he can invite or remove others.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace ARM ID>
```

直接或通过继承分配角色后，Bob 可以看到 Workbench 项目列表中的项目。 可能需要重启该应用程序才能看到该项目。 然后，Bob 可以根据[“漫游”部分](#roaming)中所述下载项目并与 Alice 协作。 

针对项目展开协作的所有用户的运行历史记录会提交到同一个远程 Git 存储库。 因此，当 Alice 执行某个运行时，Bob 可以在 Workbench 应用的项目运行历史记录部分中查看该运行。 Bob 还可将项目还原到任何运行的状态，包括 Alice 启动的运行。 

共享项目的远程 Git 存储库还可让 Alice 和 Bob 针对主分支展开协作。 如果需要，他们还可以创建个人分支，并使用 Git 提取请求与合并功能展开协作。 

### <a name="using-azure-portal-to-add-users"></a>使用 Azure 门户添加用户
<a name="portal"></a>

Azure 机器学习试验帐户、工作区和项目都是 Azure 资源管理器资源。 可以使用 [Azure 门户](https://portal.azure.com)中的“访问控制”链接分配角色。 

在“所有资源”视图中找到想要将用户添加到的资源。 在页面中单击“访问控制(IAM)”链接。 添加用户 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>协作工作流示例
我们将逐步演示一个示例，以介绍协作流。 Contoso 员工 Alice 和 Bob 想要使用 Azure ML Workbench 来协作完成一个数据科学项目。 他们的标识属于同一个 Contoso Azure AD 租户。

1. 首先，Alice 在 VSTS 项目中创建了一个空 Git 存储库。 此 VSTS 项目应位于在 Contoso AAD 租户下创建的 Azure 订阅中。 

2. 然后，Alice 在自己的计算机上创建了一个 Azure ML 试验帐户，一个工作区和一个 Azure ML Workbench 项目。 创建此项目时她提供了 Git 存储库 URL。

3. Alice 开始处理此项目。 她创建了一些脚本，并执行了一些运行。 对于每个运行，都会将整个项目文件夹的快照作为提交自动推送到 Workbench 创建的 VSTS Git 存储库的运行历史记录分支中。

4. 现在 Alice 对目前的工作很满意。 她想要将她的更改提交到本地主分支中，并将其推送到 VSTS Git 存储库主分支。 为了实现此操作，项目打开后，她从 Azure ML Workbench 启动了命令提示符窗口，并发出以下命令：
    
    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # stage all changes
    $ git add -A

    # commit changes with a comment
    $ git commit -m "this is a good milestone"

    # push the commit to the master branch of the remote Git repo in VSTS
    $ git push
    ```

5. 然后 Alice 将 Bob 作为参与者添加到工作区。 她可以通过 Azure 门户或使用上述 `az role assignment` 命令来实现此操作。 她还授予了 Bob 对 VSTS Git 存储库的读取/写入权限。

6. 现在 Bob 可以在自己的计算机上登录 Azure ML Workbench。 他可以看到 Alice 与他共享的工作区，以及该工作区下列出的项目。 

7. Bob 单击项目名称，项目即下载到他的计算机中。
    
    a. 下载的项目文件是运行历史记录中记录的最新运行的快照的克隆。 它们不是主分支上的最新提交。
    
    b. 本地项目文件夹设置在主分支上，其中包含未暂存的更改。

8. 现在 Bob 可以浏览 Alice 执行的运行，并可以还原以前任何运行的快照。

9. Bob 想要获取 Alice 推送的最新更改，并开始在其他分支上工作。 因此，他从 Azure ML Workbench 打开命令提示符窗口，并执行以下命令：

    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # get the latest commit in VSTS Git master branch and overwrite current files
    $ git pull --force

    # create a new local branch named "bob" so Bob's work is done on the "bob" branch
    $ git checkout -b bob
    ```

10. 现在 Bob 修改了此项目，并提交了新运行。 这些更改是在 bob 分支上完成的。 Alice 也可看到 Bob 的运行。

11. 现在 Bob 准备将其更改推送到远程 Git 存储库。 为了避免与 Alice 正在使用的主分支冲突，他决定将其工作推送到也叫做 bob 的新远程分支中。

    ```sh
    # verify that the current branch is "bob" and it has unstaged changes
    $ git status
    
    # stage all changes
    $ git add -A

    # commit them with a comment
    $ git commit -m "I found a cool new trick."

    # create a new branch on the remote VSTS Git repo, and push changes
    $ git push origin bob
    ```

12. 然后 Bob 可以告诉 Alice 他的代码中的新炫酷技巧，并可以从 bob 分支及主分支创建对此远程 Git 存储库的拉取请求。 然后 Alice 可以将此拉取请求合并到主分支。

## <a name="next-steps"></a>后续步骤
详细了解有关结合使用 Git 和 Azure ML Workbench 的详细信息：[结合使用 Git 存储库和 Azure Machine Learning Workbench 项目](using-git-ml-project.md)