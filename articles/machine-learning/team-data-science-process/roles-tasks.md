---
title: Team Data Science Process 角色和任务 - Azure | Microsoft Docs
description: 概述数据科学团队项目的关键组成、个人角色和关联的任务。
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: deguhath
ms.openlocfilehash: 275c1d1dad9030da776900c4e2b97152c8d2d581
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839126"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Team Data Science Process 角色和任务

Team Data Science Process 是由 Microsoft 开发的一个框架，其提供的结构化方法可以有效地生成预测分析解决方案和智能应用程序。 本文概述了关键的人员角色及其关联的任务，这些任务由致力于标准化此流程的数据科学团队处理。 

此简介链接到的教程说明了如何为整个数据科学组、数据科学团队和项目设置 TDSP 环境。 我们详细介绍了如何将教程中的 Visual Studio Team Services (VSTS) 用作代码托管平台，以及如何使用敏捷的规划工具来管理团队任务、控制访问权限和管理存储库。 

你也可以根据此信息，在自己的代码托管平台和敏捷的规划工具中实现 TDSP。 

## <a name="structures-of-data-science-groups-and-teams"></a>数据科学组和团队的结构
在企业中，数据科学职能通常可以采用以下层次结构进行组织：

1. 数据科学组

2. 组中的数据科学团队

此类结构中存在组主管和团队主管。 通常情况下，数据科学项目由数据科学团队完成，该团队可能包括项目主管（管理项目和任务）和数据科学家或工程师（个人参与者/技术人员，负责执行项目的数据科学和数据工程部分的任务）。 在执行之前，设置和管理事项由组、团队或项目的主管负责。

## <a name="definition-of-four-tdsp-roles"></a>四个 TDSP 角色的定义
通过上面的假设，我们为团队人员指定了四个不同的角色：

1. 组管理员。 组管理员是企业中整个数据科学部门的经理。 数据科学部门可能有多个团队，每个团队都在不同的业务垂直领域开展多个数据科学项目。 组管理员可以将任务委托给代理，但与角色相关的任务不变。

2. 团队主管。 团队主管管理企业数据科学部门的团队。 团队由多个数据科学家组成。 如果数据科学部门的数据科学家较少，组管理员和团队主管可能是同一人。

3. 项目主管。 项目主管管理特定数据科学项目各个数据科学家的日常活动。

4. 项目单独参与者。 数据科学家、业务分析师、数据工程师、架构师等。项目单独参与者执行数据科学项目。 


**[AZURE.NOTE]**：根据企业结构，可能会出现一个人兼职多个角色，或者多个人共同担任一个角色的情况。 在小企业中，或者在数据科学部门人员较少的企业中，常常会出现这种情况。

## <a name="tasks-to-be-completed-by-four-personnel"></a>由四人完成的任务

下图描绘了各人员在采用和实施由 Microsoft 概念化的 Team Data Science Process 时，按角色应完成的顶级任务。 

![角色和任务概述](./media/roles-tasks/overview-tdsp-top-level.png)

在了解此架构以及下面针对 TDSP 中每个角色所分配的任务提供的更详尽概述以后，你应该就可以根据自己在部门中的职责选择合适的教程。

