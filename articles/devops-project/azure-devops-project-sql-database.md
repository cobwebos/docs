---
title: 教程：使用 Azure DevOps Projects 部署 ASP.NET 应用和 Azure SQL 数据库代码
description: 可以通过 DevOps Projects 轻松地在 Azure 上开始操作。 借助 DevOps Projects，可以通过几个快速步骤部署 ASP.NET 应用和 Azure SQL 数据库代码。
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 560b16a40d73d07df6c9f446c7196cd930e2274e
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971503"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-projects"></a>教程：使用 Azure DevOps Projects 部署 ASP.NET 应用和 Azure SQL 数据库代码

Azure DevOps Projects 提供一种简化的体验，你在其中既可使用现有的代码和 Git 存储库，也可选择一个示例应用程序，以便创建连接到 Azure 的持续集成 (CI) 和持续交付 (CD) 管道。 

DevOps Projects 还可以：
* 自动创建 Azure 资源，例如 Azure SQL 数据库。
* 在 Azure Pipelines 中创建并配置一个发布管道，其中包括用于 CI 的生成管道。
* 为 CD 设置发布管道。 
* 创建用于监视的 Azure Application Insights 资源。

在本教程中，将：

> [!div class="checklist"]
> * 使用 Azure DevOps Projects 部署 ASP.NET 应用和 Azure SQL 数据库代码
> * 配置 Azure DevOps 和 Azure 订阅 
> * 检查 CI 管道
> * 检查 CD 管道
> * 提交对 Azure Repos 所做的更改并将其自动部署到 Azure
> * 连接到 Azure SQL 数据库 
> * 清理资源

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 可以通过 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 免费获取一个。

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>在 DevOps Projects 中为 ASP.NET 应用和 Azure SQL 数据库创建项目

DevOps Projects 在 Azure Pipelines 中创建 CI/CD 管道。 可以创建新的 Azure DevOps 组织，或使用现有的组织。 DevOps Projects 还可在所选的 Azure 订阅中创建 Azure 资源，例如 Azure SQL 数据库。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左窗格中，选择“创建资源”  。

1. 在搜索框中键入 **DevOps Projects**，然后选择“创建”。 

    ![DevOps Projects 仪表板](_img/azure-devops-project-github/fullbrowser.png)

1. 依次选择“.NET”、“下一步”。  

1. 在“选择应用程序框架”下，选择“ASP.NET”。  

1. 依次选择“添加数据库”、“下一步”。    
    在前述步骤中选择的应用程序框架规定了此处可用的 Azure 服务部署目标的类型。 
    
1. 选择“**下一步**”。

## <a name="configure-azure-devops-and-an-azure-subscription"></a>配置 Azure DevOps 和 Azure 订阅

1. 创建新的 Azure DevOps 组织，或选择现有的组织。 

1. 输入 Azure DevOps 项目的名称。 

1. 选择你的 Azure 订阅服务。  
    （可选）若要查看其他 Azure 配置设置并确定“数据库服务器登录详细信息”部分的用户名，可以选择“更改”。   存储该用户名，以便在本教程的后续步骤中使用。 如果执行此可选步骤，请退出 Azure 配置区域，然后选择“完成”。 
 
1. 选择“完成”  。  
    几分钟后，该过程将会完成，DevOps Projects 仪表板将在 Azure 门户中打开。 也可以从 Azure 门户中的“所有资源”直接导航到仪表板。  在右侧，选择“浏览”可查看正在运行的应用程序。 
    
## <a name="examine-the-ci-pipeline"></a>检查 CI 管道

DevOps Projects 会自动在 Azure Repos 中配置一个完整的 CI/CD 管道。 可以浏览和自定义此管道。 若要了解 Azure DevOps 生成管道，请执行以下操作：

1. 在 DevOps Projects 仪表板顶部，选择“生成管道”。   
    浏览器标签页会显示新项目的生成管道。

