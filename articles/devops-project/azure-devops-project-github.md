---
title: 教程：使用 Azure DevOps Projects 为现有的代码创建 CI/CD 管道
description: 可以通过 Azure DevOps Projects 轻松地在 Azure 上开始操作。 借助 DevOps Projects，只需通过几个简单的步骤即可使用自己的代码和 GitHub 存储库启动 Azure 服务上的应用。
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/27/2019
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: a0496999a9a5dc3e9bfd57df0ec035e6db77d620
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615129"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>教程：使用 Azure DevOps Projects 为现有的代码创建 CI/CD 管道

Azure DevOps Projects 提供简化的过程让用户与 Azure 建立持续集成 (CI) 和持续交付 (CD) 管道。 你可以引入自己的现有代码和 Git 存储库，或选择一个示例应用程序。

将能够：

> [!div class="checklist"]
> * 使用 DevOps Projects 创建 CI/CD 管道
> * 配置对 GitHub 存储库的访问权限并选择一个框架
> * 配置 Azure DevOps 和 Azure 订阅 
> * 提交对 GitHub 所做的更改并将其自动部署到 Azure
> * 检查 Azure Pipelines CI/CD 管道
> * 清理资源

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 可以通过 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 免费获取一个。
* 访问 GitHub 或外部 Git 存储库，其中包含 .NET、Java、PHP、Node.js、Python 或静态 Web 代码。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

Azure DevOps Projects 在 Azure Pipelines 中创建 CI/CD 管道。 可以创建新的 Azure DevOps 组织，或使用现有的组织。 Azure DevOps Projects 还可以在所选的 Azure 订阅中创建 Azure 资源。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 门户菜单中，选择“创建资源”  。

   ![Azure 门户菜单 - 创建资源](_img/azure-devops-project-github/createaresource.png)

3. 选择“DevOps” > “DevOps 项目”。  

   ![DevOps Projects 仪表板](_img/azure-devops-project-github/azuredashboard.png)

1. 选择“引入自己的代码”，然后选择“下一步”。  

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>配置对 GitHub 存储库的访问权限并选择框架

1. 选择 **GitHub** 或某个外部的 **Git** 代码存储库。 对于本教程，请选择“GitHub”  。 第一次时，可能需要通过 GitHub 进行身份验证，这样 Azure 就能够访问你的 GitHub 存储库。

1. 选择一个**存储库**和**分支**，然后选择“下一步”。 

1. 如果使用 Docker 容器，请将“应用是否已 Docker 化”更改为“是”。   对于本教程，请保留选择“否”。然后选择“下一步”。   若要详细了解如何使用 Docker 容器，请将鼠标悬停在 **i** 图标上。

   ![下拉菜单中的应用程序框架选项](_img/azure-devops-project-github/appframework.png)

1. 从下拉菜单中选择一个**应用程序运行时**和**应用程序框架**，然后选择“下一步”。  应用程序框架规定了可用的 Azure 服务部署目标的类型。

1. 选择一个 **Azure 服务**来部署应用程序，然后选择“下一步”。 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>配置 Azure DevOps 和 Azure 订阅

1. 在“项目名称”中输入名称。 

1. 在“Azure DevOps 组织”中创建新的免费组织，或从下拉菜单中选择现有的组织。 

1. 在“Azure 订阅”中选择订阅，然后在“Web 应用”中输入名称，或使用默认值。   选择一个**位置**，然后选择“完成”。  几分钟后，Azure 门户中会显示 DevOps Projects 部署概述。

1. 选择“转到资源”  以查看 DevOps Projects 仪表板。 在右上角，将“项目”  固定在仪表板上，以便快速访问。 Azure DevOps Projects 会自动配置 CI 生成和发布触发器。 代码保留在 GitHub 存储库或其他外部存储库中，示例应用设置在“Azure DevOps 组织”中的某个存储库内。  Azure DevOps Projects 将运行生成，并将应用部署到 Azure。

   ![Azure DevOps Projects 仪表板视图](_img/azure-devops-project-github/projectsdashboard.png)

1. 该仪表板显示代码存储库、CI/CD 管道，以及 Azure 中的应用。 在右侧的“Azure 资源”下，选择“浏览”以查看正在运行的应用。 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>提交对 GitHub 所做的更改并将其自动部署到 Azure

现在，可与某个团队展开应用协作。 CI/CD 过程会自动将最新工作部署到网站。 每次对 GitHub 存储库进行更改都会在 Azure DevOps 中启动生成，CD 管道可以运行到 Azure 的部署。

1. 从 DevOps Projects 仪表板中选择“存储库”。  GitHub 存储库会在新浏览器标签页中打开。对应用程序进行更改，然后选择“提交更改”。 

1. 片刻之后，系统会在 Azure Pipelines 中启动生成。 可以在 DevOps Projects 仪表板中监视生成状态。 也可以在 Azure DevOps 组织中监视它，只需从 DevOps Projects 仪表板中选择“生成管道”选项卡即可。 

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>检查 Azure Pipelines CI/CD 管道

Azure DevOps Projects 会自动在 Azure Pipelines 中配置一个 CI/CD 管道。 根据需要浏览和自定义管道。 若要了解生成和发布管道，请执行以下操作：

1. 在 DevOps Projects 仪表板中，选择“生成管道”。 

1. 打开“Azure Pipelines”页后，会看到一个历史记录，其中包含最近的生成以及每个生成的状态。 

   ![Azure Pipelines 生成页](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. 在“生成”页的右上角，可以选择“编辑”以更改当前的生成、选择“排队”以添加新的生成，或者选择纵向省略号按钮 ( **&#8942;** ) 打开包含更多选项的菜单。    选择“编辑”  。

1. 该生成会执行各种任务，例如，从存储库提取源、还原依赖项，以及发布用于部署的输出。 在“名称”下方右侧，将生成管道的名称更改为更具说明性的名称。  选择“保存并排队”，然后选择“保存”   。 输入注释，然后再次选择“保存”。 

   ![Azure DevOps 生成页](_img/azure-devops-project-github/buildpage.png)

1. 若要查看最近针对生成所做的更改的审核线索，请选择“历史记录”选项卡。  Azure DevOps 会跟踪对生成管道所做的任何更改，并允许进行版本比较。

1. 选择“触发器”选项卡。  Azure DevOps Projects 会自动创建 CI 触发器，其中采用了一些默认设置。 可以设置“启用持续集成”之类的触发器，以便每次提交代码更改时都运行生成。  还可以设置触发器，以将生成计划为在特定的时间运行。

## <a name="clean-up-resources"></a>清理资源

不再需要本教程中创建的 Azure 应用服务和相关资源时，可将其删除。 请使用 DevOps Projects 仪表板上的“删除”功能。 

## <a name="next-steps"></a>后续步骤

在本教程中配置 CI/CD 过程时，已在 Azure DevOps Projects 中自动创建了生成和发布管道。 可以根据你的团队的需求修改这些生成和发布管道。

若要详细了解 CI/CD 管道，请参阅：

> [!div class="nextstepaction"]
> [Define your multi-stage continuous deployment (CD) pipeline](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)（定义多阶段持续部署 (CD) 管道）

若要详细了解应用程序监视，请参阅：
  
 > [!div class="nextstepaction"]
 > [什么是 Azure Monitor？](https://docs.microsoft.com/azure/azure-monitor/overview)
