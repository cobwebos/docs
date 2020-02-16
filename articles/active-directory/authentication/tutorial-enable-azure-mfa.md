---
title: 启用 Azure 多重身份验证
description: 本教程介绍如何为一组用户启用 Azure 多重身份验证，并在登录事件期间测试第二因素提示。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 253eb23be03c1cc0f2abf4ad1fed734426dc287d
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154675"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-multi-factor-authentication"></a>教程：使用 Azure 多重身份验证保护用户登录事件

多重身份验证 (MFA) 是在登录事件期间提示用户完成其他形式的身份识别的过程。 此提示可以是让用户在手机上输入某个代码，或提供指纹扫描。 需要另一种形式的身份验证时，会提高安全性，因为攻击者并不容易获取或复制进行多重身份验证所需的额外内容。

在特定的登录事件期间，可以通过 Azure 多重身份验证和条件访问策略来灵活地为用户启用 MFA。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建条件访问策略以便为一组用户启用 Azure 多重身份验证
> * 配置提示执行 MFA 的策略条件
> * 以用户身份测试 MFA 过程

## <a name="prerequisites"></a>必备条件

需有以下资源和特权才能完成本教程：

* 一个至少启用了 Azure AD Premium 或试用版许可证的有效 Azure AD 租户。
    * 如果需要，[可免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 一个拥有全局管理员特权的帐户。 
* 你知道其密码的非管理员测试用户，例如 *testuser*。 本教程将使用此帐户测试最终用户的 Azure 多重身份验证体验。
    * 如果需要创建用户，请参阅[快速入门：向 Azure Active Directory 添加新用户](../add-users-azure-active-directory.md)。
* 该非管理员用户所属的组，例如 *MFA-Test-Group*。 本教程将为此组启用 Azure 多重身份验证。
    * 如需创建一个组，请参阅如何[在 Azure Active Directory 中创建组并添加成员](../active-directory-groups-create-azure-portal.md)。

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

建议通过条件访问策略来启用和使用 Azure 多重身份验证。 使用条件访问可以创建和定义策略，用于对登录事件做出反应，并在向用户授予对应用程序或服务的访问权限之前请求更多的操作。

![有关条件访问如何保护登录过程的概览图](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

条件访问策略可以做到精细且具体，其目标是使用户能够随时随地保持工作效率，同时为组织提供保护。 本教程将创建一个基本的条件访问策略，以便在用户登录到 Azure 门户时提示其执行 MFA。 在本教程系列的后面某篇文章中，你将使用基于风险的条件访问策略来配置 Azure 多重身份验证。

首先，按如下所述创建一个条件访问策略，并分配测试用户组：

1. 使用拥有全局管理员权限的帐户登录到 [Azure 门户](https://portal.azure.com)。 
1. 搜索并选择“Azure Active Directory”，然后从左侧菜单中选择“安全性”。  
1. 依次选择“条件访问”、“+ 新建策略”。  
1. 输入策略的名称，例如“MFA 试验”。 
1. 在“分配”下选择“用户和组”，然后选中“选择用户和组”单选按钮。   
1. 选中“用户和组”对应的框，然后选择“选择”以浏览可用的 Azure AD 用户和组。  
1. 浏览并选择 Azure AD 组（例如 *MFA-Test-Group*），然后选择“选择”。 

    [![](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png "Select your Azure AD group to use with the Conditional Access policy")](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. 若要对该组应用条件访问策略，请选择“完成”。 

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>配置多重身份验证的条件

创建条件访问策略并分配测试用户组后，接下来请定义触发该策略的云应用或操作。 这些云应用或操作是你确定需要进一步处理的方案，例如，提示执行 MFA。 例如，可以要求在访问某个财务应用程序或管理工具时发出附加的验证提示。

对于本教程，请将条件访问策略配置为在用户登录到 Azure 门户时要求执行 MFA。

1. 选择“云应用或操作”。  可以选择将条件访问策略应用到“所有云应用”，也可以“选择应用”。   为了提供灵活性，还可以从策略中排除某些应用。

    对于本教程，请在“包括”页上，选中“选择应用”单选按钮。  

1. 选择“选择”，然后浏览可用的登录事件列表。 

    对于本教程，请选择“Microsoft Azure 管理”，以便将策略应用到 Azure 门户登录事件。 

1. 若要将策略应用到选定的应用，请依次选择“选择”、“完成”。  

    ![选择要包含在条件访问策略中的 Microsoft Azure 管理应用](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

使用访问控制可以定义授予用户访问权限所要满足的要求，例如，需要一个已批准的客户端应用，或使用已加入混合 Azure AD 的设备。 在本教程中，请将访问控制配置为在 Azure 门户登录事件期间要求执行 MFA。

1. 在“访问控制”下选择“授予”，并确保已选中“授予访问权限”单选按钮。   
1. 选中“需要多重身份验证”对应的复选框，然后选择“选择”。  

若要查看配置对用户产生的影响，可将条件访问策略设置为“仅限报告”；如果不想要立即使用策略，则可将其设置为“关闭”。   由于已经为本教程指定了目标测试用户组，因此让我们启用该策略，然后测试 Azure 多重身份验证。

1. 将“启用策略”  开关设置为“开”  。
1. 若要应用条件访问策略，请选择“创建”。 

## <a name="test-azure-multi-factor-authentication"></a>测试 Azure 多重身份验证

让我们看看该条件访问策略和 Azure 多重身份验证的运作方式。 首先，登录到不要求执行 MFA 的资源，如下所述：

1. 在 InPrivate 或 incognito 模式下打开新的浏览器窗口并浏览到 [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. 以非管理员测试用户（例如 *testuser*）的身份登录。 系统不会提示完成 MFA。
1. 关闭浏览器窗口。

现在登录到 Azure 门户。 由于已在条件访问策略中将 Azure 门户配置为要求执行额外的验证，因此会出现 Azure 多重身份验证提示。

1. 在 InPrivate 或 incognito 模式下打开新的浏览器窗口并浏览到 [https://portal.azure.com](https://portal.azure.com)。
1. 以非管理员测试用户（例如 *testuser*）的身份登录。 需要注册并使用 Azure 多重身份验证。 按照提示完成该过程，并验证是否可成功登录到 Azure 门户。

    ![按照浏览器中的提示操作，并在出现已注册多重身份验证的提示后登录](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. 关闭浏览器窗口。

## <a name="clean-up-resources"></a>清理资源

如果你不再想要使用条件访问策略来启用本教程中配置的 Azure 多重身份验证，请使用以下步骤删除该策略：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”，然后从左侧菜单中选择“安全性”。  
1. 选择“条件访问”，然后选择创建的策略，例如“MFA 试验”  
1. 选择“删除”，然后确认删除该策略。 

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用条件访问策略为选定的用户组启用了 Azure 多重身份验证。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 创建条件访问策略，以便为一组 Azure AD 用户启用 Azure 多重身份验证
> * 配置提示执行 MFA 的策略条件
> * 以用户身份测试 MFA 过程

> [!div class="nextstepaction"]
> [为自助式密码重置 (SSPR) 启用密码写回](tutorial-enable-writeback.md)