1. 指向“状态”字段，然后选择省略号 (...)。   
    菜单中会显示多个选项，例如，将新生成排队、暂停某个生成，以及编辑生成管道。

1. 选择“编辑”  。

1. 在此窗格中，可以检查生成管道的各种任务。  
    该生成会执行各种任务，例如，从 Git 存储库提取源、还原依赖项、发布用于部署的输出。

1. 在生成管道的顶部，选择生成管道名称。

1. 将生成管道的名称更改为更具描述性的名称，选择“保存并排队”，然后选择“保存”。  

1. 在生成管道名称下，选择“历史记录”。   
    此窗格显示最近针对生成所做的更改的审核线索。 Azure Pipelines 会跟踪对生成管道所做的任何更改，并允许进行版本比较。

1. 选择“触发器”。   
    DevOps Projects 会自动创建一个 CI 触发器，每次向存储库提交内容都会启动新的生成。 （可选）可以选择在 CI 过程中包括或排除分支。

1. 选择“保留期”。   
    可以根据方案指定策略，以保留或删除特定数目的生成。

## <a name="examine-the-cd-pipeline"></a>检查 CD 管道

DevOps Projects 会自动创建并配置从 Azure DevOps 组织部署到 Azure 订阅所要执行的步骤。 这些步骤包括配置 Azure 服务连接，以便在 Azure 订阅中进行 Azure DevOps 身份验证。 自动化还会创建一个 CD 管道，用于提供到 Azure 虚拟机的 CD。 若要详细了解 Azure DevOps CD 管道，请执行以下操作：

1. 依次选择“生成和发布”、“发布”。    
    DevOps Projects 会创建一个发布管道，用于管理到 Azure 的部署。

1. 选择发布管道旁边的省略号 (...)，然后选择“编辑”。   
    发布管道包含一个*管道*，用于定义发布过程。

1. 在“项目”下选择“删除”   。  
    在前述步骤中检查过的生成管道会生成用于项目的输出。 

1. 在“删除”图标的右侧，选择“持续部署触发器”。    
    此发布管道有一个已启用的 CD 触发器，每次有新的生成项目可用时，此触发器就会执行部署。 （可选）可以禁用此触发器，这样就需要手动执行部署。 

    DevOps Projects 会设置一个随机 SQL 密码并将它用于发布管道。
    
1. 在左侧选择“变量”。  

   > [!NOTE]
   > 仅当更改了 SQL Server 密码时，才执行以下步骤。 有一个 password 变量。
  
1. 在“值”框的旁边选择挂锁图标，输入新密码，然后选择“保存”。  

1. 在左侧选择“任务”，然后选择你的环境。   
    任务是部署过程执行的活动，已按阶段分组。 此发布管道只有一个阶段，此阶段包含“Azure 应用服务部署”和“Azure SQL 数据库部署”任务。  

1. 选择“执行 Azure SQL”任务，并检查用于 SQL 部署的各个属性。   
    在“部署包”下，该任务使用 *SQL DACPAC* 文件。 

1. 在右侧选择“查看发布”，以显示发布历史记录。 

1. 选择某个发布旁边的省略号 (...)，然后选择“打开”。   
     可以浏览多个菜单，例如发布摘要、关联的工作项和测试。

1. 选择“提交”。   
     此视图显示与此部署关联的代码提交。 请对发布进行比较，查看不同部署之间的提交差异。

1. 选择“日志”。   
     日志包含有关部署过程的有用信息。 可以在部署期间和之后查看日志。

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>提交对 Azure Repos 所做的更改并将其自动部署到 Azure 

 > [!NOTE]
 > 以下过程通过进行简单的文本更改来测试 CI/CD 管道。 若要测试 SQL 部署过程，可以选择性地对表进行 SQL Server 架构更改。

