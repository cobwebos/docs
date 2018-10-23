---
title: 使用 Azure DevOps 项目将 ASP.NET Core 应用部署到 Azure Service Fabric | Azure DevOps Services 教程
description: 可以通过 DevOps 项目轻松地在 Azure 上开始操作。 可以通过 Azure DevOps 项目轻松地通过几个快速步骤将 ASP.NET Core 应用部署到 Azure Service Fabric。
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 6a0539b2213b99e09021a4f90d914172eac62560
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2018
ms.locfileid: "44300387"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-service-fabric-with-the-azure-devops-project"></a>教程：使用 Azure DevOps 项目将 ASP.NET Core 应用部署到 Azure Service Fabric

Azure DevOps 项目提供简化的体验，既可使用现有的代码和 Git 存储库，也可选择一个示例应用程序，以便创建连接到 Azure 的持续集成 (CI) 和持续交付 (CD) 管道。  DevOps 项目会自动创建 Azure 资源（例如 Azure Service Fabric），在 Azure DevOps 中创建和配置一种发布管道（其中包括设置 CI/CD 管道），然后创建适用于监视的 Azure Application Insights 资源。

将能够：

> [!div class="checklist"]
> * 创建适用于 ASP.NET Core 应用和 Service Fabric 的 Azure DevOps 项目
> * 配置 Azure DevOps Services 和 Azure 订阅 
> * 检查 CI 管道
> * 检查 CD 管道
> * 提交对 Git 所做的更改并将其自动部署到 Azure
> * 清理资源

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 可以通过 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 免费获取一个。

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-service-fabric"></a>创建适用于 ASP.NET Core 应用和 Service Fabric 的 Azure DevOps 项目

Azure DevOps 项目创建 CI/CD 管道。  可以创建**新的 Azure DevOps Services** 组织，也可以使用**现有的组织**。  Azure DevOps 项目也在所选的 **Azure 订阅**中创建 **Azure 资源**，例如 Service Fabric 群集。

