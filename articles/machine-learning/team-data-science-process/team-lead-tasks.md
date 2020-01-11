---
title: Team Data Science Process Team 中团队主管的任务
description: 团队数据科学过程团队的团队主管任务的详细演练
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d099d7c233c3f4b5e65bfdb7d4b875a0e4098499
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864275"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>团队数据科学过程团队的团队主管任务

本文介绍*团队主管*为其数据科学团队完成的任务。 团队主管的目标是建立在[团队数据科学过程](overview.md)（TDSP）上实现标准化的协作式团队环境。 TDSP 旨在帮助改进协作和团队学习。 

TDSP 是一种敏捷的迭代式数据科学方法，可高效交付预测分析解决方案和智能应用程序。 此过程从 Microsoft 和行业提取最佳实践和结构。  目标是成功实现数据科学计划，并充分实现分析程序的优势。 有关数据科学团队在 TDSP 上标准化的人员角色和相关任务的概述，请参阅[团队数据科学过程角色和任务](roles-tasks.md)。

团队主管在企业的数据科学单元中管理由多个数据科学家组成的团队。 根据数据科学单元的大小和结构，[组管理员](group-manager-tasks.md)和团队主管可能是同一个人，或者他们可以将其任务委托给代理项。 但是，任务本身不会改变。 

下图显示了团队主管为设置团队环境而完成的任务的工作流：

