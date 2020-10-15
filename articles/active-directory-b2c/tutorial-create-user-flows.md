---
title: 教程 - 创建用户流 - Azure Active Directory B2C
description: 按照本教程操作，了解如何在 Azure 门户中创建用户流，以便在 Azure Active Directory B2C 中启用应用程序的注册、登录和用户配置文件编辑。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 041b1766ae6a64f51d922de128ef316cc0ed8260
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87922163"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>教程：在 Azure Active Directory B2C 中创建用户流

可以在应用程序中使用[用户流](user-flow-overview.md)，让用户注册、登录或管理其配置文件。 可以在 Azure Active Directory B2C (Azure AD B2C) 租户中创建多个不同类型的用户流，并根据需要在应用程序中使用它们。 可以跨应用程序重复使用用户流。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建注册和登录用户流
> * 创建配置文件编辑用户流
> * 创建密码重置用户流

本教程介绍如何使用 Azure 门户创建一些建议的用户流。 若要了解如何在应用程序中设置资源所有者密码凭据 (ROPC) 流，请参阅[在 Azure AD B2C 中配置资源所有者密码凭据流](configure-ropc.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!IMPORTANT]
> 我们更改了引用用户流版本的方式。 之前，我们提供 V1（生产就绪）版本，还提供了 V1.1 和 V2（预览版）版本。 而现在，我们将用户流整合到了“建议”（下一代预览版）和“标准”（正式发布版）版本中 。 所有 V1.1 和 V2 旧预览版用户流将在 2021 年 8 月 1 日之前逐渐被弃用。 有关详细信息，请参阅 [Azure AD B2C 中的用户流版本](user-flow-versions.md)。

## <a name="prerequisites"></a>先决条件

[注册](tutorial-register-applications.md)属于要创建的用户流的应用程序。

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>创建注册和登录用户流

注册和登录用户流通过单一配置处理注册和登录体验。 根据上下文将应用程序用户引导至正确的路径。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。

    ![Azure 门户 中的 B2C 租户、目录和订阅窗格](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在“策略”下，依次选择“用户流”、“新建用户流”。

    ![门户中的“用户流”页面，突出显示了“新建用户流”按钮](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. 在“创建用户流”页面上，选择“注册和登录”用户流 。

    ![选择突出显示了“注册和登录”流的用户流页面](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. 在“选择版本”下，选择“建议”，然后选择“创建”  。 （[详细了解](user-flow-versions.md)用户流版本。）

    ![在 Azure 门户中创建用户流页面（其中，属性突出显示）](./media/tutorial-create-user-flows/select-version.png)

1. 输入该用户流的**名称**。 例如 *signupsignin1*。
1. 对于“标识提供者”，请选择“电子邮件注册”。
1. 对于“用户属性和声明”，请选择在注册期间要从用户收集并发送的声明和属性。 例如，选择“显示更多”，然后选择“国家/地区”、“显示名称”和“邮政编码”所对应的属性和声明。 单击“确定”。

    ![属性和声明选择页，有三个声明处于选中状态](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. 单击“创建”以添加用户流。 名称中会自动附加前缀 B2C_1。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择已创建的用户流以打开其概览页，然后选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击“运行用户流”，然后选择“立即注册”。

    ![门户中的“运行用户流”页面，突出显示了“运行用户流”按钮](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. 输入有效的电子邮件地址，单击“发送验证码”，输入收到的验证码，然后选择“验证代码”。
1. 输入新密码并确认。
1. 选择所在的国家和地区，输入要显示的名称，输入邮政编码，然后单击“创建”。 令牌将返回到 `https://jwt.ms` 并显示出来。
1. 现在可以再次运行用户流，你应该可以使用创建的帐户登录。 返回的令牌包含所选国家/地区、名称和邮政编码的声明。

## <a name="create-a-profile-editing-user-flow"></a>创建配置文件编辑用户流

如果希望用户能够在你的应用程序中编辑其个人资料，请使用个人资料编辑用户流。

1. 在 Azure AD B2C 租户概述页面的菜单中，选择“用户流”，然后选择“新建用户流”。
1. 在“创建用户流”页面上，选择“个人资料编辑”用户流 。 
1. 在“选择版本”下，选择“建议”，然后选择“创建”  。
1. 输入该用户流的**名称**。 例如 *profileediting1*。
1. 对于“标识提供者”，请选择“本地帐户登录”。
2. 对于“用户属性”，请选择你希望客户能够在其个人资料中编辑的属性。 例如，选择“显示更多”，然后选择“显示名称”和“职务”所对应的属性和声明。 单击 **“确定”** 。
3. 单击“创建”以添加用户流。 名称中会自动追加前缀 *B2C_1*。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择已创建的用户流以打开其概览页，然后选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击“运行用户流”，然后使用前面创建的帐户登录。
1. 现在，你可以更改用户的显示名称和职务。 单击 **“继续”** 。 令牌将返回到 `https://jwt.ms` 并显示出来。

## <a name="create-a-password-reset-user-flow"></a>创建密码重置用户流

若要允许应用程序用户重置其密码，请使用密码重置用户流。

1. 在 Azure AD B2C 租户概述菜单中，选择“用户流”，然后选择“新建用户流”。
1. 在“创建用户流”页面上，选择“密码重置”用户流 。 
1. 在“选择版本”下，选择“建议”，然后选择“创建”  。
1. 输入该用户流的**名称**。 例如 *passwordreset1*。
1. 对于“标识提供者”，请启用“使用电子邮件地址重置密码”。
2. 在“应用程序声明”下单击“显示更多”，并选择你希望在发回到应用程序的授权令牌中返回的声明。 例如，选择“用户的对象 ID”。
3. 单击 **“确定”** 。
4. 单击“创建”以添加用户流。 名称中会自动追加前缀 *B2C_1*。

### <a name="test-the-user-flow"></a>测试用户流

1. 选择已创建的用户流以打开其概览页，然后选择“运行用户流”。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。 “回复 URL”应显示为 `https://jwt.ms`。
1. 单击“运行用户流”，验证之前创建的帐户的电子邮件地址，然后选择“继续”。
1. 现在可以更改用户的密码。 更改密码，然后选择“继续”。 令牌将返回到 `https://jwt.ms` 并显示出来。

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建注册和登录用户流
> * 创建配置文件编辑用户流
> * 创建密码重置用户流

接下来，了解如何向应用程序添加标识提供程序，让用户能够登录到 Azure AD、Amazon、Facebook、GitHub、LinkedIn、Microsoft 或 Twitter 提供程序。

> [!div class="nextstepaction"]
> [将标识提供程序添加到应用程序 >](tutorial-add-identity-providers.md)
