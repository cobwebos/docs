---
title: 教程：使用 Azure DevOps Projects 将 ASP.NET Core 应用部署到 Azure Service Fabric
description: 可以通过 Azure DevOps Projects 轻松地在 Azure 上开始操作。 使用 DevOps Projects，可通过几个快速步骤将 ASP.NET Core 应用部署到 Azure Service Fabric。
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 8ba217cb9ce849e57b15d3e6cc73529c78bf340e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57453748"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-projects"></a>教程：使用 Azure DevOps Projects 将 ASP.NET Core 应用部署到 Azure Service Fabric

Azure DevOps Projects 提供一种简化的体验，你在其中既可使用现有的代码和 Git 存储库，也可选择一个示例应用程序，以便创建连接到 Azure 的持续集成 (CI) 和持续交付 (CD) 管道。 

DevOps Projects 还可以：
* 自动创建 Azure 资源，例如 Azure Service Fabric。
* 在 Azure DevOps 中创建并配置一个发布管道，用于设置 CI/CD 管道。
* 创建用于监视的 Azure Application Insights 资源。

在本教程中，将：

> [!div class="checklist"]
> * 使用 DevOps Projects 创建 ASP.NET Core 应用并将其部署到 Service Fabric
> * 配置 Azure DevOps 和 Azure 订阅 
> * 检查 CI 管道
> * 检查 CD 管道
> * 提交对 Git 所做的更改并将其自动部署到 Azure
> * 清理资源

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 可以通过 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 免费获取一个。

## <a name="use-devops-projects-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>使用 DevOps Projects 创建 ASP.NET Core 应用并将其部署到 Service Fabric

DevOps Projects 在 Azure Pipelines 中创建 CI/CD 管道。 可以创建新的 Azure DevOps 组织，或使用现有的组织。 DevOps Projects 也在所选的 Azure 订阅中创建 Azure 资源，例如 Service Fabric 群集。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左窗格中，选择“创建资源”。

1. 在搜索框中键入 **DevOps Projects**，然后选择“创建”。

    ![DevOps Projects 仪表板](_img/azure-devops-project-github/fullbrowser.png)

1. 选择“.NET”，然后选择“下一步”。

1. 在“选择应用程序框架”下选择“ASP.NET Core”，然后选择“下一步”。

1. 选择“Service Fabric 群集”，然后选择“下一步”。 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>配置 Azure DevOps 和 Azure 订阅

1. 创建新的 Azure DevOps 组织，或选择现有的组织。 

1. 输入 Azure DevOps 项目的名称。 

1. 选择 Azure 订阅。

1. 若要查看其他 Azure 配置设置，并确定适用于 Service Fabric 群集的节点虚拟机大小和操作系统，请选择“更改”。  
    此窗格显示了各种用于配置 Azure 服务类型和位置的选项。
 
1. 退出 Azure 配置区域，然后选择“完成”。  
    配置过程在几分钟后完成。 此过程会在你的 Azure DevOps 组织的 Git 存储库中设置一个示例 ASP.NET Core 应用，创建 Service Fabric 群集，执行 CI/CD 管道，并将应用部署到 Azure。 

    完成所有操作后，DevOps Projects 仪表板会显示在 Azure 门户中。 也可从 Azure 门户中的“所有资源”直接转到 DevOps Projects 仪表板。 

    在此仪表板中可以查看 Azure DevOps 代码存储库、CI/CD 管道和 Service Fabric 群集。 可在 Azure Repos 中为 CI/CD 管道配置其他选项。 在右侧，选择“浏览”即可查看正在运行的应用。

## <a name="examine-the-ci-pipeline"></a>检查 CI 管道

DevOps Projects 会自动在 Azure Pipelines 中配置一个 CI/CD 管道。 可以浏览和自定义此管道。 若要了解此管道，请执行以下操作：

1. 转到 DevOps Projects 仪表板。

1. 在 DevOps Projects 仪表板顶部，选择“生成管道”。  
    浏览器标签页会显示新项目的生成管道。

1. 指向“状态”字段，然后选择省略号 (...)。  
    菜单中会显示多个选项，例如，将新生成排队、暂停某个生成，以及编辑生成管道。

1. 选择“编辑”。

1. 在此窗格中，可以检查生成管道的各种任务。  
    该生成会执行各种任务，例如，从 Git 存储库提取源、还原依赖项，以及发布用于部署的输出。

1. 在生成管道的顶部，选择生成管道名称。 

1. 在生成管道名称下，选择“历史记录”。  
    此窗格显示最近针对生成所做的更改的审核线索。 Azure DevOps 会跟踪对生成管道所做的任何更改，并允许进行版本比较。

