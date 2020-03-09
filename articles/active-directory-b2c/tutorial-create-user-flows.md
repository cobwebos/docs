---
title: 教程-创建用户流-Azure Active Directory B2C
description: 了解如何在 Azure 门户中创建用户流，以便在 Azure Active Directory B2C 中为你的应用程序启用注册、登录和用户配置文件编辑。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 51adbb74635f66ca86347b536dc2607566dcb725
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389923"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>教程：在 Azure Active Directory B2C 中创建用户流

在应用程序中，你可能具有允许用户注册、登录或管理其配置文件的[用户流](user-flow-overview.md)。 可以在 Azure Active Directory B2C （Azure AD B2C）租户中创建不同类型的多个用户流，并根据需要在应用程序中使用它们。 可以跨应用程序重复使用用户流。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建注册和登录用户流
> * 创建配置文件编辑用户流
> * 创建密码重置用户流

本教程介绍如何使用 Azure 门户创建一些建议的用户流。 如果你正在查找有关如何在你的应用程序中设置资源所有者密码凭据（ROPC）流的信息，请参阅[在 Azure AD B2C 中配置资源所有者密码凭据流](configure-ropc.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

[注册](tutorial-register-applications.md)属于要创建的用户流的应用程序。

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>创建注册和登录用户流

注册和登录用户流通过单一配置处理注册和登录体验。 根据上下文将应用程序用户引导至正确的路径。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择 "**目录 + 订阅**" 图标，然后选择包含 Azure AD B2C 租户的目录。

    ![B2C 租户、目录和订阅窗格，Azure 门户](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. 在 Azure 门户中，搜索并选择 " **Azure AD B2C**"。
1. 在 "**策略**" 下，选择 "**用户流（策略）** "，然后选择 "**新建用户流**"。

    ![在门户中突出显示 "新建用户流" 按钮的用户流页面](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. 在 "**建议**" 选项卡上，选择 "**注册和登录**用户流"。

    ![选择已突出显示注册和登录流的用户流页面](./media/tutorial-create-user-flows/signup-signin-type.png)

1. 输入该用户流的**名称**。 例如 *signupsignin1*。
1. 对于“标识提供者”，请选择“电子邮件注册”。

    ![在突出显示属性的 Azure 门户中创建用户流页面](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. 对于“用户属性和声明”，请选择在注册期间要从用户收集并发送的声明和属性。 例如，选择 "**显示更多**"，然后选择 "**国家/地区**"、"**显示名称**" 和 "**邮政编码**"。 单击“确定”。

    ![选择了三个声明的属性和声明选择页](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. 单击“创建”以添加用户流。 名称中会自动追加前缀 *B2C_1*。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择您创建的用户流以打开其 "概述" 页，然后选择 "**运行用户流**"。
1. 对于“应用程序”，请选择前面已注册的名为 **webapp1** 的 Web 应用程序。 “回复 URL”应显示为 **。** `https://jwt.ms`
1. 单击“运行用户流”，然后选择“立即注册”。

    ![选中 "运行用户流" 按钮的门户中的 "运行用户流" 页面](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. 输入有效的电子邮件地址，单击 "**发送验证码**"，输入收到的验证码，然后选择 "**验证代码**"。
1. 输入新密码并确认。
1. 选择您所在的国家/地区，输入要显示的名称，输入邮政编码，然后单击 "**创建**"。 令牌将返回到 `https://jwt.ms` 并显示出来。
1. 现在可以再次运行用户流，你应该可以使用创建的帐户登录。 返回的令牌包括你在 "国家/地区"、"名称" 和 "邮政编码" 中选择的声明。

## <a name="create-a-profile-editing-user-flow"></a>创建配置文件编辑用户流

如果希望用户能够在你的应用程序中编辑其个人资料，请使用个人资料编辑用户流。

1. 在 Azure AD B2C 租户概述 "页的菜单中，选择"**用户流（策略）** "，然后选择"**新建用户流**"。
1. 选择 "**建议**" 选项卡上的 "**配置文件编辑**用户流"。
1. 输入该用户流的**名称**。 例如 *profileediting1*。
1. 对于“标识提供者”，请选择“本地帐户登录”。
1. 对于“用户属性”，请选择你希望客户能够在其个人资料中编辑的属性。 例如，选择 "**显示更多**"，然后选择 "**显示名称**" 和 "**职务**" 的属性和声明。 单击“确定”。
1. 单击“创建”以添加用户流。 名称中会自动追加前缀 *B2C_1*。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择您创建的用户流以打开其 "概述" 页，然后选择 "**运行用户流**"。
1. 对于“应用程序”，请选择前面已注册的名为 **webapp1** 的 Web 应用程序。 “回复 URL”应显示为 **。** `https://jwt.ms`
1. 单击“运行用户流”，然后使用前面创建的帐户登录。
1. 现在，你可以更改用户的显示名称和职务。 单击“继续”。 令牌将返回到 `https://jwt.ms` 并显示出来。

## <a name="create-a-password-reset-user-flow"></a>创建密码重置用户流

若要使应用程序的用户能够重置其密码，请使用密码重置用户流。

1. 在 Azure AD B2C 租户概述 "菜单中，选择"**用户流（策略）** "，然后选择"**新建用户流**"。
1. 选择 "**建议**" 选项卡上的 "**密码重置**用户流"。
1. 输入该用户流的**名称**。 例如 *passwordreset1*。
1. 对于“标识提供者”，请启用“使用电子邮件地址重置密码”。
1. 在“应用程序声明”下单击“显示更多”，并选择你希望在发回到应用程序的授权令牌中返回的声明。 例如，选择“用户的对象 ID”。
1. 单击“确定”。
1. 单击“创建”以添加用户流。 名称中会自动追加前缀 *B2C_1*。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择您创建的用户流以打开其 "概述" 页，然后选择 "**运行用户流**"。
1. 对于“应用程序”，请选择前面已注册的名为 **webapp1** 的 Web 应用程序。 “回复 URL”应显示为 **。** `https://jwt.ms`
1. 单击 "**运行用户流**"，验证你之前创建的帐户的电子邮件地址，然后选择 "**继续**"。
1. 现在可以更改用户的密码。 更改密码并选择 "**继续**"。 令牌将返回到 `https://jwt.ms` 并显示出来。

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建注册和登录用户流
> * 创建配置文件编辑用户流
> * 创建密码重置用户流

接下来，了解如何向应用程序添加标识提供程序，以便能够使用 Azure AD、Amazon、Facebook、GitHub、LinkedIn、Microsoft 或 Twitter 等提供程序进行用户登录。

> [!div class="nextstepaction"]
> [向应用程序添加标识提供程序 >](tutorial-add-identity-providers.md)