---
title: 团队数据科学流程组管理员任务
description: 用于数据科学团队项目上的组管理器的任务的详细演练。
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/24/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f95bb30f547e863fc7a796e69fffe1e2334e489c
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326796"
---
# <a name="team-data-science-process-group-manager-tasks"></a>团队数据科学流程组管理员任务

本文介绍了*组经理*对数据科学组织完成的任务。 组管理器管理企业中的整个数据科学单元。 数据科学部门可能有多个团队，每个团队都在不同的业务行业中处理多个数据科学项目。 组经理的目标是建立在[团队数据科学过程](overview.md)（TDSP）上实现标准化的协作组环境。 有关 TDSP 上的数据科学团队处理的所有人事角色和相关任务的概述，请参阅[团队数据科学过程角色和任务](roles-tasks.md)。

下图显示了六个主要的组管理器设置任务。 组管理员可以将任务委托给代理，但与角色关联的任务不会更改。

![组管理器任务](./media/group-manager-tasks/tdsp-group-manager.png)

1. 为组设置**Azure DevOps 组织**。
2. 在 Azure DevOps 组织中创建默认的**GroupCommon 项目**。
3. 在 Azure Repos 中创建**GroupProjectTemplate**存储库。
4. 在 Azure Repos 中创建**GroupUtilities**存储库。
5. 将 Microsoft TDSP 团队的**ProjectTemplate**和**实用程序**存储库的内容导入到组公用存储库中。
6. 设置团队成员访问组的**成员资格**和**权限**。

以下教程详细介绍了这些步骤。 

> [!NOTE] 
> 本文使用 Azure DevOps 设置 TDSP 组环境，因为这是如何实现 Microsoft 的 TDSP。 如果你的组使用其他代码宿主或开发平台，则组管理器的任务是相同的，但完成这些任务的方法可能不同。

## <a name="create-an-organization-and-project-in-azure-devops"></a>在 Azure DevOps 中创建组织和项目

