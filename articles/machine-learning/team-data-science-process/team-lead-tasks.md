---
title: Team Data Science Process 团队主管任务 - Azure | Microsoft Docs
description: 数据科学团队项目的团队主管任务概述。
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
ms.openlocfilehash: 9d2043808cbd61d5e2a69cbe0f2a5a611e3afa31
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "34839752"
---
# <a name="team-lead-tasks"></a>团队主管任务

本主题概述了数据科学团队的团队主管应该完成的任务。 目标在于创建致力于标准化 [Team Data Science Process](overview.md) (TDSP) 的协作型团队环境。 TDSP 是一种敏捷的迭代式数据科学方法，可有效交付预测分析解决方案和智能应用程序。 它设计用于帮助改进协作和团队学习。 该过程对 Microsoft 和行业实行的最佳做法和结构进行升华，是成功实现数据科学计划以帮助公司充分发挥其分析程序的优势所必需的。 有关致力于标准化此流程的数据科学团队要处理的人员角色及其相关任务的概述，请参阅[团队数据科学流程角色和任务](roles-tasks.md)。

**团队主管**管理企业的数据科学部门的团队。 团队由多个数据科学家组成。 如果数据科学部门的数据科学家较少，**组管理员**和**团队主管**可以是同一人，他们还可以将其任务委托给其代理。 但是，任务本身不会改变。 下图描绘了团队主管设置此环境时所要完成的任务的工作流：

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] 如果使用 Visual Studio Team Services (VSTS) 作为代码托管平台并且希望为你自己的团队创建一个单独的团队项目，则图中的块 1 和块 2 中的任务是必需的。 完成这些任务后，可以在此团队项目下创建你的团队的所有存储库。 

在组管理员使下面部分中指定的几个先决条件任务得以满足后，本教程中还有五个重要任务需要完成（有些是可选的）。 这些任务对应于本主题中主要的带编号部分：

1. 在组的 VSTS 服务器上创建一个**团队项目**并在该项目中创建两个团队存储库：
    - **ProjectTemplate 存储库** 
    - **TeamUtilities 存储库**
2. 从组管理员已设置的 **GroupProjectTemplate** 存储库中将团队 **ProjectTemplate** 存储库设为种子。 
3. 创建团队数据和分析资源：
    - 将团队特定的实用程序添加到 **TeamUtilities** 存储库。 
    - （可选）创建 **Azure 文件存储**，用来存储可能对整个团队有用的数据资产。 
4. （可选）将 Azure 文件存储装载到团队主管的 **数据科学虚拟机** (DSVM) 并在其上添加数据资产。
5. 设置**安全控制**，方法是添加团队成员并配置其权限。

>[AZURE.NOTE] 我们在以下说明中概述了使用 VSTS 设置 TDSP 团队环境所需的步骤。 本文指定了如何使用 VSTS 完成这些任务，因为这是我们在 Microsoft 中实现 TDSP 的方法。 如果你的组使用了其他代码托管平台，团队主管需要完成的任务通常不会发生变化。 但是完成这些任务的方法会有所不同。

## <a name="repositories-and-directories"></a>存储库和目录

本主题使用了存储库和目录的缩写名称。 使用这些名称，更易于跟踪存储库和目录之间的操作。 以下部分使用了此表示法（**R** 表示 Git 存储库，**D** 表示 DSVM 上的本地目录）：

- **R1**：组管理员在 Git 上在 VSTS 组服务器上设置的 **GroupProjectTemplate** 存储库。
- **R3**：你在 Git 上设置的团队 **ProjectTemplate** 存储库。
- **R4**：你在 Git 上设置的 **TeamUtilities** 存储库。
- **D1**：从 R1 克隆的并复制到 D3 的本地目录。
- **D3**：从 R3 克隆的、进行了自定义并复制回 R3 的本地目录。
- **D4**：从 R4 克隆的、进行了自定义并复制回 R4 的本地目录。

在本教程中为存储库和目录指定的名称是基于以下假设提供的：你的目标是在一个较大的数据科学组中为你自己的团队建立一个单独的团队项目。 但是，作为团队主管，你还有其他选择：

