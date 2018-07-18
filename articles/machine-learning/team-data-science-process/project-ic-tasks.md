---
title: 单个参与者的 Team Data Science Process 任务 - Azure  | Microsoft Docs
description: 数据科学团队项目的单个参与者的任务概述。
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
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: e431d89e5d74f5712f6f109075201c95dc233bd3
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838545"
---
# <a name="individual-contributor-tasks"></a>单个参与者的任务

本主题概述了数据科学团队的单个参与者应该完成的任务。 目标在于创建致力于标准化 [Team Data Science Process](overview.md) (TDSP) 的协作型团队环境。 有关致力于标准化此过程的数据科学团队处理的人员角色及其相关任务的概述，请参阅 [Team Data Science Process 角色和任务](roles-tasks.md)。

下面描述的是项目单个参与者（数据科学家）在设置项目的 TDSP 环境方面要完成的任务： 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- GroupUtilities 是你的组维护的存储库，用于在整个组中共享有用的实用程序。 
- TeamUtilities 是你的团队专门为自己的团队维护的存储库。 

有关如何在 TDSP 下执行数据科学项目的说明，请参阅[执行数据科学项目](project-execution.md)。 

>[AZURE.NOTE] 下列说明概述了使用 Visual Studio Team Services (VSTS) 设置 TDSP 团队环境所需的步骤。 本文指定了如何使用 VSTS 完成这些任务，因为这是我们在 Microsoft 中实现 TDSP 的方法。 如果组使用其他代码托管平台，团队主管需要完成的任务通常不会发生变化。 但是完成这些任务的方法会有所不同。


## <a name="repositories-and-directories"></a>存储库和目录

本教程使用存储库和目录的缩写名称。 这些名称可便于掌握存储库和目录之间的操作。 以下部分使用了此表示法（R 表示 Git 存储库，D 表示 DSVM 上的本地目录）：

- R2：你的组管理员在 VSTS 组服务器上设置的 Git 上的 GroupUtilities 存储库。
- R4：你的团队主管设置的 Git 上的 TeamUtilities 存储库。
- R5：项目主管设置的 Git 项目存储库。
- D2：从 R2 克隆的本地目录。
- D4：从 R4 克隆的本地目录。
- D5：从 R5 克隆的本地目录。


## <a name="step-0-prerequisites"></a>准备步骤：先决条件

通过完成[数据科学团队组管理员的任务](group-manager-tasks.md)中介绍的分配给组管理员的任务来满足先决条件。 此处进行了归纳，即开始完成团队主管的任务前需要满足以下要求： 
- 你的组管理员已设置 GroupUtilities 存储库（如果有）。 
- 你的团队主管已设置 TeamUtilities 存储库（如果有）。
- 你的项目主管已设置项目存储库。 
- 你的项目主管（拥有从项目存储库克隆并推送回项目存储库的权限）已将你添加到项目存储库。

第二个先决条件（TeamUtilities 存储库）为可选，具体取决于你的团队是否拥有特定于团队的实用程序存储库。 如果其他三个先决条件中的任意一个尚未完成，请联系你的团队主管、项目主管或其代理人按照[数据科学团队的团队主管的任务](team-lead-tasks.md)说明或[数据科学团队的项目主管的任务](project-lead-tasks.md)说明进行设置。

- 你的计算机必须已安装 Git。 如果使用的是数据科学虚拟机 (DSVM)，则已预安装 Git，可以继续操作。 否则，请参阅[平台和工具附录](platforms-and-tools.md#appendix)。  
- 如果使用的是 **Windows DSVM**，则需要在计算机上安装 [Git 凭据管理器 (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)。 在 README.md 文件中，向下滚动到“下载并安装”部分，然后单击“最新安装程序”。 随后会转到最新安装程序页。 从此处下载 .exe 安装程序并运行它。 
- 如果使用的是 **Linux DSVM**，则在 DSVM 上创建一个 SSH 公钥，然后将它添加到组 VSTS 服务器。 有关 SSH 的详细信息，请参阅[平台和工具附录](platforms-and-tools.md#appendix)中的“创建 SSH 公钥”部分。 
- 如果你的团队和/或项目主管已创建了一些 Azure 文件存储，并且你需要将它们装载到你的 DSVM，则你应通过他们获得这些 Azure 文件存储信息。 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>步骤 1-3：将组、团队和项目存储库克隆到本地计算机

本部分提供了完成项目单个参与者的前三个任务的说明： 

- 将 GroupUtilities 存储库 R2 克隆到 D2
- 将 TeamUtilities 存储库 R4 克隆到 D4 
- 将项目存储库 R5 克隆到 D5。

在你的本地计算机上，创建目录 C:\GitRepos（适用于 Windows）或 $home/GitRepos（适用于 Linux），然后改为该目录。 

运行以下命令之一（根据你的 OS 类型），以将你的 GroupUtilities、TeamUtilities 和项目存储库克隆到你的本地计算机上的目录中： 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

确认在你的项目目录下看到了这三个文件夹。

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

确认在你的项目目录下看到了这三个文件夹。

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>步骤 4-5：将 Azure 文件存储装载到你的 DSVM（可选）

若要将 Azure 文件存储装载到你的 DSVM，请参阅[数据科学团队的团队主管的任务](team-lead-tasks.md)第 4 部分中的说明

## <a name="next-steps"></a>后续步骤

下面是 Team Data Science Process 定义的角色和任务的详细说明链接：

- [数据科学团队的组管理员任务](group-manager-tasks.md)
- [数据科学团队的团队主管任务](team-lead-tasks.md)
- [数据科学团队的项目主管任务](project-lead-tasks.md)
- [数据科学团队的项目单独参与者](project-ic-tasks.md)

