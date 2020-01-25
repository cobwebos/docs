---
title: Team Data Science Process에서 개별 기여자에 대한 작업
description: 针对数据科学团队项目的单个参与者的任务的详细演练。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d9942c31b63de77196b8b51b88376cb8ef74b990
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721245"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Team Data Science Process에서 개별 기여자에 대한 작업

本主题概述了*单个参与者*完成的、用于在[团队数据科学过程](overview.md)（TDSP）中设置项目的任务。 目标是在协作团队环境中工作，该环境在 TDSP 上实现标准化。 TDSP 旨在帮助改进协作和团队学习。 有关 TDSP 上的数据科学团队处理的人员角色及其相关任务的概述，请参阅[团队数据科学过程角色和任务](roles-tasks.md)。

下图显示了项目各个参与者（数据科学家）完成设置其团队环境所要完成的任务。 有关如何在 TDSP 下执行数据科学项目的说明，请参阅[执行数据科学项目](project-execution.md)。 

![单个参与者任务](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository**是你的项目团队为共享项目模板和资产而维护的存储库。
- **TeamUtilities**是团队专门为你的团队维护的实用程序存储库。 
- **GroupUtilities**是你的组维护的存储库，用于跨整个组共享有用的实用程序。 

> [!NOTE] 
> 本文使用 Azure Repos 和 Data Science Virtual Machine （DSVM）来设置 TDSP 环境，因为这是如何实现 Microsoft 的 TDSP。 如果你的团队使用其他代码宿主或开发平台，则单个参与者任务是相同的，但完成这些任务的方法可能不同。

## <a name="prerequisites"></a>필수 조건

本教程假定你的[组管理员](group-manager-tasks.md)、[团队主管](team-lead-tasks.md)和[项目主管](project-lead-tasks.md)已设置以下资源和权限：

- 用于数据科学单元的 Azure DevOps**组织**
- 由项目主管设置的**项目存储库**共享项目模板和资产
- 组管理员和团队主管设置的**GroupUtilities**和**TeamUtilities**存储库（如果适用）
- 为你的团队或项目设置的共享资产的 Azure**文件存储**（如果适用）
- 用于克隆并将其推送回你的项目存储库的**权限** 

若要克隆存储库以及修改本地计算机或 DSVM 上的内容，或者将 Azure 文件存储装载到 DSVM，需考虑此清单：

- Azure 구독
- 已在计算机上安装 Git。 如果你使用的是 DSVM，则将预安装 Git。 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.
- 如果要使用 DSVM，请在 Azure 中创建和配置 Windows 或 Linux DSVM。 有关详细信息和说明，请参阅[Data Science Virtual Machine 文档](/azure/machine-learning/data-science-virtual-machine/)。
- 对于安装在计算机上的 Windows DSVM、 [Git 凭据管理器（GCM）](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 。 在*README.md*文件中，向下滚动到 "**下载并安装**" 部分，然后选择**最新的安装程序**。 从 "安装程序" 页下载 *.exe*安装程序并运行它。 
- 对于 Linux DSVM，在 DSVM 上设置 SSH 公钥，并将其添加到 Azure DevOps 中。 有关详细信息和说明，请参阅[平台和工具附录](platforms-and-tools.md#appendix)中的**创建 SSH 公钥**部分。 
- 需要装载到 DSVM 的 Azure 文件存储的 Azure 文件存储信息。 

## <a name="clone-repositories"></a>克隆存储库

若要在本地使用存储库并将更改推送到共享团队和项目存储库，请先将存储库复制或*克隆*到本地计算机。 

1. 在 Azure DevOps 中，请参阅*https：\//\<服务器名称 >/\<组织名称 >/\<团队名称 >* ，如**https：\//dev.azure.com/DataScienceUnit/MyTeam**。
   
1. 在左侧导航栏中选择 "**存储库**"，并在页面顶部选择要克隆的存储库。
   
1. 在 "存储库" 页上，选择右上角的 "**克隆**"。
   
1. 在 "**克隆存储库**" 对话框中，选择 " **HTTPS**用于 HTTP 连接 **" 或 "ssh"** 作为 Ssh 连接，然后将 "**命令行**" 下的克隆 URL 复制到剪贴板。
   
   ![리포지토리 복제](./media/project-ic-tasks/clone.png)
   
1. 在本地计算机或 DSVM 上创建以下目录：
   
   - 对于 Windows： **C:\GitRepos**
   - 对于 Linux： **$home/gitrepos**
   
1. 更改为你创建的目录。
   
1. 在 Git Bash 中，为要克隆的每个存储库运行命令 `git clone <clone URL>`。 
   
   例如，以下命令将**TeamUtilities**存储库克隆到本地计算机上的*MyTeam*目录。 
   
   **HTTPS 连接：**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH 连接：**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. 确认你可以在本地项目目录中看到克隆的存储库的文件夹。
   
   ![三个本地存储库文件夹](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>将 Azure 文件存储装载到 DSVM

如果你的团队或项目在 Azure 文件存储中有共享的资产，请将文件存储装载到你的本地计算机或 DSVM。 按照在[本地计算机上装载 Azure 文件存储或 DSVM](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm)中的说明进行操作。

## <a name="next-steps"></a>다음 단계

下面是团队数据科学过程定义的其他角色和任务的详细说明的链接：

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)

