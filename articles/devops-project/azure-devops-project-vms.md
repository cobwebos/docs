---
title: 使用 Azure DevOps 项目将 ASP.NET 应用部署到 Azure 虚拟机 | VSTS 教程
description: 可以通过 DevOps 项目轻松地在 Azure 上开始操作。 可以通过 Azure DevOps 项目轻松地通过几个快速步骤将 ASP.NET 应用部署到 Azure 虚拟机。
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b5a9ce204f68d812da4dfcfebb18b79b9c70c6c9
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967315"
---
# <a name="tutorial--deploy-your-aspnet-app-to-azure-virtual-machines-with-the-azure-devops-project"></a>教程：使用 Azure DevOps 项目将 ASP.NET 应用部署到 Azure 虚拟机

Azure DevOps 项目提供简化的体验，既可使用现有的代码和 Git 存储库，也可选择一个示例应用程序，以便创建连接到 Azure 的持续集成 (CI) 和持续交付 (CD) 管道。  DevOps Project 会自动创建 Azure 资源（例如新的 Azure 虚拟机），在 VSTS 中创建和配置一种发布管道（其中包括用于 CI 的生成定义），为 CD 设置发布定义，然后创建适用于监视的 Azure Application Insights 资源。

将能够：

> [!div class="checklist"]
> * 创建适用于 ASP.NET 应用的 Azure DevOps 项目
> * 配置 VSTS 和 Azure 订阅 
> * 检查 VSTS CI 生成定义
> * 检查 VSTS CD Release Management 定义
> * 提交对 VSTS 所做的更改并将其自动部署到 Azure
> * 配置 Azure Application Insights 监视
> * 清理资源

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 可以通过 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 免费获取一个。

## <a name="create-an-azure-devops-project-for-an-aspnet-app"></a>创建适用于 ASP.NET 应用的 Azure DevOps 项目

Azure DevOps 项目在 VSTS 中创建 CI/CD 管道。  可以创建**新 VSTS** 帐户，也可以使用**现有帐户**。  Azure DevOps 项目也在所选的 **Azure 订阅**中创建 **Azure 资源**，例如虚拟机。

