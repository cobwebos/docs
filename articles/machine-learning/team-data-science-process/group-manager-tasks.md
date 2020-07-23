---
title: 团队数据科学流程组管理员任务
description: 按照此详细演练操作，了解组管理员完成的数据科学团队项目任务。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ec7f4242e5046e90fdf0eb8c6c0579f402e4f55
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "76721347"
---
# <a name="team-data-science-process-group-manager-tasks"></a>团队数据科学流程组管理员任务

本文介绍了组管理员  为数据科学组织完成的任务。 组管理员负责管理企业中的整个数据科学部门。 数据科学部门可能有多个团队，每个团队都在不同的业务垂直领域开展多个数据科学项目。 组管理员的目标是创建致力于标准化[团队数据科学流程](overview.md) (TDSP) 的团队协作环境。 有关致力于标准化 TDSP 的数据科学团队要处理的所有人员角色及相关任务的概述，请参阅[团队数据科学流程角色和任务](roles-tasks.md)。

下图显示了六个主要的组管理员设置任务。 组管理员可以将任务委托给代理，但与角色相关的任务不变。

![组管理员任务](./media/group-manager-tasks/tdsp-group-manager.png)

1. 为组设置一个 Azure DevOps 组织  。
2. 在 Azure DevOps 组织中创建默认的 GroupCommon 项目  。
3. 在 Azure Repos 中创建 GroupProjectTemplate  存储库。
4. 在 Azure Repos 中创建 GroupUtilities  存储库。
5. 将 Microsoft TDSP 团队 ProjectTemplate 和 Utilities 存储库中的内容导入组通用存储库   。
6. 为团队成员设置成员身份和权限，以便其访问组   。

下面的教程详细介绍了相关步骤。 

> [!NOTE] 
> 本文使用 Azure DevOps 设置 TDSP 组环境，因为 Microsoft 使用此方法实现 TDSP。 如果你的组使用其他代码托管或开发平台，组管理员的任务是相同的，但完成这些任务的方法可能不同。

## <a name="create-an-organization-and-project-in-azure-devops"></a>在 Azure DevOps 中创建一个组织和项目

