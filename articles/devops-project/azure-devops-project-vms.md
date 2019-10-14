---
title: 教程：使用 Azure DevOps Projects 将 ASP.NET 应用部署到 Azure 虚拟机
description: 借助 DevOps Projects，只需执行几个快速步骤就能轻松开始使用 Azure，并将 ASP.NET 应用部署到 Azure 虚拟机。
ms.author: mlearned
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b03fb27c46d4fd925b91b8927fdd50ff4912aaec
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969564"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-projects"></a>教程：使用 Azure DevOps Projects 将 ASP.NET 应用部署到 Azure 虚拟机

Azure DevOps Projects 提供一种简化的体验，你在其中既可使用现有的代码和 Git 存储库，也可选择一个示例应用程序，以便创建连接到 Azure 的持续集成 (CI) 和持续交付 (CD) 管道。 

DevOps Projects 还可以：
* 自动创建 Azure 资源，例如新的 Azure 虚拟机 (VM)。
* 在 Azure DevOps 中创建并配置一个发布管道，其中包括用于 CI 的生成管道。
* 为 CD 设置发布管道。 
* 创建用于监视的 Azure Application Insights 资源。

在本教程中，将：

> [!div class="checklist"]
> * 使用 DevOps Projects 部署 ASP.NET 应用
> * 配置 Azure DevOps 和 Azure 订阅 
> * 检查 CI 管道
> * 检查 CD 管道
> * 提交对 Azure Repos 所做的更改并将其自动部署到 Azure
> * 配置 Azure Application Insights 监视
> * 清理资源

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 可以通过 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 免费获取一个。

## <a name="use-devops-projects-to-deploy-your-aspnet-app"></a>使用 DevOps Projects 部署 ASP.NET 应用

DevOps Projects 在 Azure Pipelines 中创建 CI/CD 管道。 可以创建新的 Azure DevOps 组织，或使用现有的组织。 DevOps Projects 还可在所选的 Azure 订阅中创建 Azure 资源，例如虚拟机。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左窗格中，选择“新建”。 

1. 在搜索框中键入 **DevOps Projects**，然后选择“创建”。 

    ![DevOps Projects 仪表板](_img/azure-devops-project-github/fullbrowser.png)

1. 依次选择“.NET”、“下一步”。  

1. 在“选择应用程序框架”下选择“ASP.NET”，然后选择“下一步”。     
    在前述步骤中选择的应用程序框架规定了此处可用的 Azure 服务部署目标的类型。 

1. 选择虚拟机，然后选择“下一步”。 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>配置 Azure DevOps 和 Azure 订阅

1. 创建新的 Azure DevOps 组织，或选择现有的组织。 

1. 输入 Azure DevOps 项目的名称。 

1. 选择你的 Azure 订阅服务。  
    （可选）可以选择“更改”，然后输入其他配置详细信息，例如 Azure 资源的位置。 
 
1. 为新的 Azure 虚拟机资源输入虚拟机名称、用户名和密码，然后选择“完成”。   
    几分钟后，该 Azure 虚拟机即可准备就绪。 将在你的 Azure DevOps 组织的存储库中设置一个示例 ASP.NET 应用程序，执行生成和发布，然后将应用程序部署到新建的 Azure VM。 

    完成上述过程后，DevOps Projects 仪表板会显示在 Azure 门户中。 也可以从 Azure 门户中的“所有资源”直接导航到仪表板。  

    在此仪表板中可以查看 Azure DevOps 代码存储库、CI/CD 管道，以及 Azure 中正在运行的应用程序。   

    ![仪表板视图](_img/azure-devops-project-vms/dashboardnopreview.png)

DevOps Projects 会自动配置 CI 生成和发布触发器，用于将代码更改部署到存储库。 可以进一步在 Azure DevOps 中配置其他选项。 若要查看正在运行的应用程序，请选择“浏览”。 
    
## <a name="examine-the-ci-pipeline"></a>检查 CI 管道
 
DevOps Projects 会自动在 Azure Pipelines 中配置一个 CI/CD 管道。 可以浏览和自定义此管道。 若要了解生成管道，请执行以下操作：

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
    此窗格显示最近针对生成所做的更改的审核线索。 Azure DevOps 会跟踪对生成管道所做的任何更改，并允许进行版本比较。

1. 选择“触发器”。   
    DevOps Projects 会自动创建一个 CI 触发器，每次向存储库提交内容都会启动新的生成。 （可选）可以选择在 CI 过程中包括或排除分库。

1. 选择“保留期”。   
    可以根据方案指定策略，以保留或删除特定数目的生成。

## <a name="examine-the-cd-pipeline"></a>检查 CD 管道

DevOps Projects 会自动创建并配置从 Azure DevOps 组织部署到 Azure 订阅所要执行的步骤。 这些步骤包括配置 Azure 服务连接，以便在 Azure 订阅中进行 Azure DevOps 身份验证。 自动化还会创建一个 CD 管道，用于提供到 Azure 虚拟机的 CD。 若要详细了解 Azure DevOps CD 管道，请执行以下操作：

1. 依次选择“生成和发布”、“发布”。    
    DevOps Projects 会创建一个发布管道，用于管理到 Azure 的部署。

