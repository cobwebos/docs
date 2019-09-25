---
title: 数据科学项目的敏捷开发 - Team Data Science Process
description: 使用团队数据科学过程，通过在项目团队中以系统控制、版本控制和协作的方式执行数据科学项目。
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/05/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 09c5962e62077fbecc9b327320d0bb5b88416ffa
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260685"
---
# <a name="agile-development-of-data-science-projects"></a>数据科学项目的敏捷开发

本文档介绍开发人员如何使用 [Team Data Science Process](overview.md) (TDSP) 在项目团队中以系统化的协作型版本控制方式执行数据科学项目。 TDSP 是 Microsoft 开发的一个框架，它提供结构化的活动序列，可高效地执行基于云的预测分析解决方案。 有关 TDSP 上的数据科学团队处理的角色和任务的概述，请参阅[团队数据科学过程角色和任务](roles-tasks.md)。 

本文包含如何执行以下操作的说明： 

- 对项目中涉及的工作项执行*冲刺（sprint）计划*。
- 向冲刺（sprint）添加*工作项*。
- 创建和使用专门与 TDSP 生命周期阶段保持一致的*敏捷派生工作项模板*。

以下说明概述了使用 Azure DevOps 中的 Azure Boards 和 Azure Repos 设置 TDSP 团队环境所需的步骤。 说明使用 Azure DevOps，因为这是如何在 Microsoft 实施 TDSP 的。 如果你的组使用不同的代码宿主平台，团队主管任务通常不会发生变化，但完成任务的方式不同。 例如，将工作项与 Git 分支链接在一起可能与 Azure Repos 的工作项不同。

下图说明了数据科学项目的典型冲刺规划、编码和源代码控制工作流：

![Team Data Science Process](./media/agile-development/1-project-execute.png)

##  <a name='Terminology-1'></a>工作项类型

在 TDSP 冲刺（sprint）规划框架中，有四个经常使用的*工作项*类型：*功能*、*用户情景*、*任务*和*bug*。 所有工作项的积压工作处于项目级别，而不是 Git 存储库级别。 

下面是工作项类型的定义：

- **功能**：功能对应于项目参与。 与客户端不同的预订是不同的功能，最好将项目的不同阶段视为不同的功能。 如果你选择诸如 *\<ClientName >-\<EngagementName >* 之类的架构来命名你的功能，则可以轻松地识别该项目的上下文，并从名称本身进行参与。
  
- **用户情景**：用户情景是完成端到端功能所需的工作项。 用户情景示例包括：
  - 获取数据 
  - 浏览数据 
  - 生成功能
  - 生成模型
  - 使模型可操作 
  - 重新定型
  
- **任务**：任务是分配的工作项，需要完成该工作才能完成特定的用户情景。 例如，*用户情景中*的任务可能是：
  - 获取 SQL Server 凭据
  - 将数据上传到 SQL 数据仓库
  
- **Bug**：Bug 是现有代码或文档中的问题，必须修复才能完成任务。 如果 Bug 是由缺少的工作项引起的，则可以将其升级为用户情景或任务。 