>[AZURE.NOTE] 在以下说明中，我们通过相关步骤介绍了如何在 Visual Studio Team Services (VSTS) 中设置 TDSP 环境并完成其他数据科学任务。 我们详述了如何使用 VSTS 完成这些任务，因为这是我们在 Microsoft 中用来实现 TDSP 的方法。 VSTS 可以集成工作项（用于跟踪任务）的管理与代码托管服务（用于共享实用程序、组织版本以及提供基于角色的安全性），从而促进协作。 可以随意选择其他平台来实施 TDSP 所概述的任务。 但是，你可能无法使用我们在 VSTS 中使用的某些功能，具体取决于你的平台。 
>
>我们还使用 Azure 云中的[数据科学虚拟机 (DSVM)](http://aka.ms/dsvm) 作为分析桌面，该桌面已预先配置了多个常用的数据科学工具，并集成了各种 Microsoft 软件和 Azure 服务。 可以通过 DSVM 或任何其他的开发环境来实现 TDSP。 


## <a name="group-manager-tasks"></a>组管理员任务

组管理员（或指定的 TDSP 系统管理员）需完成以下任务才能采用 TDSP：

- 在代码托管平台（例如 Github、Git、VSTS 等）上创建组帐户
- 在组帐户上创建项目模板存储库，然后通过 Microsoft TDSP 团队开发的项目模板存储库将其设定为种子。 Microsoft 推出的 TDSP 项目模板存储库提供标准化目录结构，其中包括数据、代码和文档的目录；同时还提供一组标准化文档模板，用于引导有效的数据科学过程。 
- 创建实用程序存储库，通过 Microsoft TDSP 团队开发的实用程序存储库将其设定为种子。 Microsoft 推出的 TDSP 实用程序存储库提供一组有用的实用程序来提高数据科学家的工作效率，其中包括用于交互式数据浏览、分析和报告的实用程序，以及用于基线建模和报告的实用程序。
- 在组帐户上设置这两个存储库的安全控制策略。  

有关详细的分步说明，请参阅[数据科学团队的组管理员任务](group-manager-tasks.md)。 


## <a name="team-lead-tasks"></a>团队主管任务

团队主管（或指定的团队项目管理员）需完成以下任务才能采用 TDSP：

- 如果选择 VSTS 作为代码托管平台来实施版本控制和协作，请在组的 VSTS 服务器上创建团队项目。 否则可以跳过此任务。
- 在团队项目下创建团队项目模板存储库，通过组管理员或管理员委托人设置的组项目模板存储库将其设定为种子。 
- 创建团队实用程序存储库，然后向该存储库添加特定于团队的实用程序。 
- （可选）创建 [Azure 文件存储](https://azure.microsoft.com/services/storage/files/)，用其存储适用于整个团队的数据资产。 其他团队成员可以在其分析桌面上装载这个共享的云文件存储。
- （可选）将 Azure 文件存储装载到团队主管的 数据科学虚拟机 (DSVM) 并在其上添加数据资产。
- 设置安全控制，方法是添加团队成员并配置其权限。 

有关详细的分步说明，请参阅[数据科学团队的团队主管任务](team-lead-tasks.md)。  


## <a name="project-lead-tasks"></a>项目主管任务

项目主管需完成以下任务才能采用 TDSP：

- 在团队项目下创建项目存储库，通过团队项目模板存储库将其设定为种子。 
- （可选）创建 Azure 文件存储，用其存储项目的数据资产。 
- （可选）将 Azure 文件存储装载到项目主管的 数据科学虚拟机 (DSVM) 并在其上添加项目数据资产。
- 设置安全控制，方法是添加项目成员并配置其权限。 

有关详细的分步说明，请参阅[数据科学团队的项目主管任务](project-lead-tasks.md)。 

## <a name="project-individual-contributor-tasks"></a>项目单独参与者任务

项目单独参与者（通常为数据科学家）需完成以下任务，然后才能使用 TDSP 开展数据科学项目：

- 克隆项目主管设置的项目存储库。 
- （可选）在团队和项目的数据科学虚拟机 (DSVM) 上装载共享的 Azure 文件存储。
- 执行项目。 

 
若要通过详细的分步说明来了解如何载入项目，请参阅[数据科学团队的项目单独参与者](project-ic-tasks.md)。 


## <a name="data-science-project-execution"></a>执行数据科学项目
 
数据科学家、项目主管和团队主管可以根据相关的一组说明来创建工作项，以便跟踪项目从头至尾需要完成的所有任务以及需要经历的阶段。 使用 Git 也可促进数据科学家之间的协作，确保在项目 (project) 执行过程中生成的项目 (artifact) 能够进行版本控制并可供所有项目 (project) 成员共享。

为了方便项目执行而提供的说明在制定时基于一个假设，即工作项和项目 Git 存储库均位于 VSTS 上。 对二者使用 VSTS 即可将 工作项与项目存储库的 Git 分支相关联。 可以通过这种方式轻松地跟踪对某个工作项执行的操作。 

下图概述了适合使用 TDSP 执行项目的这个工作流。

![典型的数据科学项目执行流程](./media/roles-tasks/overview-project-execute.png)

工作流包括的步骤可分成三个活动：

- 冲刺规划（项目主管）
- 根据工作项的情况在 Git 分支上开发项目 (artifact)（数据科学家）
- 代码审核以及将分支与主支合并（项目主管或其他团队成员）

有关项目执行工作流的详细分步说明，请参阅[执行数据科学项目](project-execution.md)。

## <a name="next-steps"></a>后续步骤

下面是 Team Data Science Process 定义的角色和任务的详细说明链接：

- [数据科学团队的组管理员任务](group-manager-tasks.md)
- [数据科学团队的团队主管任务](team-lead-tasks.md)
- [数据科学团队的项目主管任务](project-lead-tasks.md)
- [数据科学团队的项目单独参与者](project-ic-tasks.md)