1. 选择“触发器”。  
    DevOps Projects 会自动创建一个 CI 触发器，每次向存储库提交内容都会启动新的生成。 （可选）可以选择在 CI 过程中包括或排除分库。

1. 选择“保留期”。  
    可以根据方案指定策略，以保留或删除特定数目的生成。

## <a name="examine-the-cd-pipeline"></a>检查 CD 管道

DevOps Projects 会自动创建并配置从 Azure DevOps 组织部署到 Azure 订阅所要执行的步骤。 这些步骤包括配置 Azure 服务连接，以便在 Azure 订阅中进行 Azure DevOps 身份验证。 自动化还会创建一个发布管道，该管道提供到 Azure 的 CD。 若要详细了解发布管道，请执行以下操作：

1. 选择“生成和发布”，然后选择“发布”。  
    DevOps Projects 会创建一个发布管道，用于管理到 Azure 的部署。

1. 选择发布管道旁边的省略号 (...)，然后选择“编辑”。  
    发布管道包含一个*管道*，用于定义发布过程。

1. 在“项目”下选择“删除”。  
    前面检查过的生成管道将生成用于项目的输出。 

1. 在“删除”图标的右侧，选择“持续部署触发器”。  
    此发布管道有一个已启用的 CD 触发器，每次有新的生成项目可用时，此触发器就会执行部署。 （可选）可以禁用此触发器，这样就需要手动执行部署。 

1. 在右侧选择“查看发布”，以显示发布历史记录。

1. 选择某个发布旁边的省略号 (...)，然后选择“打开”。  
    可以浏览多个菜单，例如发布摘要、关联的工作项和测试。

1. 选择“提交”。  
    此视图显示与此部署关联的代码提交。 请对发布进行比较，查看不同部署之间的提交差异。

1. 选择“日志”。  
    日志包含有关部署过程的有用信息。 可以在部署期间和之后查看日志。

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>提交对 Git 所做的更改并将其自动部署到 Azure 

 > [!NOTE]
 > 以下过程通过进行简单的文本更改来测试 CI/CD 管道。

现在，可以使用可将最新工作自动部署到网站的 CI/CD 过程，与某个团队展开应用协作。 每次对 Git 存储库进行更改都会启动一个生成，而版本则会将更改部署到 Azure。 遵循本部分所述的过程，或使用其他方法提交对存储库所做的更改。 例如，可以在常用工具或 IDE 中克隆 Git 存储库，然后将更改推送到此存储库。

1. 在 Azure DevOps 菜单中选择“代码” > “文件”，然后转到你的存储库。

1. 转到 *Views\Home* 目录，选择 *Index.cshtml* 文件旁边的省略号 (...)，然后选择“编辑”。

1. 对该文件进行更改，例如，在某个 div 标记内部添加一些文本。 

1. 在右上角选择“提交”，然后再次选择“提交”以推送更改。  
    片刻之后，系统会启动生成，然后执行发布以部署所做的更改。 可以使用 Azure DevOps 实时日志记录通过 DevOps 项目仪表板或浏览器监视生成状态。

1. 发布完成后，刷新应用以验证更改。

## <a name="clean-up-resources"></a>清理资源

测试后，可以清理资源，以避免产生费用。 不再需要本教程中创建的 Azure Service Fabric 群集和相关资源时，可将其删除。 请使用 DevOps Projects 仪表板上的“删除”功能来这样做。

> [!IMPORTANT]
> 以下过程会永久删除资源。 “删除”功能会销毁 DevOps Projects 中的项目在 Azure 和 Azure DevOps 中创建的数据，删除后无法检索这些数据。 只能在仔细阅读提示后才使用此过程。

1. 在 Azure 门户中，转到 DevOps Projects 仪表板。
1. 在右上角选择“删除”。 
1. 出现提示时，请选择“是”以永久删除这些资源。

## <a name="next-steps"></a>后续步骤

可以选择根据所在团队的需求修改 Azure CI/CD 管道。 也可以将此 CI/CD 模式用作其他管道的模板。 本教程介绍了如何：

> [!div class="checklist"]
> * 使用 DevOps Projects 创建 ASP.NET Core 应用并将其部署到 Service Fabric
> * 配置 Azure DevOps 和 Azure 订阅 
> * 检查 CI 管道
> * 检查 CD 管道
> * 提交对 Git 所做的更改并将其自动部署到 Azure
> * 清理资源

若要详细了解 Service Fabric 和微服务，请参阅：

> [!div class="nextstepaction"]
> [使用微服务方法生成应用程序](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)
