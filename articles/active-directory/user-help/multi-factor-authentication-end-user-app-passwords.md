---
title: 如何管理应用密码-Azure Active Directory |Microsoft Docs
description: 了解应用密码，以及它们在双重验证方面的用途。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 5f81181ac3107307a352cdbcd0b5cc4a555deacb
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062619"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>管理双重验证的应用密码

>[!Important]
>管理员可能不允许你使用应用密码。 如果你没有看到“应用密码”作为选项列出，则它们在你的组织中不可用。

使用应用密码时，请务必记住：

- 应用密码是自动生成的，应为每个应用创建和输入一次。

- 每个用户的密码限制为 40 个。 如果在达到该限制后尝试创建密码，系统会提示删除现有的密码，然后才能创建新密码。

    >[!Note]
    >Office 2013 客户端（包括 Outlook）支持新的身份验证协议，并可用于双重验证。 此支持意味着，在启用双重验证后，不再需要对 Office 2013 客户端使用应用密码。 有关详细信息，请参阅[如何对 Office 2013 和 Office 2016 客户端应用使用新式身份验证](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)一文。

## <a name="create-new-app-passwords"></a>创建新的应用密码

初始双重验证注册过程中，系统会提供一个应用密码。 如果需要多个，则必须自行创建。 你可以根据组织中设置双因素验证的方式，从多个区域创建应用密码。 若要详细了解如何注册以对工作或学校帐户使用双重验证，请参阅[双重验证和工作或学校帐户](multi-factor-authentication-end-user-first-time.md)及其相关文章的概述。

### <a name="where-to-create-and-delete-your-app-passwords"></a>在哪里创建和删除应用密码

你可以根据使用双重验证的方式创建和删除应用密码：

- **你的组织使用双重验证和 "其他安全验证" 页。** 如果你在组织中使用工作或学校帐户（例如 alain@contoso.com）进行双因素验证，则可以从 "[其他安全验证" 页](https://account.activedirectory.windowsazure.com/Proofup.aspx)管理应用密码。 有关详细说明，请参阅本文中[的使用其他安全验证创建和删除应用密码页](#create-and-delete-app-passwords-from-the-additional-security-verification-page)。

- **你的组织使用双因素验证和 Office 365 门户。** 如果你在组织中使用工作或学校帐户（例如 alain@contoso.com）、双因素验证和 Office 365 应用，则可以从[Office 365 门户页面](https://www.office.com)管理应用密码。 有关详细说明，请参阅本文中[的使用 Office 365 门户创建和删除应用密码](#create-and-delete-app-passwords-using-the-office-365-portal)。

- **你使用的是包含个人 Microsoft 帐户的双重验证。** 如果使用的是个人 Microsoft 帐户（例如，alain@outlook.com）进行双因素验证，则可以从 "[安全基本](https://account.microsoft.com/security/)信息" 页中管理应用密码。 有关详细说明，请参阅将[应用密码用于不支持双重验证的应用](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification)。

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>在 "其他安全性验证" 页中创建和删除应用密码

你可以从你的工作或学校帐户的 "**其他安全性验证**" 页中创建和删除应用密码。

1. 登录到 "[其他安全验证" 页](https://account.activedirectory.windowsazure.com/Proofup.aspx)，然后选择 "**应用密码**"。

    !["应用密码" 页，其中突出显示了 "应用密码" 选项卡](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. 选择 "**创建**"，键入需要应用密码的应用的名称，然后选择 "**下一步**"。

    !["创建应用密码" 页，其中包含需要密码的应用的名称](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. 从 "**应用密码**" 页中复制密码，然后选择 "**关闭**"。

    ![包含指定应用的密码的应用密码页](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. 在 "**应用密码**" 页中，确保已列出你的应用。

     !["应用密码" 页，其中显示了新应用的列表](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. 打开为其创建了应用程序密码的应用程序（例如，Outlook 2010），然后在要求时粘贴应用密码。 每个应用程序只需执行此操作一次。

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>使用 "应用密码" 页删除应用密码

1. 从 "**应用密码**" 页上，选择要删除的应用密码旁边的 "**删除**"。

   ![删除应用密码](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. 选择“是”确认要删除该密码，然后选择“关闭”。

    应用密码现已成功删除。

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>使用 Office 365 门户创建和删除应用密码

如果对工作或学校帐户以及 Office 365 应用使用双重验证，可以使用 Office 365 门户创建和删除应用密码。

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>使用 Office 365 门户创建应用密码

1. 登录到 Office 365，然后前往 "我的帐户"[页](https://portal.office.com)，选择 "**安全 & 隐私**"，然后展开 "**其他安全验证**"。

    ![Office 门户，其中显示了展开的“其他安全性验证”区域](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. 选择显示的文本 "**创建和管理应用密码**" 以打开 "**应用密码**" 页。

    !["应用密码" 页，其中突出显示了 "应用密码" 选项卡](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. 选择 "**创建**"，键入需要应用密码的应用的名称，然后选择 "**下一步**"。

    !["创建应用密码" 页，其中包含需要密码的应用的名称](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. 从 "**应用密码**" 页中复制密码，然后选择 "**关闭**"。

    ![包含指定应用的密码的应用密码页](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. 在 "**应用密码**" 页中，确保已列出你的应用。

     !["应用密码" 页，其中显示了新应用的列表](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. 打开为其创建了应用程序密码的应用程序（例如，Outlook 2010），然后在要求时粘贴应用密码。 每个应用程序只需执行此操作一次。

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>使用 "应用密码" 页删除应用密码

1. 从 "**应用密码**" 页上，选择要删除的应用密码旁边的 "**删除**"。

   ![删除应用密码](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. 在确认框中选择 **"是"** ，然后选择 "**关闭**"。

    应用密码现已成功删除。

## <a name="if-your-app-passwords-arent-working-properly"></a>如果应用密码不正常工作

确保正确键入密码。 如果你确认已正确输入了密码，可以尝试重新登录，并创建新的应用密码。 如果这些选项都不能解决你的问题，请与你组织的技术支持人员联系，以便他们可以删除现有的应用密码，从而创建全新的应用密码。

## <a name="next-steps"></a>后续步骤

- [管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)

- 试用 [Microsoft 验证器应用](user-help-auth-app-download-install.md)以验证是否可以使用应用通知（而不是通过接收短信或电话）登录。
