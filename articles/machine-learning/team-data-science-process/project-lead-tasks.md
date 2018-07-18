---
title: Team Data Science Process 项目主管任务 - Azure | Microsoft Docs
description: 数据科学团队项目的项目主管任务概述。
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
ms.openlocfilehash: 58c5826240b7c49ba29c0d8e86a2896e3ce2f7f7
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34838392"
---
# <a name="project-lead-tasks"></a>项目主管任务

本教程概述项目主管预期要在其项目团队中完成的任务。 目标在于创建致力于标准化 [Team Data Science Process](overview.md) (TDSP) 的协作型团队环境。 TDSP 是 Microsoft 开发的一个框架，它提供结构化的活动序列，可高效地执行基于云的预测分析解决方案。 有关致力于标准化此流程的数据科学团队要处理的人员角色及其相关任务的概述，请参阅 [Team Data Science Process 角色和任务](roles-tasks.md)。

**项目主管**管理特定数据科学项目各个数据科学家的日常活动。 下图描绘了项目主管设置此环境时所要完成的任务的工作流：

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

本主题目前包括项目主管工作流的任务 1、2 和 6。

>[AZURE.NOTE] 以下说明概述了使用 Visual Studio Team Services (VSTS) 设置项目 TDSP 团队环境所要执行的步骤。 本文指定了如何使用 VSTS 完成这些任务，因为这是我们在 Microsoft 中实现 TDSP 的方法。 如果组使用其他代码托管平台，团队主管需要完成的任务通常不会发生变化。 但是完成这些任务的方法会有所不同。


## <a name="repositories-and-directories"></a>存储库和目录

本教程使用存储库和目录的缩写名称。 使用这些名称，更易于跟踪存储库和目录之间的操作。 以下部分使用了此表示法（R 表示 Git 存储库，D 表示 DSVM 上的本地目录）：

- **R3**：团队主管在 Git 上设置的团队 **ProjectTemplate** 存储库。
- **R5**：在 Git 上为项目设置的项目存储库。
- **D3**：从 R3 克隆的本地目录。
- **D5**：从 R5 克隆的本地目录。


## <a name="0-prerequisites"></a>0.先决条件

通过完成根据[数据科学团队的组管理员任务](group-manager-tasks.md)中所述分配给组管理员的任务，以及根据[数据科学团队的团队主管任务](team-lead-tasks.md)中所述分配给团队主管的任务，来满足先决条件。 

在开始执行团队主管任务之前，需要满足以下汇总要求： 