1. 转到 [visualstudio.microsoft.com](https://visualstudio.microsoft.com)，选择右上方的“登录”  ，然后登录到 Microsoft 帐户。 
   
   ![登录 Microsoft 帐户](./media/group-manager-tasks/signinvs.png)
   
   如果没有 Microsoft 帐户，请选择“立即注册”  创建一个 Microsoft 帐户，然后使用此帐户登录。 如果你的组织有 Visual Studio 订阅，请使用该订阅的凭据登录。
   
1. 登录后，在 Azure DevOps 页的右上方选择“新建组织”  。
   
   ![新建组织](./media/group-manager-tasks/create-organization.png)
   
1. 如果系统提示你同意服务条款、隐私声明和行为准则，请选择“继续”  。
   
1. 在注册对话框中，为 Azure DevOps 组织命名并接受分配的主机区域，或通过下拉列表选择其他区域。 然后选择“继续”。  

1. 在“创建项目以开始使用”下，输入 GroupCommon，然后选择“创建项目”    。 
   
   ![创建项目](./media/group-manager-tasks/create-project.png)

GroupCommon 项目的摘要页随即打开   。 页面 URL 是*https： \/ / \<servername> / \<organization-name> /GroupCommon*。

![项目摘要页](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>设置组通用存储库

Azure Repos 可为组托管以下类型的存储库：

- **组通用存储库**：可供数据科学部门中的多个团队用于多个数据科学项目的通用存储库。 
- **团队存储库**：专供数据科学部门内特定团队使用的存储库。 这些存储库特定于团队需求，可用于该团队内的多个项目，但不足以使数据科学部门中的多个团队使用。
- **项目存储库**：适用于特定项目的存储库。 此类存储库通用程度比较低，可能不足以供一个团队内的多个项目或数据科学部门内的其他团队使用。

可按照以下方法在项目中设置组通用存储库： 
- 将默认的 GroupCommon 存储库重命名为 GroupProjectTemplate  
- 创建新的 GroupUtilities 存储库 

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>将默认项目存储库重命名为 GroupProjectTemplate

将默认的 GroupCommon 项目存储库重命名为 GroupProjectTemplate   ：

1. 在 GroupCommon 项目的摘要页上，选择“存储库”    。 执行此操作后，将转到 GroupCommon 项目的默认 GroupCommon 存储库，该存储库当前为空  。
   
1. 在页面顶部，下拉“GroupCommon”旁边的箭头并选择“管理存储库”   。
   
   ![管理存储库](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. 在“项目设置”页上，选择“GroupCommon”旁边的“...”，然后选择“重命名存储库”     。 
   
   ![依次选择“...”和“重命名存储库”](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. 在“重命名 GroupCommon 存储库”弹出菜单中，输入 GroupProjectTemplate，然后选择“重命名”    。 
   
   ![重命名存储库](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>创建 GroupUtilities 存储库

创建 GroupUtilities  存储库：

1. 在 GroupCommon 项目的摘要页上，选择“存储库”    。 
   
1. 在页面顶部，下拉“GroupProjectTemplate”旁边的箭头并选择“新建存储库”   。
   
   ![选择“新建存储库”](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. 在“新建存储库”对话框中，为“类型”选择“Git”，为“存储库名称”输入 GroupUtilities，然后选择“创建”       。
   
   ![创建 GroupUtilities 存储库](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. 在“项目设置”页上，在左侧导航栏中选择“存储库”下的“存储库”，查看两个组存储库    ：GroupProjectTemplate 和 GroupUtilities   。
   
   ![两个组存储库](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>导入 Microsoft TDSP 团队存储库

在本教程的这一部分，需要将由 Microsoft TDSP 团队管理的 ProjectTemplate 和 Utilities 存储库中的内容导入 GroupProjectTemplate 和 GroupUtilities 存储库     。 

导入 Microsoft TDSP 团队存储库：

1. 在 GroupCommon 项目主页中，从左侧导航栏中选择“存储库”   。 默认的 GroupProjectTemplate 存储库随即打开  。 
   
1. 在“GroupProjectTemplate 为空”页上，选择“导入”   。 
   
   ![选择“导入”](./media/group-manager-tasks/import-repo.png)
   
1. 在“导入 Git 存储库”对话框中，为“源类型”选择“Git”，并为“克隆 URL”输入 https:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git      。 然后选择“导入”  。 Microsoft TDSP 团队 ProjectTemplate 存储库已导入 GroupProjectTemplate 存储库。 
   
   ![导入 Microsoft TDSP 团队存储库](./media/group-manager-tasks/import-repo-2.png)
   
1. 在“存储库”页的顶部，在下拉列表中选择“GroupUtilities”存储库   。
   
1. 重复导入过程，将 Microsoft TDSP 团队 Utilities 存储库 (https:\//github.com/Azure/Azure-TDSP-Utilities.git) 的内容导入 GroupUtilities 存储库    。 
   
现在，两个组存储库中都包含 Microsoft TDSP 团队的相应存储库中的所有文件，.git  目录中的文件除外。 

## <a name="customize-the-contents-of-the-group-repositories"></a>自定义组存储库的内容

现在可以自定义组存储库的内容以满足组的特定需求。 可以修改文件，更改目录结构，或添加你的组已制定或对组有用的文件。

### <a name="make-changes-in-azure-repos"></a>在 Azure Repos 中进行更改

自定义存储库内容：

1. 在 GroupCommon 项目的摘要页上，选择“存储库”    。 
   
1. 在页面顶部，选择要自定义的存储库。

1. 在存储库目录结构中，导航到要更改的文件夹或文件。 
   
   - 若要创建新的文件夹或文件，请选择“新建”旁边的箭头  。 
     
     ![创建新文件](./media/group-manager-tasks/new-file.png)
     
   - 若要上传文件，请选择“上传文件”  。 
     
     ![上传文件](./media/group-manager-tasks/upload-files.png)
     
   - 若要编辑现有文件，请导航到该文件，然后选择“编辑”  。 
     
     ![编辑文件](./media/group-manager-tasks/edit-file.png)
     
1. 添加或编辑文件后，选择“提交”  。
   
   ![提交更改](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>使用本地计算机或 DSVM 进行更改

如果要使用本地计算机或 DSVM 进行更改并将更改推送到组存储库，请确保满足使用 Git 和 DSVM 的先决条件：

- 拥有 Azure 订阅（如果要创建 DSVM）。
- 计算机上安装有 Git。 如果要使用 DSVM，则需预安装 Git。 否则，请参阅[平台和工具附录](platforms-and-tools.md#appendix)。
- 如果要使用 DSVM，需要在 Azure 中创建和配置 Windows 或 Linux DSVM。 有关详细信息和说明，请参阅 [Data Science Virtual Machine 文档](/azure/machine-learning/data-science-virtual-machine/)。
- 对于 Windows DSVM，需要在计算机上安装 [Git 凭据管理器 (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)。 在 README.md 文件中，向下滚动到“下载并安装”部分，然后选择“最新安装程序”    。 从安装程序页下载 .exe 安装程序并运行它  。 
- 对于 Linux DSVM，需要在 DSVM 上设置 SSH 公钥，并将其添加到 Azure DevOps 中。 有关详细信息和说明，请参阅[平台和工具附录](platforms-and-tools.md#appendix)中的“创建 SSH 公钥”  部分。 

首先，将存储库复制或克隆到本地计算机  。 
   
1. 在 GroupCommon 项目的摘要页上，选择“存储库”，然后在页面顶部选择要克隆的存储库    。
   
1. 在“存储库”页上，选择右上方的“克隆”  。
   
1. 在“克隆存储库”对话框中，为 HTTP 连接选择“HTTPS”，或为 SSH 连接选择“SSH”，并将命令行下的克隆 URL 复制到剪贴板     。
   
   ![克隆存储库](./media/group-manager-tasks/clone.png)
   
1. 在本地计算机上创建以下目录：
   
   - 对于 Windows：C:\GitRepos\GroupCommon 
   - 对于 Linux：在主目录中创建 $/GitRepos/GroupCommon  
   
1. 切换到创建的目录。
   
1. 在 Git Bash 中，运行命令 `git clone <clone URL>.`
   
   例如，以下任一命令都可将 GroupUtilities 存储库克隆到本地计算机上的 GroupCommon 目录   。 
   
   **HTTPS 连接：**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH 连接：**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

在存储库的本地克隆中进行任何所需更改后，可以将更改推送到共享组通用存储库。 

从本地 GroupProjectTemplate 或 GroupUtilities 目录运行以下 Git Bash 命令   。

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> 如果这是首次提交到 Git 存储库，则需要在运行 `git commit` 命令之前配置全局参数 user.name 和 user.email   。 运行以下两个命令：
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> 如果要提交到多个 Git 存储库，请在每次提交时都使用相同的姓名和电子邮件地址。 使用相同的姓名和电子邮件地址在构建 Power BI 仪表板来跟踪多个存储库中的 Git 活动时可提供便利。

## <a name="add-group-members-and-configure-permissions"></a>添加组成员并配置权限

向组添加成员：

1. 在 Azure DevOps 的 GroupCommon 项目主页中，从左侧导航栏中选择“项目设置”   。 
   
1. 从“项目设置”左侧导航栏中，选择“团队”，然后在“团队”页上选择“GroupCommon 团队”     。 
   
   ![配置团队](./media/group-manager-tasks/teams.png)
   
1. 在“团队资料”页上，选择“添加”   。
   
   ![添加到 GroupCommon 团队](./media/group-manager-tasks/add-to-team.png)
   
1. 在“添加用户和组”对话框中，搜索并选择要添加到组的成员，然后选择“保存更改”   。 
   
   ![添加用户和组](./media/group-manager-tasks/add-users.png)
   

配置成员权限：

1. 从“项目设置”左侧导航栏中，选择“权限”   。 
   
1. 在“权限”页上，选择要向其中添加成员的组  。 
   
1. 在该组的页面上选择“成员”，然后选择“添加”   。 
   
1. 在“邀请成员”弹出菜单中，搜索并选择要添加到组的成员，然后选择“保存”   。 
   
   ![为成员授予权限](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>后续步骤

下面是团队数据科学流程中的其他角色和任务的详细说明链接：

- [数据科学团队的团队主管任务](team-lead-tasks.md)
- [数据科学团队的项目主管任务](project-lead-tasks.md)
- [数据科学团队的项目个人参与者任务](project-ic-tasks.md)