- 整个组可以选择创建单个团队项目。 来自所有数据科学团队的所有项目都将位于此单个团队项目下。 为实现此目的，你可以指定一个 git 管理员来按照这些说明创建单个团队项目。 例如，此方案对于以下数据科学组可能有效：
    -  没有多个数据科学团队的小型数据科学组 
    -  具有多个数据科学团队的较大型数据科学组，不过，该科学组希望通过组级冲刺规划等活动来优化团队间协作 
- 团队可以选择将团队特定的项目模板或团队特定的实用程序放置到整个组的单个团队项目下。 在这种情况下，团队主管应当在同一团队项目下创建团队项目模板存储库和/或团队实用程序存储库。 将这些存储库命名为 *<TeamName\>ProjectTemplate* 和 *<TeamName\>Utilities*，例如 *TeamJohnProjectTemplate* 和 *TeamJohnUtilities*。 

在任何情况下，团队主管都需要让其团队成员知道在设置和克隆项目与实用程序存储库时要采用哪些模板和实用程序存储库。 项目主管应当按照[数据科学团队的项目主管任务](project-lead-tasks.md)所述在单独的团队项目下或者在单个团队项目下创建项目存储库。 


## <a name="0-prerequisites"></a>0.先决条件

通过完成[数据科学团队组管理员的任务](group-manager-tasks.md)中介绍的分配给组管理员的任务来满足先决条件。 在此处汇总一下，在开始执行团队主管任务之前，需要满足以下要求： 

