---
title: 教程 - 创建用户流 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何使用 Azure 门户在 Azure Active Directory B2C 中创建应用程序的用户流。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b76d56b17973c04992a8855917c814ced649bd0f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338252"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>教程：在 Azure Active Directory B2C 中创建用户流

在应用程序中，可以使用[用户流](active-directory-b2c-reference-policies.md)来让用户注册、登录或管理其个人资料。 可以在 Azure Active Directory (Azure AD) B2C 租户中创建多个不同类型的用户流，并根据需要在应用程序中使用它们。 可以跨应用程序重复使用用户流。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建注册和登录用户流
> * 创建配置文件编辑用户流
> * 创建密码重置用户流

本教程介绍如何使用 Azure 门户创建一些建议的用户流。 如需有关如何在应用程序中设置资源所有者密码凭据 (ROPC) 流的信息，请参阅[在 Azure AD B2C 中配置资源所有者密码凭据流](configure-ropc.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备组件

[注册](tutorial-register-applications.md)属于要创建的用户流的应用程序。 

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>创建注册和登录用户流

注册和登录用户流通过单一配置处理注册和登录体验。 根据上下文将应用程序用户引导至正确的路径。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。

    ![切换到订阅目录](./media/tutorial-create-user-flows/switch-directories.png)

3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
4. 在左侧菜单中，依次选择“用户流”、“新建用户流”。

    ![选择“新建用户流”](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

5. 在“建议”选项卡上选择“注册和登录”用户流。

    ![选择注册和登录用户流](./media/tutorial-create-user-flows/signup-signin-type.png)

6. 输入该用户流的**名称**。 例如 *signupsignin1*。
7. 对于“标识提供者”，请选择“电子邮件注册”。

    ![设置流属性](./media/tutorial-create-user-flows/signup-signin-properties.png)

8. 对于“用户属性和声明”，请选择在注册期间要从用户收集并发送的声明和属性。 例如，选择“显示更多”，然后选择“国家/地区”、“显示名称”和“邮政编码”。 单击“确定”。

    ![选择用户属性和声明](./media/tutorial-create-user-flows/signup-signin-attributes.png)

9. 单击“创建”以添加用户流。 名称中会自动追加前缀 *B2C_1*。

### <a name="test-the-user-flow"></a>测试用户流

1. 在创建的用户流的“概述”页上，选择“运行用户流”。
2. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
3. 单击“运行用户流”，然后选择“立即注册”。

    ![运行用户流](./media/tutorial-create-user-flows/signup-signin-run-now.png)

4. 输入有效的电子邮件地址，单击“发送验证码”，然后输入收到的验证码。
5. 输入新密码并确认。
6. 输入要显示的名称，选择所在的国家或地区，输入邮政编码，然后单击“创建”。 令牌将返回到 `https://jwt.ms` 并显示出来。
7. 现在可以再次运行用户流，你应该可以使用创建的帐户登录。 返回的令牌包含所选的名称、国家/地区和邮政编码声明。

## <a name="create-a-profile-editing-user-flow"></a>创建配置文件编辑用户流

如果希望用户能够在你的应用程序中编辑其个人资料，请使用个人资料编辑用户流。

1. 在左侧菜单中，依次选择“用户流”、“新建用户流”。
2. 在“建议”选项卡上选择“个人资料编辑”用户流。
3. 输入该用户流的**名称**。 例如 *profileediting1*。
4. 对于“标识提供者”，请选择“本地帐户登录”。
5. 对于“用户属性”，请选择你希望客户能够在其个人资料中编辑的属性。 例如，选择“显示更多”，然后选择“显示名称”和“职务”。 单击“确定”。
6. 单击“创建”以添加用户流。 名称中会自动追加前缀 *B2C_1*。

### <a name="test-the-user-flow"></a>测试用户流

1. 在创建的用户流的“概述”页上，选择“运行用户流”。
2. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
3. 单击“运行用户流”，然后使用前面创建的帐户登录。
4. 现在，你可以更改用户的显示名称和职务。 单击“继续”。 令牌将返回到 `https://jwt.ms` 并显示出来。

## <a name="create-a-password-reset-user-flow"></a>创建密码重置用户流

应用程序的用户可根据需要重置其密码。 若要启用密码重置，请使用密码重置用户流。

1. 在左侧菜单中，依次选择“用户流”、“新建用户流”。
2. 在“建议”选项卡上选择“密码重置”用户流。
3. 输入该用户流的**名称**。 例如 *passwordreset1*。
4. 对于“标识提供者”，请启用“使用电子邮件地址重置密码”。
5. 在“应用程序声明”下单击“显示更多”，并选择你希望在发回到应用程序的授权令牌中返回的声明。 例如，选择“用户的对象 ID”。
6. 单击“确定”。
7. 单击“创建”以添加用户流。 名称中会自动追加前缀 *B2C_1*。

### <a name="test-the-user-flow"></a>测试用户流

1. 在创建的用户流的“概述”页上，选择“运行用户流”。
2. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
3. 单击“运行用户流”，然后使用前面创建的帐户登录。
4. 现在可以更改用户的密码。 单击“继续”。 令牌将返回到 `https://jwt.ms` 并显示出来。

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建注册和登录用户流
> * 创建配置文件编辑用户流
> * 创建密码重置用户流

> [!div class="nextstepaction"]
> [将标识提供者添加到 Azure Active Directory B2C 中的应用程序](tutorial-add-identity-providers.md)