1. 登录到 [Microsoft Azure 门户](https://portal.azure.com)。

1. 在左侧导航栏中选择“创建资源”图标，然后搜索“DevOps 项目”。  选择“创建”。

    ![启动持续交付](_img/azure-devops-project-service-fabric/fullbrowser.png)

1. 选择“.NET”，然后选择“下一步”。

1. 出现“选择应用程序框架”时，请选择“ASP.NET Core”，然后选择“下一步”。

1. 选择“Service Fabric 群集”，然后选择“下一步”。  

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>配置 Azure DevOps Services 和 Azure 订阅

1. 创建**新的** Azure DevOps Services 组织，或者选择**现有的**组织。  为 Azure DevOps 项目选择一个**名称**。  

1. 选择 **Azure 订阅**。

1. 选择“更改”链接，以便查看其他 Azure 配置设置，并确定适用于 **Service Fabric 群集**的**节点虚拟机大小**和**操作系统**。  这里有各种选项，用于配置 Azure 服务的类型和位置。
 
1. 退出 Azure 配置区域，然后选择“完成”。

1. 此过程将需要数分钟才能完成。  首先会在 Azure DevOps Services 组织的存储库中设置示例 ASP.NET Core 应用程序，然后创建 Service Fabric 群集并执行 CI/CD 管道，最后将应用程序部署到 Azure。  

    完成后，Azure DevOps **项目仪表板**会加载到 Azure 门户中。  也可直接从 **Azure 门户**中的“所有资源”直接导航到“Azure DevOps 项目仪表板”。  

    可以通过此仪表板查看 Azure DevOps Services **代码存储库**、**Azure DevOps Services CI/CD 管道**以及 **Service Fabric 群集**。  可以进一步在 Azure DevOps Services 中配置其他选项。  在仪表板的右侧，选择“浏览”即可查看正在运行的应用程序。

## <a name="examine-the-azure-devops-services-ci-pipeline"></a>检查 Azure DevOps Services CI 管道

Azure DevOps 项目自动在 Azure DevOps Services 组织中配置 Azure DevOps Services CI/CD 管道。  可以浏览和自定义此管道。  请执行以下步骤，自行熟悉 Azure DevOps Services 生成管道。

1. 导航到“Azure DevOps 项目仪表板”。

1. 在 **Azure DevOps 项目仪表板** **顶部**选择“生成管道”。  此链接会打开一个浏览器标签页，并打开新项目的 Azure DevOps Services 生成管道。

1. 将鼠标光标移到“状态”字段旁边的生成管道的右侧。 选择显示的**省略号**。  此操作会打开一个菜单，可以在其中启动多项活动，例如**将新生成排队**、**暂停某个生成**、**编辑生成管道**。

1. 选择“编辑”。

1. 在此视图中，请针对生成管道**检查各种任务**。  此生成会执行各种任务，例如从 Azure DevOps Services Git 存储库提取源、还原依赖项、发布用于部署的输出。

1. 在生成管道的顶部，选择“生成管道名称”。 

1. 在生成管道名称下，选择“历史记录”。  此时会看到最近针对生成所做的更改的审核线索。  Azure DevOps Services 会记录对生成管道所做的任何更改，并可用于版本比较。

1. 选择“触发器”。  Azure DevOps 项目已自动创建一个 CI 触发器，每次向存储库提交内容都会启动新的生成。  可以选择在 CI 过程中包括或排除分库。

1. 选择“保留期”。  可以根据方案指定策略，以便保留或删除特定数目的生成。

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>检查 Azure DevOps Services CD 管道

Azure DevOps 项目自动创建和配置从 Azure DevOps Services 组织部署到 Azure 订阅所需的步骤。  这些步骤包括配置 Azure 服务连接，以便向 Azure 订阅进行 Azure DevOps Services 身份验证。  此自动化还创建 Azure DevOps Services 发布管道，该发布为 Azure 提供 CD。  请执行以下步骤，以便详细了解 Azure DevOps Services 发布管道。

1. 选择“生成和发布”，然后选择“发布”。  Azure DevOps 项目创建的 Azure DevOps Services 发布管道可以用于管理到 Azure 的部署。

1. 在浏览器左侧，选择发布管道旁边的**省略号**，然后选择“编辑”。

1. 发布管道包含一个**管道**，用于定义发布过程。  在“项目”下选择“删除”。  在前述步骤中检查过的生成管道生成用于项目的输出。 

1. 在“删除”图标右侧，选择“持续部署触发器”**图标**（显示为闪电）。此发布管道有一个启用的 CD 触发器。  每次有新的生成项目可用时，此触发器就会启动一个部署。  也可选择禁用此触发器，这样一来，部署就需要手动执行。 

1. 在浏览器右侧选择“查看发布”。  此视图显示发布历史记录。

1. 选择某个发布旁边的**省略号**，然后选择“打开”。  可以在此视图中浏览多个菜单，例如**发布摘要**、**关联的工作项**、**测试**。

1. 选择“提交”。  此视图显示与特定部署相关联的代码提交。 可以对发布进行比较，查看不同部署之间的提交差异。

1. 选择“日志”。  日志包含有关部署过程的有用信息。  日志可以在部署过程中以及部署以后查看。

## <a name="commit-changes-to-git-and-automatically-deploy-to-azure"></a>提交对 Git 所做的更改并将其自动部署到 Azure 

 > [!NOTE]
 > 下面的步骤通过对 Web 应用进行简单的文本更改来测试 CI/CD 管道。

现在可以与应用团队协作，通过 CI/CD 过程自动将最新工作部署到网站。  每次对 git 存储库进行更改都会启动一个生成，而版本则会将更改部署到 Azure。  请执行以下步骤，或者使用其他方法提交对存储库所做的更改。  例如，可以在常用工具或 IDE 中**克隆** Git 存储库，然后将更改推送到该存储库。

1. 在 Azure DevOps Services 菜单中选择“代码”，然后选择“文件”，导航到存储库。
1. 导航到 **Views\Home** 目录，选择 **Index.cshtml** 文件旁边的**省略号**，然后选择“编辑”。

1. 对文件进行更改，例如更改某个 **div 标记**中的某些文本。  在右上角选择“提交”。  再次选择“提交”，推送所做的更改。 

1. 片刻之后，系统会**启动生成**，然后执行发布以部署所做的更改。  可以使用 Azure DevOps Services 实时日志记录通过 DevOps 项目仪表板或浏览器监视**生成状态**。

1. 发布完成以后，请在浏览器中**刷新应用程序**，验证是否能够看到所做的更改。

## <a name="clean-up-resources"></a>清理资源

 > [!NOTE]
 > 以下步骤会永久删除资源。  在使用此功能之前，必须仔细阅读提示。

若在进行测试，可以清除资源，避免产生账单费用。  不再需要本教程中创建的 Azure Service Fabric 群集和相关的资源时，可以在 Azure DevOps 项目仪表板中使用“删除”功能将其删除。  **请小心操作**，因为删除功能会破坏 Azure DevOps 项目在 Azure 和 Azure DevOps Services 中创建的数据，而这些数据一旦删除就无法检索。

1. 从 **Azure 门户**导航到 **Azure DevOps 项目**。
2. 在仪表板的**右上角**，选择“删除”。  阅读提示以后，请选择“是”以**永久删除**这些资源。

## <a name="next-steps"></a>后续步骤

可以选择根据所在团队的需求修改 Azure CI/CD 管道。 也可以将此 CI/CD 模式用作其他项目的模板。  你已了解如何：

> [!div class="checklist"]
> * 创建适用于 ASP.NET Core 应用和 Service Fabric 的 Azure DevOps 项目
> * 配置 Azure DevOps Services 和 Azure 订阅 
> * 检查 CI 管道
> * 检查 CD 管道
> * 提交对 Git 所做的更改并将其自动部署到 Azure
> * 清理资源

若要详细了解 Service Fabric 和微服务，请参阅以下文章：

> [!div class="nextstepaction"]
> [使用微服务方法生成应用程序](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
