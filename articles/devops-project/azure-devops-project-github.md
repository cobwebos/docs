---
title: 使用 Azure DevOps 项目为现有的代码创建 CI/CD 管道 | VSTS 教程
description: 可以通过 DevOps 项目轻松地在 Azure 上开始操作。 有了它，就可以使用自己的代码和 GitHub 存储库快速启动所选 Azure 服务上的应用。
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 192992917432a64c2f9f81761e22bf7d9205703a
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205555"
---
# <a name="create-a-cicd-pipeline-for-your-existing-code-with-the-azure-devops-project"></a>使用 Azure DevOps 项目为现有的代码创建 CI/CD 管道

Azure DevOps 项目提供简化的体验，既可使用现有的代码和 Git 存储库，也可选择一个示例应用程序，以便创建连接到 Azure 的持续集成 (CI) 和持续交付 (CD) 管道。

将能够：

> [!div class="checklist"]
> * 创建 Azure DevOps 项目
> * 配置到 GitHub 存储库的访问权限并选择一个框架
> * 配置 VSTS 和 Azure 订阅 
> * 提交对 GitHub 所做的更改并将其自动部署到 Azure
> * 检查 VSTS CI/CD 管道
> * 配置 Azure Application Insights 监视

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 可以通过 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 免费获取一个。
* 访问 GitHub 或外部 Git 存储库，其中包含 .NET、Java、PHP、Node、Python 或静态 Web 代码。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

Azure DevOps 项目在 VSTS 中创建 CI/CD 管道。  可以创建**新 VSTS** 帐户，也可以使用**现有帐户**。  Azure DevOps 项目也在所选的 **Azure 订阅**中创建 **Azure 资源**。

