---
title: "Azure Machine Learning Workbench 中的漫游和协作 | Microsoft Docs"
description: "了解如何在 Machine Learning Workbench 中设置漫游和协作。"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 137608007716452ec6468f1e13f494b095a11cb0
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench 中的漫游和协作
本文介绍如何使用 Azure Machine Learning Workbench 在计算机之间设置漫游项目及与团队成员进行协作。 

创建包含远程 Git 存储库链接的 Azure 机器学习项目时，该项目元数据和快照将存储在云中。 使用云链接以从不同的计算机访问该项目（漫游）。 还可以通过向团队成员授予该项目的访问权限，来与其进行协作。 

## <a name="prerequisites"></a>先决条件
1. 安装 Machine Learning Workbench 应用。 确保有权访问 Azure 机器学习试验帐户。 有关详细信息，请参阅[安装指南](quickstart-installation.md)。

2. 访问 [Visual Studio Team Services](https://www.visualstudio.com) (Team Services)，然后创建要将项目链接到的存储库。 有关详细信息，请参阅[将 Git 存储库与 Machine Learning Workbench 项目配合使用](using-git-ml-project.md)。

## <a name="create-a-new-machine-learning-project"></a>创建一个新的机器学习项目
打开 Machine Learning Workbench，然后创建新项目（例如名为 iris 的项目）。 在“Visualstudio.com GIT 存储库 URL”框中，输入 Team Services Git 存储库的有效 URL。 

> [!IMPORTANT]
> 如果选择空白项目模板，选择使用的 Git 存储库可能已有主分支。 机器学习只需在本地克隆此主分支即可。 这会将 aml_config 文件夹和其他项目元数据文件添加到本地项目文件夹。 
>
> 如果选择任何其他项目模板，Git 存储库中则不能有主分支。 否则，便会出现错误。 另一种方法是使用 `az ml project create` 命令行创建具有 `--force` 开关的项目。 这会删除原始主分支中的文件，并将其替换为所选模板中的新文件。

创建项目后，通过项目中的任何脚本提交几个运行。 此操作会将项目状态提交到远程 Git 存储库的运行历史记录分支。 

> [!NOTE] 
> 仅脚本运行会触发到运行历史记录分支的提交。 数据准备执行和 Notebook 运行不会触发运行历史记录分支上的项目快照。

如果已设置 Git 身份验证，仍然可以在主分支中进行操作。 或者创建新分支。 

示例： 
```
# Check current repo status.
$ git status

# Stage all changes in the current repo.
$ git add -A

# Commit changes.
$ git commit -m "my commit fixes this weird bug!"

# Push to the remote repo.
$ git push origin master
```

## <a name="roaming"></a>漫游
<a name="roaming"></a>

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>在另一台计算机上打开 Machine Learning Workbench
将 Team Services Git 存储库链接到项目后，可以从装有 Machine Learning Workbench 的任何计算机访问 iris 项目。 

若要在另一台计算机上访问 iris 项目，必须使用创建项目时所用的相同凭据登录到应用。 还需要在同一个机器学习试验帐户和工作区中。 iris 项目连同其他项目按字母顺序列在工作区中。 

### <a name="download-the-project-on-a-second-computer"></a>将项目下载到另一台计算机上
在另一台计算机上打开工作区后，iris 项目旁边的图标不同于典型的文件夹图标。 下载图标表示项目的内容在云中，且已可下载到当前计算机中。 

![创建项目](./media/roaming-and-collaboration/downloadable-project.png)

选择要开始下载的 iris 项目。 下载完成后，便可在另一台计算机上访问该项目了。 

在 Windows 上，项目位于 C:\Users\\<username\>\Documents\AzureML。

在 macOS 上，项目位于 /home/\<username\>/Documents/AzureML。

我们已计划在将来的版本中增强此功能，以便可选择目标文件夹。 

> [!NOTE]
> 如果机器学习目录中恰好有一个与项目完全同名的文件夹，下载则会失败。 要解决此问题，可暂时重命名现有文件夹。


### <a name="work-on-the-downloaded-project"></a>处理下载的项目 
新下载的项目反映最后一次在项目中运行时的项目状态。 每次提交运行时，项目状态的快照会自动提交到 Team Services Git 存储库中的运行历史记录分支。 与最新运行相关联的快照用于实例化第二台计算机上的项目。 
 

## <a name="collaboration"></a>协作
可以与团队成员协作处理链接到 Team Services Git 存储库的项目。 可将机器学习试验帐户、工作区和项目的权限分配给用户。 现在，可以使用 Azure CLI 执行 Azure 资源管理器命令。 还可以使用 [Azure 门户](https://portal.azure.com)。 有关详细信息，请参阅[使用 Azure 门户添加用户](#portal)。    

### <a name="use-the-command-line-to-add-users"></a>使用命令行添加用户
例如，Alice 是 iris 项目的所有者。 Alice 想要与 Bob 共享项目的访问权限。 

Alice 选择“文件”菜单，然后选择“命令提示符”菜单项。 命令提示符窗口随 iris 项目打开。 然后，Alice 可决定想授予 Bob 的访问权限级别。 然后，她执行以下命令授予权限：  

```azurecli
# Find the Resource Manager ID of the Experimentation account.
az ml account experimentation show --query "id"

# Add Bob to the Experimentation account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <Experimentation account Resource Manager ID>

# Find the Resource Manager ID of the workspace.
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the workspace by inheritance. Bob can invite or remove other users.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace Resource Manager ID>
```

直接或通过继承分配角色后，Bob 可以在 Machine Learning Workbench 项目列表中看到该项目。 不过，Bob 可能需要重启该应用程序才能看到该项目。 然后，Bob 可以根据[漫游](#roaming)中所述下载项目，并开始与 Alice 协作。 

针对该项目展开协作的所有用户的运行历史记录会提交到同一个远程 Git 存储库。 因此，当 Alice 执行某个运行时，Bob 可以在 Machine Learning Workbench 应用中该项目的运行历史记录部分看到该运行。 Bob 还可将项目还原到任何运行的状态，包括 Alice 启动的运行。 

共享项目的远程 Git 存储库还可让 Alice 和 Bob 在主分支中展开协作。 如果需要，他们还可以创建个人分支，并使用 Git 拉取请求与合并功能展开协作。 

### <a name="use-the-azure-portal-to-add-users"></a>使用 Azure 门户添加用户
<a name="portal"></a>

机器学习试验帐户、工作区和项目都是 Azure 资源管理器资源。 可以使用 [Azure 门户](https://portal.azure.com)中的“访问控制”链接分配角色。 

在“所有资源”视图中找到想要将用户添加到的资源。 选择“访问控制 (IAM)”链接，然后选择“添加用户”。 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>协作工作流示例
我们将演示一个示例，以说明协作流。 Contoso 员工 Alice 和 Bob 想要使用 Machine Learning Workbench 来协作完成一个数据科学项目。 他们的标识属于同一个 Contoso Azure Active Directory (Azure AD) 租户。 下面是 Alice 和 Bob 采取的步骤：

1. 首先，Alice 在 Team Services 项目中创建了一个空的 Git 存储库。 Team Services 项目应位于在 Contoso Azure AD 租户下创建的 Azure 订阅中。 

2. Alice 在自己的计算机上创建了一个机器学习试验帐户、一个工作区和一个 Machine Learning Workbench 项目。 创建项目时，她输入了 Team Services Git 存储库 URL。

3. Alice 开始处理此项目。 她创建了一些脚本，并执行了一些运行。 对于每个运行，整个项目文件夹的快照都会作为提交内容自动推送到 Machine Learning Workbench 创建的 Team Services Git 存储库的运行历史记录分支中。

4. 现在 Alice 对目前的工作很满意。 她希望将更改提交到本地分支中，并将其推送到 Team Services Git 存储库主分支。 打开项目后，Alice 在 Machine Learning Workbench 中打开命令提示符窗口，然后输入以下命令：
    
    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Team Services.
    $ git push
    ```

5. 然后 Alice 将 Bob 作为参与者添加到工作区中。 这一步可以在 Azure 门户中完成，也可如先前所述使用 `az role assignment` 命令完成。 Alice 还授予 Bob Team Services Git 存储库的读取/写入权限。

6. Bob 在自己的计算机上登录到 Machine Learning Workbench。 他可以看到 Alice 共享给他的工作区。 他可以看到 iris 项目在该工作区下列出。 

7. Bob 选择该项目名称。 该项目便下载到自己的计算机中。
    * 下载的项目文件是运行历史记录中记录的最新运行的快照的副本。 它们不是主分支上的最新提交。
    * 本地项目文件夹设置在主分支上，其中包含未暂存的更改。

8. Bob 可以浏览 Alice 执行的运行。 可以还原所有早期运行的快照。

9. Bob 希望获取 Alice 推送的最新更改，然后在其他分支中开始工作。 Bob 在 Machine Learning Workbench 中打开命令提示符窗口，并执行以下命令：

    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Team Services Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Bob 修改了此项目，并提交了新运行。 这些更改是在 bob 分支上完成的。 但 Alice 也可看到 Bob 的运行。

11. Bob 准备将其更改推送到远程 Git 存储库。 为了避免与 Alice 正在使用的主分支冲突，Bob 将其工作推送到也叫做 bob 的新远程分支中。

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Team Services Git repo, and then push the changes.
    $ git push origin bob
    ```

12. 然后告诉 Alice 他代码中的新炫酷技巧，并可以从 bob 分支及主分支创建对此远程 Git 存储库的拉取请求。 然后 Alice 可以将此拉取请求合并到主分支中。

## <a name="next-steps"></a>后续步骤
- 详细了解如何[将 Git 存储库与 Machine Learning Workbench 项目配合使用](using-git-ml-project.md)。
