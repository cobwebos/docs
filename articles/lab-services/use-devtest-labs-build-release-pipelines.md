---
title: 在 Azure Pipelines 生成和发布管道中使用开发测试实验室
description: 了解如何在 Azure 管道生成和发布管道中使用 Azure 开发人员测试实验室。
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e16f3c5a0c0b2b86d6a893f541cefb275a8e7d07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169241"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>在 Azure Pipelines 生成和发布管道中使用开发测试实验室
本文提供有关如何在 Azure 管道生成和发布管道中使用 DevTest Labs 的信息。 

## <a name="overall-flow"></a>总体流程
基本流程是具有执行以下任务的**生成管道**：

1. 生成应用程序代码。
1. 在 DevTest 实验室中创建基本环境。
1. 使用自定义信息更新环境。
1. 将应用程序部署到开发人员测试实验室环境
1. 测试代码。 

成功完成生成后，**发布管道**将使用生成项目来部署暂存或生产。 

必要的前提之一是，在生成工件中提供重新创建测试生态系统所需的所有信息，包括 Azure 资源的配置。 由于 Azure 资源在使用时会产生成本，因此公司希望控制或跟踪这些资源的使用。 在某些情况下，用于创建和配置资源的 Azure 资源管理器模板可能由 IT 等其他部门管理。 而且，这些模板可能存储在不同的存储库中。 它会导致一个有趣的情况，即将创建和测试生成，并且代码和配置都需要存储在生成工件中，以便在生产中正确重新创建系统。 

在生成/测试阶段使用 DevTest Labs，可以将 Azure 资源管理器模板和支持文件添加到生成源，以便在发布阶段将用于测试的确切配置部署到生产阶段。 使用正确配置**创建 Azure DevTest 实验室环境**任务将资源管理器模板保存在生成工件中。 在此示例中，您将使用教程中的代码[：在 Azure 应用服务中构建 .NET Core 和 SQL 数据库 Web 应用](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)，以在 Azure 中部署和测试 Web 应用。

![总体流程](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>设置 Azure 资源
需要事先创建几个项：

- 两个存储库。 第一个包含教程中的代码和资源管理器模板，其中有两个额外的 VM。 第二个将包含基本 Azure 资源管理器模板（现有配置）。
- 用于部署生产代码和配置的资源组。
- 需要使用与生成管道[的配置存储库的连接](devtest-lab-create-environment-from-arm.md)来设置实验室。 需要将资源管理器模板作为 azuredeploy.json 与元数据.json 一起签入配置存储库，以允许 DevTest Labs 识别和部署该模板。

生成管道将创建一个 DevTest Labs 环境，并部署用于测试的代码。

## <a name="set-up-a-build-pipeline"></a>设置生成管道
在 Azure 管道中，使用教程中的代码创建生成管道[：在 Azure 应用服务中生成 .NET 核心和 SQL 数据库 Web 应用](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)。 使用**ASP.NET核心**模板，该模板将填充生成、测试和发布代码所需的任务。

![选择ASP.NET模板](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

您需要添加三个附加任务，以在 DevTest 实验室中创建环境并部署到环境中。

![具有三个任务的管道](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>创建环境任务
在创建环境任务 **（Azure DevTest 实验室创建环境**任务）中使用下拉列表选择以下值：

- Azure 订阅
- 实验室名称
- 存储库的名称
- 模板的名称（显示存储环境的文件夹）。 

我们建议您在页面上使用下拉列表，而不是手动输入信息。 如果手动输入信息，请输入完全合格的 Azure 资源 ID。 任务显示友好名称而不是资源 Id。 

环境名称是开发人员测试实验室中显示的显示名称。 它应该是每个生成的唯一名称。 例如 **：TestEnv$（生成.BuildId）**。 

您可以指定参数文件或参数以将信息传递到资源管理器模板。 

**根据环境模板输出选项选择"创建输出变量**"并输入引用名称。 在此示例中，输入参考名称的**BaseEnv。** 配置下一个任务时，您将使用此 BaseEnv。 

![创建 Azure 开发人员测试实验室环境任务](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>填充环境任务
第二个任务 **（Azure 开发人员测试实验室填充环境**任务）是更新现有的开发人员测试实验室环境。 创建环境任务输出用于为此任务配置环境名称的**BaseEnv.环境ResourceId。** 此示例的资源管理器模板有两个参数 -**管理员用户名**和**管理员密码**。 

![填充 Azure 开发人员测试实验室环境任务](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>应用服务部署任务
第三个任务是**Azure 应用服务部署**任务。 应用类型设置为 Web**应用**，应用服务名称设置为 **$（WebSite）。**

![应用服务部署任务](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>设置发布管道
创建具有两个任务的发布管道 **：Azure 部署：创建或更新资源组**和**部署 Azure 应用服务**。 

对于第一个任务，指定资源组的名称和位置。 模板位置是链接项目。 如果资源管理器模板包含链接模板，则需要实施自定义资源组部署。 模板位于已发布的删除工件中。 覆盖资源管理器模板的模板参数。 您可以将其余设置保留为默认值。 

对于第二个任务 **"部署 Azure 应用服务**"，指定 Azure 订阅，为**应用类型**选择**Web 应用**，为**应用服务名称**选择 **$（WebSite）。** 您可以将其余设置保留为默认值。 

## <a name="test-run"></a>测试运行
现在，两个管道都已设置，手动排队生成，并看到它工作。 下一步是为生成设置适当的触发器，并将生成连接到发布管道。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [将 Azure 开发人员测试实验室集成到 Azure 管道中，持续集成和交付管道](devtest-lab-integrate-ci-cd-vsts.md)
- [将环境集成到 Azure 管道 CI/CD 管道中](integrate-environments-devops-pipeline.md)
