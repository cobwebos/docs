---
title: "执行数据科学项目 - Azure | Microsoft Docs"
description: "数据科学家如何以可跟踪的协作型版本控制方式执行数据科学项目。"
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev;
ms.openlocfilehash: 8c318f87243d0c98b6a42bebcdffb433f9cc456e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="execution-of-data-science-projects"></a>执行数据科学项目

本文档介绍了数据科学家如何使用 [Team Data Science Process](overview.md) (TDSP) 在项目团队中以系统化的协作型版本控制方式执行数据科学项目。 TDSP 是 Microsoft 开发的一个框架，它提供了结构化的活动序列，可高效地执行基于云的预测分析解决方案。 有关致力于标准化此过程的数据科学团队处理的人员角色及其相关任务的概述，请参阅 [Team Data Science Process 角色和任务](roles-tasks.md)。 

本主题包含如何执行以下操作的说明： 

1. 执行项目涉及的工作项的冲刺规划。<br> 如果不了解冲刺规划，可在下方查看详细信息并在[此处](https://en.wikipedia.org/wiki/Sprint_(software_development) "此处")查看常规信息。 
2. 向冲刺添加工作项。
3. 将工作项与 git 跟踪的编码活动链接在一起。
4. 执行代码评审。 


>[AZURE.NOTE] 下组说明概述了使用 Visual Studio Team Services (VSTS) 设置 TDSP 团队环境所需的步骤。 我们指定了如何使用 VSTS 完成这些任务，因为这是我们在 Microsoft 中实现 TDSP 的方法。 前面的列表中的第 3 项和第 4 项是选择使用 VSTS 后必然会获得的权益。 如果你的组使用了其他代码托管平台，团队主管需要完成的任务通常不会发生变化。 但是完成这些任务的方法将会有所不同。 例如，第 6 部分“将工作项与 git 分支链接在一起”中的项目可能不会和在 VSTS 上的操作一样简单。

下图说明了实现数据科学项目过程中涉及的典型冲刺规划、编码和源代码控制工作流：

![1](./media/project-execution/project-execution-1-project-execute.png)


##  1.<a name='Terminology-1'></a>术语 

在 TDSP 冲刺规划框架中，有四种频繁使用的工作项类型：功能、用户情景、任务和 Bug。 每个团队项目维持一个针对所有工作项的积压工作。 团队项目下没有 git 存储库级别的积压工作。 以下是它们的定义：

- 功能：一个功能对应一个项目协定。 将客户端的不同协定视为不同的功能。 同样，最好将客户端的项目的不同阶段视为不同的功能。 如果选择类似 ClientName-EngagementName 的架构为功能命名，则可以通过名称轻松识别出项目/协定的上下文。
- 情景：情景是端到端完成功能（项目）所需的不同工作项。 情景示例包括：
    - 获取数据 
    - 浏览数据 
    - 生成功能
    - 构建模型
    - 实施模型 
    - 重新定型模型
- 任务：任务是完成特定情景需要执行的可赋值代码、文档工作项或其他活动。 例如，获取数据情景中的任务可能是：
    -  获取 SQL Server 的凭据 
    -  将数据上传到 SQL 数据仓库。 
- Bug：Bug 通常指需要对完成任务时完成的现有代码或文档执行的修复。 如果此 bug 是由缺少阶段或任务引起的，则它可分别升级为情景或任务。 

>[AZURE.NOTE] 我们借用了要用于数据科学的软件代码管理 (SCM) 中的功能、情景、任务和 bug 概念。 它们可能与其传统意义上的 SCM 定义略有不同。

##  2.<a name='SprintPlanning-2'></a>冲刺规划 

冲刺规划有助于设置项目优先级、进行资源规划和分配。 许多数据科学家同时参与了多个项目，每个项目可能都需要数月才能完成。 项目的进展速度通常会有所不同。 在 VSTS 服务器上，你可以轻松创建、管理和跟踪团队项目的工作项，并执行冲刺规划来确保项目按预期进行。 

请访问[此链接](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning)获取有关 VSTS 中的冲刺规划的分步说明。 


##  3.<a name='AddFeature-3'></a>添加功能  

在团队项目中创建项目存储库后，转到团队“概述”页，然后单击“管理工作”。

![#N/A](./media/project-execution/project-execution-2-sprint-team-overview.png)

若要在积压工作中包括某个功能，请单击“积压工作” --> “功能” --> “新建”，键入功能“标题”（通常是你的项目名称），然后单击“添加”。

![3](./media/project-execution/project-execution-3-sprint-team-add-work.png)

双击刚刚创建的功能。 填写说明，为此功能分配团队成员，并为此功能设置计划参数。 

还可以将此功能链接到项目存储库。 单击“开发”部分下的“添加链接”。 完成编辑功能后，单击“保存并关闭”以退出。


##  4.<a name='AddStoryunderfeature-4'></a>在功能下添加情景 

可以在功能下添加情景，来描述完成（功能）项目所需的主要步骤。 若要添加新情景，请在积压工作视图中单击功能左侧的 + 号。  

![4](./media/project-execution/project-execution-4-sprint-add-story.png)

可以在弹出窗口中编辑情景的详细信息，例如状态、说明、注释、计划和优先级。

![5](./media/project-execution/project-execution-5-sprint-edit-story.png)

通过单击“开发”下的“+ 添加链接”，可以将此情景连接到现有存储库。 

![6](./media/project-execution/project-execution-6-sprint-link-existing-branch.png)


##  5.<a name='AddTaskunderstory-5'></a>向情景添加任务 

任务是完成各个情景所需的特定详细步骤。 完成一个情景的所有任务后，此情景应也已完成。 

若要向情景添加任务，请单击情景项旁的 + 号，选择“任务”，然后在弹出窗口中填写此任务的详细信息。

![7](./media/project-execution/project-execution-7-sprint-add-task.png)

创建功能、情景和任务后，可以在“积压工作”或“版块”视图中查看它们，来跟踪它们的状态。

![8](./media/project-execution/project-execution-8-sprint-backlog-view.png)

![9](./media/project-execution/project-execution-9-link-to-a-new-branch.png)


##  6.<a name='Linkaworkitemwithagitbranch-6'></a>将工作项与 git 分支链接 

VSTS 提供了一种将工作项（情景或任务）与 git 分支连接的简便方法。 它可以让你将情景或任务直接链接到与它关联的代码。 

若要将工作项与新分支连接，请双击工作项，然后在弹出窗口中的“+ 添加链接”下单击“创建新分支”。  

![10](./media/project-execution/project-execution-10-sprint-board-view.png)

提供该新分支的相关信息，例如分支名称、基 git 存储库和分支。 所选的 git 存储库必须是该工作项隶属的同一个团队项目下的存储库。 基分支可以是主分支或某个其他现有分支。

![11](./media/project-execution/project-execution-11-create-a-branch.png)

好的做法是分别为每个情景工作项创建一个 git 分支。 然后，根据情景分支分别为每个任务工作项创建一个分支。 有多个人在参与同一个项目的不同情景或有多个人在参与同一个情景的不同任务时，以这种与情景任务关系相对应的分层方式组织分支将很有帮助。 每个团队成员使用不同的分支，共享一个分支时每个成员使用不同的代码或其他项目，这种情况下可以最大限度地减少冲突。 

下图说明了 TDSP 的建议分支策略。 你可能并不需要此处所示的许多分支，尤其是在只有一个或两个人参与同一个项目或者只有一个人参与某个情景的所有任务的情况下。 但将开发分支与主分支区分开来始终是不错的做法。 这样有助于防止开发活动中断发布分支。 可在[成功的 Git 分支模型](http://nvie.com/posts/a-successful-git-branching-model/)中查看 git 分支模型的完整说明。

![12](./media/project-execution/project-execution-12-git-branches.png)

若要切换想要使用的分支，请在 Shell 命令（Windows 或 Linux）中运行以下命令。 

    git checkout <branch name>

将 <分支名称\> 更改为主分支会将你重新切换到主分支。 切换到工作分支后，可以开始处理相应的工作项、开发完成该工作项所需的代码或文档项目。 

还可以将工作项链接到现有的分支。 在工作项的“详细信息”页中，单击“+ 添加链接”，而不要单击“创建新分支”。 然后选择想要将该工作项链接到的分支。 

![13](./media/project-execution/project-execution-13-link-to-an-existing-branch.png)

也可以在 git bash 命令中创建新分支。 如果缺少 <基分支名称\>，<新分支名称\> 将以主分支为基础。 
    
    git checkout -b <new branch name> <base branch name>


##  7.<a name='WorkonaBranchandCommittheChanges-7'></a>在分支上工作并提交更改 

现在假设你对工作项的数据\_引入分支进行了一些更改，例如在你的本地计算机的分支上添加了 R 文件。 则在 Git Shell 中的相应分支中使用以下 Git 命令，即可将添加的 R 文件提交到此工作项的分支中：

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![14](./media/project-execution/project-execution-14-sprint-push-to-branch.png)

##  8.<a name='CreateapullrequestonVSTS-8'></a>在 VSTS 上创建拉取请求 

在已完成几次提交和推送并已准备就绪后，若要将当前分支合并到它的基分支，可以在 VSTS 服务器上提交拉取请求。 

转到团队项目的主页，然后单击“代码”。 选择要合并的分支及要将此分支合并到的 git 存储库的名称。 然后单击“拉取请求”，单击“新建拉取请求”，以在将该分支上的工作合并到其基分支前先创建拉取请求评审。

![15](./media/project-execution/project-execution-15-spring-create-pull-request.png)

填写该拉取请求的一些相关说明，添加审阅者，然后将它发送出去。

![16](./media/project-execution/project-execution-16-spring-send-pull-request.png)

##  9.<a name='ReviewandMerge-9'></a>评审及合并 

创建此拉取请求后，审阅者将收到通知他们评审此拉取请求的电子邮件通知。 审阅者需要检查更改是否起作用，并通过请求者测试这些更改（如果可能）。 审阅者可以根据他们的评估批准或拒绝此拉取请求。 

![17](./media/project-execution/project-execution-17-add_comments.png)

![18](./media/project-execution/project-execution-18-spring-approve-pullrequest.png)

评审结束后，可通过单击“完成”按钮将工作分支合并到它的基分支。 合并此工作分支后，可以选择将其删除。 

![19](./media/project-execution/project-execution-19-spring-complete-pullrequest.png)

在右上角确认请求已被标记为“已完成”。 

![20](./media/project-execution/project-execution-20-spring-merge-pullrequest.png)

在“代码”下返回到该存储库后，将提示你你已切换到主分支。

![21](./media/project-execution/project-execution-21-spring-branch-deleted.png)

也可以使用以下 Git 命令将工作分支合并到它的基分支并在合并后将工作分支删除：

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![22](./media/project-execution/project-execution-22-spring-branch-deleted-commandline.png)


##  10.<a name='DataQualityReportUtility-10'></a>交互式数据探索、分析和报告 (IDEAR) 实用程序

这款基于 R Markdown 的实用程序提供了一个灵活的交互式工具，可用于评估和探索数据集。 用户可以最少的编码工作从数据集快速生成报告。 用户可以单击按钮来将他在此交互式工具中看到的探索结果导出到最终报告中，并且可将此报告传递到客户端或者使用它来决定在后续的建模步骤中包含哪些变量。

目前，此工具仅适用于内存中的数据帧。 需要使用 yaml 文件指定要探索的数据集的参数。 有关详细信息，请参阅 [TDSP 数据科学实用程序中的 IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils)。


##  11.<a name='ModelingUtility-11'></a>基线建模和报告实用程序

此实用程序提供了一个可自定义的半自动化工具，它可通过超参数扫描执行建模并比较这些模型的准确性。 

该建模实用程序是一个 R Markdown 文件，运行该文件可生成自包含 html 输出，其中包含一个目录，可通过此目录导航到它的不同部分。 运行（编织）此 Markdown 文件时会执行三个算法：正则化回归（使用 glmnet 包）、随机林（使用 randomForest 包）和提升树（使用 xgboost 包）。 上述每个算法均会生成一个定型的模型。 然后会比较这些模型的准确性，并报告相对特征重要性绘图。 目前，有两个实用程序：一个用于二进制分类任务，另一个用于回归任务。 二者之间的主要区别在于指定这些学习任务的控制参数和准确性指标的方式。 

Yaml 文件用于指定：

- 数据输入（SQL 源或 R-Data 文件） 
- 使用数据的哪些部分进行定型及使用哪些部分进行测试
- 要运行哪些算法 
- 用于优化模型的控制参数选项：
    - 交叉验证 
    - 启动
    - 交叉验证折叠数
- 每个算法的超参数集。 

也可在 Yaml 文件中修改要扫描的算法数量、用于优化的折叠数、超参数和超参数集的数量，以快速运行模型。 例如，可使用较低的 CV 折叠数、较低的参数集数运行它们。 如有必要，也可以使用较高的 CV 折叠数或较大的参数集数以更全面的方式运行它们。

有关详细信息，请参阅 [TDSP 数据科学实用程序中的自动建模和报告实用工具](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling)。


##  12.<a name='PowerBI-12'></a>使用 Power BI 仪表板跟踪项目进度

数据科学组管理员、团队主管和项目主管需要跟踪项目的进度、已完成了哪些工作及是由谁完成的、待办事项列表上还剩下哪些工作。 如果使用的是 VSTS，你可以生成 Power BI 仪表板来跟踪与 Git 存储库关联的活动和工作项。 有关如何将 Power BI 连接到 Visual Studio Team Services 的详细信息，请参阅[将 Power BI 连接到 Team Services](https://www.visualstudio.com/en-us/docs/report/powerbi/connect-vso-pbi-vs)。 

若要了解在将 VSTS 的数据连接到 Power BI 后如何创建 Power BI 仪表板和报表来跟踪 Git 存储库活动和工作项，请参阅[创建 Power BI 仪表板和报表](https://www.visualstudio.com/en-us/docs/report/powerbi/report-on-vso-with-power-bi-vs)。 

下面是我们构建的用于跟踪 Git 活动和工作项的两个简单示例仪表板。 在第一个示例仪表板中，按不同的用户、不同的日期和不同的存储库列出了 git 提交活动。 你可以轻松地进行详尽分析，以筛选出你感兴趣的内容。

![23](./media/project-execution/project-execution-23-powerbi-git.png)

在第二个示例仪表板中，显示了不同迭代中的工作项（情景和任务）。 已按被分派人和优先级将它们分组，并按状态设置了它们的颜色。

![24](./media/project-execution/project-execution-24-powerbi-workitem.png)

 
## <a name="next-steps"></a>后续步骤

还提供了完整的、端到端的演练，演示**特定方案**处理过程中的所有步骤。 在[示例演练](walkthroughs.md)主题中，列出了相关步骤并链接了缩略图说明。 这些演练演示如何将云、本地工具和服务合并到工作流或管道中，以创建智能应用程序。 

有关在团队数据科学过程中执行使用 Azure 机器学习工作室的步骤的示例，请参阅[使用 Azure ML](http://aka.ms/datascienceprocess) 学习路径。