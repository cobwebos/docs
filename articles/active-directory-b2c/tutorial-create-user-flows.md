---
title: 教程-创建用户流-Azure Active Directory B2C
description: 了解如何在 Azure 门户上启用登录，请登录，并为 Azure Active Directory B2C 中的应用程序的用户配置文件编辑中创建用户流。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 111196388d0e17ecde8a2055959f2f573e43ade8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056329"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>教程：在 Azure Active Directory B2C 中创建用户流

在您的应用程序可能有[用户流](active-directory-b2c-reference-policies.md)，使用户能注册、 登录，或管理其配置文件。 可以在 Azure Active Directory (Azure AD) B2C 租户中创建多个不同类型的用户流，并根据需要在应用程序中使用它们。 可以跨应用程序重复使用用户流。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建注册和登录用户流
> * 创建配置文件编辑用户流
> * 创建密码重置用户流

本教程介绍如何使用 Azure 门户创建一些建议的用户流。 如果您正在寻找有关如何设置资源所有者密码凭据 (ROPC) 流应用程序中的信息，请参阅[配置 Azure AD B2C 中的资源所有者密码凭据流](configure-ropc.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

[注册](tutorial-register-applications.md)属于要创建的用户流的应用程序。

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>创建注册和登录用户流

注册和登录用户流通过单一配置处理注册和登录体验。 根据上下文将应用程序用户引导至正确的路径。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录  。

    ![切换到订阅目录](./media/tutorial-create-user-flows/switch-directories.PNG)

1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”   。
1. 在左侧菜单中下**策略**，选择**用户流 （策略）** ，然后选择**新建用户流**。

    ![选择“新建用户流”](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. 上**推荐**选项卡上，选择**登录并在登录**用户流。

    ![选择注册和登录用户流](./media/tutorial-create-user-flows/signup-signin-type.png)

1. 输入该用户流的**名称**。 例如 *signupsignin1*。
1. 对于“标识提供者”，请选择“电子邮件注册”。  

    ![设置流属性](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. 对于“用户属性和声明”，请选择在注册期间要从用户收集并发送的声明和属性。  例如，选择**显示更多**，然后选择属性和声明**国家/地区**，**显示名称**，并**邮政编码**。 单击“确定”。 

    ![选择用户属性和声明](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. 单击“创建”  以添加用户流。 名称中会自动追加前缀 *B2C_1*。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择以打开其概述页上，然后选择你创建的用户流**运行用户流**。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。  “回复 URL”应显示为 `https://jwt.ms`。 
1. 单击“运行用户流”，然后选择“立即注册”。  

    ![运行用户流](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. 输入有效的电子邮件地址，请单击**发送验证码**，输入的验证码的接收，然后选择**验证代码**。
1. 输入新密码并确认。
1. 选择你的国家/地区和区域，输入你想显示的名称、 输入邮政编码，，然后单击**创建**。 令牌将返回到 `https://jwt.ms` 并显示出来。
1. 现在可以再次运行用户流，你应该可以使用创建的帐户登录。 返回的令牌包含所选的国家/地区、 名称和邮政编码的声明。

## <a name="create-a-profile-editing-user-flow"></a>创建配置文件编辑用户流

如果希望用户能够在你的应用程序中编辑其个人资料，请使用个人资料编辑用户流。

1. 在 Azure AD B2C 租户的概述页的左侧菜单中，选择**用户流 （策略）** ，然后选择**新建用户流**。
1. 在“建议”选项卡上选择“个人资料编辑”用户流。 
1. 输入该用户流的**名称**。 例如 *profileediting1*。
1. 对于“标识提供者”  ，请选择“本地帐户登录”。 
1. 对于“用户属性”，请选择你希望客户能够在其个人资料中编辑的属性。  例如，选择**显示更多**，然后选择属性和声明**显示名称**并**职务**。 单击“确定”。 
1. 单击“创建”  以添加用户流。 名称中会自动追加前缀 *B2C_1*。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择以打开其概述页上，然后选择你创建的用户流**运行用户流**。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。  “回复 URL”应显示为 `https://jwt.ms`。 
1. 单击“运行用户流”，然后使用前面创建的帐户登录。 
1. 现在，你可以更改用户的显示名称和职务。 单击 **“继续”** 。 令牌将返回到 `https://jwt.ms` 并显示出来。

## <a name="create-a-password-reset-user-flow"></a>创建密码重置用户流

若要启用应用程序重置其密码的用户，您使用的密码重置用户流。

1. 在左侧菜单中，选择**用户流 （策略）** ，然后选择**新建用户流**。
1. 在“建议”选项卡上选择“密码重置”用户流。 
1. 输入该用户流的**名称**。 例如 *passwordreset1*。
1. 对于“标识提供者”  ，请启用“使用电子邮件地址重置密码”。 
1. 在“应用程序声明”下单击“显示更多”，并选择你希望在发回到应用程序的授权令牌中返回的声明。  例如，选择“用户的对象 ID”。 
1. 单击“确定”。 
1. 单击“创建”  以添加用户流。 名称中会自动追加前缀 *B2C_1*。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择以打开其概述页上，然后选择你创建的用户流**运行用户流**。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。  “回复 URL”应显示为 `https://jwt.ms`。 
1. 单击**运行用户流**，请验证您以前创建的并选择的帐户的电子邮件地址**继续**。
1. 现在可以更改用户的密码。 更改密码，然后选择**继续**。 令牌将返回到 `https://jwt.ms` 并显示出来。

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建注册和登录用户流
> * 创建配置文件编辑用户流
> * 创建密码重置用户流

接下来，了解如何将标识提供者添加到应用程序以使用户登录与 Azure AD 中，如提供商 Amazon、 Facebook、 GitHub、 LinkedIn、 Microsoft 或 Twitter。

> [!div class="nextstepaction"]
> [标识提供者添加到你的应用程序 >](tutorial-add-identity-providers.md)