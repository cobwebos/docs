---
title: 教程：使用 Azure DevOps Projects 为现有的代码创建 CI/CD 管道
description: 可以通过 Azure DevOps Projects 轻松地在 Azure 上开始操作。 有了 DevOps Projects，就可以使用自己的代码和 GitHub 存储库快速启动所选 Azure 服务上的应用。
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
ms.openlocfilehash: 88ee15a3b5cc53542d9e098dee485b8a526bb9a6
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52161746"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>教程：使用 Azure DevOps Projects 为现有的代码创建 CI/CD 管道

Azure DevOps Projects 提供一种简化的体验，你在其中既可使用现有的代码和 Git 存储库，也可选择一个示例应用程序，以便创建连接到 Azure 的持续集成 (CI) 和持续交付 (CD) 管道。

将能够：

> [!div class="checklist"]
> * 使用 DevOps Projects 创建 CI/CD 管道
> * 配置对 GitHub 存储库的访问权限并选择一个框架
> * 配置 Azure DevOps 和 Azure 订阅 
> * 提交对 GitHub 所做的更改并将其自动部署到 Azure
> * 检查 Azure Pipelines CI/CD 管道
> * 配置 Azure Application Insights 监视
> * 清理资源

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 可以通过 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 免费获取一个。
* 访问 GitHub 或外部 Git 存储库，其中包含 .NET、Java、PHP、Node、Python 或静态 Web 代码。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

Azure DevOps Projects 在 Azure Pipelines 中创建 CI/CD 管道。 可以创建新的 Azure DevOps 组织，或使用现有的组织。 Azure DevOps Projects 还可以在所选的 Azure 订阅中创建 Azure 资源。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左窗格中，选择“新建”。 

1. 在搜索框中键入 **DevOps Projects**，然后选择“创建”。 

    ![DevOps Projects 仪表板](_img/azure-devops-project-github/fullbrowser.png)

1. 选择“引入自己的代码”，然后选择“下一步”。  

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>配置对 GitHub 存储库的访问权限并选择一个框架

1. 选择 **GitHub** 或外部 Git 存储库，然后选择你的存储库以及包含应用程序的分库。

1. 选择 Web 框架，然后选择“下一步”。 

    ![.NET Framework](_img/azure-devops-project-github/webframework.png)

    以前选择的应用程序框架规定了此处提供的 Azure 服务部署目标的类型。 
    
1. 选择目标服务，然后选择“下一步”。 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>配置 Azure DevOps 和 Azure 订阅 

1. 创建新的 Azure DevOps 组织，或选择现有的组织。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 输入 Azure DevOps 中项目的名称。 
    
    b. 选择 Azure 订阅和位置，输入应用程序的名称，然后选择“完成”。 

    几分钟后，DevOps Projects 仪表板会显示在 Azure 门户中。 首先会在 Azure DevOps 组织的存储库中设置示例应用程序，然后执行生成并将应用程序部署到 Azure。 可以通过此仪表板查看 GitHub 代码存储库、CI/CD 管道以及 Azure 中的应用程序。 
    
