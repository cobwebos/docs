---
title: Team Data Science Process Team 中团队主管的任务
description: 面向 Team Data Science Process 团队主管的详细任务演练
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864275"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Team Data Science Process 团队主管的任务

本文介绍团队主管要为其数据科学团队完成的任务。** 团队主管的目标在于创建致力于标准化 [Team Data Science Process](overview.md) (TDSP) 的协作型团队环境。 TDSP 设计用于帮助改进协作和团队学习。 

TDSP 是一种敏捷的迭代式数据科学方法，可有效交付预测分析解决方案和智能应用程序。 该过程从 Microsoft 和业界提炼最佳实践和结构。  目标是成功实施数据科学计划，并充分实现其分析计划的好处。 有关致力于标准化 TDSP 的数据科学团队要处理的人员角色及相关任务的概述，请参阅[团队数据科学流程角色和任务](roles-tasks.md)。

团队主管管理由企业的数据科学单位中的若干数据科学家组成的团队。 根据数据科学单位的规模和结构，[小组经理](group-manager-tasks.md)和团队主管可能是同一个人，或者可将其任务委托给代理人。 但是，任务本身不会改变。 

下图显示了团队主管为了设置团队环境而要完成的任务的工作流：

![团队主管任务工作流](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. 在小组所在组织的 Azure DevOps 中创建**团队项目**。 
  
1. 将默认的团队存储库重命名为 **TeamUtilities**。
  
1. 在团队项目中创建新的 **TeamTemplate** 存储库。 
  
1. 将小组的 **GroupUtilities** 和 **GroupProjectTemplate** 存储库的内容导入 **TeamUtilities** 和 **TeamTemplate** 存储库。 
  
1. 通过添加团队成员并配置其权限来设置**安全控制**。
  
1. 根据需要创建团队数据和分析资源：
   - 将团队特定的实用工具添加到 **TeamUtilities** 存储库。 
   - 创建 **Azure 文件存储**，用于存储可用于整个团队的数据资产。 
   - 将 Azure 文件存储装载到团队主管的 **Data Science Virtual Machine** (DSVM) 并在其中添加数据资产。

以下教程详细介绍了相关步骤。

> [!NOTE] 
> 本文使用 Azure DevOps 和 DSVM 设置 TDSP 团队环境，因为 Microsoft 使用此方法实现 TDSP。 如果团队使用其他代码托管或开发平台，则团队领导的任务是相同的，但完成这些任务的方法可能不同。

## <a name="prerequisites"></a>先决条件

本教程假设[小组经理](group-manager-tasks.md)已设置以下资源和权限：

- 数据单位的 Azure DevOps **组织**
- **GroupProjectTemplate** 和 **GroupUtilities** 存储库，其中填充了 Microsoft TDSP 团队的 **ProjectTemplate** 和 **Utilities** 存储库的内容
- 对组织帐户的权限，用于为团队创建项目和存储库

若要克隆存储库并修改其在本地计算机或 DSVM 上的内容，或者要设置 Azure 文件存储并将其装载到 DSVM，需要做好以下准备：

- Azure 订阅。
- 计算机上安装的 Git。 如果要使用 DSVM，则需预安装 Git。 否则，请参阅[平台和工具附录](platforms-and-tools.md#appendix)。
- 如果要使用 DSVM，需要在 Azure 中创建和配置 Windows 或 Linux DSVM。 有关详细信息和说明，请参阅 [Data Science Virtual Machine 文档](/azure/machine-learning/data-science-virtual-machine/)。
- 对于 Windows DSVM，需要在计算机上安装 [Git 凭据管理器 (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)。 在 README.md 文件中，向下滚动到“下载并安装”部分，然后选择“最新安装程序”**********。 从安装程序页下载 .exe 安装程序并运行它**。 
- 对于 Linux DSVM，需要在 DSVM 上设置 SSH 公钥，并将其添加到 Azure DevOps 中。 有关详细信息和说明，请参阅[平台和工具附录](platforms-and-tools.md#appendix)中的“创建 SSH 公钥”**** 部分。 

## <a name="create-a-team-project-and-repositories"></a>创建团队项目和存储库

在本部分，你将在小组的 Azure DevOps 组织中创建以下资源：

- 在 Azure DevOps 中创建 **MyTeam** 项目
- **TeamTemplate** 存储库
- **TeamUtilities** 存储库

在本教程中为存储库和目录指定的名称假设目标是在较大的数据科学组织中为你自己的团队建立一个单独的项目。 但是，整个小组可以选择在小组经理或组织管理员创建的单个项目下工作。 然后，所有数据科学团队将在此单个项目下创建存储库。 此方案可能适用于：
- 没有多个数据科学团队的小型数据科学小组。 
- 具有多个数据科学团队的较大型数据科学小组，不过，该科学小组希望通过小组级冲刺规划等活动来优化团队间协作。 

如果团队选择在单个组项目下拥有特定于团队的存储库，则团队领导应创建具有*\<"团队名称>模板*和*\<团队名称>实用程序*等名称的存储库。 例如：*团队模板*和*团队实用程序*。 

在任何情况下，团队主管都需要让其团队成员知道要设置和克隆哪个模板和实用工具存储库。 项目主管应当按照[数据科学团队的项目主管任务](project-lead-tasks.md)所述在单独的项目下或者在单个项目下创建项目存储库。 

### <a name="create-the-myteam-project"></a>创建 MyTeam 项目

为团队创建单独的项目：

1. 在 Web 浏览器中，转到组位于 URL https 的 Azure DevOps 组织主页*\//\<：\<服务器名称>/组织名称>*，然后选择 **"新建项目**"。 
   
   ![选择“新建项目”](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. 在“创建项目”对话框中的“项目名称”下输入团队名称（例如 *MyTeam*），然后选择“高级”。************ 
   
1. 在“版本控制”下选择“Git”，然后在“工作项流程”下选择“敏捷”。**************** 然后选择 **"创建**"。 
   
   ![创建项目](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
将打开团队**项目摘要**页面，其中页面 URL *\//\<https：服务器名称\<>/组织名称\<>/团队名称>*。

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>将 MyTeam 默认存储库重命名为 TeamUtilities

1. 在“MyTeam”项目“摘要”页上的“要从哪个服务开始?”下，选择“存储库”。**************** 
   
   ![选择“存储库”](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. 在“MyTeam”存储库页的顶部选择“MyTeam”存储库，然后从下拉列表中选择“管理存储库”。************ 
   
   ![选择“管理存储库”](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. 在“项目设置”页上，选择“MyTeam”存储库旁边的“...”，然后选择“重命名存储库”。**************** 
   
   ![选择“重命名存储库”](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. 在“重命名 MyTeam 存储库”弹出窗口中输入 *TeamUtilities*，然后选择“重命名”。******** 

### <a name="create-the-teamtemplate-repository"></a>创建 TeamTemplate 存储库

1. 在“项目设置”页上，选择“新建存储库”。******** 
   
   ![选择“新建存储库”](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   或者，从“MyTeam”项目“摘要”页的左侧导航窗格中选择“存储库”，选择页面顶部的存储库，然后从下拉列表中选择“新建存储库”。****************
   
1. 在“创建新存储库”对话框中，确保已在“类型”下选择“Git”。************ 在“存储库名称”下输入 *TeamTemplate*，然后选择“创建”。********
   
   ![创建存储库](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. 确认可以在项目设置页上看到两个存储库：“TeamUtilities”和“TeamTemplate”。******** 
   
   ![两个团队存储库](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>导入小组通用存储库的内容

若要在团队存储库中填充小组经理设置的小组通用存储库的内容：

1. 在 **MyTeam** 项目主页中，从左侧导航窗格中选择“存储库”****。 如果有消息指出找不到 **MyTeam** 模板，请选择“否则请导航到默认的 TeamTemplate 存储库”中的链接。**** 
   
   此时将打开默认的 **TeamTemplate** 存储库。 
   
1. 在“TeamTemplate 为空”页上，选择“导入”。******** 
   
   ![选择“导入”](./media/team-lead-tasks/import-repo.png)
   
1. 在“导入 Git 存储库”对话框中，选择“Git”作为“源类型”，然后在“克隆 URL”下输入小组通用模板存储库的 URL。**************** URL 是*\//\<https：服务器名称>/\<组织名称>/_git/\<存储库名称>*。 例如：*https:\//dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*。 
   
1. 选择“导入”****。 小组模板存储库的内容随即会导入到团队模板存储库中。 
   
   ![导入小组通用模板存储库](./media/team-lead-tasks/import-repo-2.png)
   
1. 在项目的“存储库”页的顶部，选择“TeamUtilities”存储库。********
   
1. 重复导入过程，将小组通用实用工具存储库（例如 *GroupUtilities*）的内容导入 **TeamUtilities** 存储库。 
   
两个团队存储库中的每一个现在包含相应小组通用存储库中的文件。 

### <a name="customize-the-contents-of-the-team-repositories"></a>自定义团队存储库的内容

现在可以根据团队的具体需求，自定义团队存储库的内容。 可以修改文件、更改目录结构，或添加文件和文件夹。

若要直接在 Azure DevOps 中修改、上传或者创建文件或文件夹：

1. 在“MyTeam”项目的“摘要”页上，选择“存储库”。************ 
   
1. 在页面顶部，选择要自定义的存储库。

1. 在存储库目录结构中，导航到要更改的文件夹或文件。 
   
   - 若要创建新的文件夹或文件，请选择“新建”旁边的箭头****。 
     
     ![创建新文件](./media/team-lead-tasks/new-file.png)
     
   - 若要上传文件，请选择“上传文件”****。 
     
     ![上传文件](./media/team-lead-tasks/upload-files.png)
     
   - 若要编辑现有文件，请导航到该文件，然后选择“编辑”****。 
     
     ![编辑文件](./media/team-lead-tasks/edit-file.png)
     
1. 添加或编辑文件后，选择“提交”****。
   
   ![提交更改](./media/team-lead-tasks/commit.png)

若要在本地计算机或 DSVM 上使用存储库，请先将存储库复制或克隆到本地计算机，然后将所做的更改提交并推送到共享的团队存储库**。 

若要克隆存储库：

1. 在“MyTeam”项目的“摘要”页上选择“存储库”，然后在页面顶部选择要克隆的存储库************。
   
1. 在“存储库”页上，选择右上方的“克隆”****。
   
1. 在“克隆存储库”对话框中的“命令行”下，为 HTTP 连接选择“HTTPS”，或者为 SSH 连接选择“SSH”，然后将克隆 URL 复制到剪贴板****************。
   
   ![复制克隆 URL](./media/team-lead-tasks/clone.png)
   
1. 在本地计算机上创建以下目录：
   
   - 对于窗口 **：C：[GitRepos]我的团队**
   - 对于 Linux：**$home/GitRepos/MyTeam** 
   
1. 切换到创建的目录。
   
1. 在 Git Bash 中运行命令 `git clone <clone URL>`，其中 \<clone URL> 是从“克隆”对话框复制的 URL。****
   
   例如，使用以下命令之一将 **TeamUtilities** 存储库克隆到本地计算机上的 *MyTeam* 目录。 
   
   **HTTPS 连接：**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH 连接：**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

在存储库的本地克隆中进行任何所需的更改后，将更改提交并推送到共享的团队存储库。 

从本地 **GitRepos\MyTeam\TeamTemplate** 或 **GitRepos\MyTeam\TeamUtilities** 目录运行以下 Git Bash 命令。

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> 如果这是首次提交到 Git 存储库，则需要在运行 `git commit` 命令之前配置全局参数 *user.name* 和 *user.email*。 运行以下两个命令：
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> 如果要提交到多个 Git 存储库，请在每次提交时都使用相同的姓名和电子邮件地址。 使用相同的姓名和电子邮件地址在构建 Power BI 仪表板来跟踪多个存储库中的 Git 活动时可提供便利。

## <a name="add-team-members-and-configure-permissions"></a>添加团队成员并配置权限

若要向团队添加成员：

1. 在 Azure DevOps 的 **MyTeam** 项目主页中，从左侧导航窗格中选择“项目设置”****。 
   
1. 在“项目设置”左侧导航窗格中选择“团队”，然后在“团队”页上选择“MyTeam 团队”****************。 
   
   ![配置团队](./media/team-lead-tasks/teams.png)
   
1. 在“团队资料”页上，选择“添加”********。
   
   ![添加到 MyTeam 团队](./media/team-lead-tasks/add-to-team.png)
   
1. 在“添加用户和组”对话框中，搜索并选择要添加到组的成员，然后选择“保存更改”********。 
   
   ![添加用户和组](./media/team-lead-tasks/add-users.png)
   

若要配置团队成员的权限：

1. 在“项目设置”左侧导航窗格中，选择“权限”********。 
   
1. 在“权限”页上，选择要向其中添加成员的组****。 
   
1. 在该组的页面上选择“成员”，然后选择“添加”********。 
   
1. 在“邀请成员”弹出菜单中，搜索并选择要添加到组的成员，然后选择“保存”********。 
   
   ![为成员授予权限](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>创建团队数据和分析资源：

此步骤是可选的，但与整个团队共享数据和分析资源可以带来性能和成本方面的优势。 团队成员可以在共享的资源中执行其项目、节省预算并更有效地展开协作。 可以创建 Azure 文件存储并将其装载到 DSVM 上，以便与团队成员共享。 

有关与团队共享其他资源（例如 Azure HDInsight Spark 群集）的信息，请参阅[平台和工具](platforms-and-tools.md)。 本主题从数据科学的角度针对如何选择适合需求的资源提供了指导，并提供了指向产品页面和其他相关且有用教程的链接。

>[!NOTE]
> 为了避免跨数据中心传输数据（这可能很慢而且成本很高），请确保 Azure 资源组、存储帐户和 DSVM 全部托管在同一 Azure 区域。 

### <a name="create-azure-file-storage"></a>创建 Azure 文件存储

1. 运行以下脚本，为整个团队可用的数据资产创建 Azure 文件存储。 此脚本会提示输入 Azure 订阅信息，因此请准备好这些信息。 

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
   
1. 根据提示登录到 Microsoft Azure 帐户，然后选择要使用的订阅。
   
1. 在所选订阅下选择要使用的存储帐户或者创建一个新的存储帐户。 对于 Azure 文件存储名称，可以使用小写字符、数字和连字符。
   
1. 为了方便装载和共享存储，请按 Enter 或输入 *Y*，将 Azure 文件存储信息保存到当前目录中的某个文本文件内。 可将此文本文件签入 **TeamTemplate** 存储库（最好是在 **Docs\DataDictionaries** 下），以便团队中的所有项目都可以访问它。 在下一部分，也需要使用这些文件信息将 Azure 文件存储装载到 Azure DSVM。 
   
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
   
1. 如果在上一步骤中保存了 Azure 文件存储信息文件，请按 Enter 或输入 *Y* 继续。 输入创建的文件的完整路径和名称。 
   
   如果没有 Azure 文件存储信息文件，请输入 *n*，然后按照说明输入订阅、Azure 存储帐户和 Azure 文件存储信息。
   
1. 输入要在其上装载文件共享的本地驱动器或 TDSP 驱动器的名称。 屏幕中会显示现有驱动器名称的列表。 提供尚不存在的驱动器名称。
   
1. 确认新驱动器和存储已成功装载到计算机上。

## <a name="next-steps"></a>后续步骤

下面是 Team Data Science Process 定义的其他角色和任务的详细说明链接：

- [数据科学团队的组管理员任务](group-manager-tasks.md)
- [数据科学团队的项目主管任务](project-lead-tasks.md)
- [数据科学团队的项目个人参与者任务](project-ic-tasks.md)
