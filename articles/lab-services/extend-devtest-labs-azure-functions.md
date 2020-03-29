---
title: 使用 Azure 功能扩展 Azure 开发人员测试实验室 |微软文档
description: 了解如何使用 Azure 函数扩展 Azure 开发人员测试实验室。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "70014355"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>使用 Azure Functions 扩展开发测试实验室
可以使用 Azure 函数支持除 DevTest Labs 已支持的方案之外的其他方案。 Azure 函数可用于扩展服务的内置功能，以满足特定于业务的需求。 下面的列表提供了一些可能的方案。 本文介绍如何实现这些示例方案之一。

- 在实验室中提供虚拟机 （VM） 的顶级摘要
- [将实验室配置为使用远程桌面网关](configure-lab-remote-desktop-gateway.md)
- 内部支持页面上的合规性报告
- 使用户能够完成需要在订阅中增加权限的操作
- [基于 DevTest 实验室事件启动工作流](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>概述
[Azure 函数](../azure-functions/functions-overview.md)是 Azure 中的无服务器计算平台。 使用使用开发人员测试实验室的解决方案中的 Azure 函数，可以利用自己的自定义代码增强现有功能。 有关 Azure 函数的详细信息，请参阅[Azure 函数文档](../azure-functions/functions-overview.md)。 为了说明 Azure 函数如何帮助满足 DevTest 实验室中的要求或完整的方案，本文使用在实验室中提供 VM 的顶级摘要的示例，如下所示：

**示例要求/方案**：用户可以查看有关实验室中所有 VM 的详细信息，包括操作系统、所有者和任何应用的项目。  此外，如果最近未应用 **"应用 Windows 更新**"项目，则应用它的方法很简单。

要完成方案，您将使用如下图中所述的两个函数：  

![总体流程](./media/extend-devtest-labs-azure-functions/flow.png)

这些示例函数的源代码位于[DevTest Labs GitHub 存储库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions)中（C# 和 PowerShell 实现都可用）。

- **更新内部支持页**：此功能查询 DevTest 实验室，并直接更新内部支持页，并提供有关虚拟机的详细信息。
- **应用 Windows 更新工件**：对于实验室中的 VM，此功能应用**Windows 更新**项目。

## <a name="how-it-works"></a>工作原理
当用户选择 DevTest Labs**中的"内部支持**"页时，他们有一个预填充的页面，其中包含有关 VM、实验室所有者和支持联系人的信息。  

当您选择 **"选择此处刷新**"按钮时，页面将调用第一个 Azure 函数：**更新内部支持页**。 该函数查询 DevTest Labs 的信息，然后使用新信息重写**内部支持**页。

对于最近未应用 Windows Update 项目的任何 VM，还可以执行其他操作，将有一个按钮将窗口更新应用于 VM。 当您为 VM 选择 "**运行 Windows 更新**"按钮时，该页将调用第二个 Azure 函数：应用 Windows**更新项**。 此函数检查虚拟机是否正在运行，如果是，则直接应用[Windows 更新](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates)项目。

## <a name="step-by-step-walkthrough"></a>分步演练
本节提供用于设置更新**内部支持**页所需的 Azure 资源的分步说明。 本演练提供了扩展 DevTest 实验室的一个示例。 您可以将此模式用于其他方案。

### <a name="step-1-create-a-service-principal"></a>第 1 步：创建服务主体 
第一步是获取具有包含实验室的订阅权限的服务主体。 服务主体必须使用基于密码的身份验证。 它可以使用 Azure [CLI、Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) [PowerShell](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)或[Azure 门户](../active-directory/develop/howto-create-service-principal-portal.md)来完成。 如果您已有要使用的服务主体，则可以跳过此步骤。

记下服务主体**的应用程序 ID**、**密钥**和**租户 ID。** 在本演练中，您将需要它们。 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>第 2 步：下载示例并在 Visual Studio 2019 中打开
在本地下载[C# Azure 函数示例](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp)的副本（通过克隆存储库或[从这里](https://github.com/Azure/azure-devtestlab/archive/master.zip)下载存储库）。  

1. 使用 Visual Studio 2019 打开示例解决方案。  
1. 如果尚未安装 Visual Studio，请安装 Azure**开发**工作负荷。 它可以通过**工具** -> **获取工具和功能**菜单项安装）。

    ![“Azure 开发”工作负载](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. 生成解决方案。 选择 **"生成****"，然后选择"生成解决方案"** 菜单项。

### <a name="step-3-deploy-the-sample-to-azure"></a>步骤 3：将示例部署到 Azure
在可视化工作室中，在 **"解决方案资源管理器"** 窗口中，右键单击**Azure 功能**项目，然后选择 **"发布**"。 请按照向导完成发布到新函数应用或现有 Azure 函数应用。 有关使用 Visual Studio 开发和部署 Azure 函数的详细信息，请参阅[使用 Visual Studio 开发 Azure 函数](../azure-functions/functions-develop-vs.md)。

![“发布”对话框](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>第 4 步：收集应用程序设置
发布函数后，您需要从 Azure 门户获取这些函数的 URL。 

1. 导航到[Azure 门户](https://portal.azure.com)。 
1. 查找功能应用。
1. 在 **"功能应用"** 页上，选择该函数。 
1. 选择 **"获取函数 URL"，** 如下图所示。 

    ![Azure 函数 URL](./media/extend-devtest-labs-azure-functions/function-url.png)
4. 复制并保存 URL。 对其他 Azure 函数重复这些步骤。 

您还需要有关服务主体的其他信息，如应用程序 ID、密钥和租户 ID。


### <a name="step-5--update-application-settings"></a>第 5 步：更新应用程序设置
在可视化工作室中，在发布 Azure 函数后，选择"**操作**"下的 **"编辑 Azure 应用服务设置**"。 更新以下应用程序设置（远程）：

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- Windows 更新允许天（默认为 7）
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![应用程序设置](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>第 6 步：测试 Azure 函数
本演练的最后一步是测试 Azure 函数。  

1. 导航到步骤 3 中创建的函数应用中的 **"更新内部支持Page"** 功能。 
1. 选择页面右侧的 **"测试**"。 
1. 在路由属性（LABNAME、资源组名称和订阅 ID）中输入。
1. 选择 **"运行**"以执行该函数。  

    此功能将更新指定实验室的内部支持页。 它还包括一个按钮，供用户下次直接调用该函数

    ![测试功能](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>后续步骤
Azure 函数可帮助将 DevTest Labs 的功能扩展到已内置功能之外，并帮助客户满足其团队的独特要求。 这种模式可以&进一步扩展，以覆盖更多。  要了解有关 DevTest 实验室的更多内容，请参阅以下文章： 

- [开发人员测试实验室企业参考体系结构](devtest-lab-reference-architecture.md)
- [常见问题](devtest-lab-faq.md)
- [扩展开发测试实验室](devtest-lab-guidance-scale.md)
- [使用 PowerShell 实现自动化开发测试实验室](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








