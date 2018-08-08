---
title: 如何在 Azure Active Directory 中管理应用密码 | Microsoft Docs
description: 本页帮助用户了解什么是应用密码，以及它们在双重验证中起到什么作用。
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: c6340133971a226002ce11ae1521bdc88e3e7975
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343282"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>管理双重验证的应用密码

某些非浏览器应用（例如 Outlook 2010）不支持双重验证。 缺少这种支持意味着，使用双重验证时应用不会正常工作。 若要解决此问题，可以创建一个与普通密码不同的自动生成的密码，并在每个非浏览器应用中使用该密码。

使用应用密码时，请务必记住：

- 应用密码是自动生成的，并且只能为每个应用输入一次。

- 每个用户的密码限制为 40 个。 如果在达到该限制后尝试创建密码，系统会提示删除现有的密码，然后才能创建新密码。

- 对每个设备（而不是每个应用）使用一个应用密码。 例如，为笔记本电脑上的所有应用创建一个密码，然后为台式机上的所有应用创建另一个密码。

    >[!Note]
    >Office 2013 客户端（包括 Outlook）支持新的身份验证协议，并可用于双重验证。 此支持意味着，在启用双重验证后，不再需要对 Office 2013 客户端使用应用密码。 有关详细信息，请参阅[如何对 Office 2013 和 Office 2016 客户端应用使用新式身份验证](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)一文。

## <a name="where-to-create-and-delete-your-app-passwords"></a>在哪里创建和删除应用密码

在最初注册双重验证期间，系统会提供一个应用密码。 如果需要多个密码，可以根据双重验证的使用方式创建更多的密码：

- **对 Microsoft Azure 帐户使用双重验证。** 使用 [Azure 门户](https://portal.azure.com)创建和删除应用密码。 有关详细信息，请参阅[应用密码和双重验证](https://support.microsoft.com/en-us/help/12409/microsoft-account-app-passwords-two-step-verification)一文。

- **对个人 Microsoft 帐户使用双重验证。** 使用 Microsoft 帐户的[安全基本信息](https://account.microsoft.com/account/)页创建和删除应用密码。 有关详细信息，请参阅[应用密码和双重验证](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification)一文。

- **对工作或学校帐户以及 Office 365 应用使用双重验证。** 遵照本文的[使用 Office 365 门户创建和删除应用密码](#create-and-delete-app-passwords-using-the-office-365-portal)部分中的说明创建和删除应用密码。

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>使用 Office 365 门户创建和删除应用密码

如果对工作或学校帐户以及 Office 365 应用使用双重验证，可以使用 Office 365 门户创建和删除应用密码。 每次最多可以创建 40 个应用密码。 如果达到该限制后需要创建另一个应用密码，必须删除一个现有的应用密码。

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>使用 Office 365 门户创建应用密码

1. 登录到工作或学校帐户。

2. 转到 https://portal.office.com，在“Office 365 门户”页的右上角选择“设置”图标，然后展开“其他安全性验证”。

    ![Office 门户，其中显示了展开的“其他安全性验证”区域](media/security-info/security-info-o365password.png)

3. 选择带有“创建和管理应用程序密码”字样的文本，打开“应用密码”页。

4. 选择“创建”，键入需要应用密码的应用的友好名称，然后选择“下一步”。

5. 依次选择“将密码复制到剪贴板”、“关闭”。

6. 使用复制的应用密码登录到非浏览器应用。 只需输入此密码一次，系统会记住它。

### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>使用 Office 365 门户删除应用密码

1. 登录到工作或学校帐户。

2. 转到 https://portal.office.com，在“Office 365 门户”页的右上角选择“设置”图标，然后选择“其他安全性验证”。

3. 选择带有“创建和管理应用程序密码”字样的文本，打开“应用密码”页。

4. 选择要删除的应用密码旁边的“删除”，在确认框中选择“是”，然后选择“关闭”。

    应用密码现已成功删除。

5. 遵循创建应用密码的步骤来创建新的应用密码。

## <a name="manage-app-passwords-in-the-azure-portal"></a>在 Azure 门户中管理应用密码

如果在 Azure 中使用双重验证，则需要通过 Azure 门户创建应用密码。

## <a name="manage-app-passwords-with-the-myapps-portal"></a>使用“我的应用”门户管理应用密码

也可以通过“我的应用”门户创建和删除应用密码。

### <a name="to-create-an-app-password-using-the-my-apps-portal"></a>使用“我的应用”门户创建应用密码

1. 登录到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。

2. 选择右上角的姓名，然后选择“个人资料”。

3. 选择“其他安全性验证”。

   ![选择“其他安全性验证”- 屏幕截图](./media/multi-factor-authentication-end-user-app-passwords/myapps1.png)

4. 选择“应用密码”。

   ![选择“应用密码”- 屏幕截图](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. 单击“创建”。

6. 键入应用密码的名称，然后选择“下一步”。

7. 将应用密码复制到剪贴板，并将它粘贴到你的应用。
   
    ![创建应用密码](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-my-apps-portal"></a>使用“我的应用”门户删除应用密码

1. 转到个人资料，并选择“其他安全性验证”。

2. 选择“应用密码”，然后选择要删除的应用密码旁边的“删除”。

   ![删除应用密码](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

3. 选择“是”确认要删除该密码，然后选择“关闭”。

## <a name="if-your-app-passwords-arent-working-properly"></a>如果应用密码不正常工作

确保正确键入密码。 如果你确认已正确输入了密码，可以尝试重新登录，并创建新的应用密码。 如果这些做法都不能解决问题，请联系公司支持人员，请求他们删除现有的应用密码，并让你创建新的密码。 

## <a name="next-steps"></a>后续步骤

- [管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)

- 试用 [Microsoft 验证器应用](microsoft-authenticator-app-how-to.md)以验证是否可以使用应用通知（而不是通过接收短信或电话）登录。
