---
title: 将环境集成到 Azure 开发测试实验室中的 Azure 管道 |Microsoft Docs
description: 了解如何将 Azure 开发测试实验室环境集成到你的 Azure DevOps 持续集成 (CI) 和持续交付 (CD) 管道。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: deb5595ac6a8b0d189e5594fda8e4b60480d038c
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357407"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>将环境集成到你的 Azure DevOps CI/CD 管道
可以使用 Azure 开发测试实验室任务扩展安装 Azure DevOps 服务 （以前称为 Visual Studio Team Services） 以轻松地集成在持续集成 (CI) / 持续交付 (CD) 生成和发布管道与 Azure开发测试实验室。 这些扩展使其更轻松地快速部署[环境](devtest-lab-test-env.md)为特定测试任务，然后在测试完成时删除它。 

本文介绍如何创建和部署环境，然后删除该环境，在一个完整的管道。 您将通常在中执行这些任务的每个单独自己自定义生成-测试-部署管道。 在本文中使用的扩展不包括这些[创建/删除 DTL VM 任务](devtest-lab-integrate-ci-cd-vsts.md):

- 创建环境
- 删除环境

## <a name="before-you-begin"></a>开始之前
可以使用 Azure 开发测试实验室集成到 CI/CD 管道之前，请安装[Azure 开发测试实验室任务](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)扩展从 Visual Studio Marketplace。 

## <a name="create-and-configure-the-lab-for-environments"></a>创建和配置在实验室环境
本部分介绍如何创建和配置将在其中将在 Azure 环境部署到实验室。

1. [创建实验室](devtest-lab-create-lab.md)如果还没有一个。 
2. 配置在实验室并按照这篇文章的说明创建的环境模板：[使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)。
3. 对于此示例中，使用现有的 Azure 快速入门模板[ https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/ ](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/)。
4. 复制**201-web-app-redis-cache-sql-database**文件夹复制到**ArmTemplate**步骤 2 中配置存储库中的文件夹。

## <a name="create-a-release-definition"></a>创建发布定义
要创建发布定义，请执行以下操作：

1.  上**版本**选项卡**生成和发布中心**，选择**加号 （+）** 按钮。
2.  在“创建发布定义”窗口中，选择“空”模板，然后选择“下一步”。
3.  选择“稍后选择”和“创建”，以新建有一个默认环境且无链接项目的发布定义。
4.  若要打开快捷菜单中，新的发布定义中，选择**省略号 （...）** 到环境名称下, 一步，然后选择**配置变量**。
5.  在“配置 - 环境”窗口中，对于在发布定义中使用的变量，输入以下值：
1.  有关**administratorLogin**，输入 SQL 管理员登录名。
2.  有关**administratorLoginPassword**，输入要使用的 SQL 管理员登录名的密码。 使用“挂锁”图标隐藏和保护密码。
3.  有关**databaseName**，输入 SQL 数据库名称。
4.  这些变量特定于示例环境，不同的环境可能具有不同的变量。

## <a name="create-an-environment"></a>创建环境
下一阶段的部署将创建环境以用于开发或测试用途。

1. 在发布定义中，选择“添加任务”。
2. 上**任务**选项卡上，添加 Azure 开发测试实验室创建环境任务。 对任务进行如下配置：
    1. 对于“Azure RM 订阅”，从“可用 Azure 服务连接”列表中选择连接，或创建到 Azure 订阅的限制更严格的权限连接。 有关详细信息，请参阅 [Azure 资源管理器服务终结点](/azure/devops/pipelines/library/service-endpoints)。
2. 有关**实验室名称**，选择前面创建的实例的名称 *。
3. 有关**存储库名称**，选择资源管理器模板 (201) 推送到其中的存储库 *。
4. 有关**模板名称**，选择已保存到您源代码存储库 * 的环境的名称。 
5. **实验室名称**，**存储库名称**，并**模板名称**是 Azure 资源 Id 的友好的表示形式。 手动输入的友好名称将会导致失败，请使用下拉列表选择的信息。
6. 有关**环境名称**，输入用于唯一地标识在实验室中的环境实例的名称。  它必须在实验室中唯一。
7. **参数文件**并**参数**，允许自定义参数传递给环境。 或者两种方法可以用于设置参数值。 对于此示例中，将使用参数部分。 使用在环境中定义的变量的名称，例如： `-administratorLogin “$(administratorLogin)” -administratorLoginPassword “$(administratorLoginPassword)” -databaseName “$(databaseName)” -cacheSKUCapacity 1`
8. 在模板的输出部分中，可以通过传递环境模板中的信息。 检查**创建基于环境模板输出的输出变量**以便其他任务可以使用的数据。 `$(Reference name.Output Name)` 是要遵循的模式。 例如，如果引用名称为 DTL 和模板中的输出名称为位置变量将为`$(DTL.location)`。

## <a name="delete-the-environment"></a>删除环境
最后一个阶段是删除 Azure 开发测试实验室实例中部署的环境。 执行所需开发任务或在已部署的资源上运行所需的测试后，通常会删除该环境。

在发布定义中，选择**添加任务**，然后在**部署**选项卡上，添加**Azure 开发测试实验室删除环境**任务。 请如下所述对其进行配置：

1. 若要删除 VM，请参阅[Azure 开发测试实验室任务](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. 对于“Azure RM 订阅”，从“可用 Azure 服务连接”列表中选择连接，或创建到 Azure 订阅的限制更严格的权限连接。 有关详细信息，请参阅 [Azure 资源管理器服务终结点](/azure/devops/pipelines/library/service-endpoints)。
    2. 有关**实验室名称**，选择存在环境的实验室。
    3. 有关**环境名称**，输入要删除的环境的名称。
2. 输入发布定义名称，然后保存它。

## <a name="next-steps"></a>后续步骤
请参阅以下文章： 
- [使用资源管理器模板创建多 VM 环境](devtest-lab-create-environment-from-arm.md)。
- 适用于开发测试实验室自动化的快速入门资源管理器模板[开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-quickstart-templates)。
- [VSTS 进行故障排除页](/azure/devops/pipelines/troubleshooting)