1. 选择发布管道旁边的省略号 (...)，然后选择“编辑”。   
    发布管道包含一个*管道*，用于定义发布过程。

1. 在“项目”下选择“删除”   。  
    在前述步骤中检查过的生成管道将生成用于项目的输出。 

1. 在“删除”图标的旁边，选择“持续部署触发器”。    
    此发布管道有一个已启用的 CD 触发器，每次有新的生成项目可用时，此触发器就会执行部署。 （可选）可以禁用此触发器，这样就需要手动执行部署。 

1. 在左侧选择“任务”，然后选择你的环境。   
    任务是部署过程执行的活动，已按阶段分组。 此发布管道包括两个阶段：
    * 第一阶段包含“Azure 资源组部署”任务，该任务执行以下操作：
      * 配置用于部署的 VM
      * 将新 VM 添加到某个 Azure DevOps 部署组。 Azure DevOps 中的 VM 部署组管理部署目标计算机的逻辑组。
    * 在第二阶段中，某个“IIS Web 应用管理”任务会在 VM 上创建 IIS 网站。 在此阶段，还会创建另一个用于部署站点的“IIS Web 应用部署”任务。

1. 在右侧选择“查看发布”，以显示发布历史记录。 

1. 选择某个发布旁边的省略号 (...)，然后选择“打开”。   
    可以浏览多个菜单，例如发布摘要、关联的工作项和测试。

1. 选择“提交”。   
    此视图显示与此部署关联的代码提交。 请对发布进行比较，查看不同部署之间的提交差异。

1. 选择“日志”。   
    日志包含有关部署过程的有用信息。 可以在部署期间和之后查看日志。

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>提交对 Azure Repos 所做的更改并将其自动部署到 Azure 

现在，可以使用可将最新工作部署到网站的 CI/CD 过程，与某个团队展开应用协作。 每次对 Git 存储库进行更改都会在 Azure DevOps 中启动一个生成，CD 管道可以执行到 Azure 的部署。 遵循本部分所述的过程，或使用其他方法提交对存储库所做的更改。 代码更改会启动 CI/CD 过程，并自动在 Azure VM 上部署对 IIS 网站所做的更改。

1. 在左窗格中选择“代码”，然后转到你的存储库。 

1. 转到 *Views\Home* 目录，选择 *Index.cshtml* 文件旁边的省略号 (...)，然后选择“编辑”。 

1. 对该文件进行更改，例如，在某个 div 标记内部添加一些文本。 

1. 在右上角选择“提交”，然后再次选择“提交”以推送更改。    
    片刻之后，某个生成会在 Azure DevOps 中启动，并且某个发布将会执行以部署更改。 使用 Azure DevOps 组织通过 DevOps Projects 仪表板或浏览器监视生成状态。

1. 发布完成后，刷新应用程序以验证更改。

## <a name="configure-azure-application-insights-monitoring"></a>配置 Azure Application Insights 监视

可以使用 Azure Application Insights 轻松监视应用程序的性能和使用情况。 DevOps Projects 会自动为应用程序配置 Application Insights 资源。 你可以根据需要进一步配置各种警报和监视功能。

1. 在 Azure 门户中，转到 DevOps Projects 仪表板。 

1. 在右下角，选择应用对应的“Application Insights”链接。   
    此时会打开“Application Insights”窗格。  此视图包含应用的使用情况、性能和可用性监视信息。

    ![“Application Insights”窗格](_img/azure-devops-project-github/appinsights.png) 

1. 选择“时间范围”，然后选择“过去一小时”。   若要筛选结果，请选择“更新”。   
    现在可以查看过去 60 分钟的所有活动。 
    
1. 若要退出时间范围，请选择“x”。 

1. 选择“警报”，然后选择“添加指标警报”。   

1. 输入警报的名称。

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

测试后，可以清理资源，以避免产生费用。 不再需要本教程中创建的 Azure 虚拟机和相关资源时，可将其删除。 为此，可以使用 DevOps Projects 仪表板上的“删除”功能。  

> [!IMPORTANT]
> 以下过程会永久删除资源。 “删除”功能会销毁 DevOps Projects 中的项目在 Azure 和 Azure DevOps 中创建的数据，删除后无法检索这些数据。  只能在仔细阅读提示后才使用此过程。

1. 在 Azure 门户中，转到 DevOps Projects 仪表板。
1. 在右上角选择“删除”  。 
1. 出现提示时，请选择“是”以永久删除这些资源。  

可以选择根据所在团队的需求修改这些生成和发布管道。 也可以将此 CI/CD 模式用作其他管道的模板。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 使用 DevOps Projects 部署 ASP.NET 应用
> * 配置 Azure DevOps 和 Azure 订阅 
> * 检查 CI 管道
> * 检查 CD 管道
> * 提交对 Azure Repos 所做的更改并将其自动部署到 Azure
> * 配置 Azure Application Insights 监视
> * 清理资源

若要详细了解 CI/CD 管道，请参阅：

> [!div class="nextstepaction"]
> [Define your multi-stage continuous deployment (CD) pipeline](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)（定义多阶段持续部署 (CD) 管道）