![团队主管任务工作流](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. 在 Azure DevOps 中的组组织中创建**团队项目**。 
  
1. 将默认团队存储库重命名为 " **TeamUtilities**"。
  
1. 在团队项目中创建新的**TeamTemplate**存储库。 
  
1. 将组的 " **GroupUtilities** " 和 " **GroupProjectTemplate** " 存储库的内容导入到**TeamUtilities**和**TeamTemplate**存储库中。 
  
1. 设置**安全控制**，方法是添加团队成员并配置其权限。
  
1. 如果需要，请创建团队数据和分析资源：
   - 将特定于团队的实用程序添加到**TeamUtilities**存储库。 
   - 创建**Azure 文件存储**来存储可对整个团队有用的数据资产。 
   - 将 Azure 文件存储装载到团队主管的**Data Science Virtual Machine** （DSVM）并向其添加数据资产。

以下教程详细介绍了这些步骤。

> [!NOTE] 
> 本文使用 Azure DevOps 和 DSVM 设置 TDSP 团队环境，因为这是在 Microsoft 实施 TDSP 的方法。 如果你的团队使用其他代码宿主或开发平台，团队主管任务是相同的，但完成这些任务的方法可能不同。

## <a name="prerequisites"></a>必备组件

本教程假定你的[组管理员](group-manager-tasks.md)已设置以下资源和权限：

- 数据单元的 Azure DevOps**组织**
- **GroupProjectTemplate**和**GroupUtilities**存储库，其中填充了 Microsoft TDSP 团队**ProjectTemplate**和**实用程序**存储库的内容
- 你的组织帐户的权限，你可以为你的团队创建项目和存储库

若要克隆存储库，并在本地计算机或 DSVM 上修改其内容，或设置 Azure 文件存储并将其装载到 DSVM，需要以下各项：

- Azure 订阅。
- 已在计算机上安装 Git。 如果你使用的是 DSVM，则将预安装 Git。 否则，请参阅[平台和工具附录](platforms-and-tools.md#appendix)。
- 如果要使用 DSVM，请在 Azure 中创建和配置 Windows 或 Linux DSVM。 有关详细信息和说明，请参阅[Data Science Virtual Machine 文档](/azure/machine-learning/data-science-virtual-machine/)。
- 对于安装在计算机上的 Windows DSVM、 [Git 凭据管理器（GCM）](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 。 在*README.md*文件中，向下滚动到 "**下载并安装**" 部分，然后选择**最新的安装程序**。 从 "安装程序" 页下载 *.exe*安装程序并运行它。 
- 对于 Linux DSVM，在 DSVM 上设置 SSH 公钥，并将其添加到 Azure DevOps 中。 有关详细信息和说明，请参阅[平台和工具附录](platforms-and-tools.md#appendix)中的**创建 SSH 公钥**部分。 

## <a name="create-a-team-project-and-repositories"></a>创建团队项目和存储库

在本部分中，将在组的 Azure DevOps 组织中创建以下资源：

- Azure DevOps 中的**MyTeam**项目
- **TeamTemplate**存储库
- **TeamUtilities**存储库

本教程中为存储库和目录指定的名称假设您要在更大的数据科学组织中为自己的团队建立单独的项目。 但是，整个组可以选择在由组管理器或组织管理员创建的单个项目下工作。 然后，所有数据科学团队在此单一项目下创建存储库。 此方案可能适用于：
- 不具有多个数据科学团队的小型数据科学组。 
- 具有多个数据科学团队的更大数据科学组，但又想要将团队间协作与活动（如组级冲刺规划）进行优化。 

如果团队选择将其团队特定的存储库置于单个组项目下，则团队主管应创建包含名称（如 *\<TeamName > 模板*和 *\<TeamName > 实用程序*）的存储库。 例如： *TeamATemplate*和*TeamAUtilities*。 

在任何情况下，团队主管都需要让其团队成员知道要设置和克隆哪些模板和实用程序存储库。 项目主管应按照[数据科学团队的项目主管任务](project-lead-tasks.md)创建项目存储库，无论是在单独的项目中还是在单个项目中。 

### <a name="create-the-myteam-project"></a>创建 MyTeam 项目

为团队创建一个单独的项目：

1. 在 web 浏览器中，在 URL *https：\//\<服务器名称 >/\<组织名称 >* 中转到组的 Azure DevOps 组织主页，然后选择 "**新建项目**"。 
   
   ![选择新项目](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. 在 "**创建项目**" 对话框中，在 "**项目名称**" 下输入团队名称，如*MyTeam*，然后选择 "**高级**"。 
   
1. 在 "**版本控制**" 下，选择 " **Git**"，然后在 "**工作项进程**" 下选择 "**敏捷**"。 然后选择“创建”。 
   
   ![创建项目](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
此时将打开 "团队项目**摘要**" 页，其中包含页 URL *https：\//\<服务器名称 >/\<组织名称 >/\<团队名称 >* 。

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>将 MyTeam 默认存储库重命名为 TeamUtilities

1. 在 " **MyTeam**项目**摘要**" 页上，在 "要**从哪个服务开始？** " 下选择 "**存储库**"。 
   
   ![选择存储库](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. 在 " **MyTeam**存储库" 页上，选择页面顶部的 " **MyTeam**存储库"，然后从下拉列表中选择 "**管理存储库**"。 
   
   ![选择管理存储库](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. 在 "**项目设置**" 页上，选择 " **MyTeam** " 存储库旁边的 " **...** "，然后选择 "**重命名存储库**"。 
   
   ![选择重命名存储库](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. 在 "**重命名 MyTeam 存储库**" 弹出窗口中，输入*TeamUtilities*，然后选择 "**重命名**"。 

### <a name="create-the-teamtemplate-repository"></a>创建 TeamTemplate 存储库

1. 在 "**项目设置**" 页上，选择 "**新建存储库"。** 
   
   ![选择新存储库](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   或者，从 " **MyTeam**项目**摘要**" 页的左侧导航栏中选择 "**存储库**"，在页面顶部选择一个存储库，然后从下拉列表中选择 "**新建存储库**"。
   
1. 在 "**创建新存储库**" 对话框中，确保在 "**类型**" 下选择 " **Git** "。 在 "**存储库名称**" 下输入*TeamTemplate* ，然后选择 "**创建**"。
   
   ![创建存储库](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. 确认你可以在项目设置页面上看到两个存储库**TeamUtilities**和**TeamTemplate** 。 
   
   ![两个团队存储库](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>导入组公共存储库的内容

使用组管理器设置的组公共存储库的内容填充团队存储库：

1. 从**MyTeam**项目主页的左侧导航栏中选择 "**存储库**"。 如果收到一条消息，指出找不到**MyTeam**模板，请选择中的链接 **，或者导航到默认的 TeamTemplate 存储库。** 
   
   将打开默认的**TeamTemplate**存储库。 
   
1. 在 " **TeamTemplate** " 页上，选择 "**导入**"。 
   
   ![选择导入](./media/team-lead-tasks/import-repo.png)
   
1. 在 "**导入 git 存储库**" 对话框中，选择 " **Git** " 作为**源类型**，并在 "**克隆 url**" 下输入组通用模板存储库的 URL。 URL 为*https：\//\<服务器名称 >/\<组织名称 >/_git/\<存储库名称 >* 。 例如： *https：\//dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*。 
   
1. 选择“导入”。 组模板存储库的内容将导入到团队模板存储库中。 
   
   ![导入组通用模板存储库](./media/team-lead-tasks/import-repo-2.png)
   
1. 在项目的**存储库**页面顶部，选择 "TeamUtilities 存储库"，并选择 " " 存储库。
   
1. 重复导入过程，将组常用实用程序存储库（例如*GroupUtilities*）的内容导入到**TeamUtilities**存储库。 
   
你的两个团队存储库现在包含相应组公共存储库中的文件。 

### <a name="customize-the-contents-of-the-team-repositories"></a>自定义团队存储库的内容

如果要自定义团队存储库的内容以满足团队的特定需求，可以立即执行此操作。 您可以修改文件、更改目录结构，或添加文件和文件夹。

直接在 Azure DevOps 中修改、上载或创建文件或文件夹：

1. 在 " **MyTeam**项目**摘要**" 页上，选择 "**存储库**"。 
   
1. 在页面顶部，选择要自定义的存储库。

1. 在存储库目录结构中，导航到要更改的文件夹或文件。 
   
   - 若要创建新文件夹或文件，请选择 "**新建**" 旁边的箭头。 
     
     ![创建新文件](./media/team-lead-tasks/new-file.png)
     
   - 若要上传文件，请选择 "**上传文件**"。 
     
     ![上传文件](./media/team-lead-tasks/upload-files.png)
     
   - 若要编辑现有文件，请导航到该文件，然后选择 "**编辑**"。 
     
     ![编辑文件](./media/team-lead-tasks/edit-file.png)
     
1. 添加或编辑文件后，选择 "**提交**"。
   
   ![提交更改](./media/team-lead-tasks/commit.png)

若要在本地计算机或 DSVM 上使用存储库，请先将存储库复制或*克隆*到本地计算机，然后将更改提交并推送到共享团队存储库， 

克隆存储库：

1. 在 " **MyTeam**项目**摘要**" 页上，选择 "**存储库**"，然后在页面顶部选择要克隆的存储库。
   
1. 在 "存储库" 页上，选择右上角的 "**克隆**"。
   
1. 在 "**克隆存储库**" 对话框中的 "**命令行**" 下，选择 " **HTTPS** " 作为 HTTP**连接或 ssh 进行 SSH 连接**，然后将 "克隆 URL" 复制到剪贴板。
   
   ![复制克隆 URL](./media/team-lead-tasks/clone.png)
   
1. 在本地计算机上，创建以下目录：
   
   - 对于 Windows： **C:\GitRepos\MyTeam**
   - 对于 Linux，请 **$home/gitrepos/myteam** 
   
1. 更改为你创建的目录。
   
1. 在 Git Bash 中运行命令 `git clone <clone URL>`，其中 \<clone URL > 是从**clone**对话框复制的 url。
   
   例如，使用以下命令之一将**TeamUtilities**存储库克隆到本地计算机上的*MyTeam*目录。 
   
   **HTTPS 连接：**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH 连接：**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

在存储库的本地克隆中进行任何所需的更改后，提交对共享团队存储库所做的更改并将其推送到其中。 

从本地**GitRepos\MyTeam\TeamTemplate**或**GitRepos\MyTeam\TeamUtilities**目录运行以下 Git Bash 命令。

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> 如果这是您第一次提交到 Git 存储库，则在运行 `git commit` 命令之前，您可能需要配置全局参数*user.name*和*user. email* 。 运行以下两个命令：
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> 如果要提交到多个 Git 存储库，请使用相同的名称和电子邮件地址。 在构建 Power BI 仪表板来跟踪多个存储库中的 Git 活动时，使用相同的名称和电子邮件地址是非常方便的。

## <a name="add-team-members-and-configure-permissions"></a>添加团队成员并配置权限

向团队添加成员：

1. 在 Azure DevOps 中，从**MyTeam**项目主页的左侧导航栏中选择 "**项目设置**"。 
   
1. 从**项目设置**左侧导航栏中选择 **"团队**"，然后在 "**团队**" 页上，选择 " **MyTeam" 团队**。 
   
   ![配置团队](./media/team-lead-tasks/teams.png)
   
1. 在 "**团队配置文件**" 页上，选择 "**添加**"。
   
   ![添加到 MyTeam 团队](./media/team-lead-tasks/add-to-team.png)
   
1. 在 "**添加用户和组**" 对话框中，搜索并选择要添加到组的成员，然后选择 "**保存更改**"。 
   
   ![添加用户和组](./media/team-lead-tasks/add-users.png)
   

为团队成员配置权限：

1. 从**项目设置**左侧导航窗格中，选择 "**权限**"。 
   
1. 在 "**权限**" 页上，选择要向其中添加成员的组。 
   
1. 在该组的页上，选择 "**成员**"，然后选择 "**添加**"。 
   
1. 在 "**邀请成员**" 弹出窗口中，搜索并选择要添加到组的成员，然后选择 "**保存**"。 
   
   ![向成员授予权限](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>创建团队数据和分析资源

此步骤是可选的，但与整个团队共享数据和分析资源具有性能和成本效益。 团队成员可以对共享资源执行其项目、节省预算并更有效地进行协作。 可以创建 Azure 文件存储，并将其装载到 DSVM 上以与团队成员共享。 

有关与团队共享其他资源（如 Azure HDInsight Spark 群集）的信息，请参阅[平台和工具](platforms-and-tools.md)。 本主题提供了有关选择适合你的需求的资源的数据科学观点的指导，以及指向产品页面和其他相关和有用教程的链接。

>[!NOTE]
> 若要避免跨数据中心传输数据，这可能会变慢且成本高昂，请确保 Azure 资源组、存储帐户和 DSVM 都托管在同一 Azure 区域中。 

### <a name="create-azure-file-storage"></a>创建 Azure 文件存储

1. 运行以下脚本，为对整个团队有用的数据资产创建 Azure 文件存储。 此脚本会提示你输入 Azure 订阅信息，因此可随时进入。 

   - 在 Windows 计算机上，从 PowerShell 命令提示符运行该脚本：
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - 在 Linux 计算机上，从 Linux shell 运行该脚本：
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. 出现提示时，请登录到 Microsoft Azure 帐户，并选择要使用的订阅。
   
1. 选择要使用的存储帐户，或在所选订阅下创建新的存储帐户。 对于 Azure 文件存储名称，可以使用小写字符、数字和连字符。
   
1. 若要帮助装载和共享存储，请按 Enter 或输入*Y*将 Azure 文件存储信息保存到当前目录中的文本文件中。 您可以将此文本文件签入到**TeamTemplate**存储库，理想情况下在**Docs\DataDictionaries**下，以便您团队中的所有项目都可以访问它。 在下一部分中，还需要文件信息将 Azure 文件存储装载到 Azure DSVM。 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>在本地计算机或 DSVM 上装载 Azure 文件存储

1. 若要将 Azure 文件存储装载到本地计算机或 DSVM，请使用以下脚本。
   
   - 在 Windows 计算机上，从 PowerShell 命令提示符运行该脚本：
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - 在 Linux 计算机上，从 Linux shell 运行该脚本：
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. 如果在上一步中保存了 Azure 文件存储信息文件，请按 Enter 或输入*Y*继续。 输入创建的文件的完整路径和名称。 
   
   如果没有 Azure 文件存储信息文件，请输入*n*，并按照说明输入订阅、azure 存储帐户和 azure 文件存储信息。
   
1. 输入要在其上装载文件共享的本地驱动器或 TDSP 驱动器的名称。 屏幕将显示现有驱动器名称的列表。 提供尚不存在的驱动器名称。
   
1. 确认新驱动器和存储已成功装载到计算机上。

## <a name="next-steps"></a>后续步骤

下面是团队数据科学过程定义的其他角色和任务的详细说明的链接：

- [数据科学团队的组管理员任务](group-manager-tasks.md)
- [数据科学团队的项目主管任务](project-lead-tasks.md)
- [为数据科学团队项目单独的参与者任务](project-ic-tasks.md)
