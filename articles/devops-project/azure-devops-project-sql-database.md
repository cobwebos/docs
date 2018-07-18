---
title: 使用 Azure DevOps 项目部署 ASP.NET 应用和 Azure SQL 数据库 | VSTS 教程
description: 可以通过 DevOps 项目轻松地在 Azure 上开始操作。 可以通过 Azure DevOps 项目轻松地通过几个快速步骤部署 ASP.NET 和 Azure SQL 数据库。
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: ba15a57f92ad58a46732444890d9e670fcc75cf3
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967298"
---
# <a name="tutorial--deploy-your-aspnet-app-and-azure-sql-database-with-the-azure-devops-project"></a>教程：使用 Azure DevOps 项目部署 ASP.NET 应用和 Azure SQL 数据库

Azure DevOps 项目提供简化的体验，既可使用现有的代码和 Git 存储库，也可选择一个示例应用程序，以便创建连接到 Azure 的持续集成 (CI) 和持续交付 (CD) 管道。  DevOps 项目会自动创建 Azure 资源（例如 Azure SQL 数据库），在 VSTS 中创建和配置一种发布管道（其中包括用于 CI 的生成定义），为 CD 设置发布定义，然后创建适用于监视的 Azure Application Insights 资源。

将能够：

> [!div class="checklist"]
> * 创建适用于 ASP.NET 应用和 Azure SQL 数据库的 Azure DevOps 项目
> * 配置 VSTS 和 Azure 订阅 
> * 检查 VSTS CI 生成定义
> * 检查 VSTS CD Release Management 定义
> * 提交对 VSTS 所做的更改并将其自动部署到 Azure
> * 连接到 Azure SQL Server 数据库 
> * 清理资源

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 可以通过 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 免费获取一个。

## <a name="create-an-azure-devops-project-for-an-aspnet-app-and-azure-sql-database"></a>创建适用于 ASP.NET 应用和 Azure SQL 数据库的 Azure DevOps 项目

Azure DevOps 项目在 VSTS 中创建 CI/CD 管道。  可以创建**新 VSTS** 帐户，也可以使用**现有帐户**。  Azure DevOps 项目也在所选的 **Azure 订阅**中创建 **Azure 资源**，例如 Azure SQL 数据库。

