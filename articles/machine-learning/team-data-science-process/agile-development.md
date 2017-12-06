---
title: "数据科学项目的敏捷开发 - Azure 机器学习 | Microsoft Docs"
description: "介绍开发人员如何使用 Team Data Science Process 在项目团队中以系统化的协作型版本控制方式执行数据科学项目。"
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
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: 686f751b241d49d116948711c683f4b504d5d5f9
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2017
---
# <a name="agile-development-of-data-science-projects"></a>数据科学项目的敏捷开发

本文档介绍开发人员如何使用 [Team Data Science Process](overview.md) (TDSP) 在项目团队中以系统化的协作型版本控制方式执行数据科学项目。 TDSP 是 Microsoft 开发的一个框架，它提供结构化的活动序列，可高效地执行基于云的预测分析解决方案。 有关致力于标准化此过程的数据科学团队处理的人员角色及其相关任务的概述，请参阅 [Team Data Science Process 角色和任务](roles-tasks.md)。 

本文包含如何执行以下操作的说明： 

1. 执行项目涉及的工作项的冲刺规划。<br> 如果不了解冲刺规划，可查看详细信息并在[此处](https://en.wikipedia.org/wiki/Sprint_(software_development) "此处")查看常规信息。 
2. 向冲刺添加工作项。 

> [!NOTE]
> 下面一组说明概述了使用 Visual Studio Team Services (VSTS) 设置 TDSP 团队环境所需的步骤。 它们指定了如何使用 VSTS 完成这些任务，因为这是在 Microsoft 中实现 TDSP 的方法。  前面的列表中的第 3 项和第 4 项是选择使用 VSTS 后必然会获得的权益。 如果你的组使用了其他代码托管平台，团队主管需要完成的任务通常不会发生变化。 但是完成这些任务的方法将会有所不同。 例如，第 6 部分“将工作项与 Git 分支链接在一起”中的项目可能不会和在 VSTS 上的操作一样简单。
>
>

下图说明了实现数据科学项目流程中涉及的典型冲刺规划、编码和源代码控制工作流：

![1](./media/agile-development/1-project-execute.png)


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

> [!NOTE]
> 数据科学中要使用的一些概念借用了软件代码管理 (SCM) 中的功能、情景、任务和 bug。 它们可能与其传统意义上的 SCM 定义略有不同。
>
>

> [!NOTE]
> 数据科学家可能更喜欢使用敏捷模板，因为该模板专门与 TDSP 生命周期阶段保持一致。 因此，创建了由敏捷派生的冲刺规划模板，并在其中将长篇故事、故事等替换为 TDSP 生命周期阶段和子阶段。 有关如何创建敏捷模板的说明，请参阅[在 Visual Studio Online 设置敏捷数据科学流程](agile-development.md#set-up-agile-dsp-6)。
>
>

## 2.<a name='SprintPlanning-2'></a>冲刺规划 

冲刺规划有助于设置项目优先级、进行资源规划和分配。 许多数据科学家同时参与了多个项目，每个项目可能都需要数月才能完成。 项目的进展速度通常会有所不同。 在 VSTS 服务器上，你可以轻松创建、管理和跟踪团队项目的工作项，并执行冲刺规划来确保项目按预期进行。 

请访问[此链接](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning)获取有关 VSTS 中的冲刺规划的分步说明。 


## 3.<a name='AddFeature-3'></a>添加功能  

在团队项目中创建项目存储库后，转到团队“概述”页，然后单击“管理工作”。

![#N/A](./media/agile-development/2-sprint-team-overview.png)

若要在积压工作中包括某个功能，请单击“积压工作” --> “功能” --> “新建”，键入功能“标题”（通常是你的项目名称），然后单击“添加”。

![3](./media/agile-development/3-sprint-team-add-work.png)

双击创建的功能。 填写说明，为此功能分配团队成员，并为此功能设置计划参数。 

还可以将此功能链接到项目存储库。 单击“开发”部分下的“添加链接”。 完成编辑功能后，单击“保存并关闭”以退出。


## 4.<a name='AddStoryunderfeature-4'></a>在功能下添加情景 

可以在功能下添加情景，来描述完成（功能）项目所需的主要步骤。 若要添加新情景，请在积压工作视图中单击功能左侧的 + 号。  

![4](./media/agile-development/4-sprint-add-story.png)

可以在弹出窗口中编辑情景的详细信息，例如状态、说明、注释、计划和优先级。

![5](./media/agile-development/5-sprint-edit-story.png)

通过单击“开发”下的“+ 添加链接”，可以将此情景连接到现有存储库。 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5.<a name='AddTaskunderstory-5'></a>向情景添加任务 

任务是完成各个情景所需的特定详细步骤。 完成一个情景的所有任务后，此情景应也已完成。 

若要向情景添加任务，请单击情景项旁的 + 号，选择“任务”，然后在弹出窗口中填写此任务的详细信息。

![7](./media/agile-development/7-sprint-add-task.png)

创建功能、情景和任务后，可以在“积压工作”或“版块”视图中查看它们，来跟踪它们的状态。

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6.<a name='set-up-agile-dsp-6'></a>在 Visual Studio Online 中设置敏捷 TDSP 工作模板

本文介绍如何设置使用 Visual Studio Online (VSO) 设置一个使用 TDSP 数据科学生命周期阶段并可跟踪工作项的敏捷数据科学流程模板。 以下步骤逐步演示一个有关如何设置特定于数据科学的敏捷流程模板 *AgileDataScienceProcess* 的示例，并演示如何基于该模板创建数据科学工作项。

### <a name="agile-data-science-process-template-setup"></a>敏捷数据科学流程模板设置

1. 导航到服务器主页，选择“配置” -> “流程”。

    ![10](./media/agile-development/10-settings.png) 

2. 导航到“所有流程” -> “流程”，在“敏捷”下面，单击“创建继承流程”。 输入流程名称“AgileDataScienceProcess”，然后单击“创建流程”。

    ![11](./media/agile-development/11-agileds.png)

3. 在“AgileDataScienceProcess” -> “工作项类型”选项卡下面，通过单击“配置”->“禁用”来禁用“长篇故事”、“功能”、“用户情景”和“任务”工作项类型

    ![12](./media/agile-development/12-disable.png)

4. 导航到“AgileDataScienceProcess” -> “积压工作级别”选项卡。单击“配置” -> “编辑/重命名”，将“长篇故事”重命名为“TDSP 项目”。 在同一对话框中，单击“数据科学项目”中的“+新建工作项”，并将“默认工作项类型”设置为“TDSP 项目” 

    ![13](./media/agile-development/13-rename.png)  

5. 同样，将积压工作名称“功能”更改为“TDSP 阶段”，并将以下内容添加到“新建工作项类型”：

    - 了解业务
    - 数据采集
    - 建模
    - 部署

6. 将“用户情景”重命名为“TDSP 子阶段”，并将默认工作项类型设置为新建的“TDSP 子阶段”类型。

7. 将“任务”设置为新建的工作项类型“TDSP 任务” 

8. 执行这些步骤之后，积压工作级别应如下所示：

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>创建数据科学工作项

创建数据科学流程模板之后，可以创建和跟踪对应于 TDSP 生命周期的数据科学工作项。

1. 创建新的团队项目时，请选择“Agile\AgileDataScienceProcess”作为“工作项流程”：

    ![15](./media/agile-development/15-newproject.png)

2. 导航到新建的团队项目，单击“工作” -> “积压工作”。

3. 单击“配置团队设置”使“TDSP 项目”可见，并选中“TDSP 项目”；保存设置。

    ![16](./media/agile-development/16-enabledsprojects.png)

4. 现在，可以开始创建数据科学特定的工作项。

    ![17](./media/agile-development/17-dsworkitems.png)

5. 下面是数据科学项目工作项显示方式的示例：

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>后续步骤

[使用 Git 进行协作编程](collaborative-coding-with-git.md)介绍了如何使用 Git 作为共享代码开发框架针对数据科学项目执行协作代码开发，以及如何将这些编程活动链接到使用敏捷流程规划的工作。

下面有关敏捷流程的其他资源链接。

- 敏捷流程：[https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- 敏捷流程工作项类型和工作流：[https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


我们还提供了相应的演练，用于演示**具体方案**的操作过程的所有步骤。 [示例演练](walkthroughs.md)一文列出了相关步骤并以缩略图说明的形式提供了链接。 这些演练演示如何将云、本地工具和服务合并到工作流或管道中，以创建智能应用程序。 