数据科学家可能更喜欢使用敏捷模板，该模板将功能、用户情景和任务替换为 TDSP 生命周期阶段和 tdsp。 若要创建专门与 TDSP 生命周期阶段保持一致的敏捷派生模板，请参阅[使用 AGILE TDSP 工作模板](#set-up-agile-dsp-6)。

> [!NOTE]
> TDSP 借用软件代码管理（SCM）中的功能、用户情景、任务和 Bug 的概念。 TDSP 概念可能与传统的 SCM 定义略有不同。

## <a name='SprintPlanning-2'></a>计划冲刺（sprint）

许多数据科学家都与多个项目进行了合作，这可能需要几个月才能完成并在不同的节奏中继续进行。 冲刺规划有助于设置项目优先级、进行资源规划和分配。 在 Azure Boards 中，可以轻松地创建、管理和跟踪项目的工作项，并执行冲刺（sprint）计划，以确保项目按预期方式前进。

有关冲刺（sprint）计划的详细信息，请参阅[Scrum 冲刺](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint)。 

有关 Azure Boards 中的冲刺（sprint）计划的详细信息，请参阅[将积压工作项分配给冲刺（sprint）](/azure/devops/boards/sprints/assign-work-sprint)。 

## <a name='AddFeature-3'></a>向积压工作（backlog）添加功能 

创建项目和项目代码存储库后，可以将功能添加到积压工作（backlog）以表示项目的工作。

1. 从项目页的左侧导航栏中选择 "**板** > **积压**工作"。 
   
1. 在 "**积压**工作" 选项卡上，如果顶部栏中的工作项类型为 "**情景**"，请选择 "**功能**"。 然后选择 "**新建工作项"。**
   
   ![选择新工作项](./media/agile-development/2-sprint-team-overview.png)
   
1. 输入功能的标题，通常为项目名称，然后选择 "**添加到顶部**"。 
   
   ![输入标题，并选择 "添加到顶部"](./media/agile-development/3-sprint-team-add-work.png)
   
1. 从 "**积压工作**" 列表中，选择并打开新功能。 填写 "说明"、"分配团队成员" 和 "设置计划参数"。 
   
   还可以通过选择 "**开发**" 部分下的 "**添加链接**"，将功能链接到项目的 Azure Repos 代码存储库。 
   
   编辑完此功能后，选择 "**保存" & "关闭**"。
   
   ![编辑功能并选择保存 & 关闭](./media/agile-development/3a-add-link-repo.png)

## <a name='AddStoryunderfeature-4'></a>向功能添加用户情景 

在功能下，可以添加用户情景，以描述完成项目所需的主要步骤。 

向功能中添加新的用户情景：

1. 在 "**积压工作**" 选项卡 **+** 上，选择该功能左侧的。 
   
   ![在功能下添加新用户情景](./media/agile-development/4-sprint-add-story.png)
   
1. 为用户情景指定标题，并编辑分配、状态、描述、注释、计划和优先级等详细信息。 
   
   还可以通过选择 "**开发**" 部分下的 "**添加链接**"，将用户情景链接到项目的 Azure Repos 代码存储库的分支。 选择要将工作项链接到的存储库和分支，然后选择 **"确定"** 。
   
   ![添加链接](./media/agile-development/5-sprint-edit-story.png)
   
1. 完成用户情景的编辑后，请选择 "**保存" & "关闭**"。 

## <a name='AddTaskunderstory-5'></a>向用户情景添加任务 

任务是完成每个用户情景所需的特定详细步骤。 用户情景的所有任务完成后，用户情景也应为 "已完成"。 

若要向用户情景添加任务，请选择 " **+** 用户情景" 项旁边的，然后选择 "**任务**"。 在任务中填写标题和其他信息。

![向用户情景添加任务](./media/agile-development/7-sprint-add-task.png)

创建功能、用户情景和任务后，可以在积压工作（ **backlog** ）或**板**视图中查看这些功能，以跟踪其状态。

![积压工作视图](./media/agile-development/8-sprint-backlog-view.png)

![板视图](./media/agile-development/8a-sprint-board-view.png)

## <a name='set-up-agile-dsp-6'></a>使用 agile TDSP 工作模板

数据科学家可能更喜欢使用敏捷模板，该模板将功能、用户情景和任务替换为 TDSP 生命周期阶段和 tdsp。 在 Azure Boards 中，可以创建一个 agile 派生模板，该模板使用 TDSP 生命周期阶段来创建和跟踪工作项。 以下步骤将指导您设置特定于数据科学的敏捷过程模板，并基于该模板创建数据科学工作项。

### <a name="set-up-an-agile-data-science-process-template"></a>设置敏捷数据科学过程模板

1. 在 Azure DevOps 组织主页上，从左侧导航栏中选择 "**组织设置**"。 
   
1. 在 "**组织设置**" 左侧导航栏中的 "**板**" 下，选择 "**处理**"。 
   
1. 在 "**所有进程**" 窗格中，选择 " **Agile**" 旁边的 " **...** "，然后选择 "**创建继承进程**"。
   
   ![从敏捷创建继承进程](./media/agile-development/10-settings.png) 
   
1. 在 "**从敏捷创建继承进程**" 对话框中，输入名称*AgileDataScienceProcess*，然后选择 "**创建进程**"。
   
   ![创建 AgileDataScienceProcess 进程](./media/agile-development/11-agileds.png)
   
1. 在**所有进程**中，选择新**AgileDataScienceProcess**。 
   
1. 在 "**工作项类型**" 选项卡上，通过选择每个项**旁边的 "** ..."，然后选择 "**禁用**" 来禁用**长篇故事**、**功能**、**用户情景**和**任务**。 
   
   ![禁用工作项类型](./media/agile-development/12-disable.png)
   
1. 在**所有进程**中，选择 "**积压工作（Backlog）级别**" 选项卡。在 "**包积压**工作" 下，选择 "**长篇故事（禁用）** " 旁边的 **"..."** ，然后选择 "**编辑/重命名**"。 
   
1. 在 "**编辑积压工作级别**" 对话框中：
   1. 在 "**名称**" 下，将**长篇故事**替换为*TDSP 项目*。 
   1. 在**此积压工作（backlog）级别的 "工作项类型**" 下，选择 "**新建工作项类型**"，输入*TDSP 项目*，然后选择 "**添加**" 
   1. 在 "**默认工作项类型**" 下，选择 " **TDSP 项目**"。 
   1. 选择“保存”。
   
   ![设置组合积压工作（backlog）级别](./media/agile-development/13-rename.png)  
   
1. 按照相同的步骤将**功能**重命名为 " *TDSP 阶段*"，并添加以下新的工作项类型：
   
   - *了解业务*
   - *数据采集*
   - *建模*
   - *部署*
   
1. 在 "**要求积压工作**" 下，将 "**情景**" 重命名为*TDSP tdsp*，添加新的工作项类型*TDSP tdsp*，并将 "默认工作项类型" 设置为 " **TDSP tdsp**"。
   
1. 在 "**迭代积压工作**" 下，添加新的工作项类型 " *TDSP 任务*"，并将其设置为默认工作项类型。 
   
完成这些步骤后，积压工作（backlog）级别应如下所示：
   
 ![TDSP 模板积压工作（backlog）级别](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>创建敏捷数据科学处理工作项

您可以使用数据科学过程模板创建 TDSP 项目并跟踪与 TDSP 生命周期阶段相对应的工作项。

1. 在 Azure DevOps 组织主页上，选择 "**新建项目**"。 
   
1. 在 "**创建新项目**" 对话框中，为项目命名，然后选择 "**高级**"。 
   
1. 在 "**工作项进程**" 下，选择 " **AgileDataScienceProcess**"，然后选择 "**创建**"。
   
   ![创建 TDSP 项目](./media/agile-development/15-newproject.png)
   
1. 在新创建的项目中，在左侧导航栏中选择 "**板** > **积压**工作"。
   
1. 若要使 TDSP 项目可见，请选择 "**配置团队设置**" 图标。 在 "**设置**" 屏幕中，选中 " **TDSP 项目**" 复选框，然后选择 "**保存并关闭**"。
   
   !["选择 TDSP 项目" 复选框](./media/agile-development/16-enabledsprojects1.png)
   
1. 若要创建特定于数据科学的 TDSP 项目，请在顶部栏中选择 " **TDSP 项目**"，然后选择 "**新建工作项**"。 
   
1. 在弹出窗口中，为 TDSP 项目工作项指定一个名称，然后选择 "**添加到顶部**"。
   
   ![创建数据科学项目工作项](./media/agile-development/17-dsworkitems0.png)
   
1. 若要在 TDSP 项目下添加工作项，请选择 **+** 该项目旁边的，然后选择要创建的工作项的类型。 
   
   ![选择数据科学工作项类型](./media/agile-development/17-dsworkitems1.png)
   
1. 在新工作项中填写详细信息，然后选择 "**保存" & "关闭**"。
   
1. 继续选择 "工作 **+** 项" 旁边的符号，以添加新的 "TDSP" 阶段、"tdsp" 和 "任务"。 
   
下面的示例演示数据科学项目工作项应如何出现在**积压**工作视图中：

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>后续步骤

[使用 git 进行协作编码](collaborative-coding-with-git.md)介绍了如何使用 git 作为共享代码开发框架，为数据科学项目执行协作代码开发，以及如何将这些编码活动链接到使用敏捷过程规划的工作中。

[示例演练](walkthroughs.md)列出了特定方案的演练，其中包含链接和缩略图说明。 链接的方案说明了如何将云和本地工具和服务合并到工作流或管道中，以创建智能应用程序。
  
敏捷过程的其他资源：

- [敏捷过程](/azure/devops/boards/work-items/guidance/agile-process)
  
- [敏捷过程工作项类型和工作流](/azure/devops/boards/work-items/guidance/agile-process-workflow)

