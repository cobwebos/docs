---
title: 如何管理应用密码 - Azure 活动目录 |微软文档
description: 了解应用密码及其用于两步验证的功能。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253216"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>管理双重验证的应用密码

>[!Important]
>管理员可能不允许你使用应用密码。 如果你没有看到“应用密码”**** 作为选项列出，则它们在你的组织中不可用。

使用应用密码时，请务必记住：

- 应用密码是自动生成的，应创建并输入每个应用一次。

- 每个用户的密码限制为 40 个。 如果在达到该限制后尝试创建密码，系统会提示删除现有的密码，然后才能创建新密码。

    >[!Note]
    >Office 2013 客户端（包括 Outlook）支持新的身份验证协议，并可用于双重验证。 此支持意味着，在启用双重验证后，不再需要对 Office 2013 客户端使用应用密码。 有关详细信息，请参阅[如何对 Office 2013 和 Office 2016 客户端应用使用新式身份验证](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)一文。

## <a name="create-new-app-passwords"></a>创建新的应用密码

在最初的双因素验证注册过程中，您会获得单个应用密码。 如果您需要多个，您必须自己创建它们。 您可以根据组织中如何设置双重验证，从多个区域创建应用密码。 有关注册以对工作或学校帐户使用双因素验证的详细信息，请参阅[概述，了解双因素验证以及您的工作或学校帐户](multi-factor-authentication-end-user-first-time.md)及其相关文章。

### <a name="where-to-create-and-delete-your-app-passwords"></a>在哪里创建和删除应用密码

您可以根据使用双重验证的方式创建和删除应用密码：

- **您的组织使用双重验证和"附加安全验证"页。** 如果您在组织中使用工作或学校帐户（如），alain@contoso.com具有双重验证功能，则可以从["其他安全验证"页](https://account.activedirectory.windowsazure.com/Proofup.aspx)管理应用密码。 有关详细说明，请参阅使用本文中的["其他安全验证"页创建和删除应用密码](#create-and-delete-app-passwords-from-the-additional-security-verification-page)。

- **您的组织使用双重验证和 Office 365 门户。** 如果您正在组织中使用工作或学校帐户（如alain@contoso.com、双重验证和 Office 365 应用），则可以从 Office [365 门户页](https://www.office.com)管理应用密码。 有关详细说明，请参阅使用本文[中的 Office 365 门户创建和删除应用密码](#create-and-delete-app-passwords-using-the-office-365-portal)。

- **您使用的是个人 Microsoft 帐户的双因素验证。** 如果您使用具有双重验证的个人 Microsoft 帐户（例如alain@outlook.com），则可以从["安全基础知识"页](https://account.microsoft.com/security/)管理应用密码。 有关详细说明，请参阅[将应用密码与不支持两步验证的应用一起使用](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification)。

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>从"其他安全验证"页创建和删除应用密码

您可以在工作或学校帐户的 **"其他安全验证**"页中创建和删除应用密码。

1. 登录到["其他安全验证"页](https://account.activedirectory.windowsazure.com/Proofup.aspx)，然后选择**应用密码**。

    ![应用密码页面，突出显示应用密码选项卡](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. 选择 **"创建**"键入需要应用密码的应用的名称，然后选择 **"下一步**"。

    ![创建应用密码页面，包含需要密码的应用名称](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. 从**应用密码**页复制密码，然后选择 **"关闭**"。

    ![带有指定应用密码的应用密码页](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. 在 **"应用密码**"页中，确保列出你的应用。

     ![应用密码页面，列表中显示新应用](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. 打开您为其创建应用密码的应用（例如，Outlook 2010），然后在要求应用密码时粘贴应用密码。 每个应用只需执行此操作一次。

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>使用应用密码页面删除应用密码

1. 在 **"应用密码**"页中，选择要删除的应用密码旁边的 **"删除**"。

   ![删除应用密码](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. 选择“是”确认要删除该密码，然后选择“关闭”。********

    应用密码现已成功删除。

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>使用 Office 365 门户创建和删除应用密码

如果对工作或学校帐户以及 Office 365 应用使用双重验证，可以使用 Office 365 门户创建和删除应用密码。

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>使用 Office 365 门户创建应用密码

1. 登录到 Office 365，然后转到["我的帐户"页面](https://portal.office.com)，选择**安全&隐私**，然后展开**其他安全验证**。

    ![Office 门户，其中显示了展开的“其他安全性验证”区域](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. 选择显示"**创建和管理应用密码**"以打开**应用密码**页的文本。

    ![应用密码页面，突出显示应用密码选项卡](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. 选择 **"创建**"键入需要应用密码的应用的名称，然后选择 **"下一步**"。

    ![创建应用密码页面，包含需要密码的应用名称](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. 从**应用密码**页复制密码，然后选择 **"关闭**"。

    ![带有指定应用密码的应用密码页](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. 在 **"应用密码**"页中，确保列出你的应用。

     ![应用密码页面，列表中显示新应用](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. 打开您为其创建应用密码的应用（例如，Outlook 2010），然后在要求应用密码时粘贴应用密码。 每个应用只需执行此操作一次。

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>使用应用密码页面删除应用密码

1. 在 **"应用密码**"页中，选择要删除的应用密码旁边的 **"删除**"。

   ![删除应用密码](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. 在确认框中选择 **"是**"，然后选择 **"关闭**"。

    应用密码现已成功删除。

## <a name="if-your-app-passwords-arent-working-properly"></a>如果应用密码不正常工作

确保正确键入密码。 如果你确认已正确输入了密码，可以尝试重新登录，并创建新的应用密码。 如果这两个选项都不能解决问题，请与组织的帮助台联系，以便他们可以删除现有应用密码，从而允许您创建全新的应用密码。

## <a name="next-steps"></a>后续步骤

- [管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)

- 试用 [Microsoft 验证器应用](user-help-auth-app-download-install.md)以验证是否可以使用应用通知（而不是通过接收短信或电话）登录。
