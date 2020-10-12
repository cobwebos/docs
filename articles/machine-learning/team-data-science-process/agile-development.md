---
title: 数据科学项目的敏捷开发 - Team Data Science Process
description: 使用团队数据科学流程在项目团队中以系统化的协作型版本控制方式执行数据科学项目。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 113c11b749b42bbaf9e5e40cdf63623288997a92
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440280"
---
# <a name="agile-development-of-data-science-projects"></a>数据科学项目的敏捷开发

本文档介绍开发人员如何使用 [Team Data Science Process](overview.md) (TDSP) 在项目团队中以系统化的协作型版本控制方式执行数据科学项目。 TDSP 是 Microsoft 开发的一个框架，它提供结构化的活动序列，可高效地执行基于云的预测分析解决方案。 有关致力于标准化 TDSP 的数据科学团队要处理的角色和任务的概述，请参阅[团队数据科学流程角色和任务](roles-tasks.md)。 

本文包含如何执行以下操作的说明： 

- 执行项目涉及的工作项的冲刺 (sprint) 规划  。
- 向冲刺 (sprint) 添加工作项  。
- 创建和使用敏捷派生的工作项模板，该模板专门与 TDSP 生命周期阶段保持一致  。

以下说明概述了使用 Azure DevOps 中的 Azure Boards 和 Azure Repos 设置 TDSP 团队环境所需的步骤。 此说明使用 Azure DevOps，因为这是在 Microsoft 实现 TDSP 的方法。 如果你的组使用不同的代码托管平台，团队主导的任务通常不会发生变化，但完成任务的方式不同。 例如，使用 GitHub 链接工作项与 Git 分支的方式可能不同于使用 Azure Repos 的链接方式。

下图说明了数据科学项目涉及的典型冲刺 (sprint) 规划、编码和源代码控制工作流：