- 组管理员已设置**组 VSTS 服务器**（或其他某个代码托管平台上的组帐户）。
- 团队主管已在计划使用的代码托管平台上你的组帐户下设置了 **TeamProjectTemplate 存储库** (R3)。
- 团队主管**已授权**你在团队的组帐户中创建存储库。
- 计算机上必须安装 Git。 如果使用的是数据科学虚拟机 (DSVM)，则已预安装 Git，可以继续操作。 否则，请参阅[平台和工具附录](platforms-and-tools.md#appendix)。  
- 如果使用的是 **Windows DSVM**，则需要在计算机上安装 [Git 凭据管理器 (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)。 在 README.md 文件中，向下滚动到“下载并安装”部分，然后单击“最新安装程序”。 随后会转到最新安装程序页。 从此处下载 .exe 安装程序并运行它。 
- 如果使用的是 **Linux DSVM**，则在 DSVM 上创建一个 SSH 公钥，然后将它添加到组 VSTS 服务器。 有关 SSH 的详细信息，请参阅[平台和工具附录](platforms-and-tools.md#appendix)中的**创建 SSH 公钥**部分。 


## <a name="1-create-a-project-repository-r5"></a>1.创建项目存储库 (R5)

- 通过 *https://\<VSTS 服务器名称\>.visualstudio.com* 登录到组 VSTS 服务器。 
- 在“最近的项目和团队”下面单击“浏览”。 此时会弹出一个窗口，其中列出了 VSTS 服务器上的所有团队项目。 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- 单击要在其中创建项目存储库的团队项目名称。 在本示例中，请单击“MyTeam”。 
- 然后，单击“导航”定向到团队项目 **MyTeam** 的主页：

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- 单击“围绕代码开展协作”，定向到团队项目的 git 主页。  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- 单击左上角的向下箭头，选择“+ 新建存储库”。 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- 在“创建新存储库”窗口中，输入项目 git 存储库的名称。 确保选择“Git”作为存储库的类型。 本示例使用名称 *DSProject1*。 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- 若要创建 ***DSProject1*** 项目 git 存储库，请单击“创建”。


## <a name="2-seed-the-dsproject1-project-repository"></a>2.设定 DSProject1 项目存储库的种子

此处的任务是从团队项目模板存储库 (R3) 设定 **DSProject1** 项目存储库 (R5) 的种子。 种子设定过程使用本地 DSVM 上的目录 D3 和 D5 作为中间过渡站点。 概括而言，种子设定路径为：R3 -> D3 -> D5 -> R5。

如果需要根据某些具体的项目需求自定义 **DSProject1** 项目存储库，可在以下过程的倒数第二个步骤中执行此操作。 下面是用于设定 **DSProject1** 项目存储库内容种子的步骤摘要。 各个步骤对应于种子设定过程中的小节：

- 将团队项目模板存储库克隆到本地目录：团队 R3 - 克隆到 -> 本地 D3。
- 将 DSProject1 存储库克隆到本地目录：团队 R5 - 克隆到 -> 本地 D5。
- 将克隆的团队项目模板内容复制到 DSProject1 存储库的本地复本：D3 - 内容复制到 -> D5。
- （可选）自定义本地 D5。
- 将本地 DSProject1 内容推送到团队存储库：D5 - 内容添加到 -> 团队 R5。


### <a name="clone-your-team-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>将团队项目模板存储库 (R3) 克隆到本地计算机上的某个目录 (D3)。

在本地计算机上创建一个目录：

- *C:\GitRepos\MyTeamCommon*（对于 Windows） 
- *$home/GitRepos/MyTeamCommon*（对于 Linux）

切换到该目录。 然后运行以下命令，将团队项目模板存储库克隆到本地计算机。 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
如果使用 VSTS 作为代码托管平台，则团队项目模板存储库的 HTTPS URL 通常为：

 ***https://\<VSTS 服务器名称\>.visualstudio.com/\<团队项目名称\>/_git/\<团队项目模板存储库名称\>***。 

本示例使用：

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***： 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

如果使用 VSTS 作为代码托管平台，团队项目模板存储库的 SSH URL 通常为：

***ssh://\<VSTS 服务器名称\>@\<VSTS 服务器名称\>.visualstudio.com:22/\<团队项目名称>/_git/\<团队项目模板存储库名称\>。*** 

本示例使用：

***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***： 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>将 DSProject1 存储库 (R5) 克隆到本地计算机上的某个目录 (D5)

将目录切换到 **GitRepos**，运行以下命令将项目存储库克隆到本地计算机。 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

如果使用 VSTS 作为代码托管平台，项目存储库的 HTTPS URL 通常为 ***https://\<VSTS 服务器名称\>.visualstudio.com/\<团队项目名称>/_git/<项目存储库名称\>***。 本示例使用 ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***。

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

如果使用 VSTS 作为代码托管平台，项目存储库的 SSH URL 通常为 _ssh://<VSTS 服务器名称\>@<VSTS 服务器名称\>.visualstudio.com:22/<Your Team Project Name>/\_git/<项目存储库名称\>。 本示例使用 ***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***。

### <a name="copy-contents-of-d3-to-d5"></a>将 D3 的内容复制到 D5 

现在，需要在本地计算机中将 _D3_ 的内容复制到 _D5_，.git 目录中的 git 元数据除外。 以下脚本会完成该作业。 请确保键入目录的正确完整路径。 源文件夹是团队 (_D3_) 的文件夹；目标文件夹是项目 (_D5_) 的文件夹。    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

现在，在 _DSProject1_ 文件夹中可以看到，所有文件（.git 除外）都是从 _MyTeamProjectTemplate_ 复制的。

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

现在，在 _DSProject1_ 文件夹中可以看到，所有文件（.git 中的元数据除外）都是从 _MyTeamProjectTemplate_ 复制的。

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>根据需要自定义 D5（可选）

如果项目需要某些特定的目录或文档，则现在可以自定义 D5 的内容，但从团队项目模板获取的（已在上一步骤中复制到 D5 目录）的内容除外。 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-vsts-server"></a>将 D5 中 DSProject1 的内容添加到组 VSTS 服务器上的 R5

现在，需要将 **_DSProject1_** 中的内容推送到组 VSTS 服务器上团队项目中的 _R5_ 存储库。 


- 切换到目录 **D5**。 
- 使用以下 git 命令将 **D5** 中的内容添加到 **R5**。 在 Windows 和 Linux 系统上，这些命令是相同的。 
    
    git status git add .
    git commit -m"push from win DSVM" git push
    
- 提交更改并推送。 

>[AZURE.NOTE] 如果这是首次提交到 Git 存储库，则需要在运行 `git commit` 命令之前，配置全局参数 *user.name* 和 *user.email*。 运行以下两个命令：
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> 如果要提交到多个 Git 存储库，请每次提交时都使用相同的姓名和电子邮件地址。 事实证明，使用相同的姓名和电子邮件地址在以后构建 PowerBI 仪表板来跟踪多个存储库上的 Git 活动时提供了不少方便。

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6.创建 Azure 文件存储并将其装载为项目资源（可选）

如果想要创建 Azure 文件存储来共享数据（例如项目原始数据或针对项目生成的特征），以便所有项目成员都可以从多个 DSVM 访问相同的数据集，请遵照[数据科学团队的团队主管任务](team-lead-tasks.md)第 3 和第 4 部分中的说明。 


## <a name="next-steps"></a>后续步骤

下面是 Team Data Science Process 定义的角色和任务的详细说明链接：

- [数据科学团队的组管理员任务](group-manager-tasks.md)
- [数据科学团队的团队主管任务](team-lead-tasks.md)
- [数据科学团队的项目主管任务](project-lead-tasks.md)
- [数据科学团队的项目单独参与者](project-ic-tasks.md)