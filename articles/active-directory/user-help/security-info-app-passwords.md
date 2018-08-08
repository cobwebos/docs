---
title: 使用安全信息设置应用密码 - Azure Active Directory | Microsoft Docs
description: 使用安全信息设置要在每个非浏览器应用中使用的、与普通密码不同的自动生成的密码（应用密码）。
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 56d207dc47b9e055782568f1c4a4cedc4d19a288
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39347677"
---
# <a name="manage-app-passwords-using-security-info-preview"></a>使用安全信息管理应用密码（预览版）

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

某些非浏览器应用（例如 Outlook 2010）不支持双重验证。 缺少这种支持意味着，使用双重验证时应用不会正常工作。 若要解决此问题，可以创建一个与普通密码不同的自动生成的密码，并在每个非浏览器应用中使用该密码。

使用应用密码时，请务必记住：

- 应用密码是自动生成的，并且只能为每个应用输入一次。

- 每个用户的密码限制为 40 个。 如果在达到该限制后尝试创建密码，系统会提示删除现有的密码，然后才能创建新密码。

- 对每个设备（而不是每个应用）使用一个应用密码。 例如，为笔记本电脑上的所有应用创建一个密码，然后为台式机上的所有应用创建另一个密码。

    >[!Note]
    >Office 2013 客户端（包括 Outlook）支持新的身份验证协议，并可用于双重验证。 此支持意味着，在启用双重验证后，不再需要对 Office 2013 客户端使用应用密码。 有关详细信息，请参阅[如何对 Office 2013 和 Office 2016 客户端应用使用新式身份验证](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)一文。

## <a name="create-and-delete-app-passwords-using-security-info"></a>使用安全信息创建和删除应用密码

如果对工作或学校帐户使用双重验证，并且管理员已启用安全信息体验，则你可以使用“我的应用”门户创建和删除应用密码。

如果管理员尚未启用安全信息体验，则必须根据[管理双重验证的应用密码](multi-factor-authentication-end-user-app-passwords.md)部分中的说明和信息操作。

### <a name="to-create-app-passwords-using-the-my-apps-portal"></a>使用“我的应用”门户创建应用密码

1. 登录到工作或学校帐户。

2. 转到 myapps.microsoft.com，在页面右上角选择自己的姓名，然后选择“个人资料”。

3. 在“管理帐户”区域，选择“编辑安全信息”。

    ![“个人资料”屏幕，其中已突出显示“编辑安全性信息”链接](media/security-info/security-info-profile.png)

4. 在“保护帐户安全”屏幕中，选择“添加安全信息”。

    ![包含现有可编辑信息的安全信息屏幕](media/security-info/security-info-edit-add-info.png)

5. 在“添加安全信息”屏幕中，选择“应用密码”。

6. 在“创建应用密码”屏幕中，键入应用密码的名称，然后选择“下一步”。

    ![为应用密码命名的屏幕](media/security-info/security-info-name-app-password.png)

7. 复制“复制”以将密码复制到剪贴板，然后选择“下一步”。

    ![复制应用密码的屏幕](media/security-info/security-info-create-app-password.png)
    
8. 确保应用密码显示在“保护帐户安全”屏幕上。

    ![显示应用密码的“保护帐户安全”屏幕](media/security-info/security-info-keep-secure-app-password.png)

### <a name="to-delete-app-passwords-using-the-my-apps-portal"></a>使用“我的应用”门户删除应用密码

1. 在“保护帐户安全”屏幕中，选择要删除的应用密码旁边的“X”。

    ![“保护帐户安全”屏幕，删除应用密码](media/security-info/security-info-keep-secure-delete-app-password.png)

2. 在“删除应用程序密码”屏幕中，选择“删除”。

    ![“删除应用密码”屏幕](media/security-info/security-info-keep-secure-delete-app-password2.png)

## <a name="next-steps"></a>后续步骤

- 如需更新安全信息，请遵照[管理安全信息](security-info-manage-settings.md)一文中的说明。

- 有关安全信息和可执行的操作的更多一般信息，请参阅[安全信息概述](user-help-security-info-overview.md) 