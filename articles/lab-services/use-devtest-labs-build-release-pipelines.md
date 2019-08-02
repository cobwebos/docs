---
title: 使用 Azure DevOps 生成和发布管道中的开发测试实验室 |Microsoft Docs
description: 了解如何在 Azure DevOps 生成和发布管道中使用 Azure 开发测试实验室。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2019
ms.author: spelluru
ms.openlocfilehash: 606563cd7d7adcdd79bf9561876eb0640fb68b21
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620891"
---
# <a name="use-devtest-labs-in-azure-devops-build-and-release-pipelines"></a>在 Azure DevOps 生成和发布管道中使用开发测试实验室
本文提供了有关如何在 Azure DevOps 生成和发布管道中使用开发测试实验室的信息。 

## <a name="overall-flow"></a>总体流程
基本流程是具有执行以下任务的**生成管道**:

1. 生成应用程序代码。
1. 在开发测试实验室中创建基础环境。
1. 用自定义信息更新环境。
1. 将应用程序部署到开发测试实验室环境
1. 测试代码。 

成功完成生成后,**发布管道**将使用生成项目来部署过渡或生产。 

其中一项必要的是, 在生成项目中提供重新创建已测试生态系统所需的所有信息, 包括 Azure 资源的配置。 随着 Azure 资源的使用, 企业需要对这些资源的使用进行控制或跟踪。 在某些情况下, 用于创建和配置资源的 Azure 资源管理器模板可能由其他部门管理。 而且, 这些模板可能存储在不同的存储库中。 这会带来一个有趣的情况, 即会创建和测试生成, 并且需要在生成项目中存储代码和配置, 以便在生产中正确地重新创建系统。 

通过在生成/测试阶段使用开发测试实验室, 你可以将 Azure 资源管理器模板和支持文件添加到生成源, 以便在发布阶段, 用于测试的确切配置将部署到生产环境中。 带有正确配置的 "**创建 Azure 开发测试实验室" 环境**任务将在生成项目中保存资源管理器模板。 在此示例中, 将使用[教程中的代码:在 Azure App Service](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)中生成 .net Core 和 SQL 数据库 web 应用, 以便在 Azure 中部署和测试 web 应用。

![总体流程](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>设置 Azure 资源
需要预先创建几项:

- 两个存储库。 第一个具有教程中的代码的代码和一个包含两个额外 Vm 的资源管理器模板。 第二个将包含基础 Azure 资源管理器模板 (现有配置)。
- 用于部署生产代码和配置的资源组。
- 需要使用与生成管道的[配置存储库的连接来](devtest-lab-create-environment-from-arm.md)设置实验室。 需要将资源管理器模板签入到配置存储库中, 将其作为 azuredeploy.json, 以允许开发测试实验室识别和部署模板。

生成管道将创建开发测试实验室环境并部署代码进行测试。

## <a name="set-up-a-build-pipeline"></a>设置生成管道
在 Azure DevOps 中, 使用[教程中的代码创建生成管道:在 Azure App Service](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)中生成 .NET Core 和 SQL 数据库 web 应用。 使用**ASP.NET Core**模板, 该模板将填充生成、测试和发布代码所需的任务。

![选择 ASP.NET 模板](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

需要添加三个其他任务来在开发测试实验室中创建环境并部署到环境中。

![包含三个任务的管道](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>创建环境任务
在 "创建环境" 任务 (**Azure 开发测试实验室创建环境**任务) 中, 使用下拉列表选择以下值:

- Azure 订阅
- 实验室的名称
- 存储库的名称
- 模板的名称 (显示存储环境的文件夹)。 

建议你使用页面上的下拉列表, 而不是手动输入信息。 如果手动输入信息, 请输入完全限定的 Azure 资源 Id。 任务显示友好名称, 而不是资源 Id。 

环境名称是显示的名称, 显示在开发测试实验室中。 它应该是每个生成的唯一名称。 例如：**TestEnv $ (BuildId)** 。 

你可以指定参数文件或参数以将信息传递到资源管理器模板。 

选择 "**基于环境模板输出创建输出变量**", 然后输入引用名称。 对于此示例, 请输入 " **BaseEnv** " 作为 "引用名称"。 配置下一任务时将使用此 BaseEnv。 

![创建 Azure 开发测试实验室环境任务](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>填充环境任务
第二个任务 (**Azure 开发测试实验室填充环境**任务) 更新现有的开发测试实验室环境。 创建环境任务将输出用于为此任务配置环境名称的**BaseEnv。** 此示例的资源管理器模板具有两个参数- **adminUserName**和**adminPassword**。 

![填充 Azure 开发测试实验室环境任务](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>应用服务部署任务
第三个任务是**Azure App Service 部署**任务。 应用类型设置为**Web 应用**, 应用服务名称设置为 **$ (网站)** 。

![应用服务部署任务](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="setup-release-pipeline"></a>安装程序发布管道
创建包含两个任务的发布管道:**Azure 部署:创建或更新资源组**并**部署 Azure App Service**。 

对于第一个任务, 指定资源组的名称和位置。 模板位置是链接的项目。 如果资源管理器模板包含链接的模板, 则需要实现自定义资源组部署。 模板位于已发布的删除项目中。 重写资源管理器模板的模板参数。 您可以保留剩余的默认值设置。 

对于第二个任务 "**部署 Azure App Service**", 指定 Azure 订阅, 选择 "**应用类型**" 的 " **Web 应用**", 并选择 "**应用服务名称**" **(网站)** 。 您可以保留剩余的默认值设置。 

## <a name="test-run"></a>测试运行
设置这两个管道后, 手动将生成排队并查看其工作。 下一步是为生成设置适当的触发器, 并将生成连接到发布管道。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [将 Azure 开发测试实验室集成到 Azure DevOps 持续集成和交付管道](devtest-lab-integrate-ci-cd-vsts.md)
- [将环境集成到 Azure DevOps CI/CD 管道](integrate-environments-devops-pipeline.md)
