---
title: Team Data Science Process 中项目主管的任务
description: 面向 Team Data Science Process 项目主管的详细任务演练
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a4d3a1480a852218e698862a509c4af45e49eb8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "76714408"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Team Data Science Process 中的项目主管任务

本文介绍了“项目主管”  在 [Team Data Science Process](overview.md) (TDSP) 中为其项目团队设置存储库时完成的任务。 TDSP 是 Microsoft 开发的一个框架，它提供结构化的活动序列，可高效地执行基于云的预测分析解决方案。 TDSP 设计用于帮助改进协作和团队学习。 有关致力于标准化 TDSP 的数据科学团队要处理的人员角色及相关任务的概述，请参阅[团队数据科学流程角色和任务](roles-tasks.md)。

项目主管在 TDSP 中管理各个数据科学家在特定数据科学项目中的日常活动。 下图显示了项目主管任务的工作流：

![团队主管任务工作流](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

本教程包括步骤 1：创建项目存储库和步骤 2：从你的团队 ProjectTemplate 存储库中播种项目存储库。 

对于步骤 3：为项目创建特征工作项，和步骤4：添加项目阶段的情景，请参阅[数据科学项目的敏捷开发](agile-development.md)。

对于步骤 5：创建并自定义存储/分析资产和共享（如有必要），请参阅[创建团队数据和分析资源](team-lead-tasks.md#create-team-data-and-analytics-resources)。

对于步骤 6：设置项目存储库的安全控制，请参阅[添加团队成员并配置权限](team-lead-tasks.md#add-team-members-and-configure-permissions)。

> [!NOTE] 
> 本文使用 Azure Repos 来设置 TDSP 项目，因为 Microsoft 使用此方法实现 TDSP。 如果你的团队使用其他代码托管平台，则团队主管任务是相同的，但完成这些任务的方法可能不同。

## <a name="prerequisites"></a>先决条件

本教程假设[组管理员](group-manager-tasks.md)和[团队主管](team-lead-tasks.md)已设置了以下资源和权限：

- 数据单位的 Azure DevOps **组织**
- 数据科学团队的团队**项目**
- 团队模板和实用工具**存储库**
- 你对组织帐户的**权限**，用于为项目创建和编辑存储库

若要克隆存储库并修改本地计算机或 Data Science Virtual Machine （DSVM）上的内容，或设置 Azure 文件存储并将其装载到 DSVM，还需要考虑此清单：

- Azure 订阅。
- 计算机上安装的 Git。 如果要使用 DSVM，则需预安装 Git。 否则，请参阅[平台和工具附录](platforms-and-tools.md#appendix)。
- 如果要使用 DSVM，需要在 Azure 中创建和配置 Windows 或 Linux DSVM。 有关详细信息和说明，请参阅 [Data Science Virtual Machine 文档](/azure/machine-learning/data-science-virtual-machine/)。
- 对于 Windows DSVM，需要在计算机上安装 [Git 凭据管理器 (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)。 在 README.md 文件中，向下滚动到“下载并安装”部分，然后选择“最新安装程序”    。 从安装程序页下载 .exe 安装程序并运行它  。 
- 对于 Linux DSVM，需要在 DSVM 上设置 SSH 公钥，并将其添加到 Azure DevOps 中。 有关详细信息和说明，请参阅[平台和工具附录](platforms-and-tools.md#appendix)中的“创建 SSH 公钥”  部分。 

## <a name="create-a-project-repository-in-your-team-project"></a>在团队项目中创建项目存储库

若要在团队的 **MyTeam** 项目中创建项目存储库，请执行以下操作：

1. 请在*https \/ / \<server name> ： / \<organization name> （ / 例如，https：/dev.azure.com/DataScienceUnit/MyTeam）中转到你的团队的项目摘要页，然后从左侧导航栏中选择 "存储库"。 \<team name> * ** \/ ** **Summary** **Repos** 
   
1. 在页面顶部选择存储库名称，然后从下拉列表中选择“新建存储库”  。
   
   ![选择“新建存储库”](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. 在“创建新存储库”对话框中，确保已在“类型”下选择“Git”。    在“存储库名称”下输入 *DSProject1*，然后选择“创建”。  
   
   ![创建存储库](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. 确认可以在项目设置页面上看到新的 **DSProject1** 存储库。 
   
   ![“项目设置”中的项目存储库](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>将团队模板导入到项目存储库中

若要使用团队模板存储库的内容填充项目存储库，请执行以下操作：

1. 在团队项目的“摘要”页上，在左侧导航栏中选择“Repos”。   
   
1. 在页面顶部选择存储库名称，然后从下拉列表中选择 **DSProject1**。
   
1. 在“DSProject1 为空”  页面上，选择“导入”  。 
   
   ![选择“导入”](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. 在“导入 Git 存储库”  对话框中，选择“Git”作为“源类型”，然后在“克隆 URL”下输入 **TeamTemplate** 存储库的 URL。    URL 为*https： \/ / \<server name> / \<organization name> / \<team name> /_git/ \<team template repository name> *。 例如：**https:\//dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**。 
   
1. 选择“导入”  。 团队模板存储库的内容将导入到项目存储库中。 
   
   ![导入团队模板存储库](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

如果需要自定义项目存储库的内容以满足项目的特定需求，你可以添加、删除或修改存储库文件和文件夹。 你可以直接在 Azure Repos 中进行操作，或者将存储库克隆到本地计算机或 DSVM，进行更改，然后将更新提交并推送到共享的项目存储库。 按照[自定义团队存储库的内容](team-lead-tasks.md#customize-the-contents-of-the-team-repositories)中的说明进行操作。

## <a name="next-steps"></a>后续步骤

下面是 Team Data Science Process 定义的其他角色和任务的详细说明链接：

- [数据科学团队的组管理员任务](group-manager-tasks.md)
- [数据科学团队的团队主管任务](team-lead-tasks.md)
- [数据科学团队的个人参与者任务](project-ic-tasks.md)
