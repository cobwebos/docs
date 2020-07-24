---
title: Team Data Science Process 角色和任务
description: 数据科学组的关键组成、个人角色和关联任务的概述。
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4bcbbd9378b6ea861c926eed16d2ceed8131b913
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082743"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Team Data Science Process 角色和任务

Team Data Science Process (TDSP) 是由 Microsoft 开发的一个框架，其提供的结构化方法可以有效地生成预测分析解决方案和智能应用程序。 本文概述了数据科学团队对此过程进行标准化的关键人员角色和关联任务。

本文介绍了有关如何设置 TDSP 环境的教程的链接。 教程提供了有关使用 Azure DevOps Projects、Azure Repos 存储库和 Azure Boards 的详细指南。  激发目标是通过建模和部署来转变概念。

教程使用了 Azure DevOps，因为这是 Microsoft 内部实现 TDSP 的方法。 Azure DevOps 通过集成基于角色的安全性、工作项管理和跟踪以及代码托管、共享和源代码管理来促进协作。 教程还使用 Azure [数据科学虚拟机](https://aka.ms/dsvm) (DSVM) 作为分析桌面，该桌面已预先配置了多个常用的数据科学工具，并集成了 Microsoft 软件和 Azure 服务。 

可以借助教程，通过其他代码托管方法、敏捷规划、开发工具和环境来实现 TDSP，但某些功能可能不可用。

## <a name="structure-of-data-science-groups-and-teams"></a>数据科学组和团队的结构

在企业中，数据科学职能通常采用以下层次结构进行组织：

- 数据科学组
  - 组中的数据科学团队

此类结构中存在组主管和团队主管。 通常，数据科学项目由数据科学团队完成。 数据科学团队由项目主管负责项目管理和治理，由特定数据科学家和工程师负责执行项目的数据科学和数据工程方面的任务。 初始项目设置和管理事项由组、团队或项目的主管负责。

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>四个 TDSP 角色的定义和任务
假设数据科学部门由一个组内的各团队构成，则 TDSP 人员有四种不同的职能角色：

1. **组管理员**：管理企业中整个数据科学单元。 数据科学部门可能有多个团队，每个团队都在不同的业务垂直领域开展多个数据科学项目。 组管理员可以将任务委托给代理，但与角色相关的任务不变。
   
2. **团队主管**：管理企业数据科学单位的团队。 团队由多个数据科学家组成。 对于小型数据科学部门，组管理员和团队主管可能是同一人。
   
3. **项目主管**：管理特定数据科学项目上各个数据科学家的日常活动。
   
4. **项目各个参与者**：数据科学家、业务分析人员、数据工程师、架构师和执行数据科学项目的其他人。

> [!NOTE]
> 根据企业结构和规模，一个人可能会兼任多个角色，或者多个人可能担任一个角色的情况。

### <a name="tasks-to-be-completed-by-the-four-roles"></a>将由四个角色完成的任务

下图显示了每个“团队数据科学流程”角色的最高级别的任务。 此架构和下面每个 TDSP 角色的更详细的任务概述可帮助你根据自己的职责选择所需教程。

![角色和任务概述](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>组管理员任务

组管理员或指定的 TDSP 系统管理员需完成以下任务以采用 TDSP：

- 在组织中创建 Azure DevOps“组织”和组项目****。 
- 在 Azure DevOps 组项目中创建“项目模板存储库”，并通过 Microsoft TDSP 团队开发的项目模板存储库将其设定为种子****。 Microsoft TDSP 项目模板存储库提供：
  - “标准化目录结构”，包括数据、代码和文档的目录****。
  - 一套“标准化的文档模板”，用于引导有效的数据科学过程****。
- 创建“实用程序存储库”，通过 Microsoft TDSP 团队开发的实用程序存储库将其设定为种子****。 Microsoft 的 TDSP 实用工具存储库提供了一套有用的实用程序，使数据科学家工作更有效率。 Microsoft 实用程序存储库包含用于交互式数据浏览、分析、报告和基线建模和报告的实用程序。
- 为组织帐户设置“安全控制策略”****。

有关详细说明，请参阅[数据科学团队的组管理员任务](group-manager-tasks.md)。

## <a name="team-lead-tasks"></a>团队主管任务

团队主管或指定的项目管理员需完成以下任务以采用 TDSP：

- 在组的 Azure DevOps 组织中创建团队“项目”****。
- 在项目下创建“项目模板存储库”，通过组管理员或管理员委托人设置的组项目模板存储库将其设定为种子****。
- 创建**团队实用工具存储库**，将其从组实用工具存储库中植入，并将特定于团队的实用程序添加到存储库。
- 创建“Azure 文件存储”，为团队存储有用的数据资产（可选）[](https://azure.microsoft.com/services/storage/files/)。 其他团队成员可以在其分析桌面上装载这个共享的云文件存储。
- 将 Azure 文件存储装载到团队的“DSVM”上，并向其中添加团队数据资产（可选）****。
- 设置安全控制，方法是添加团队成员并配置其权限****。

有关详细说明，请参阅[数据科学团队的团队主管任务](team-lead-tasks.md)。


## <a name="project-lead-tasks"></a>项目主管任务

项目主管需完成以下任务以采用 TDSP：

- 在团队项目中创建“项目存储库”，通过项目模板存储库将其设定为种子****。
- 创建“Azure 文件存储”来存储项目的数据资产（可选）****。
- 将 Azure 文件存储装载到“DSVM”并向其中添加项目数据资产（可选）****。
- 通过添加项目成员并配置其权限来设置“安全控制”****。

有关详细说明，请参阅 [数据科学团队的项目主管任务](project-lead-tasks.md)。

## <a name="project-individual-contributor-tasks"></a>项目单独参与者任务

项目单独参与者（通常是数据科学家）使用 TDSP 执行以下任务：

- 克隆项目主管设置的项目存储库****。
- 在团队和项目的“数据科学虚拟机”(DSVM) 上装载共享的“Azure 文件存储”（可选）********。
- 执行项目。

若要通过详细说明来了解如何载入项目，请参阅 [数据科学团队的项目单独参与者任务](project-ic-tasks.md)。

## <a name="data-science-project-execution-workflow"></a>数据科学项目执行工作流

数据科学家、项目主管和团队主管可以根据相关教程来创建工作项，以便从头至尾跟踪项目的所有任务和阶段。 使用 Azure Repos 促进数据科学家之间的协作，确保在项目执行过程中生成的项目受版本控制并由所有项目成员共享。 使用 Azure DevOps，可以将 Azure Boards 工作项与 Azure Repos 存储库分支链接起来，并轻松跟踪为工作项执行的操作。

下图概述了用于项目执行的 TDSP 工作流：

![典型的数据科学项目工作流](./media/roles-tasks/overview-project-execute.png)

工作流步骤可分为三个活动：

- 项目主管执行冲刺 (sprint) 规划
- 数据科学家在 `git` 分支上开发项目，以处理工作项
- 项目主管或其他团队成员执行代码评审并将工作分支合并到主分支

有关项目执行工作流的详细说明，请参阅[数据科学项目的敏捷开发](agile-development.md)。

## <a name="tdsp-project-template-repository"></a>TDSP 项目模板存储库

使用 Microsoft TDSP 团队的[项目模板存储库](https://github.com/Azure/Azure-TDSP-ProjectTemplate)来支持高效的项目执行和协作。 此存储库提供标准化的目录结构和文档模板，可将其用于自己的 TDSP 项目。

## <a name="next-steps"></a>后续步骤

浏览 Team Data Science Process 定义的角色和任务的更详细说明：

- [数据科学团队的组管理员任务](group-manager-tasks.md)
- [数据科学团队的团队主管任务](team-lead-tasks.md)
- [数据科学团队的项目主管任务](project-lead-tasks.md)
- [数据科学团队的项目个人参与者任务](project-ic-tasks.md)