1. 登录到 [Microsoft Azure 门户](https://portal.azure.com)。

1. 在左侧导航栏中选择“+ 新建”图标，然后搜索“DevOps 项目”。  选择“创建”。

    ![启动持续交付](_img/azure-devops-project-github/fullbrowser.png)

1. 选择“自带代码”。  完成后，选择“下一步”。

## <a name="configure-access-to-your-github-repository-and-choose-a-framework"></a>配置到 GitHub 存储库的访问权限并选择一个框架

1. 选择“GitHub”。  也可选择“外部 Git 存储库”。  选择“存储库”以及包含应用程序的“分库”。

1. 选择你的 **Web 框架**。  完成后，选择“下一步”。

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

1. 在前述步骤中选择的应用程序框架规定了此处提供的 Azure 服务部署目标的类型。  选择所要的**目标服务**。  完成后，选择“下一步”。

## <a name="configure-vsts-and-an-azure-subscription"></a>配置 VSTS 和 Azure 订阅 

1. 创建**新的** VSTS 帐户，或者选择**现有的**帐户。  为 VSTS 项目选择一个**名称**。  选择 **Azure 订阅**、**位置**，然后为应用程序选择一个**名称**。  完成后，选择“完成”。

    ![输入 VSTS 信息](_img/azure-devops-project-github/vstsazureinfo.png)

1. **项目仪表板**会在数分钟内加载到 Azure 门户中。  首先会在 VSTS 帐户的存储库中设置示例应用程序，然后执行生成并将应用程序部署到 Azure。  可以通过此仪表板查看 GitHub **代码存储库**、**VSTS CI/CD 管道**以及 **Azure 中的应用程序**。  在仪表板的右侧，选择“浏览”即可查看正在运行的应用程序。

    ![仪表板视图](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Azure DevOps 项目会自动配置 CI 生成和发布触发器。  代码保留在 GitHub 或其他外部存储库中。  

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>提交对 GitHub 所做的更改并将其自动部署到 Azure 

现在可以与应用团队协作，通过 CI/CD 过程自动将最新工作部署到网站。  每次对 GitHub 存储库进行更改都会在 VSTS 中启动一个生成，而 VSTS Release Management 定义则执行到 Azure 的部署。

1.  对应用程序进行更改，并将更改**提交**到 GitHub 存储库。
2.  片刻之后，系统会在 VSTS 中启动生成。  可以使用 VSTS 帐户通过 DevOps 项目仪表板或浏览器监视生成状态。
3.  生成完成以后，请在浏览器中**刷新应用程序**，验证是否能够看到所做的更改。

## <a name="examine-the-vsts-cicd-pipeline"></a>检查 VSTS CI/CD 管道

Azure DevOps 项目已自动在 VSTS 帐户中配置完整的 VSTS CI/CD 管道。  根据需要浏览和自定义管道。  请执行以下步骤，自行熟悉 VSTS 生成和发布定义。

1. 在 Azure DevOps 项目仪表板**顶部**选择“生成管道”。  此链接会打开一个浏览器标签页，并打开新项目的 VSTS 生成定义。

1. 将鼠标光标移到“状态”字段旁边的生成定义的右侧。 选择显示的**省略号**。  此操作会打开一个菜单，可以在其中启动多项活动，例如将新生成排队、暂停某个生成、编辑生成定义。

1. 选择“编辑”。

1. 在此视图中，针对生成定义**检查各种任务**。  此生成会执行各种任务，例如从 Git 存储库提取源、还原依赖项、发布用于部署的输出。

1. 在生成定义的顶部，选择“生成定义名称”。

1. 将生成定义的**名称**更改为更具说明性的名称。  选择“保存和排队”，然后选择“保存”。

1. 在生成定义名称下，选择“历史记录”。  此时会看到最近针对生成所做的更改的审核线索。  VSTS 会记录对生成定义所做的任何更改，并可用于版本比较。

1. 选择“触发器”。  Azure DevOps 项目已自动创建一个 CI 触发器，每次向存储库提交内容都会启动新的生成。  可以选择在 CI 过程中包括或排除分库。

1. 选择“保留期”。  可以根据方案指定策略，以便保留或删除特定数目的生成。

1. 选择“生成和发布”，然后选择“发布”。  Azure DevOps 项目创建的 VSTS 发布定义可以用于管理到 Azure 的部署。

1. 在浏览器左侧，选择发布定义旁边的**省略号**，然后选择“编辑”。

1. 发布定义包含一个**管道**，用于定义发布过程。  在“项目”下选择“删除”。  在前述步骤中检查过的生成定义生成用于项目的输出。 

1. 在“删除”图标右侧，选择“持续部署触发器”。  此发布定义有一个启用的 CD 触发器，每次有新的生成项目可用时，此触发器就会执行部署。  也可选择禁用此触发器，这样一来，部署就需要手动执行。 

1. 在浏览器左侧选择“任务”。  这些任务是部署过程执行的活动。  在此示例中，已创建了一个用于将项目部署到 **Azure 应用服务**的任务。

1. 在浏览器右侧选择“查看发布”。  此视图显示发布历史记录。

1. 选择某个发布旁边的**省略号**，然后选择“打开”。  可以在此视图中浏览多个菜单，例如发布摘要、关联的工作项、测试。

1. 选择“提交”。  此视图显示与特定部署相关联的代码提交。 

1. 选择“日志”。  日志包含有关部署过程的有用信息。  日志可以在部署过程中以及部署以后查看。

## <a name="configure-azure-application-insights-monitoring"></a>配置 Azure Application Insights 监视

可以使用 Azure Application Insights 轻松监视应用程序的性能和使用情况。  Azure DevOps 项目已自动为应用程序配置 Application Insights 资源。  你可以根据需要进一步配置各种警报和监视功能。

1. 在 Azure 门户中导航到“Azure DevOps 项目”仪表板。  在仪表板的右下角，选择适用于应用的 **Application Insights** 链接。

1. 此时会在 Azure 门户中打开“Application Insights”边栏选项卡。  此视图包含应用的使用情况、性能和可用性监视信息。

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. 选择“时间范围”，然后选择“过去一小时”。  选择“更新”，对结果进行筛选。  现在可以看到过去 60 分钟的所有活动。  选择“x”退出时间范围。

1. 选择“警报”，然后选择“+ 添加指标警报”。  

1. 输入警报的**名称**。

1. 选择“源更改日期”对应的下拉列表。  选择你的应用服务资源。
<!-- Could you please confirm if this should be "Source Alter on" instead of "Source Alert on"? -->
1. 默认警报针对**服务器响应时间大于 1 秒**的情况。  若要检查各种警报指标，请选择“指标”下拉列表。  可以轻松地配置各种警报，以便改进应用的监视功能。

1. 选择“通过电子邮件所有者、参与者和读者进行通知”所对应的复选框。  也可以在警报触发时，通过执行 Azure 逻辑应用来执行其他操作。

1. 选择“确定”以创建警报。  稍后，警报会在仪表板上显示为活动状态。  **退出**“警报”区域，导航回“Application Insights”边栏选项卡。

1. 选择“可用性”，然后选择“+ 添加测试”。 

1. 输入一个**测试名称**，然后选择“创建”。  此时会创建简单的 ping 测试来验证应用程序的可用性。  数分钟后，测试结果可用，此时 Application Insights 仪表板会显示可用性状态。

## <a name="clean-up-resources"></a>清理资源

不再需要本快速入门中创建的 Azure 应用服务和相关的资源时，可以在 Azure DevOps 项目仪表板中使用“删除”功能将其删除。

## <a name="next-steps"></a>后续步骤

当你在本教程中配置 CI/CD 过程时，系统会在 VSTS 项目中自动创建生成和发布定义。 可以根据所在团队的需求修改这些生成和发布定义。 你已了解如何：

> [!div class="checklist"]
> * 创建 Azure DevOps 项目
> * 配置到 GitHub 存储库的访问权限并选择一个框架
> * 配置 VSTS 和 Azure 订阅 
> * 提交对 GitHub 所做的更改并将其自动部署到 Azure
> * 检查 VSTS CI/CD 管道
> * 配置 Azure Application Insights 监视

若要详细了解 VSTS 管道，请参阅以下教程：

> [!div class="nextstepaction"]
> [自定义 CD 过程](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)