![Team Data Science Process](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>工作项类型

在 TDSP 冲刺 (sprint) 规划框架中，有四种频繁使用的工作项类型  ：功能、用户情景、任务和 Bug     。 所有工作项的积压工作 (backlog) 都处于项目级别，而不是 Git 存储库级别。 

下面是工作项类型的定义：

- **功能**：一个功能对应一个项目协定。 与客户端不同的协定是不同的功能，最好将项目的不同阶段视为不同的功能。 如果选择类似 \<ClientName>-\<EngagementName> 的架构作为功能命名，则可以通过名称轻松识别出项目和协定的上下文。
  
- **用户情景**：用户情景是完成端到端功能所需的工作项。 用户情景示例包括：
  - 获取数据 
  - 浏览数据 
  - 生成功能
  - 生成模型
  - 使模型可操作 
  - 重新定型
  
- **任务**：任务是可分配的工作项，需要完成这些工作项才能完成特定的用户情景。 例如，用户情景“获取数据”中的任务可能是  ：
  - 获取 SQL Server 凭据
  - 将数据上传到 Azure Synapse Analytics
  
- **Bug**：Bug 是现有代码或文档中的问题，必须修复这些问题才能完成任务。 如果 Bug 是由于缺少工作项引起的，则可以将其升级为用户情景或任务。 

数据科学家可能更习惯于使用敏捷模板，该模板将功能、用户情景和任务替换为 TDSP 生命周期阶段和子阶段。 若要创建敏捷派生的模板，使其专门与 TDSP 生命周期阶段保持一致，请参阅[使用敏捷 TDSP 工作模板](#set-up-agile-dsp-6)。

> [!NOTE]
> TDSP 借用了软件代码管理 (SCM) 中的功能、用户情景、任务和 Bug 概念。 TDSP 概念可能与其传统意义上的 SCM 定义略有不同。

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>计划冲刺 (sprint)

许多数据科学家同时参与了多个项目，每个项目可能都需要数月才能完成，并且进度不同。 冲刺规划有助于设置项目优先级、进行资源规划和分配。 在 Azure Boards 中，你可以轻松创建、管理和跟踪项目的工作项，并执行冲刺 (sprint) 规划来确保项目按预期进行。

有关冲刺 (sprint) 规划的详细信息，请参阅 [Scrum 冲刺 (sprint)](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint)。 

有关 Azure Boards 中的冲刺 (sprint) 规划的详细信息，请参阅[将积压工作项分配给冲刺 (sprint)](/azure/devops/boards/sprints/assign-work-sprint)。 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>向积压工作 (backlog) 添加功能 

创建项目和项目代码存储库后，可以将功能添加到积压工作 (backlog) 来表示项目的工作。

1. 在项目页中，在左侧导航栏中选择“版块” > “积压工作(backlog)”   。 
   
1. 在“积压工作(backlog)”选项卡上，如果顶部栏的工作项类型为“情景”，请下拉列表并选择“功能”    。 然后选择“新建工作项”  。
   
   ![选择“新建工作项”](./media/agile-development/2-sprint-team-overview.png)
   
1. 输入功能的标题，通常为项目名称，然后选择“添加到顶部”  。 
   
   ![输入标题，并选择“添加到顶部”](./media/agile-development/3-sprint-team-add-work.png)
   
1. 从“积压工作(backlog)”列表中，选择并打开新功能  。 填写描述、分配团队成员并设置计划参数。 
   
   还可以通过选择“开发”部分下的“添加链接”，将功能链接到项目的 Azure Repos 代码存储库   。 
   
   编辑此功能后，选择“保存并关闭”。
   
   ![编辑功能并选择“保存并关闭”](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>向功能添加用户情景 

可以在“功能”下添加用户情景来描述完成项目所需的主要步骤。 

向功能中添加新的用户情景：

1. 在“积压工作(backlog)”选项卡上，选择功能左侧的 +   。 
   
   ![在“功能”下添加新的用户情景](./media/agile-development/4-sprint-add-story.png)
   
1. 为用户情景提供一个标题，并编辑分配、状态、描述、注释、计划和优先级等详细信息。 
   
   还可以通过选择“开发”部分下的“添加链接”，将用户情景链接到项目的 Azure Repos 代码存储库分支   。 选择想要将该工作项链接到的存储库和分支，然后选择“确定”  。
   
   ![添加链接](./media/agile-development/5-sprint-edit-story.png)
   
1. 编辑用户情景后，选择“保存并关闭”  。 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>向用户情景添加任务 

任务是完成各个用户情景所需的特定详细步骤。 完成一个用户情景的所有任务后，此用户情景应也已完成。 

若要向用户情景添加任务，请选择“用户情景”项旁边的 +，然后选择“任务”   。 在任务中填写标题和其他信息。

![向用户情景添加任务](./media/agile-development/7-sprint-add-task.png)

创建功能、用户情景和任务后，可以在“积压工作(backlog)”或“版块”视图中查看它们，以跟踪它们的状态   。

![积压工作 (backlog) 视图](./media/agile-development/8-sprint-backlog-view.png)

![版块视图](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>使用敏捷 TDSP 工作模板

数据科学家可能更习惯于使用敏捷模板，该模板将功能、用户情景和任务替换为 TDSP 生命周期阶段和子阶段。 在 Azure Boards 中，可以创建一个敏捷派生模板，该模板使用 TDSP 生命周期阶段来创建和跟踪工作项。 以下步骤将引导你设置特定于数据科学的敏捷过程模板，并基于该模板创建数据科学工作项。

### <a name="set-up-an-agile-data-science-process-template"></a>设置敏捷数据科学流程模板

1. 从 Azure DevOps 组织主页的左侧导航栏中选择 “组织设置”  。 
   
1. 在“组织设置”左侧导航栏中的“版块”下，选择“进程”    。 
   
1. 在“所有进程”窗格中，选择“敏捷”旁边的“...”，然后选择“创建继承的进程”     。
   
   ![从敏捷中创建继承的进程](./media/agile-development/10-settings.png) 
   
1. 在“从敏捷中创建继承的进程”对话框中，输入名称“AgileDataScienceProcess”，然后选择“创建进程”    。
   
   ![创建 AgileDataScienceProcess 进程](./media/agile-development/11-agileds.png)
   
1. 在“所有进程”中，选择新的 AgileDataScienceProcess   。 
   
1. 在“工作项类型”选项卡上，通过选择每个项旁边的“...”，再选择“禁用”来禁用“长篇故事”、“功能”、“用户情景”和“任务”        。 
   
   ![禁用工作项类型](./media/agile-development/12-disable.png)
   
1. 在“所有进程”中，选择“积压工作(backlog)级别”选项卡   。在“组合积压工作”下，选择“长篇故事(已禁用)”旁边的“...”，然后选择“编辑/重命名”     。 
   
1. 在“编辑积压工作(backlog)级别”对话框中  ：
   1. 在“名称”下，将“长篇故事”替换为“TDSP 项目”    。 
   1. 在“此积压工作(backlog)级别上的工作项类型”下，选择“新建工作项类型”，输入“TDSP 项目”，然后选择“添加”     。 
   1. 在“默认工作项类型”下，下拉菜单并选择“TDSP 项目”   。 
   1. 选择“保存”  。
   
   ![设置组合积压工作级别](./media/agile-development/13-rename.png)  
   
1. 按照相同步骤，将“功能”重命名为“TDSP 阶段”，并添加以下新建工作项类型   ：
   
   - *了解业务*
   - 数据采集 
   - *建模*
   - *部署*
   
1. 在“需求积压工作(backlog)”下，将“情景”重命名为“TDSP 子阶段”，添加新工作项类型“TDSP 子阶段”，将默认工作项类型设置为“TDSP 子阶段”      。
   
1. 在“迭代积压工作(backlog)”下，添加新工作项类型“TDSP 任务”，并将其设置为默认工作项类型   。 
   
完成步骤之后，积压工作 (backlog) 级别应如下所示：
   
 ![TDSP 模板积压工作 (backlog) 级别](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>创建敏捷数据科学流程工作项

可以使用数据科学流程模板创建 TDSP 项目并跟踪与 TDSP 生命周期阶段相对应的工作项。

1. 在 Azure DevOps 组织主页上，选择“新建项目”  。 
   
1. 在“创建新项目”对话框中，为项目命名，然后选择“高级”   。 
   
1. 在“工作项进程”下，选择“AgileDataScienceProcess”，然后选择“创建”    。
   
   ![创建 TDSP 项目](./media/agile-development/15-newproject.png)
   
1. 在新建的项目中，在左侧导航栏中选择“版块” > “积压工作(backlog)”   。
   
1. 若要使 TDSP 项目可见，请选择“配置团队设置”图标  。 在“设置”屏幕中，选中“TDSP 项目”复选框，然后选择“保存并关闭”    。
   
   ![选中“TDSP 项目”复选框](./media/agile-development/16-enabledsprojects1.png)
   
1. 若要创建特定于数据科学的 TDSP 项目，请在顶部栏中选择“TDSP 项目”，然后选择“新建工作项”   。 
   
1. 在弹出窗口中，为 TDSP 项目工作项命名，并选择“添加到顶部”  。
   
   ![创建数据科学项目工作项](./media/agile-development/17-dsworkitems0.png)
   
1. 若要在 TDSP 项目下添加工作项，请选择项目旁边的 +，然后选择要创建的工作项类型  。 
   
   ![选择数据科学工作项类型](./media/agile-development/17-dsworkitems1.png)
   
1. 在新工作项中填写详细信息，然后选择“保存并关闭”  。
   
1. 继续选择工作项旁边的 + 符号，以添加新的 TDSP 阶段、子阶段和任务  。 
   
下面是数据科学项目工作项在“积压工作(backlog)”视图中的显示方式示例  ：

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>后续步骤

[使用 Git 进行协作编程](collaborative-coding-with-git.md)介绍了如何使用 Git 作为共享代码开发框架针对数据科学项目执行协作代码开发，以及如何将这些编程活动链接到使用敏捷流程规划的工作。

[示例演练](walkthroughs.md)一文列出了特定方案的演练，并提供链接和缩略图描述。 链接的方案展示了如何将云、本地工具以及服务合并到工作流或管道中，以此创建智能应用程序。
  
关于敏捷流程的其他资源：

- [敏捷流程](/azure/devops/boards/work-items/guidance/agile-process)
  
- [敏捷流程工作项类型和工作流](/azure/devops/boards/work-items/guidance/agile-process-workflow)