- 组管理员已设置**组 VSTS 服务器**（或其他某个代码托管平台上的组帐户）。
- 组管理员已在计划使用的代码托管平台上你的组帐户下设置了 **GroupProjectTemplate 存储库** (R1)。
- 已在你的组帐户中**授权**你为团队创建存储库。
- 计算机上必须安装有 Git。 如果使用的是数据科学虚拟机 (DSVM)，则已预安装 Git，可以继续操作。 否则，请参阅[平台和工具附录](platforms-and-tools.md#appendix)。  
- 如果使用的是 **Windows DSVM**，则需要在计算机上安装 [Git 凭据管理器 (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)。 在 README.md 文件中，向下滚动到“下载并安装”部分，然后单击“最新安装程序”。 随后会转到最新安装程序页。 从此处下载 .exe 安装程序并运行它。 
- 如果使用的是 **Linux DSVM**，则在 DSVM 上创建一个 SSH 公钥，然后将它添加到组 VSTS 服务器。 有关 SSH 的详细信息，请参阅[平台和工具附录](platforms-and-tools.md#appendix)中的**创建 SSH 公钥**部分。 
    
## <a name="1-create-a-team-project-and-repositories"></a>1.创建团队项目和存储库

如果使用 VSTS 作为代码托管平台来进行版本控制和协作，请完成此步骤。 在本部分中，还需要在组的 VSTS 服务器中创建三个项目：

- VSTS 中的 **MyTeam** 项目
- Git 上的 **MyProjectTemplate** 存储库 (**R3**)
- Git 上的 **MyTeamUtilities** 存储库 (**R4**)

### <a name="create-the-myteam-project"></a>创建 MyTeam 项目

- 转到组的 VSTS 服务器主页，URL 为 `https://<VSTS Server Name\>.visualstudio.com`。 
- 单击“新建”创建团队项目。 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- “创建团队项目”窗口会要求 输入项目名称（在此示例中为 **MyTeam**）。 确保选择“敏捷”作为“过程模板”，选择“Git”作为“版本控制”。 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- 单击“创建项目”。 团队项目 **MyTeam** 将在 1 分钟内完成创建。 

- 在创建团队项目 **MyTeam** 后，单击“导航到项目”按钮，以定向到团队项目的主页。 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- 如果看到“祝贺你！” 弹出窗口，请单击“添加代码” （红色框中的按钮）。 否则，请单击“代码”（在黄色框中）。 这会定向到团队项目的 Git 存储库页面。 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>在 Git 上创建 MyProjectTemplate 存储库 (R3)

- 在团队项目的 Git 存储库页面上，单击存储库名称“MyTeam”旁边的向下箭头，然后选择“管理存储库...”。

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- 在团队项目的控制面板的“版本控制”选项卡上，单击“MyTeam”，然后选择“重命名存储库...”。 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- 在“重命名 MyTeam 存储库”窗口中，为存储库输入一个新名称。 在本例中为 *MyTeamProjectTemplate*。 可以选择使用诸如 *<你的团队名称\>ProjectTemplate* 的名称。 单击“重命名”以继续操作。

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>在 Git 上创建 MyTeamUtilities 存储库 (R4)

- 若要在团队项目下创建新存储库 *<你的团队名称\>Utilities*，请在团队项目的控制面板的“版本控制”选项卡上单击“新建存储库...”。  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- 在弹出的“创建新的存储库”窗口中，为此存储库提供一个名称。 在本例中，我们将其命名为“MyTeamUtilities”，在我们的表示法中为“R4”。 可以选择使用诸如 *<你的团队名称\>Utilities* 的名称。 对于“类型”，请确保选择“Git”。 然后，单击“创建”以继续操作。

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- 确认看到在团队项目“MyTeam”下创建了两个新的 Git 存储库。 在本示例中： 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4)。

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-team-projecttemplate-and-teamutilities-repositories"></a>2.将团队 ProjectTemplate 和 TeamUtilities 存储库设为种子

种子设定过程使用本地 DSVM 上的目录作为中间暂存站点。 如果需要自定义“ProjectTemplate”和“TeamUtilities”存储库以满足某些特定的团队需求，请在以下过程的倒数第二个步骤中进行自定义。 下面摘要列出了用来将数据科学团队的 **MyTeamProjectTemplate** 和 **MyTeamUtilities** 存储库内容设为种子的步骤。 各个步骤对应于种子设定过程中的各个小节：

- 将组存储库克隆到本地目录中：团队 R1 - 克隆到 -> 本地 D1
- 将团队存储库克隆到本地目录中：团队 R3 和 R4 - 克隆到 -> 本地 D3 和 D4
- 将组项目模板内容复制到本地团队文件夹：D1 - 内容复制到 -> D3
- （可选）自定义本地 D3 和 D4
- 将本地目录内容推送到团队存储库：D3 和 D4 - 内容添加到 -> 团队 R3 和 R4


### <a name="initialize-the-team-repositories"></a>初始化团队存储库

在此步骤中，将基于组项目模板存储库初始化团队项目模板存储库：

- 基于 **GroupProjectTemplate** (**R1**) 存储库初始化 **MyTeamProjectTemplate** 存储库 (**R3**)


### <a name="clone-group-repositories-into-local-directories"></a>将组存储库克隆到本地目录中

开始此过程：

- 在本地计算机上创建目录：
    - 对于 **Windows**：**C:\GitRepos\GroupCommon** 和 **C:\GitRepos\MyTeam**
    - 对于 **Linux**：在主目录中创建 **GitRepos\GroupCommon** 和 **GitRepos\MyTeam** 
- 更改到 **GitRepos\GroupCommon** 目录。
- 根据情况，在本地计算机的操作系统上运行以下命令。

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

这些命令将组 VSTS 服务器上的 **GroupProjectTemplate** (R1) 存储库克隆到本机计算机上的 **GitRepos\GroupCommon** 中的本地目录。 在克隆后，会在 **GitRepos\GroupCommon** 目录中创建 **GroupProjectTemplate** (D1) 目录。 此处，我们假定组管理员已创建了一个团队项目 **GroupCommon**，并且 **GroupProjectTemplate** 存储库位于此团队项目下。 


### <a name="clone-your-team-repositories-into-local-directories"></a>将团队存储库克隆到本地目录中

这些命令将组 VSTS 服务器上的团队项目 **MyTeam** 下的 **MyTeamProjectTemplate** (R3) 和 **MyTeamUtilities** (R4) 存储库克隆到本地计算机上 **GitRepos\MyTeam** 中的 **MyTeamProjectTemplate** (D3) 和 **MyTeamUtilities** (D4) 目录中。 

- 更改到 **GitRepos\MyTeam** 目录
- 根据情况，在本地计算机的操作系统上运行以下命令。 

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

在克隆后，会在 **GitRepos\MyTeam** 目录中创建两个目录：**MyTeamProjectTemplate** (D3) 和 **MyTeamUtilities** (D4) 。 此处，我们假定已将团队项目模板和实用程序存储库命名为 **MyTeamProjectTemplate** 和 **MyTeamUtilities**。 

### <a name="copy-the-group-project-template-content-to-the-local-team-project-template-directory"></a>将组项目模板内容复制到本地团队项目模板目录

若要将本地 **GroupProjectTemplate** (D1) 文件夹的内容复制到本地 **MyTeamProjectTemplate** (D3)，请运行下列 shell 脚本之一： 

#### <a name="from-the-powershell-command-line-for-windows"></a>从适用于 Windows 的 PowerShell 命令行窗口       

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

#### <a name="from-the-linux-shell-for-the-linux-dsvm"></a>从适用于 **Linux DSVM** 的 Linux shell
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

这些脚本不包括 .git 目录的内容。 脚本会提示你提供源目录 D1 和目标目录 D3 的**完整路径**。
        

### <a name="customize-your-team-project-template-or-team-utilities-optional"></a>自定义团队项目模板或团队实用程序（可选）

如果需要，在设置过程的此阶段自定义 **MyTeamProjectTemplate** (D3) 和 **MyTeamUtilities** (D4)。 

- 如果希望自定义 D3 的内容以满足团队的特定需求，可以修改模板文档或更改目录结构。

- 如果团队开发了希望在整个团队中共享的一些实用程序，请将这些实用程序复制到目录 D4。 


### <a name="push-local-directory-content-to-team-repositories"></a>将本地目录内容推送到团队存储库

若要将（可自定义的）本地目录 D3 和 D4 中的内容添加到团队存储库 R3 和 R4，请从 Windows PowerShell 控制台或从 Linux shell 运行以下 git bash 命令。 请从 **GitRepos\MyTeam\MyTeamProjectTemplate** 目录运行命令。

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

运行此脚本时，几乎会立即对组的 VSTS 服务器的 MyTeamProjectTemplate 存储库中的文件进行同步。

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

现在，从 **GitRepos\MyTeam\MyTeamUtilities** 目录运行同一组四个 git 命令。 

> [AZURE.NOTE]如果这是首次提交到 Git 存储库，则需要在运行 `git commit` 命令之前，配置全局参数 *user.name* 和 *user.email*。 运行以下两个命令：
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> 如果要提交到多个 Git 存储库，则在提交到每个存储库时请使用相同的姓名和电子邮件地址。 事实证明，使用相同的姓名和电子邮件地址在以后构建 PowerBI 仪表板来跟踪多个存储库上的 Git 活动时提供了不少方便。

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3.创建团队数据和分析资源（可选）

与整个团队共享数据和分析资源可提供性能和成本优势：团队成员可以在共享资源上执行其项目，从而节省预算并且更高效地进行协作。 在本部分中，我们提供了有关如何创建 Azure 文件存储的说明。 在下一部分中，我们提供了有关如何将 Azure 文件存储装载到本地计算机的说明。 有关共享其他资源（例如 Azure 数据科学虚拟机、Azure HDInsight Spark 群集）的更多信息，请参阅[平台和工具](platforms-and-tools.md)。 本主题从数据科学的角度针对如何选择适合需求的资源提供了指导，并提供了指向产品页面和我们已发布的其他相关和有用教程的链接。

>[AZURE.NOTE] 为避免跨数据中心进行数据传输（这可能很慢而且成本很高），请确保资源组、存储帐户和 Azure VM（例如 DSVM）位于同一 Azure 数据中心内。 

运行以下脚本来为团队创建 Azure 文件存储。 可以使用团队的 Azure 文件存储来存储对整个团队有用的数据资产。 脚本将提示你输入 Azure 帐户和订阅信息，因此请准备好这些凭据以便输入。 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>从 Windows 中使用 PowerShell 创建 Azure 文件存储

从 PowerShell 命令行窗口运行以下脚本：

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

在出现提示时，登录到 Microsoft Azure 帐户：

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

选择要使用的 Azure 订阅：

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

在所选订阅下选择要使用的存储帐户或者创建一个新的存储帐户：

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

输入要创建的 Azure 文件存储的名称。 仅接受小写字符、数字和 -：

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

为方便在创建此存储后装载并共享此存储，请将 Azure 文件存储信息保存到文本文件中并记下其位置路径。 具体而言，在下一部分中，需要使用此文件将 Azure 文件存储装载到 Azure 虚拟机。 

最好将此文本文件签入到团队 ProjectTemplate 存储库中。 建议将其放置在 **Docs\DataDictionaries** 目录中。 因此，团队中的所有项目都可以访问此数据资产。 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>使用 Linux 脚本创建 Azure 文件存储

从 Linux shell 运行此脚本：

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

按照以下屏幕上的说明登录到你的 Microsoft Azure 帐户：

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

选择要使用的 Azure 订阅：

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

在所选订阅下选择要使用的存储帐户或者创建一个新的存储帐户：

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

输入要创建的 Azure 文件存储的名称，仅接受小写字符、数字和 -：

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

为方便在创建此存储后访问此存储，请将 Azure 文件存储信息保存到文本文件中并记下其位置路径。 具体而言，在下一部分中，需要使用此文件将 Azure 文件存储装载到 Azure 虚拟机。

最好将此文本文件签入到团队 ProjectTemplate 存储库中。 建议将其放置在 **Docs\DataDictionaries** 目录中。 因此，团队中的所有项目都可以访问此数据资产。 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4.装载 Azure 文件存储（可选）

成功创建 Azure 文件存储后，可以使用下列 PowerShell 或 Linux 脚本之一将其装载到本地计算机。

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>从 Windows 中使用 PowerShell 装载 Azure 文件存储

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
如果尚未登录，会要求你首先登录。 

在询问你是否有 Azure 文件存储信息文件时，单击 **Enter** 或 **y** 以继续操作，然后输入在上一步骤中创建的文件的 ***完整路径和名称**。 将直接从该文件中读取用于装载 Azure 文件存储的信息，你可以转到下一步骤。

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] 如果你没有包含 Azure 文件存储信息的文件，本部分的末尾提供了从键盘输入该信息的步骤。

然后，会要求你输入要添加到虚拟机的驱动器的名称。 屏幕上输出了现有驱动器名称的列表。 应当提供列表中尚不存在的一个驱动器名称。

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

确认新的 F 驱动器已成功装载到计算机。

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**如何手动输入 Azure 文件存储信息：** 如果没有包含 Azure 文件存储信息的文本文件，可以按照以下屏幕上的说明键入必需的订阅、存储帐户和 Azure 文件存储信息：

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

键入 Azure 订阅名称，选择在其中创建 Azure 文件存储的存储帐户，然后键入 Azure 文件存储名称：

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>使用 Linux 脚本装载 Azure 文件存储

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

如果尚未登录，会要求你首先登录。 

在询问你是否有 Azure 文件存储信息文件时，单击 **Enter** 或 **y** 以继续操作，然后输入在上一步骤中创建的文件的 ***完整路径和名称**。 将直接从该文件中读取用于装载 Azure 文件存储的信息，你可以转到下一步骤。

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

然后，会要求你输入要添加到虚拟机的驱动器的名称。 屏幕上输出了现有驱动器名称的列表。 应当提供列表中尚不存在的一个驱动器名称。

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

确认新的 F 驱动器已成功装载到计算机。

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**如何手动输入 Azure 文件存储信息：** 如果没有包含 Azure 文件存储信息的文本文件，可以按照以下屏幕上的说明键入必需的订阅、存储帐户和 Azure 文件存储信息：

- 输入 n。
- 选择在上一步骤中在其中创建了 Azure 文件存储的订阅名称的索引：

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- 选择你的订阅下的存储帐户并键入 Azure 文件存储名称：

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- 输入要添加到计算机的驱动器的名称，它不应当与任何现有的驱动器名称相同：

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5.设置安全控制策略 

在组 VSTS 服务器的主页上，单击右上角用户名旁边的**齿轮图标**，然后选择“安全性”选项卡。可以在此处向你的团队添加具有各种权限的成员。

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>后续步骤

下面是 Team Data Science Process 定义的角色和任务的详细说明链接：

- [数据科学团队的组管理员任务](group-manager-tasks.md)
- [数据科学团队的团队主管任务](team-lead-tasks.md)
- [数据科学团队的项目主管任务](project-lead-tasks.md)
- [数据科学团队的项目单独参与者](project-ic-tasks.md)