---
title: Team Data Science Process 中单个参与者的任务
description: 数据科学团队项目的单个参与者的任务详细演练。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "76721245"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Team Data Science Process 中单个参与者的任务

本主题概述在 [Team Data Science Process](overview.md) (TDSP) 中设置项目时，单个参与者完成的任务  。 目标是在标准化 TDSP 的协作型团队环境中工作。 TDSP 设计用于帮助改进协作和团队学习。 有关致力于标准化 TDSP 的数据科学团队要处理的人员角色及其相关任务的概述，请参阅[团队数据科学流程角色和任务](roles-tasks.md)。

下图显示了各个项目参与者（数据科学家）在设置其团队环境时所要完成的任务。 有关如何在 TDSP 下执行数据科学项目的说明，请参阅[执行数据科学项目](project-execution.md)。 

![单个参与者的任务](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- ProjectRepository 是项目团队维护的存储库，用于共享项目模板和资产  。
- TeamUtilities 是团队专门为自己维护的实用程序存储库  。 
- GroupUtilities 是组维护的存储库，用于在整个组中共享有用的实用程序  。 

> [!NOTE] 
> 本文使用 Azure Repos 和 Data Science Virtual Machine (DSVM) 设置 TDSP 环境，因为 Microsoft 使用此方法实现 TDSP。 如果团队使用其他代码托管或开发平台，则单个参与者的任务是相同的，但完成这些任务的方法可能不同。

## <a name="prerequisites"></a>先决条件

本教程假设[组管理员](group-manager-tasks.md)、[团队主管](team-lead-tasks.md)和[项目主管](project-lead-tasks.md)已设置以下资源和权限：

- Azure DevOps 组织，用于数据科学单元 
- 项目存储库，由项目主管设置，用于共享项目模板和资产 
- GroupUtilities 和 TeamUtilities 存储库，由组管理员和团队主管设置（如果适用）  
- Azure 文件存储，设置用于共享团队或项目的资产（如果适用） 
- 权限，用于从项目存储库中克隆并推送回项目存储库  

若要克隆存储库以及修改本地计算机或 DSVM 上的内容，或者将 Azure 文件存储装载到 DSVM，需考虑此清单：

- Azure 订阅。
- 计算机上安装的 Git。 如果要使用 DSVM，则需预安装 Git。 否则，请参阅[平台和工具附录](platforms-and-tools.md#appendix)。
- 如果要使用 DSVM，需要在 Azure 中创建和配置 Windows 或 Linux DSVM。 有关详细信息和说明，请参阅 [Data Science Virtual Machine 文档](/azure/machine-learning/data-science-virtual-machine/)。
- 对于 Windows DSVM，需要在计算机上安装 [Git 凭据管理器 (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)。 在 README.md 文件中，向下滚动到“下载并安装”部分，然后选择“最新安装程序”    。 从安装程序页下载 .exe 安装程序并运行它  。 
- 对于 Linux DSVM，需要在 DSVM 上设置 SSH 公钥，并将其添加到 Azure DevOps 中。 有关详细信息和说明，请参阅[平台和工具附录](platforms-and-tools.md#appendix)中的“创建 SSH 公钥”  部分。 
- 针对需要装载到 DSVM 的任何 Azure 文件存储的 Azure 文件存储信息。 

## <a name="clone-repositories"></a>克隆存储库

要在本地使用存储库并将所做的更改推送到共享的团队和项目存储库，请先将存储库复制或克隆到本地计算机  。 

1. 在 Azure DevOps 中，转到团队项目的“摘要”页，地址为 https:\//\<server name>/\<organization name>/\<team name>，例如 https:\//dev.azure.com/DataScienceUnit/MyTeam   。
   
1. 在左侧导航栏中，选择“存储库”，然后在页面顶部选择要克隆的存储库  。
   
1. 在“存储库”页上，选择右上方的“克隆”  。
   
1. 在“克隆存储库”对话框中，为 HTTP 连接选择“HTTPS”，或为 SSH 连接选择“SSH”，并将命令行下的克隆 URL 复制到剪贴板     。
   
   ![克隆存储库](./media/project-ic-tasks/clone.png)
   
1. 在本地计算机或 DSVM 上，创建以下目录：
   
   - 对于 Windows：C:\GitRepos 
   - 对于 Linux，则为 $home/GitRepos 
   
1. 切换到创建的目录。
   
1. 在 Git Bash 中，针对要克隆的各个存储库运行命令 `git clone <clone URL>`。 
   
   例如，以下命令可将 TeamUtilities 存储库克隆到本地计算机上的 MyTeam 目录   。 
   
   **HTTPS 连接：**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH 连接：**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. 确认可在本地项目目录中看到克隆的存储库的文件夹。
   
   ![三个本地存储库文件夹](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>将 Azure 文件存储装载到 DSVM

如果团队或项目在 Azure 文件存储中具有共享的资产，请将文件存储装载到本地计算机或 DSVM。 请按照[在本地计算机或 DSVM 上装载 Azure 文件存储](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm)中的说明进行操作。

## <a name="next-steps"></a>后续步骤

下面是 Team Data Science Process 定义的其他角色和任务的详细说明链接：

- [数据科学团队的组管理员任务](group-manager-tasks.md)
- [数据科学团队的团队主管任务](team-lead-tasks.md)
- [数据科学团队的项目主管任务](project-lead-tasks.md)

