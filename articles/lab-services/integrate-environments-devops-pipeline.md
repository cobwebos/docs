---
title: 在 Azure 开发测试实验室中将环境集成到 Azure Pipelines |Microsoft Docs
description: 了解如何将 Azure 开发测试实验室环境集成到 Azure DevOps 持续集成（CI）和持续交付（CD）管道。
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
ms.openlocfilehash: c665c65dcda2266a7acd7bc78726179d559f4d64
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163926"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>将环境集成到 Azure DevOps CI/CD 管道
你可以使用 Azure DevOps Services （以前称为 Visual Studio Team Services）中安装的 Azure 开发测试实验室任务扩展，轻松地将持续集成（CI）/持续交付（CD）生成和发布管道与 Azure 集成开发测试实验室。 这些扩展使你可以更轻松地快速部署特定测试任务的[环境](devtest-lab-test-env.md)，并在测试完成后将其删除。 

本文介绍如何在一个完整的管道中创建和部署环境，并删除环境。 通常会在自己的自定义生成-测试-部署管道中单独执行每个任务。 本文中使用的扩展是这些[create/DELETE DTL VM 任务](devtest-lab-integrate-ci-cd-vsts.md)的补充：

- 创建环境
- 删除环境

## <a name="before-you-begin"></a>开始之前
将 CI/CD 管道与 Azure 开发测试实验室集成之前，请从 Visual Studio Marketplace 安装[Azure 开发测试实验室任务](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)扩展。 

## <a name="create-and-configure-the-lab-for-environments"></a>创建和配置适用于环境的实验室
本部分介绍如何创建和配置将在其中部署 Azure 环境的实验室。

1. 如果还没有实验室，请[创建](devtest-lab-create-lab.md)一个。 
2. 按照本文中的说明配置实验室和创建环境模板：[使用 Azure 资源管理器模板创建多 VM 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)。
3. 对于本示例，请使用现有的 Azure 快速入门模板[https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/)。
4. 将**201 redis**文件夹复制到在步骤2中配置的存储库中的**ArmTemplate**文件夹。

## <a name="create-a-release-definition"></a>创建发布定义
要创建发布定义，请执行以下操作：

1.  在 "**生成 & 发布" 中心**的 "**发布**" 选项卡上，选择**加号（+）** 按钮。
2.  在“创建发布定义”窗口中，选择“空”模板，然后选择“下一步”。
3.  选择“稍后选择”和“创建”，以新建有一个默认环境且无链接项目的发布定义。
4.  若要打开快捷菜单，请在新的发布定义中，选择环境名称旁边的**省略号（...）** ，然后选择 "**配置变量**"。
5.  在“配置 - 环境”窗口中，对于在发布定义中使用的变量，输入以下值：
1.  对于**administratorLogin**，请输入 SQL 管理员登录名。
2.  对于 " **administratorLoginPassword**"，请输入 SQL 管理员登录名要使用的密码。 使用“挂锁”图标隐藏和保护密码。
3.  对于**databaseName**，输入 SQL 数据库名称。
4.  这些变量特定于示例环境，不同环境可能具有不同的变量。

## <a name="create-an-environment"></a>创建环境
部署的下一阶段是创建环境以用于开发或测试目的。

1. 在发布定义中，选择“添加任务”。
2. 在 "**任务**" 选项卡上，添加 "Azure 开发测试实验室创建环境" 任务。 对任务进行如下配置：
    1. 对于“Azure RM 订阅”，从“可用 Azure 服务连接”列表中选择连接，或创建到 Azure 订阅的限制更严格的权限连接。 有关详细信息，请参阅 [Azure 资源管理器服务终结点](/azure/devops/pipelines/library/service-endpoints)。
2. 对于 "**实验室名称**"，请选择之前创建的实例的名称 *。
3. 对于 "**存储库名称**"，请选择已将资源管理器模板（201）推送到的存储库。
4. 对于 "**模板名称**"，请选择保存到源代码存储库中的环境的名称 *。 
5. **实验室名称**、**存储库名称**和**模板名称**是 Azure 资源 id 的友好表示形式。 手动输入友好名称将导致失败，请使用下拉列表选择该信息。
6. 对于 "**环境名称**"，请输入一个名称以唯一标识实验室中的环境实例。  它在实验室中必须是唯一的。
7. **参数文件**和**参数**，允许将自定义参数传递到环境。 其中一个或两个都可用于设置参数值。 在此示例中，将使用 Parameters 节。 使用在环境中定义的变量的名称，例如： `-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. 可以在模板的输出部分中传递环境模板中的信息。 选中 "**基于环境模板输出创建输出变量"** ，以便其他任务可以使用该数据。 `$(Reference name.Output Name)` 是要遵循的模式。 例如，如果引用名称为 DTL，并且模板中的输出名称为 location，则变量将 `$(DTL.location)`。

## <a name="delete-the-environment"></a>删除环境
最后一个阶段是删除在 Azure 开发测试实验室实例中部署的环境。 通常会在执行开发任务后删除环境，或在部署的资源上运行所需的测试。

在发布定义中，选择 "**添加任务**"，然后在 "**部署**" 选项卡上添加 " **Azure 开发测试实验室删除环境**" 任务。 请如下所述对其进行配置：

1. 若要删除 VM，请参阅[Azure 开发测试实验室任务](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)：
    1. 对于“Azure RM 订阅”，从“可用 Azure 服务连接”列表中选择连接，或创建到 Azure 订阅的限制更严格的权限连接。 有关详细信息，请参阅 [Azure 资源管理器服务终结点](/azure/devops/pipelines/library/service-endpoints)。
    2. 对于 "**实验室名称**"，请选择环境所在的实验室。
    3. 对于 "**环境名称**"，请输入要删除的环境的名称。
2. 输入发布定义名称，然后保存它。

## <a name="next-steps"></a>后续步骤
请参阅以下文章： 
- [资源管理器模板创建多 VM 环境](devtest-lab-create-environment-from-arm.md)。
- 快速入门资源管理器[开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-quickstart-templates)中的开发测试实验室自动化的模板。
- [VSTS 故障排除页](/azure/devops/pipelines/troubleshooting)