1. 登录到 [Microsoft Azure 门户](https://portal.azure.com)。

1. 在左侧导航栏中选择“创建资源”图标，然后搜索“DevOps 项目”。  选择“创建”。

    ![启动持续交付](_img/azure-devops-project-github/fullbrowser.png)

1. 选择“.NET”，然后选择“下一步”。

1. 出现“选择应用程序框架”时，请选择“ASP.NET”。

1. 选择“添加数据库”，然后选择“下一步”。  

1. 在前述步骤中选择的应用程序框架规定了此处提供的 Azure 服务部署目标的类型。  选择“**下一步**”。

## <a name="configure-vsts-and-an-azure-subscription"></a>配置 VSTS 和 Azure 订阅

1. 创建**新的** VSTS 帐户，或者选择**现有的**帐户。  为 VSTS 项目选择一个**名称**。  

1. 选择 **Azure 订阅**。

1. 可以选择“更改”链接，以便查看其他 Azure 配置设置，并确定“数据库服务器登录详细信息”部分的“用户名”。  **存储**此**用户名**，以便在本教程后面的步骤中使用。
 
1. 如果已执行上一步骤，请退出 Azure 配置区域，然后选择“完成”。  否则，请直接选择“完成”。

1. 此过程将需要数分钟才能完成。  完成后，Azure DevOps **项目仪表板**会加载到 Azure 门户中。  也可直接从 **Azure 门户**中的“所有资源”直接导航到“Azure DevOps 项目仪表板”。  在仪表板的右侧，选择“浏览”即可查看正在运行的应用程序。
    
## <a name="examine-the-vsts-ci-build-definition"></a>检查 VSTS CI 生成定义

Azure DevOps 项目会自动在 VSTS 帐户中配置完整的 VSTS CI/CD 管道。  可以浏览和自定义此管道。  请执行以下步骤，自行熟悉 VSTS 生成定义。

1. 导航到“Azure DevOps 项目仪表板”。

1. 在 **Azure DevOps 项目仪表板****顶部**选择“生成管道”。  此链接会打开一个浏览器标签页，并打开新项目的 VSTS 生成定义。

1. 将鼠标光标移到“状态”字段旁边的生成定义的右侧。 选择显示的**省略号**。  此操作会打开一个菜单，可以在其中执行多项活动，例如**将新生成排队**、**暂停某个生成**、**编辑生成定义**。

1. 选择“编辑”。

1. 在此视图中，针对生成定义**检查各种任务**。  此生成会执行各种任务，例如从 VSTS Git 存储库提取源、还原依赖项、发布用于部署的输出。

1. 在生成定义的顶部，选择“生成定义名称”。

1. 将生成定义的**名称**更改为更具说明性的名称。  选择“保存和排队”，然后选择“保存”。

1. 在生成定义名称下，选择“历史记录”。  此时会看到最近针对生成所做的更改的审核线索。  VSTS 会记录对生成定义所做的任何更改，并可用于版本比较。

1. 选择“触发器”。  Azure DevOps 项目已自动创建一个 CI 触发器，每次向存储库提交内容都会启动新的生成。  可以选择在 CI 过程中包括或排除分库。

1. 选择“保留期”。  可以根据方案指定策略，以便保留或删除特定数目的生成。

## <a name="examine-the-vsts-cd-release-management-definition"></a>检查 VSTS CD Release Management 定义

Azure DevOps 项目自动创建和配置从 VSTS 帐户部署到 Azure 订阅所需的步骤。  这些步骤包括配置 Azure 服务连接，以便向 Azure 订阅进行 VSTS 的身份验证。  此自动化还创建 VSTS 发布定义，该发布为 Azure 提供 CD。  请执行以下步骤，以便详细了解 VSTS 发布定义。

1. 选择“生成和发布”，然后选择“发布”。  Azure DevOps 项目创建的 VSTS 发布定义可以用于管理到 Azure 的部署。

1. 在浏览器左侧，选择发布定义旁边的**省略号**，然后选择“编辑”。

1. 发布定义包含一个**管道**，用于定义发布过程。  在“项目”下选择“删除”。  在前述步骤中检查过的生成定义生成用于项目的输出。 

1. 在“删除”图标右侧，选择“持续部署触发器”**图标**（显示为闪电）。此发布定义有一个启用的 CD 触发器。  每次有新的生成项目可用时，此触发器就会启动一个部署。  也可选择禁用此触发器，这样一来，部署就需要手动执行。 

1. Azure DevOps 项目已设置一个随机的 SQL 密码并将该密码用于发布定义。  在浏览器左侧选择“变量”。 

1. **只有在已更改 SQL Server 密码的情况下，才执行此步骤。**  有一个“密码”变量。  在“值”文本框的右侧选择“挂锁”图标。  **输入**新密码，然后选择“保存”。

1. 在浏览器左侧选择“任务”，然后选择你的**环境**。  

1. 这些任务是部署过程执行的活动，按**阶段**分组。  此发布定义有一个阶段。  此阶段包含“Azure 应用服务部署”和“Azure SQL 数据库部署”任务。

1. 选择“执行 Azure SQL”任务，并检查用于 SQL 部署的各种属性。  请注意，在“部署包”下，该任务使用 **SQL DACPAC 文件**。

1. 在浏览器右侧选择“查看发布”。  此视图显示发布历史记录。

1. 选择某个发布旁边的**省略号**，然后选择“打开”。  可以在此视图中浏览多个菜单，例如**发布摘要**、**关联的工作项**、**测试**。

1. 选择“提交”。  此视图显示与特定部署相关联的代码提交。 可以对发布进行比较，查看不同部署之间的提交差异。

1. 选择“日志”。  日志包含有关部署过程的有用信息。  日志可以在部署过程中以及部署以后查看。

## <a name="commit-changes-to-vsts-and-automatically-deploy-to-azure"></a>提交对 VSTS 所做的更改并将其自动部署到 Azure 

 > [!NOTE]
 > 下面的步骤通过简单的文本更改来测试 CI/CD 管道。  可以选择对表进行 SQL Server 架构更改，以便测试 SQL 部署过程。

现在可以与应用团队协作，通过 CI/CD 过程自动将最新工作部署到网站。  每次对 VSTS git 存储库进行更改都会在 VSTS 中启动一个生成，而 VSTS Release Management 定义则执行到 Azure 的部署。  请执行以下步骤，或者使用其他方法提交对存储库所做的更改。  代码更改会启动 CI/CD 过程，并自动将新更改部署到 Azure。

1. 在 VSTS 菜单中选择“代码”，然后导航到存储库。

1. 导航到 **SampleWebApplication\Views\Home** 目录，选择 **Index.cshtml** 文件旁边的**省略号**，然后选择“编辑”。

1. 对文件进行更改，例如更改某个 **div 标记**中的某些文本。  在右上角选择“提交”。  再次选择“提交”，推送所做的更改。 

1. 片刻之后，系统会**在 VSTS 中启动生成**，然后执行发布以部署所做的更改。  可以使用 VSTS 帐户通过 DevOps 项目仪表板或浏览器监视**生成状态**。

1. 发布完成以后，请在浏览器中**刷新应用程序**，验证是否能够看到所做的更改。

## <a name="connect-to-the-azure-sql-server-database"></a>连接到 Azure SQL Server 数据库

需要适当的权限才能连接到 Azure SQL 数据库。

1. 在 Azure DevOps 项目仪表板中选择“SQL 数据库”，以便导航到 SQL DB 的管理页。
   
1. 选择“设置服务器防火墙”，然后选择“+ 添加客户端 IP”。  

1. 选择“保存”。  客户端 IP 现在可以访问 **SQL Server Azure 资源**。

1. 导航回“SQL 数据库”边栏选项卡。 

1. 在屏幕右侧选择“服务器名称”，导航到 **SQL Server** 的配置页。

1. 选择“重置密码”，输入“SQL Server 管理员登录名”的密码，然后选择“保存”。  **保留**此密码，以便在本教程后面的步骤中使用。

1. 现在可以选择使用客户端工具（例如 **SQL Server Management Studio** 或 **Visual Studio**）连接到 Azure SQL Server 和数据库。  使用“服务器名称”属性进行连接。

   如果在一开始配置 DevOps 项目时没有更改 DB 用户名，则用户名为电子邮件地址的本地部分。  例如，如果电子邮件地址为 johndoe@microsoft.com，则用户名为 johndoe。

 > [!NOTE]
 > 如果更改 SQL 登录名的密码，则需在 VSTS 发布定义变量中更改该密码，如**检查 VSTS CD Release Management 定义**部分所述

## <a name="clean-up-resources"></a>清理资源

 > [!NOTE]
 > 以下步骤会永久删除资源。  在使用此功能之前，必须仔细阅读提示。

若在进行测试，可以清除资源，避免产生账单费用。  不再需要本教程中创建的 Azure SQL 数据库和相关的资源时，可以在 Azure DevOps 项目仪表板中使用“删除”功能将其删除。  **请小心操作**，因为删除功能会破坏 Azure DevOps 项目在 Azure 和 VSTS 中创建的数据，而这些数据一旦删除就无法检索。

1. 从 **Azure 门户**导航到 **Azure DevOps 项目**。
2. 在仪表板的**右上角**，选择“删除”。  阅读提示以后，请选择“是”以**永久删除**这些资源。

## <a name="next-steps"></a>后续步骤

可以选择根据所在团队的需求修改这些生成和发布定义。 也可以将此 CI/CD 模式用作其他项目的模板。  你已了解如何：

> [!div class="checklist"]
> * 创建适用于 ASP.NET 应用和 Azure SQL 数据库的 Azure DevOps 项目
> * 配置 VSTS 和 Azure 订阅 
> * 检查 VSTS CI 生成定义
> * 检查 VSTS CD Release Management 定义
> * 提交对 VSTS 所做的更改并将其自动部署到 Azure
> * 连接到 Azure SQL Server 数据库 
> * 清理资源

若要详细了解 VSTS 管道，请参阅以下教程：

> [!div class="nextstepaction"]
> [自定义 CD 过程](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>视频

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]