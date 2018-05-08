---
title: 使用 Git 进行协作编程 - Azure 机器学习 | Microsoft Docs
description: 如何配合敏捷规划使用 Git 针对数据科学项目执行协作代码开发。
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: 55b47362f334d7366b6cc537a96dc7d046c23733
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="collaborative-coding-with-git"></a>使用 Git 进行协作编程

本文介绍如何使用 Git 作为共享代码开发框架，针对数据科学项目执行协作代码开发。 其中介绍了如何将这些编程活动链接到[敏捷开发](agile-development.md)中规划的工作，以及如何执行代码评审。


## 1.<a name='Linkaworkitemwithagitbranch-1'></a>将工作项链接到 Git 分支 

VSTS 提供了一种将工作项（情景或任务）与 Git 分支连接的简便方法。 它可以让你将情景或任务直接链接到与它关联的代码。 

若要将工作项与新分支连接，请双击工作项，然后在弹出窗口中的“+ 添加链接”下单击“创建新分支”。  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

提供该新分支的相关信息，例如分支名称、基 Git 存储库和分支。 所选的 Git 存储库必须是该工作项隶属的同一个团队项目下的存储库。 基分支可以是主分支或某个其他现有分支。

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

好的做法是分别为每个情景工作项创建一个 Git 分支。 然后，根据情景分支分别为每个任务工作项创建一个分支。 有多个人在参与同一个项目的不同情景或有多个人在参与同一个情景的不同任务时，以这种与情景任务关系相对应的分层方式组织分支将很有帮助。 每个团队成员使用不同的分支，共享一个分支时每个成员使用不同的代码或其他项目，这种情况下可以最大限度地减少冲突。 

下图说明了 TDSP 的建议分支策略。 你可能并不需要此处所示的许多分支，尤其是在只有一个或两个人参与同一个项目或者只有一个人参与某个情景的所有任务的情况下。 但将开发分支与主分支区分开来始终是不错的做法。 这样有助于防止开发活动中断发布分支。 可在[成功的 Git 分支模型](http://nvie.com/posts/a-successful-git-branching-model/)中查看 git 分支模型的完整说明。

![3](./media/collaborative-coding-with-git/3-git-branches.png)

若要切换想要使用的分支，请在 Shell 命令（Windows 或 Linux）中运行以下命令。 

    git checkout <branch name>

将 <分支名称\> 更改为主分支会将你重新切换到主分支。 切换到工作分支后，可以开始处理相应的工作项、开发完成该工作项所需的代码或文档项目。 

还可以将工作项链接到现有的分支。 在工作项的“详细信息”页中，单击“+ 添加链接”，而不要单击“创建新分支”。 然后选择想要将该工作项链接到的分支。 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

也可以在 Git Bash 命令中创建新分支。 如果缺少 <基分支名称\>，<新分支名称\> 将以主分支为基础。 
    
    git checkout -b <new branch name> <base branch name>


## 2.<a name='WorkonaBranchandCommittheChanges-2'></a>在分支上工作并提交更改 

现在假设你对工作项的数据\_引入分支进行了一些更改，例如在你的本地计算机的分支上添加了 R 文件。 则在 Git Shell 中的相应分支中使用以下 Git 命令，即可将添加的 R 文件提交到此工作项的分支中：

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3.<a name='CreateapullrequestonVSTS-3'></a>在 VSTS 上创建拉取请求 

在已完成几次提交和推送并已准备就绪后，若要将当前分支合并到它的基分支，可以在 VSTS 服务器上提交拉取请求。 

转到团队项目的主页，然后单击“代码”。 选择要合并的分支及要将此分支合并到的 Git 存储库的名称。 然后单击“拉取请求”，单击“新建拉取请求”，以在将该分支上的工作合并到其基分支前先创建拉取请求评审。

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

填写该拉取请求的一些相关说明，添加审阅者，然后将它发送出去。

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4.<a name='ReviewandMerge-4'></a>评审及合并 

创建此拉取请求后，审阅者将收到通知他们评审此拉取请求的电子邮件通知。 审阅者需要检查更改是否起作用，并通过请求者测试这些更改（如果可能）。 审阅者可以根据他们的评估批准或拒绝此拉取请求。 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

评审结束后，可通过单击“完成”按钮将工作分支合并到它的基分支。 合并此工作分支后，可以选择将其删除。 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

在右上角确认请求已被标记为“已完成”。 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

在“代码”下返回到该存储库后，将提示你你已切换到主分支。

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

也可以使用以下 Git 命令将工作分支合并到它的基分支并在合并后将工作分支删除：

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>后续步骤

[执行数据科学任务](execute-data-science-tasks.md)介绍了如何使用实用工具来完成一些常见的数据科学任务，例如交互式数据探索、数据分析、报告和建模。

我们还提供了相应的演练，用于演示**具体方案**的操作过程的所有步骤。 [示例演练](walkthroughs.md)一文列出了相关步骤并以缩略图说明的形式提供了链接。 这些演练演示如何将云、本地工具和服务合并到工作流或管道中，以创建智能应用程序。 