1. 请访问[visualstudio.microsoft.com](https://visualstudio.microsoft.com)，选择右上角的 "**登录**"，然后登录到 Microsoft 帐户。 
   
   ![登录 Microsoft 帐户](./media/group-manager-tasks/signinvs.png)
   
   如果没有 Microsoft 帐户，请选择 "**立即注册**"，创建 Microsoft 帐户，然后使用此帐户登录。 如果你的组织有 Visual Studio 订阅，请使用该订阅的凭据登录。
   
1. 登录后，在 "Azure DevOps" 页的右上角，选择 "新建**组织**"。
   
   ![创建新组织](./media/group-manager-tasks/create-organization.png)
   
1. 如果系统提示你同意服务条款、隐私声明和行为准则，请选择 "**继续**"。
   
1. 在注册对话框中，为 Azure DevOps 组织命名并接受主机区域分配，或删除并选择其他区域。 然后选择“继续”。 

1. 在 "**创建要开始的项目**" 下，输入*GroupCommon*，然后选择 "**创建项目**"。 
   
   ![创建项目](./media/group-manager-tasks/create-project.png)

此时将打开 " **GroupCommon**项目**摘要**" 页。 页面 URL 是*https： \/ @ no__t-2 @ no__t-3servername >/@no__t >/GroupCommon*。

![项目摘要页](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>设置组通用存储库

Azure Repos 为你的组托管以下类型的存储库：

- **分组常用存储库**：一个数据科学单位内的多个团队可采用多个数据科学项目的通用存储库。 
- **团队存储库**：数据科学单元中特定团队的存储库。 这些存储库是特定于团队需求的，可用于该团队内的多个项目，但并不是足够通用，无法在一个数据科学单位内的多个团队中使用。
- **项目存储库**：特定项目的存储库。 对于一个团队内的多个项目或数据科学单元中的其他团队而言，此类存储库可能不够普遍。

若要在项目中设置公用存储库，请执行以下操作： 
- 将默认**GroupCommon**存储库重命名为**GroupProjectTemplate**
- 创建新的**GroupUtilities**存储库

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>将默认的项目存储库重命名为 GroupProjectTemplate

将默认的**GroupCommon**项目存储库重命名为**GroupProjectTemplate**：

1. 在 " **GroupCommon**项目**摘要**" 页上，选择 "**存储库**"。 此操作会将你转到 GroupCommon 项目的默认**GroupCommon**存储库，该存储库当前为空。
   
1. 在页面顶部，下拉 " **GroupCommon** " 旁边的箭头，然后选择 "**管理存储库**"。
   
   ![管理存储库](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. 在 "**项目设置**" 页上，选择 " **GroupCommon**"**旁边的 "..."** ，然后选择 "**重命名存储库**"。 
   
   ![选择 .。。然后选择 "重命名存储库"](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. 在 "**重命名 GroupCommon 存储库**" 弹出窗口中，输入*GroupProjectTemplate*，然后选择 "**重命名**"。 
   
   ![重命名存储库](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>创建 GroupUtilities 存储库

创建**GroupUtilities**存储库：

1. 在 " **GroupCommon**项目**摘要**" 页上，选择 "**存储库**"。 
   
1. 在页面顶部，下拉 " **GroupProjectTemplate** " 旁边的箭头，然后选择 "**新建存储库**"。
   
   ![选择新存储库](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. 在 "**创建新存储库**" 对话框中，选择 " **Git** " 作为 "**类型**"，输入*GroupUtilities*作为**存储库名称**，然后选择 "**创建**"。
   
   ![创建 GroupUtilities 存储库](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. 在 "**项目设置**" 页上，在左侧导航栏中选择 "**存储库**" 下的 "**存储库**" 以查看两个组存储库：**GroupProjectTemplate**和**GroupUtilities**。
   
   ![两个组存储库](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>导入 Microsoft TDSP 团队存储库

在本教程的此部分中，会将 Microsoft TDSP 团队管理的**ProjectTemplate**和**实用程序**存储库的内容导入到**GroupProjectTemplate**和**GroupUtilities**存储库中。 

导入 TDSP 团队存储库：

1. 从**GroupCommon**项目主页的左侧导航栏中选择 "**存储库**"。 将打开默认的**GroupProjectTemplate**存储库。 
   
1. 在 " **GroupProjectTemplate** " 页上，选择 "**导入**"。 
   
   ![选择导入](./media/group-manager-tasks/import-repo.png)
   
1. 在 "**导入 git 存储库**" 对话框中，选择 " **Git** " 作为**源类型**，然后输入 " *https： \//github .Com/Azure/Azure-TDSP-ProjectTemplate* " 作为**克隆 URL**。 然后选择 "**导入**"。 Microsoft TDSP team ProjectTemplate 存储库的内容将导入到 GroupProjectTemplate 存储库中。 
   
   ![导入 Microsoft TDSP 团队存储库](./media/group-manager-tasks/import-repo-2.png)
   
1. 在**存储库**页面顶部，选择 "GroupUtilities 存储库"，并选择 " " 存储库。
   
1. 重复导入过程，将 Microsoft TDSP 团队**实用工具**存储库的内容（ *https： @no__t/Github .com/Azure/Azure-TDSP-Utilities*）导入**GroupUtilities**存储库。 
   
现在，两个组存储库中的每个文件都包含 Microsoft TDSP 团队的相应存储库中的所有文件，这些文件除外 *。* 

## <a name="customize-the-contents-of-the-group-repositories"></a>自定义组存储库的内容

如果要自定义组存储库的内容以满足组的特定需求，可以立即执行此操作。 你可以修改文件、更改目录结构，或添加组已开发或对你的组有用的文件。

### <a name="make-changes-in-azure-repos"></a>在 Azure Repos 中进行更改

自定义存储库内容：

1. 在 " **GroupCommon**项目**摘要**" 页上，选择 "**存储库**"。 
   
1. 在页面顶部，选择要自定义的存储库。

1. 在存储库目录结构中，导航到要更改的文件夹或文件。 
   
   - 若要创建新文件夹或文件，请选择 "**新建**" 旁边的箭头。 
     
     ![创建新文件](./media/group-manager-tasks/new-file.png)
     
   - 若要上传文件，请选择 "**上传文件**"。 
     
     ![上载文件](./media/group-manager-tasks/upload-files.png)
     
   - 若要编辑现有文件，请导航到该文件，然后选择 "**编辑**"。 
     
     ![编辑文件](./media/group-manager-tasks/edit-file.png)
     
1. 添加或编辑文件后，选择 "**提交**"。
   
   ![提交更改](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>使用本地计算机或 DSVM 进行更改

如果要使用本地计算机或 DSVM 进行更改并将更改推送到组存储库，请确保具有使用 Git 和 Dsvm 的先决条件：

- Azure 订阅（如果想要创建 DSVM）。
- 已在计算机上安装 Git。 如果你使用的是 DSVM，则将预安装 Git。 否则，请参阅[平台和工具附录](platforms-and-tools.md#appendix)。
- 如果要使用 DSVM，请在 Azure 中创建和配置 Windows 或 Linux DSVM。 有关详细信息和说明，请参阅[Data Science Virtual Machine 文档](/azure/machine-learning/data-science-virtual-machine/)。
- 对于安装在计算机上的 Windows DSVM、 [Git 凭据管理器（GCM）](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) 。 在*README.md*文件中，向下滚动到 "**下载并安装**" 部分，然后选择**最新的安装程序**。 从 "安装程序" 页下载 *.exe*安装程序并运行它。 
- 对于 Linux DSVM，在 DSVM 上设置 SSH 公钥，并将其添加到 Azure DevOps 中。 有关详细信息和说明，请参阅[平台和工具附录](platforms-and-tools.md#appendix)中的**创建 SSH 公钥**部分。 

首先，将存储库复制或*克隆*到本地计算机。 
   
1. 在 " **GroupCommon**项目**摘要**" 页上，选择 "**存储库**"，然后在页面顶部选择要克隆的存储库。
   
1. 在 "存储库" 页上，选择右上角的 "**克隆**"。
   
1. 在 "**克隆存储库**" 对话框中，选择 " **HTTPS**用于 HTTP 连接 **" 或 "ssh"** 作为 Ssh 连接，然后将 "**命令行**" 下的克隆 URL 复制到剪贴板。
   
   ![克隆存储库](./media/group-manager-tasks/clone.png)
   
1. 在本地计算机上，创建以下目录：
   
   - 对于 Windows：**C:\GitRepos\GroupCommon**
   - 对于 Linux，主目录上的 **$/GitRepos/GroupCommon** 
   
1. 更改为你创建的目录。
   
1. 在 Git Bash 中运行命令 `git clone <clone URL>.`
   
   例如，以下命令之一将**GroupUtilities**存储库克隆到本地计算机上的*GroupCommon*目录。 
   
   **HTTPS 连接：**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH 连接：**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

在存储库的本地克隆中进行任何所需的更改后，可以将更改推送到共享组公用存储库。 

从本地**GroupProjectTemplate**或**GroupUtilities**目录运行以下 Git Bash 命令。

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> 如果这是您第一次提交到 Git 存储库，则在运行该`git commit`命令之前，您可能需要配置全局参数*user.name*和*user。* 运行以下两个命令：
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> 如果要提交到多个 Git 存储库，请使用相同的名称和电子邮件地址。 在构建 Power BI 仪表板来跟踪多个存储库中的 Git 活动时，使用相同的名称和电子邮件地址是非常方便的。

## <a name="add-group-members-and-configure-permissions"></a>添加组成员并配置权限

将成员添加到组：

1. 在 Azure DevOps 中，从**GroupCommon**项目主页的左侧导航栏中选择 "**项目设置**"。 
   
1. 从**项目设置**左侧导航栏中选择 **"团队**"，然后在 "**团队**" 页上，选择 " **GroupCommon" 团队**。 
   
   ![配置团队](./media/group-manager-tasks/teams.png)
   
1. 在 "**团队配置文件**" 页上，选择 "**添加**"。
   
   ![添加到 GroupCommon 团队](./media/group-manager-tasks/add-to-team.png)
   
1. 在 "**添加用户和组**" 对话框中，搜索并选择要添加到组的成员，然后选择 "**保存更改**"。 
   
   ![添加用户和组](./media/group-manager-tasks/add-users.png)
   

为成员配置权限：

1. 从**项目设置**左侧导航窗格中，选择 "**权限**"。 
   
1. 在 "**权限**" 页上，选择要向其中添加成员的组。 
   
1. 在该组的页上，选择 "**成员**"，然后选择 "**添加**"。 
   
1. 在 "**邀请成员**" 弹出窗口中，搜索并选择要添加到组的成员，然后选择 "**保存**"。 
   
   ![向成员授予权限](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>后续步骤

下面是团队数据科学过程中其他角色和任务的详细说明的链接：

- [数据科学团队的团队主管任务](team-lead-tasks.md)
- [数据科学团队的项目主管任务](project-lead-tasks.md)
- [为数据科学团队项目单独的参与者任务](project-ic-tasks.md)
