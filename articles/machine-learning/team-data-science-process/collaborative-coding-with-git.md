---
title: 利用 Git 实现协作编码 - Team Data Science Process
description: 如何配合敏捷规划使用 Git 针对数据科学项目执行协作代码开发。
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 08/23/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3b57621fcec654f11c8e9a68e4568f332dbf9ac6
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195412"
---
# <a name="collaborative-coding-with-git"></a>使用 Git 进行协作编程

本文介绍如何使用 Git 作为数据科学项目的协作代码开发框架。 本文介绍如何将 Azure Repos 中的代码链接到 Azure Boards 中的[敏捷开发](agile-development.md)工作项、如何执行代码评审以及如何创建和合并对更改的拉取请求。

## <a name='Linkaworkitemwithagitbranch-1'></a>将工作项链接到 Azure Repos 分支 

使用 Azure DevOps 可以轻松地将 Azure Boards 用户情景或任务工作项与 Azure Repos Git 存储库分支连接起来。 您可以将用户情景或任务直接链接到与其关联的代码。 

若要将工作项连接到新分支, 请选择该工作项旁边的省略号 ( **...** ), 并在上下文菜单上, 滚动到, 然后选择 "**新建分支**"。  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

在 "**创建分支**" 对话框中, 提供新的分支名称和基本 Azure Repos Git 存储库和分支。 基本存储库必须位于与工作项相同的 Azure DevOps 项目中。 基本分支可以是主分支或其他现有分支。 选择 "**创建分支**"。 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

你还可以使用 Windows 或 Linux 中的以下 Git bash 命令创建新分支:

```bash
git checkout -b <new branch name> <base branch name>

```
如果未指定\<基础分支名称 >, 则新的分支将`master`基于。 

若要切换到工作分支, 请运行以下命令: 

```bash
git checkout <working branch name>
```

切换到工作分支后, 可以开始开发代码或文档项目来完成工作项。 运行`git checkout master`会切换回`master`分支。

最好为每个用户情景工作项创建一个 Git 分支。 然后, 对于每个任务工作项, 你可以创建基于用户情景分支的分支。 如果有多个用户在同一项目的不同用户情景上工作, 或者针对同一用户情景的不同任务, 则组织与用户情景-任务关系相对应的分支。 您可以通过使每个团队成员在不同的分支中工作, 或在共享分支时在不同的代码或其他项目上工作, 从而最大程度地减少冲突。 

下图显示了 TDSP 的建议的分支策略。 你可能不需要多个分支 (如此处所示), 尤其是当只有一个或两个人员处理某个项目, 或只有一个人员处理某个用户情景的所有任务时。 但是, 从主分支分离开发分支始终是一种很好的做法, 可帮助防止发布分支被开发活动中断。 有关 Git 分支模型的完整说明, 请参阅[成功的 git 分支模型](https://nvie.com/posts/a-successful-git-branching-model/)。

![3](./media/collaborative-coding-with-git/3-git-branches.png)

还可以将工作项链接到现有的分支。 在工作项的**详细信息**页上, 选择 "**添加链接**"。 然后选择要将工作项链接到的现有分支, 然后选择 **"确定"** 。 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name='WorkonaBranchandCommittheChanges-2'></a>处理分支并提交更改 

对工作项进行更改 (如向本地计算机的`script`分支添加 R 脚本文件) 后, 可以使用以下 Git bash 命令将本地分支中的更改提交到上游工作分支:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name='CreateapullrequestonVSTS-3'></a>创建拉取请求

在一个或多个提交和推送后, 当你准备将当前工作分支合并到它的基分支中时, 你可以在 Azure Repos 中创建和提交*拉取请求*。 

在 Azure DevOps 项目的主页中, 指向左侧导航栏中的 "**存储库** > **拉取请求**"。 然后选择 "**新建拉取请求**" 按钮或 "**创建拉取请求**" 链接。

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

如果需要, 在 "**新建拉取请求**" 屏幕上, 导航到要将更改合并到的 Git 存储库和分支。 添加或更改所需的任何其他信息。 在 "**审阅者**" 下, 添加查看更改所需的名称, 然后选择 "**创建**"。 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name='ReviewandMerge-4'></a>评审及合并

创建拉取请求后, 你的审阅者会收到电子邮件通知以查看拉取请求。 评审者测试更改是否有效, 并尽可能使用请求者检查更改。 审阅者可以根据自己的评估提出注释、请求更改以及批准或拒绝拉取请求。 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

审阅者批准更改后, 你或具有合并权限的其他人可以将工作分支合并到它的基分支。 选择 "**完成**", 然后在 "**完成拉取请求**" 对话框中选择 "**完成合并**"。 可以选择在合并工作分支后将其删除。 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

确认请求标记为 "**已完成**"。 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

返回到左侧导航栏中的 "**存储库**" 时, 可以看到已切换到主分支, 因为已删除该`script`分支。

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

你还可以使用以下 Git bash 命令将工作分支合并`script`到它的基分支, 并在合并后删除工作分支:

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>后续步骤

[执行数据科学任务](execute-data-science-tasks.md)介绍如何使用实用程序来完成几个常见的数据科学任务, 例如交互式数据探索、数据分析、报告和模型创建。

[示例演练](walkthroughs.md)列出了特定方案的演练, 其中包含链接和缩略图说明。 链接的方案说明了如何将云和本地工具和服务合并到工作流或管道中, 以创建智能应用程序。 

