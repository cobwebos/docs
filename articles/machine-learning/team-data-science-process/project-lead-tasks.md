---
title: Team Data Science Process 中项目主管的任务
description: 团队数据科学过程团队的项目主管任务的详细演练
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/24/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8a94a2ae5298bbee8bb1c9c0fa044eb3189147be
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244391"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>团队数据科学过程中的项目主管任务

本文介绍*项目主管*在[团队数据科学过程](overview.md)（TDSP）中为其项目团队设置存储库所完成的任务。 TDSP 是 Microsoft 开发的一个框架，它提供结构化的活动序列，可高效地执行基于云的预测分析解决方案。 TDSP 旨在帮助改进协作和团队学习。 有关数据科学团队在 TDSP 上标准化的人员角色和相关任务的概述，请参阅[团队数据科学过程角色和任务](roles-tasks.md)。

项目主管管理 TDSP 中特定数据科学项目的各个数据科学家的日常活动。 下图显示了项目领导任务的工作流：

![项目主管任务工作流](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

本教程介绍第1步：创建项目存储库和步骤2：从团队 ProjectTemplate 存储库创建种子项目存储库。 

对于步骤3：为项目创建功能工作项，并使用步骤4：为项目阶段添加情景，请参阅[数据科学项目的敏捷开发](agile-development.md)。

对于步骤5：创建和自定义存储/分析资产并共享，如有必要，请参阅[创建团队数据和分析资源](team-lead-tasks.md#create-team-data-and-analytics-resources)。

对于步骤6：设置对项目存储库的安全控制，请参阅[添加团队成员和配置权限](team-lead-tasks.md#add-team-members-and-configure-permissions)。

> [!NOTE] 
> 本文使用 Azure Repos 来设置 TDSP 项目，因为这是在 Microsoft 实现 TDSP 的方法。 如果你的团队使用另一个代码宿主平台，则项目主管任务是相同的，但完成这些任务的方法可能不同。

## <a name="prerequisites"></a>先决条件

本教程假定你的[组管理员](group-manager-tasks.md)和[团队主管](team-lead-tasks.md)已设置以下资源和权限：

- 数据单元的 Azure DevOps**组织**
- 用于数据科学团队的团队**项目**
- 团队模板和实用程序**存储库**
- 你的组织帐户的**权限**，你可以为你的项目创建和编辑存储库

若要克隆存储库并修改本地计算机或 Data Science Virtual Machine （DSVM）上的内容，或设置 Azure 文件存储并将其装载到 DSVM，还需要以下各项：

- Azure 订阅。
- 已在计算机上安装 Git。 如果你使用的是 DSVM，则将预安装 Git。 否则，请参阅[平台和工具附录](platforms-and-tools.md#appendix)。
- 如果要使用 DSVM，请在 Azure 中创建和配置 Windows 或 Linux DSVM。 有关详细信息和说明，请参阅[Data Science Virtual Machine 文档](/azure/machine-learning/data-science-virtual-machine/)。
- 对于安装在计算机上的 Windows DSVM、 [Git 凭据管理器（GCM）](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 。 在*README.md*文件中，向下滚动到 "**下载并安装**" 部分，然后选择**最新的安装程序**。 从 "安装程序" 页下载 *.exe*安装程序并运行它。 
- 对于 Linux DSVM，在 DSVM 上设置 SSH 公钥，并将其添加到 Azure DevOps 中。 有关详细信息和说明，请参阅[平台和工具附录](platforms-and-tools.md#appendix)中的**创建 SSH 公钥**部分。 

## <a name="create-a-project-repository-in-your-team-project"></a>在团队项目中创建项目存储库

在团队的**MyTeam**项目中创建项目存储库：

1. 请参阅*https：\//\<服务器名称 >/\<组织名称 >/\<团队名称 >* ，如**https：\//dev.azure.com/DataScienceUnit/MyTeam**，并从左侧导航栏中选择**存储库**。 
   
1. 选择页面顶部的 "存储库名称"，然后从下拉列表中选择 "**新建存储库**"。
   
   ![选择新存储库](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. 在 "**创建新存储库**" 对话框中，确保在 "**类型**" 下选择 " **Git** "。 在 "**存储库名称**" 下输入*DSProject1* ，然后选择 "**创建**"。
   
   ![创建存储库](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. 确认你可以在项目设置页上看到新的**DSProject1**存储库。 
   
   ![项目设置中的项目存储库](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>将团队模板导入到你的项目存储库

若要将项目存储库填充到团队模板存储库的内容，请执行以下操作：

1. 在团队项目的 "**摘要**" 页上，在左侧导航栏中选择 "**存储库**"。 
   
1. 选择页面顶部的 "存储库名称"，然后从下拉列表中选择 " **DSProject1** "。
   
1. 在 " **DSProject1** " 页上，选择 "**导入**"。 
   
   ![选择导入](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. 在 "**导入 git 存储库**" 对话框中，选择 " **Git** " 作为**源类型**，并在 "**克隆 url**" 下输入**TeamTemplate**存储库的 url。 URL 为*https：\//\<服务器名称 >/\<组织名称 >/\<团队名称 >/_git 团队模板存储库名称\<* 。 例如： **https：\//dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**。 
   
1. 选择“导入”。 你的团队模板存储库的内容将导入到你的项目存储库中。 
   
   ![导入团队模板存储库](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

如果需要自定义项目存储库的内容以满足项目的特定需求，可添加、删除或修改存储库文件和文件夹。 你可以直接在 Azure Repos 中进行操作，或将存储库克隆到本地计算机或 DSVM，进行更改，然后将更新提交并推送到共享的项目存储库。 按照[自定义团队存储库内容](team-lead-tasks.md#customize-the-contents-of-the-team-repositories)中的说明进行操作。

## <a name="next-steps"></a>后续步骤

下面是团队数据科学过程定义的其他角色和任务的详细说明的链接：

- [数据科学团队的组管理员任务](group-manager-tasks.md)
- [数据科学团队的团队主管任务](team-lead-tasks.md)
- [用于数据科学团队的单个参与者任务](project-ic-tasks.md)
