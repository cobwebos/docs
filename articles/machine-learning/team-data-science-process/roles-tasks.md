---
title: Team Data Science Process 역할 및 작업
description: 用于数据科学组的关键组件、人员角色和相关任务的概述。
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
ms.openlocfilehash: c1ed731943abf0efdd99ea54d2318fa402835e08
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720004"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Team Data Science Process 역할 및 작업

团队数据科学过程（TDSP）是 Microsoft 开发的一个框架，它提供了一种高效构建预测分析解决方案和智能应用程序的结构化方法。 本文概述了在此过程中实现数据科学团队标准化的主要人员角色和相关任务。

本文介绍了有关如何设置 TDSP 环境的教程的链接。 教程提供了有关使用 Azure DevOps Projects、Azure Repos 存储库和 Azure Boards 的详细指南。  激发目标是通过建模和部署来转变概念。

教程使用 Azure DevOps，因为这是如何实现 Microsoft 的 TDSP 的。 Azure DevOps 通过集成基于角色的安全性、工作项管理和跟踪以及代码托管、共享和源代码管理，简化了协作。 本教程还使用 Azure [Data Science Virtual Machine](https://aka.ms/dsvm) （DSVM）作为分析桌面，该桌面已预先配置了多个常用的数据科学工具，并与 Microsoft 软件和 Azure 服务集成。 

您可以使用教程通过其他代码托管、敏捷规划、开发工具和环境实现 TDSP，但某些功能可能不可用。

## <a name="structure-of-data-science-groups-and-teams"></a>数据科学组和团队的结构

企业中的数据科学函数通常按以下层次结构进行组织：

- 数据科学组
  - 组中的数据科学团队

在这种结构中，有一个组领导和团队主管。 通常，数据科学项目由数据科学团队完成。 数据科学团队具有项目管理和管理任务的项目主管，以及各个数据科学家和工程师，可执行项目的数据科学和数据工程部分。 初始项目设置和调控由组、团队或项目主管完成。

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>四个 TDSP 角色的定义和任务
假设数据科学单元由组中的团队组成，TDSP 人员有四种不同的角色：

1. **组管理员**：管理企业中整个数据科学单元。 데이터 과학 단위에는 여러 팀이 있을 수 있고 각 팀은 고유 비즈니스 직종에서 여러 데이터 과학 프로젝트 작업을 수행합니다. 그룹 관리자는 대리인에게 작업을 위임할 수는 있지만 역할에 관련된 작업은 변경되지 않습니다.
   
2. **团队主管**：管理企业数据科学单位的团队。 팀은 여러 명의 데이터 과학자로 구성됩니다. 对于小型数据科学单位，组经理和团队主管可能是同一个人。
   
3. **项目主管**：管理特定数据科学项目上各个数据科学家的日常活动。
   
4. **项目各个参与者**：数据科学家、业务分析人员、数据工程师、架构师和执行数据科学项目的其他人。

> [!NOTE]
> 根据企业的结构和规模，单个人员可能会扮演多个角色，或多人可能会填充角色。

### <a name="tasks-to-be-completed-by-the-four-roles"></a>由四个角色完成的任务

下图显示了每个团队数据科学流程角色的顶级任务。 此架构和下面更详细地概述了每个 TDSP 角色的任务，可帮助你根据自己的责任选择所需教程。

![역할 및 작업 개요](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>그룹 관리자 작업

组管理器或指定的 TDSP 系统管理员完成以下任务以采用 TDSP：

- 在组织中创建 Azure DevOps**组织**和组项目。 
- 在 Azure DevOps 组项目中创建一个**项目模板存储库**，并通过 Microsoft TDSP 团队开发的项目模板存储库将其设定为种子。 Microsoft TDSP 项目模板存储库提供：
  - **标准化的目录结构**，其中包括数据、代码和文档的目录。
  - 一组**标准化文档模板**，用于指导高效的数据科学过程。
- 创建**实用程序存储库**，并通过 Microsoft TDSP 团队开发的实用程序存储库将其设定为种子。 Microsoft 提供的 TDSP 实用程序存储库提供了一套有用的实用程序，使数据科学家工作更有效率。 Microsoft 实用程序存储库包含用于交互式数据浏览、分析、报告和基线建模和报告的实用程序。
- 为组织帐户设置**安全控制策略**。

有关详细说明，请参阅[数据科学团队的组管理员任务](group-manager-tasks.md)。

## <a name="team-lead-tasks"></a>팀 리더 작업

团队主管或指定的项目管理员完成以下任务以采用 TDSP：

- 在组的 Azure DevOps 组织中创建团队**项目**。
- 在项目中创建**项目模板存储库**，并通过组管理器或委托设置的组项目模板存储库将其设定为种子。
- 创建**团队实用工具存储库**，将其从组实用工具存储库中植入，并将特定于团队的实用程序添加到存储库。
- （可选）创建[Azure 文件存储](https://azure.microsoft.com/services/storage/files/)来存储团队有用的数据资产。 다른 팀 구성원도 팀의 분석 데스크톱에 이 공유 클라우드 파일 저장소를 탑재할 수 있습니다.
- 可以选择在团队**DSVM**上装载 Azure 文件存储，并向其中添加团队数据资产。
- 设置**安全控制**，方法是添加团队成员并配置其权限。

有关详细说明，请参阅[数据科学团队的团队主管任务](team-lead-tasks.md)。


## <a name="project-lead-tasks"></a>프로젝트 리더 작업

项目主管完成以下任务以采用 TDSP：

- 在团队项目中创建一个**项目存储库**，并从项目模板存储库中将其设定为种子。
- （可选）创建**Azure 文件存储**来存储项目的数据资产。
- 可以选择将 Azure 文件存储装载到**DSVM** ，并向其添加项目数据资产。
- 通过添加项目成员并配置其权限来设置**安全控制**。

有关详细说明，请参阅[数据科学团队的项目主管任务](project-lead-tasks.md)。

## <a name="project-individual-contributor-tasks"></a>프로젝트 개별 참가자 작업

项目个体撰稿人（通常是数据科学家）使用 TDSP 执行以下任务：

- 克隆项目主管设置的**项目存储库**。
- 可以选择在其**Data Science Virtual Machine**上装载共享团队和项目**Azure 文件存储**（DSVM）。
- 执行项目。

有关加入项目的详细说明，请参阅为[数据科学团队投影各个参与者任务](project-ic-tasks.md)。

## <a name="data-science-project-execution-workflow"></a>数据科学项目执行工作流

按照相关教程，数据科学家、项目主管和团队主管可以创建工作项来跟踪项目的所有任务和阶段。 使用 Azure Repos 促进数据科学家间的协作，并确保在项目执行期间生成的项目受版本控制并由所有项目成员共享。 使用 Azure DevOps，你可以将 Azure Boards 工作项与 Azure Repos 存储库分支链接，并轻松跟踪为工作项执行的操作。

下图概述了用于项目执行的 TDSP 工作流：

![典型的数据科学项目工作流](./media/roles-tasks/overview-project-execute.png)

工作流步骤可分为三个活动：

- 项目主管执行冲刺（sprint）规划
- 数据科学家在 `git` 分支上开发项目，以对工作项进行寻址
- 项目主管或其他团队成员执行代码评审并将工作分支合并到主分支

有关项目执行工作流的详细说明，请参阅[数据科学项目的敏捷开发](agile-development.md)。

## <a name="tdsp-project-template-repository"></a>TDSP 项目模板存储库

使用 Microsoft TDSP 团队的[项目模板存储库](https://github.com/Azure/Azure-TDSP-ProjectTemplate)来支持高效的项目执行和协作。 存储库提供了可用于自己的 TDSP 项目的标准化目录结构和文档模板。

## <a name="next-steps"></a>다음 단계

Team Data Science Process에서 정의한 역할 및 작업에 대한 자세한 설명을 살펴봅니다.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)
- [为数据科学团队项目单独的参与者任务](project-ic-tasks.md)