现在，可以使用可将最新工作部署到网站的 CI/CD 过程，与某个团队展开应用协作。 每次对 Git 存储库进行更改都会在 Azure DevOps 中启动一个生成，CD 管道可以执行到 Azure 的部署。 遵循本部分所述的过程，或使用其他方法提交对存储库所做的更改。 代码更改会启动 CI/CD 过程，并自动将更改部署到 Azure。

1. 在左窗格中选择“代码”，然后转到你的存储库。 

1. 转到 *SampleWebApplication\Views\Home* 目录，选择 *Index.cshtml* 文件旁边的省略号 (...)，然后选择“编辑”。  

1. 对该文件进行更改，例如，在某个 div 标记内部添加一些文本。 

1. 在右上角选择“提交”，然后再次选择“提交”以推送更改。    
    片刻之后，某个生成会在 Azure DevOps 中启动，并且某个发布将会执行以部署更改。 使用 Azure DevOps 组织通过 DevOps Projects 仪表板或浏览器监视生成状态。

1. 发布完成后，刷新应用程序以验证更改。

## <a name="connect-to-the-azure-sql-database"></a>连接到 Azure SQL 数据库

需要相应的权限才能连接到 Azure SQL 数据库。

1. 在 DevOps Projects 仪表板中选择“SQL 数据库”，转到 SQL 数据库的管理页。 
   
1. 依次选择“设置服务器防火墙”、“添加客户端 IP”。   

1. 选择“保存”。   
    现在，该客户端 IP 可以访问 SQL Server Azure 资源。

1. 返回到“SQL 数据库”窗格。  

1. 在右侧选择服务器名称，导航到 **SQL Server** 的配置页。

1. 选择“重置密码”，输入 SQL Server 管理员登录名的密码，然后选择“保存”。    
    请务必保存此密码，因为本教程稍后需要用到。

    现在可以选择性地使用客户端工具（例如 SQL Server Management Studio 或 Visual Studio）连接到 SQL Server 和 Azure SQL 数据库。 使用“服务器名称”属性进行连接。 

    如果最初在 DevOps Projects 中配置项目时未更改数据库用户名，则用户名为电子邮件地址的本地部分。 例如，如果电子邮件地址为“johndoe\@microsoft.com”  ，则用户名为“johndoe”  。

   > [!NOTE]
   > 如果更改了 SQL 登录名的密码，则必须根据“检查 CD 管道”部分中所述，在发布管道变量中更改密码。

## <a name="clean-up-resources"></a>清理资源

测试后，可以清理资源，以避免产生费用。 不再需要本教程中创建的 Azure SQL 数据库和相关资源时，可将其删除。 为此，可以使用 DevOps Projects 仪表板上的“删除”功能。 

> [!IMPORTANT]
> 以下过程会永久删除资源。 “删除”功能会销毁 DevOps Projects 中的项目在 Azure 和 Azure DevOps 中创建的数据，删除后无法检索这些数据。  只能在仔细阅读提示后才使用此过程。

1. 在 Azure 门户中，转到 DevOps Projects 仪表板。
2. 在右上角选择“删除”  。 
3. 出现提示时，请选择“是”以永久删除这些资源。  

## <a name="next-steps"></a>后续步骤

可以选择根据所在团队的需求修改这些生成和发布管道。 也可以将此 CI/CD 模式用作其他管道的模板。 本教程介绍了如何：

> [!div class="checklist"]
> * 使用 Azure DevOps Projects 部署 ASP.NET 应用和 Azure SQL 数据库代码
> * 配置 Azure DevOps 和 Azure 订阅 
> * 检查 CI 管道
> * 检查 CD 管道
> * 提交对 Azure Repos 所做的更改并将其自动部署到 Azure
> * 连接到 Azure SQL 数据库 
> * 清理资源

若要详细了解 CI/CD 管道，请参阅：

> [!div class="nextstepaction"]
> [Define your multi-stage continuous deployment (CD) pipeline](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)（定义多阶段持续部署 (CD) 管道）

## <a name="videos"></a>视频

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]
