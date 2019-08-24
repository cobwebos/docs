---
title: 使用 Azure Functions 扩展 Azure 开发测试实验室 |Microsoft Docs
description: 了解如何使用 Azure Functions 扩展 Azure 开发测试实验室。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014355"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>使用 Azure Functions 扩展开发测试实验室
您可以使用 Azure Functions 来支持开发测试实验室已支持的方案以外的其他方案。 Azure Functions 可用于扩展服务的内置功能, 以满足特定于业务的需求。 以下列表提供了一些可能的方案。 本文介绍如何实现这些示例方案之一。

- 提供实验室中虚拟机 (Vm) 的顶级摘要
- [配置实验室以使用远程桌面网关](configure-lab-remote-desktop-gateway.md)
- 内部支持页上的相容性报告
- 使用户能够完成需要订阅中的权限增加的操作
- [基于开发测试实验室事件启动工作流](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>概述
[Azure Functions](../azure-functions/functions-overview.md)是 Azure 中的无服务器计算平台。 通过开发测试实验室在解决方案中使用 Azure Functions, 可以使用自己的自定义代码来增强现有功能。 有关 Azure Functions 的详细信息, 请参阅[Azure Functions 文档](../azure-functions/functions-overview.md)。 为了说明 Azure Functions 如何帮助满足你的需求或开发测试实验室中的完整方案, 本文使用了一个示例, 提供了实验室中 Vm 的顶级摘要, 如下所示:

**示例要求/方案**:用户可以查看有关实验室中所有 Vm 的详细信息, 包括操作系统、所有者和任何已应用的项目。  此外, 如果 "**应用 Windows 更新**" 项目最近未应用, 则可以使用一种简单的方法来应用它。

若要完成该方案, 你将使用下面的关系图中所述的两个函数:  

![总体流程](./media/extend-devtest-labs-azure-functions/flow.png)

这些示例函数的源代码位于[开发测试 Labs GitHub 存储库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions)中 ( C#和 PowerShell 实现都可用)。

- **UpdateInternalSupportPage**:此函数查询开发测试实验室, 并直接通过有关虚拟机的详细信息更新内部支持页。
- **ApplyWindowsUpdateArtifact**:对于实验室中的 VM, 此函数会应用**Windows update**项目。

## <a name="how-it-works"></a>工作原理
当用户在开发测试实验室中选择 "**内部支持**" 页面时, 会有一个预先填充的页面, 其中包含有关 vm、实验室所有者和支持联系人的信息。  

当你选择 "在**此处刷新**" 按钮时, 页面将调用第一个 Azure 函数:**UpdateInternalSupportPage**。 函数向开发测试实验室查询信息, 然后用新信息重写**内部支持**页。

对于未应用 Windows 更新项目的任何 Vm, 还可以执行其他操作, 此时将有一个按钮将 Windows 更新应用到 VM。 当你为 VM 选择了 "**运行 Windows 更新**" 按钮时, 页面将调用第二个 Azure 函数:**ApplyWindowsUpdateArtifact**。 此函数检查虚拟机是否正在运行, 如果是, 则直接应用[Windows 更新](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates)项目。

## <a name="step-by-step-walkthrough"></a>分步演练
本部分提供了有关设置更新**内部支持**页面所需的 Azure 资源的分步说明。 本演练提供了一个扩展开发测试实验室的示例。 对于其他方案, 可使用此模式。

### <a name="step-1-create-a-service-principal"></a>步骤 1：创建服务主体 
第一步是获取有权访问包含实验室的订阅的服务主体。 服务主体必须使用基于密码的身份验证。 可以通过[Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)、 [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)或[Azure 门户](../active-directory/develop/howto-create-service-principal-portal.md)来完成此操作。 如果你已有要使用的服务主体, 则可以跳过此步骤。

记下服务主体的**应用程序 id**、**密钥**和**租户 id** 。 稍后在本演练中将需要它们。 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>步骤 2：下载该示例, 并在 Visual Studio 2019 中打开
在本地下载[ C# Azure Functions 示例](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp)的副本 (通过克隆存储库或从[此处](https://github.com/Azure/azure-devtestlab/archive/master.zip)下载存储库)。  

1. 打开带有 Visual Studio 2019 的示例解决方案。  
1. 如果尚未安装, 请安装适用于 Visual Studio 的**Azure 开发**工作负荷。 可以通过 "**工具** -> " "**获取工具和功能**" 菜单项来安装它。

    ![“Azure 开发”工作负载](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. 生成解决方案。 选择 "**生成**", 然后选择 "**生成解决方案**" 菜单项。

### <a name="step-3-deploy-the-sample-to-azure"></a>步骤 3：将示例部署到 Azure
在 Visual Studio 的 "**解决方案资源管理器**" 窗口中, 右键单击**AzureFunctions**项目, 然后选择 "**发布**"。 按照向导完成发布到新的或现有的 Azure Function App。 有关使用 Visual Studio 开发和部署 Azure 函数的详细信息, 请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md)。

![“发布”对话框](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>步骤 4：收集应用程序设置
发布函数后, 需要从 Azure 门户获取这些函数的 Url。 

1. 导航到 [Azure 门户](https://portal.azure.com)。 
1. 查找函数应用。
1. 在 "**函数应用**" 页上, 选择函数。 
1. 选择 "**获取函数 URL** ", 如下图所示。 

    ![Azure 函数 Url](./media/extend-devtest-labs-azure-functions/function-url.png)
4. 复制并保存 URL。 对于其他 Azure 函数, 请重复这些步骤。 

还需要有关服务主体的其他信息, 例如应用程序 ID、密钥和租户 ID。


### <a name="step-5--update-application-settings"></a>步骤 5：更新应用程序设置
在 Visual Studio 中发布 Azure 函数后, 在 "**操作**" 下选择 "**编辑 Azure App Service 设置**"。 更新以下应用程序设置 (远程):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (默认值为 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![应用程序设置](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>步骤 6：测试 Azure 函数
本演练中的最后一步是测试 Azure 函数。  

1. 导航到在步骤3中创建的函数应用中的**UpdateInternalSupportPage**函数。 
1. 选择页面右侧的 "**测试**"。 
1. 在路由属性 (LABNAME、RESOURCEGROUPNAME 和 SUBSCRIPTIONID) 中输入。
1. 选择 "**运行**" 以执行该函数。  

    此函数将更新指定实验室的内部支持页。 它还包括一个按钮, 供用户下次调用该函数时

    ![Test 函数](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>后续步骤
Azure Functions 可以帮助扩展开发测试实验室的功能, 使其不超过内置功能, 帮助客户满足其团队的独特要求。 可以扩展此模式 & 进一步进一步扩展。  若要了解有关开发测试实验室的详细信息, 请参阅以下文章: 

- [开发测试实验室企业参考体系结构](devtest-lab-reference-architecture.md)
- [常见问题](devtest-lab-faq.md)
- [扩展开发测试实验室](devtest-lab-guidance-scale.md)
- [通过 PowerShell 实现开发测试实验室自动化](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