1. 选择“浏览”，查看正在运行的应用程序。 

    ![DevOps Projects 仪表板视图](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Azure DevOps Projects 会自动配置 CI 生成和发布触发器。 代码保留在 GitHub 存储库或其他外部存储库中。 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>提交对 GitHub 所做的更改并将其自动部署到 Azure 

现在，可以使用可将最新工作自动部署到网站的 CI/CD 过程，与某个团队展开应用协作。 每次对 GitHub 存储库进行更改都会在 Azure DevOps 中启动一个生成，CD 管道可以执行到 Azure 的部署。

1. 对应用程序进行更改，然后将更改提交到 GitHub 存储库。  
    片刻之后，系统会在 Azure Pipelines 中启动生成。 可以使用 Azure DevOps 组织通过 DevOps Projects 仪表板或浏览器监视生成状态。

1. 生成完成后，刷新应用程序以验证更改。

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>检查 Azure Pipelines CI/CD 管道

Azure DevOps Projects 会自动在 Azure Pipelines 中配置一个 CI/CD 管道。 根据需要浏览和自定义管道。 若要了解生成和发布管道，请执行以下操作：

1. 在 DevOps Projects 仪表板顶部，选择“生成管道”。   
    浏览器标签页会显示新项目的生成管道。

1. 指向“状态”字段，然后选择省略号 (...)。   
    菜单中会显示多个选项，例如，将新生成排队、暂停某个生成，以及编辑生成管道。

1. 选择“编辑”  。

1. 在此窗格中，可以检查生成管道的各种任务。  
    该生成会执行各种任务，例如，从 Git 存储库提取源、还原依赖项，以及发布用于部署的输出。

1. 在生成管道的顶部，选择生成管道名称。

1. 将生成管道的名称更改为更具描述性的名称，选择“保存并排队”，然后选择“保存”。  

1. 在生成管道名称下，选择“历史记录”。   
    此时会看到最近针对生成所做的更改的审核线索。 Azure DevOps 会跟踪对生成管道所做的任何更改，并允许进行版本比较。

1. 选择“触发器”。   
    Azure DevOps Projects 会自动创建一个 CI 触发器，每次向存储库提交内容都会启动新的生成。 （可选）可以选择在 CI 过程中包括或排除分支。

1. 选择“保留期”。   
        可以根据方案指定策略，以保留或删除特定数目的生成。

1. 选择“生成和发布”，然后选择“发布”。    
    Azure DevOps Projects 会创建一个发布管道，用于管理到 Azure 的部署。

1. 选择发布管道旁边的省略号 (...)，然后选择“编辑”。   
    发布管道包含一个*管道*，用于定义发布过程。 
    
1. 在“项目”下选择“删除”   。  
    在前述步骤中检查过的生成管道会生成用于项目的输出。 

1. 在“删除”图标的旁边，选择“持续部署触发器”。    
    此发布管道有一个启用的 CD 触发器，每次有新的生成项目可用时，此触发器就会执行部署。 （可选）可以禁用此触发器，这样就需要手动执行部署。 

1. 在左侧，选择“任务”。   
    任务是部署过程执行的活动。 在此示例中，已创建一个用于将项目部署到 Azure 应用服务的任务。

1. 在右侧选择“查看发布”，以显示发布历史记录。 

1. 选择某个发布旁边的省略号 (...)，然后选择“打开”。   
    可以浏览多个菜单，例如“发布摘要”、“关联的工作项”、“测试”。

1. 选择“提交”。   
    此视图显示与此部署关联的代码提交。 

1. 选择“日志”。   
    日志包含有关部署过程的有用信息。 可以在部署期间和之后查看日志。

## <a name="configure-azure-application-insights-monitoring"></a>配置 Azure Application Insights 监视

可以使用 Azure Application Insights 轻松监视应用程序的性能和使用情况。 Azure DevOps Projects 会自动为应用程序配置 Application Insights 资源。 你可以根据需要进一步配置各种警报和监视功能。

1. 在 Azure 门户中，转到 DevOps Projects 仪表板。 

1. 在右下角，选择应用对应的“Application Insights”链接。   
    此时会打开“Application Insights”窗格。  此视图包含应用的使用情况、性能和可用性监视信息。

    ![“Application Insights”窗格](_img/azure-devops-project-github/appinsights.png) 

1. 选择“时间范围”，然后选择“过去一小时”。   若要筛选结果，请选择“更新”。   
    现在可以查看过去 60 分钟的所有活动。 若要退出时间范围，请选择“x”。 

1. 选择“警报”，然后选择“添加指标警报”。   

1. 输入警报的名称。

1. 在“源警报触发条件”下拉列表中，选择你的**应用服务资源**。  <!-- Please confirm whether this should be "Source Alter on" or "Source Alert on" -->

1. 在“指标”下拉列表中，检查各种警报指标。   
    默认警报针对**服务器响应时间大于 1 秒**的情况。 可以轻松地配置各种警报，以便改进应用的监视功能。

1. 选中“通过电子邮件通知所有者、参与者和读者”复选框。   
    （可选）可以在显示警报时，通过执行 Azure 逻辑应用来执行其他操作。

1. 选择“确定”  以创建警报。  
    片刻之后，警报就会在仪表板上显示为活动状态。
    
1. 退出“警报”区域，返回到“Application Insights”窗格。  

1. 选择“可用性”，然后选择“添加测试”。   

1. 输入测试名称，然后选择“创建”。   
    此时会创建简单的 ping 测试来验证应用程序的可用性。 数分钟后，测试结果可用，此时 Application Insights 仪表板会显示可用性状态。

## <a name="clean-up-resources"></a>清理资源

不再需要本教程中创建的 Azure 应用服务和相关资源时，可将其删除。 请使用 DevOps Projects 仪表板上的“删除”功能来这样做。 

## <a name="next-steps"></a>后续步骤

当你在本教程中配置 CI/CD 过程后，系统已在 Azure DevOps Projects 中自动创建了生成和发布管道。 可以根据你的团队的需求修改这些生成和发布管道。 你已了解如何：

> [!div class="checklist"]
> * 使用 DevOps Projects 创建 CI/CD 管道
> * 配置对 GitHub 存储库的访问权限并选择一个框架
> * 配置 Azure DevOps 和 Azure 订阅 
> * 提交对 GitHub 所做的更改并将其自动部署到 Azure
> * 检查 Azure Pipelines CI/CD 管道
> * 配置 Azure Application Insights 监视
> * 清理资源

若要详细了解 CI/CD 管道，请参阅：

> [!div class="nextstepaction"]
> [Define your multi-stage continuous deployment (CD) pipeline](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)（定义多阶段持续部署 (CD) 管道）
