---
title: 利用 Git 实现协作编码 - Team Data Science Process
description: 如何配合敏捷规划使用 Git 针对数据科学项目执行协作代码开发。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 0708e395eff90ff5b889c05f0fd5e7a98205c5bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "76721891"
---
# <a name="collaborative-coding-with-git"></a>使用 Git 进行协作编程

本文介绍如何将 Git 作为数据科学项目的协作代码开发框架来使用。 本文介绍如何将 Azure Repos 中的代码链接到 Azure Boards 中的[敏捷开发](agile-development.md)工作项、如何执行代码评审以及如何创建和合并请求进行更改的拉取请求。

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>将工作项链接到某个 Azure Repos 分支 

使用 Azure DevOps 可以轻松地将 Azure Boards“用户情景”或“任务”工作项与 Azure Repos Git 存储库分支连接起来。 可以将用户情景或任务直接链接到与之关联的代码。 

若要将工作项连接到新分支，请选择该工作项旁边的“操作”  省略号（ **...** ），并在上下文菜单上，滚动到并选中“新分支”  。  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

在“创建分支”  对话框中，提供新的分支名称和基本 Azure Repos Git 存储库和基分支。 基本存储库必须与工作项位于同一 Azure DevOps 项目中。 基分支可以是主分支或其他某个现有分支。 选择“创建分支”  。 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

还可以在 Windows 或 Linux 中使用以下 Git bash 命令创建新分支：

```bash
git checkout -b <new branch name> <base branch name>

```
如果未指定\<基分支名称>，则新的分支将基于 `master`。 

若要切换到工作分支，请运行以下命令： 

```bash
git checkout <working branch name>
```

切换到工作分支后，可以开始开发完成该工作项所需的代码或文档项目。 运行 `git checkout master` 会切换回 `master` 分支。

一个好的做法是分别为每个用户情景工作项创建一个 Git 分支。 然后，根据用户情景分支分别为每个“任务”工作项创建一个分支。 如果安排了多个人员在处理同一项目的不同用户情景，或处理同一用户情景的不同任务，应按与“用户情景-任务”关系相应的层次结构来组织分支。 可以通过使每个团队成员处理不同的分支，或在处理同一分支时处理不同的代码或其他项目，从而最大程度地减少冲突。 

下图展示了建议的 TDSP 分支策略。 你可能并不需要此处所示的许多分支，尤其是在只有一个或两个人参与同一个项目或者只有一个人参与某个用户情景的所有任务的情况下。 但是，将开发分支从主分支分离出来始终是一个很好的做法，可帮助防止发布分支因开发活动而中断。 可在[成功的 Git 分支模型](https://nvie.com/posts/a-successful-git-branching-model/)中查看 Git 分支模型的完整说明。

![3](./media/collaborative-coding-with-git/3-git-branches.png)

还可以将工作项链接到现有的分支。 在工作项的“详细信息”  页上，选择“添加链接”  。 然后选择要将工作项链接到的现有分支，并选择“确定”  。 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>在分支上工作并提交更改 

对工作项进行更改（如向本地计算机的 `script` 分支添加 R 脚本文件）后，可以使用以下 Git bash 命令将本地分支中的更改提交到上游工作分支：

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>创建拉取请求

在执行了一个或多个提交和推送后，如果已准备好将当前工作分支合并到它的基分支中时，可以在 Azure Repos 中创建并提交一个拉取请求  。 

在 Azure DevOps 项目的主页中，指向左侧导航栏中的“存储库”   > “拉取请求”  。 然后选择“新建拉取请求”  按钮，或“创建拉取请求”  链接。

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

如有必要，请在“新建拉取请求”  屏幕上，导航到要将更改合并到的 Git 存储库和分支。 添加或更改所需的任何其他信息。 在 "**审阅者**" 下，添加审阅者的姓名，然后选择 "**创建**"。 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>评审及合并

创建此拉取请求后，审阅者将收到通知他们评审此拉取请求的电子邮件通知。 审阅者测试更改是否有效，如果可能，会与请求者核实更改。 审阅者可以给予评论、请求更改，并根据其评估批准或拒绝此拉取请求。 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

审阅者批准更改后，你或具有合并权限的其他人可以将工作分支合并到它的基分支中。 选择“完成”  ，然后在“完成拉取请求”  对话框中选择“完成合并”  。 可选择在合并完此工作分支后将其删除。 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

确认该请求已被标记为“已完成”  。 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

返回到左侧导航栏中的“存储库”  时，可以看到已切换到主分支，因为 `script` 分支已被删除。

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

也可以使用以下 Git bash 命令将 `script` 工作分支合并到它的基分支并在合并后将工作分支删除：

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>后续步骤

[执行数据科学任务](execute-data-science-tasks.md)介绍了如何使用实用工具来完成一些常见的数据科学任务，例如交互式数据探索、数据分析、报告和建模。

[示例演练](walkthroughs.md)一文列出了特定方案的演练，并提供链接和缩略图描述。 链接的方案展示了如何将云、本地工具以及服务合并到工作流或管道中，以此创建智能应用程序。 

