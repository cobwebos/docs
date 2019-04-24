---
title: 从“安全信息”（预览版）页面设置应用密码 - Azure Active Directory | Microsoft Docs
description: 设置自动生成的密码（应用密码）以用于组织中的任何非浏览器应用或不支持双重验证的任何应用。 此应用密码有别于普通密码，可以从“安全信息”页面进行设置。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55dfab0c60e77b86157a005db34c37917a5e08d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60475046"
---
# <a name="manage-app-passwords-from-your-security-info-preview-page"></a>从“安全信息”（预览版）页面管理应用密码
某些应用（例如 Outlook 2010）不支持双重验证。 缺少这种支持意味着，如果你在组织中使用双重验证，该应用将不会正常工作。 若要解决此问题，可以创建一个与普通密码不同的自动生成的密码，并在每个非浏览器应用中使用该密码。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>管理员可能不允许你使用应用密码。 如果你没有看到“应用密码”作为选项列出，则它们在你的组织中不可用。

使用应用密码时，请务必记住：

- 应用密码是自动生成的，并且只能为每个应用输入一次。

- 每个用户的密码限制为 40 个。 如果在达到该限制后尝试创建密码，系统会提示删除现有的密码，然后才能创建新密码。

- 对每个设备（而不是每个应用）使用一个应用密码。 例如，为笔记本电脑上的所有应用创建一个密码，然后为台式机上的所有应用创建另一个密码。

    >[!Note]
    >Office 2013 客户端（包括 Outlook）支持新的身份验证协议，并可用于双重验证。 此支持意味着，在启用双重验证后，不再需要对 Office 2013 客户端使用应用密码。 有关详细信息，请参阅[如何对 Office 2013 和 Office 2016 客户端应用使用新式身份验证](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)一文。

## <a name="create-new-app-passwords"></a>创建新的应用密码
如果对工作或学校帐户使用双重验证，并且管理员已启用安全信息体验，则你可以使用“安全信息”页面创建和删除应用密码。

>[!Note]
>如果管理员尚未启用安全信息体验，则必须根据[管理双重验证的应用密码](multi-factor-authentication-end-user-app-passwords.md)部分中的说明和信息操作。

### <a name="to-create-a-new-app-password"></a>创建新的应用密码
1. 登录到你的工作或学校帐户，然后转到 https://myprofile.microsoft.com/ 页面。

    ![“我的个人资料”页，其中突出显示了“安全信息”链接](media/security-info/securityinfo-myprofile-page.png)

2. 在左侧导航窗格中或通过“安全信息”块中的链接选择“安全信息”，然后从“安全信息”页中选择“添加方法”。

    ![“安全信息”页面，其中突出显示了“添加方法”选项](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. 在“添加方法”页面上，从下拉列表中选择“应用密码”，然后选择“添加”。

    ![“添加方法”框，其中选定了“应用密码”](media/security-info/securityinfo-myprofile-addpassword.png)

4. 键入要求使用应用密码的应用的名称，然后选择“下一步”。

    ![“应用密码”页面，其中显示了应用名称](media/security-info/securityinfo-myprofile-password-appname.png)

5. 复制“密码”框中的文本，将密码粘贴到应用（在本例中为 Outlook 2010）的密码区域，然后选择“完成”。

    ![“应用密码”页面，其中显示了应用名称](media/security-info/securityinfo-myprofile-password-copytext.png)
    
    密码已添加，你将可以成功登录到应用程序。

## <a name="delete-your-app-passwords"></a>删除应用密码
如果你不再需要使用要求使用应用密码的应用，则可以删除关联的应用密码。 删除应用密码将释放一个可用的应用密码位置点供将来使用。

>[!Important]
>如果错误地删除了应用密码，则无法撤消该操作。 必须按照本文的[创建新的应用密码](#create-new-app-passwords)部分中的步骤创建新的应用密码并将其重新输入到应用中。

### <a name="to-delete-an-app-password"></a>删除应用密码

1. 在“安全信息”页面上，选择特定应用的“应用密码”选项旁边的“删除”链接。

    ![“安全信息”页面中用于删除应用密码方法的链接](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. 从确认框中选择“是”，以删除“应用密码”。 删除应用密码后，它将从你的安全信息中删除，并从“安全信息”页面中消失。

## <a name="for-more-information"></a>更多信息
- 有关“安全信息”页面以及如何设置它的详细信息，请参阅[安全信息概述](user-help-security-info-overview.md)