1. 登录到 [Microsoft Azure 门户](https://portal.azure.com)。

1. 在左侧导航栏中选择“+ 新建”图标，然后搜索“DevOps 项目”。  选择“创建”。

    ![启动持续交付](_img/azure-devops-project-github/fullbrowser.png)

1. 选择“.NET”，然后选择“下一步”。

1. 出现“选择应用程序框架”时，请选择“ASP.NET”，然后选择“下一步”。 

1. 在前述步骤中选择的应用程序框架规定了此处提供的 Azure 服务部署目标的类型。  选择“虚拟机”，然后选择“下一步”。

## <a name="configure-vsts-and-an-azure-subscription"></a>配置 VSTS 和 Azure 订阅

1. 创建**新的** VSTS 帐户，或者选择**现有的**帐户。  为 VSTS 项目选择一个**名称**。  

1. 选择 **Azure 订阅**。  可以选择“更改”链接，然后输入更多配置详细信息，例如更改 Azure 资源的位置。
 
1. 为新的 Azure 虚拟机资源输入**虚拟机名称**、**用户名**和**密码**，然后选择“完成”。

1. Azure 虚拟机就绪需要数分钟的时间。  首先会在 VSTS 帐户的存储库中设置示例 ASP.NET 应用程序，然后执行生成和发布，最后将应用程序部署到新创建的 Azure VM。  

    完成后，Azure DevOps **项目仪表板**会加载到 Azure 门户中。  也可直接从 **Azure 门户**中的“所有资源”直接导航到“Azure DevOps 项目仪表板”。  

    可以通过此仪表板查看 VSTS **代码存储库**、**VSTS CI/CD 管道**以及 **Azure 中正在运行的应用程序**。    

    ![仪表板视图](_img/azure-devops-project-vms/dashboardnopreview.png)

1. Azure DevOps 项目会自动配置 CI 生成和发布触发器，以便自动部署对存储库所做的任何代码更改。  可以进一步在 VSTS 中配置其他选项。  在仪表板的右侧，选择“浏览”即可查看正在运行的应用程序。
    
## <a name="examine-the-vsts-ci-build-definition"></a>检查 VSTS CI 生成定义

Azure DevOps 项目会自动在 VSTS 帐户中配置完整的 VSTS CI/CD 管道。  可以浏览和自定义此管道。  请执行以下步骤，自行熟悉 VSTS 生成定义。

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

Azure DevOps 项目自动创建和配置从 VSTS 帐户部署到 Azure 订阅所需的步骤。  这些步骤包括配置 Azure 服务连接，以便向 Azure 订阅进行 VSTS 的身份验证。  此自动化还创建 VSTS 发布定义，以便为 Azure 虚拟机提供 CD。  请执行以下步骤，以便详细了解 VSTS 发布定义。

1. 选择“生成和发布”，然后选择“发布”。  Azure DevOps 项目创建的 VSTS 发布定义可以用于管理到 Azure 的部署。

1. 在浏览器左侧，选择发布定义旁边的**省略号**，然后选择“编辑”。

1. 发布定义包含一个**管道**，用于定义发布过程。  在“项目”下选择“删除”。  在前述步骤中检查过的生成定义生成用于项目的输出。 

1. 在“删除”图标右侧，选择“持续部署触发器”**图标**（显示为闪电）。此发布定义有一个启用的 CD 触发器。  每次有新的生成项目可用时，此触发器就会启动一个部署。  也可选择禁用此触发器，这样一来，部署就需要手动执行。 

1. 在浏览器左侧选择“任务”，然后选择你的**环境**。  

1. 这些任务是部署过程执行的活动，按**阶段**分组。  此发布定义有两个**阶段**。  第一阶段包含一个“Azure 资源组部署”任务，该任务配置用于部署的 VM 并将新 VM 添加到某个 **VSTS 部署组**。  VSTS 中的 VM 部署组管理**部署目标**计算机的逻辑组。

1. 第二阶段创建一个“IIS Web 应用管理”任务，以便在 VM 上创建 IIS 网站。  另外还创建一个用于部署站点的“IIS Web 应用部署”任务。

1. 在浏览器右侧选择“查看发布”。  此视图显示发布历史记录。

1. 选择某个发布旁边的**省略号**，然后选择“打开”。  可以在此视图中浏览多个菜单，例如**发布摘要**、**关联的工作项**、**测试**。

1. 选择“提交”。  此视图显示与特定部署相关联的代码提交。 请对发布进行比较，查看不同部署之间的提交差异。

1. 选择“日志”。  日志包含有关部署过程的有用信息。  日志可以在部署过程中以及部署以后查看。

## <a name="commit-changes-to-vsts-and-automatically-deploy-to-azure"></a>提交对 VSTS 所做的更改并将其自动部署到 Azure 

现在可以与应用团队协作，通过 CI/CD 过程自动将最新工作部署到网站。  每次对 VSTS git 存储库进行更改都会在 VSTS 中启动一个生成，而 VSTS Release Management 定义则执行到 Azure VM 的部署。  请执行以下步骤，或者使用其他方法提交对存储库所做的更改。  代码更改会启动 CI/CD 过程，并自动在 Azure VM 上部署对 IIS 网站所做的新更改。

1. 在 VSTS 菜单中选择“代码”，然后导航到存储库。

1. 导航到 **Views\Home** 目录，选择 **Index.cshtml** 文件旁边的**省略号**，然后选择“编辑”。

1. 对文件进行更改，例如更改某个 **div 标记**中的某些文本。  在右上角选择“提交”。  再次选择“提交”，推送所做的更改。 

1. 片刻之后，系统会**在 VSTS 中启动生成**，然后执行发布以部署所做的更改。  使用 VSTS 帐户通过 DevOps 项目仪表板或浏览器监视**生成状态**。

1. 发布完成以后，请在浏览器中**刷新应用程序**，验证是否能够看到所做的更改。

## <a name="configure-azure-application-insights-monitoring"></a>配置 Azure Application Insights 监视

可以使用 Azure Application Insights 轻松监视应用程序的性能和使用情况。  Azure DevOps 项目已自动为应用程序配置 Application Insights 资源。  你可以根据需要进一步配置各种警报和监视功能。

1. 在 **Azure 门户**中导航到“Azure DevOps 项目”仪表板。  在仪表板的右下角，选择适用于应用的 **Application Insights** 链接。

1. 此时会在 Azure 门户中打开“Application Insights”边栏选项卡。  此视图包含应用的使用情况、性能和可用性监视信息。

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. 选择“时间范围”，然后选择“过去一小时”。  选择“更新”，对结果进行筛选。  现在可以看到过去 60 分钟的所有活动。  选择“x”退出时间范围。

1. 可以在仪表板顶部附近找到“警报”和一些其他的有用链接。  选择“警报”，然后选择“+ 添加指标警报”。

1. 输入警报的**名称**。

1. 默认警报针对**服务器响应时间大于 1 秒**的情况。  若要检查各种警报指标，请选择“指标”下拉列表。  例如，可以为 ASP.NET 应用配置“ASP.NET 请求执行时间”。  可以轻松地配置各种警报，以便改进应用的监视功能。

1. 选择“通过电子邮件所有者、参与者和读者进行通知”所对应的复选框。  也可以在警报触发时，通过执行 Azure 逻辑应用来执行其他操作。

1. 选择“确定”以创建警报。  稍后，警报会在仪表板上显示为活动状态。  **退出**“警报”区域，导航回“Application Insights”边栏选项卡。

1. 选择“可用性”，然后选择“+ 添加测试”。 

1. 输入一个**测试名称**，然后选择“创建”。  这样会创建简单的 ping 测试来验证应用程序的可用性。  数分钟后，测试结果可用，此时 Application Insights 仪表板会显示可用性状态。

## <a name="clean-up-resources"></a>清理资源

 > [!NOTE]
 > 以下步骤会永久删除资源。  在使用此功能之前，必须仔细阅读提示。

若在进行测试，可以清除资源，避免产生账单费用。  不再需要本教程中创建的 Azure 虚拟机和相关的资源时，可以在 Azure DevOps 项目仪表板中使用“删除”功能将其删除。  **请小心操作**，因为删除功能会破坏 Azure DevOps 项目在 Azure 和 VSTS 中创建的数据，而这些数据一旦删除就无法检索。

1. 从 **Azure 门户**导航到 **Azure DevOps 项目**。
2. 在仪表板的**右上角**，选择“删除”。  阅读提示以后，请选择“是”以**永久删除**这些资源。

## <a name="next-steps"></a>后续步骤

可以选择根据所在团队的需求修改这些生成和发布定义。 也可以将此 CI/CD 模式用作其他项目的模板。  你已了解如何：

> [!div class="checklist"]
> * 创建适用于 ASP.NET 应用的 Azure DevOps 项目
> * 配置 VSTS 和 Azure 订阅 
> * 检查 VSTS CI 生成定义
> * 检查 VSTS CD Release Management 定义
> * 提交对 VSTS 所做的更改并将其自动部署到 Azure
> * 配置 Azure Application Insights 监视
> * 清理资源

若要详细了解 VSTS 管道，请参阅以下教程：

> [!div class="nextstepaction"]
> [自定义 CD 过程](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
