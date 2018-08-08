---
title: 管理安全信息 - Azure Active Directory | Microsoft Docs
description: 了解如何管理安全信息，包括如何使用双重验证设置。
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: abd2984574f80f03f276861782ff9ee51348d07e
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391371"
---
# <a name="manage-your-security-info-preview"></a>管理安全信息（预览版）

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

可以使用安全信息登录到工作或学校帐户，或重置密码。

登录时，根据具体的组织设置，可能会出现带有“在 X 天内不再询问”字样的复选框。 选中此复选框可以在管理员允许的天数内在设备上保持登录状态，而无需重复验证。

## <a name="change-your-info"></a>更改信息
可以根据管理员和组织允许的设置来更新或添加安全信息，或更改默认方法。

### <a name="to-change-your-info"></a>更改信息

1. 登录到工作或学校帐户。

2. 转到 myapps.microsoft.com，在页面右上角选择自己的姓名，然后选择“个人资料”。

3. 在“管理帐户”区域，选择“编辑安全信息”。

    ![“个人资料”屏幕，其中已突出显示“编辑安全性信息”链接](media/security-info/security-info-profile.png)

4. 使用默认方法批准访问权限并查看当前的安全信息详情（如果管理员已经为组织设置了此体验）。

5. 在“保护帐户安全”页上，可以：

    - 选择“添加安全信息”以添加更多的方法。

    - 选择“更改默认方法”以更改默认方法。

    - 选择现有方法旁边的**铅笔**图标来更新信息。

    ![包含现有可编辑信息的安全信息屏幕](media/security-info/security-info-edit.png)

6. 完成更改后，可以退出此页，系统会保存所做的更改。

如果看不到这些选项或者无法访问 myapps.microsoft.com 页，则可能是组织使用了自定义选项或自定义页。 需要联系管理员来获得更多帮助。

## <a name="manage-your-security-info-for-a-lost-or-potentially-compromised-device"></a>管理遗失或可能泄密的设备的安全信息

如果你遗失了设备，或者设备已泄密，则必须针对以前信任的设备重新执行验证过程。

### <a name="to-manage-your-security-info-for-lost-or-potentially-compromised-devices"></a>管理遗失或可能泄密的设备的安全信息

1. 登录到工作或学校帐户。

2. 转到 myapps.microsoft.com，在页面右上角选择自己的姓名，然后选择“个人资料”。

3. 在“管理帐户”区域，选择“在记住的设备上忽略使用 MFA”。
    
    选择此选项意味着在登录后必须再次完成多重身份验证过程。

    ![“个人资料”屏幕，其中已突出显示“在记住的...”链接](media/security-info/security-info-forget.png)

## <a name="common-problems-and-solutions-with-your-security-info"></a>安全信息相关的常见问题与解决方法

本文将帮助你排查安全信息问题，包括双重验证相关的问题。

|问题|解决方案|
|-------|--------|
|我没有带手机|有可能你的手机不在身边，但仍想登录到工作或学校帐户。 若要解决此问题，可以使用不需要手机的其他身份验证方法登录，例如，使用电子邮件地址或办公电话号码。 若要将其他方法添加到安全信息，可以遵循[更改信息](#change-your-info)部分所述的步骤。|
|我的手机已丢失或被盗|不幸地，你的手机可能会遗失或被盗。 在这种情况下，我们强烈建议你告知组织，以便 IT 工作人员重置你的应用密码，并从受信任设备列表中清除所有已记住的设备。 还可以遵循[管理遗失或可能泄密的设备的安全信息](#manage-your-security-info-for-a-lost-or-potentially-compromised-device)部分所述的步骤，来忽略自己的受信任设备。|
|我买了一个新电话号码|可通过两种方法解决此问题。 可以使用不需要电话号码的备用身份验证方法登录，例如使用电子邮件。如果这种方法不可行，请联系组织的 IT 工作人员，让他们清除设置。 若要将其他方法添加到安全信息，可以遵循[更改信息](#change-your-info)部分所述的步骤。|
|我的默认方法不正确|可以在安全选项中更新默认方法。 有关具体详细信息，请转到[更改信息](#change-your-info)部分。|
|我的移动设备未收到短信或来电|如果以前曾经在该移动设备上成功收到过短信或来电，则此问题很可能与手机提供商有关，而与帐户无关。 请确保手机信号良好，可以接收短信和来电。 可以请求好友给你拨打电话或发送短信，以进行测试。<br><br>如果可以成功接收短信和消息，但仍收不到通知，可以尝试使用另一种方法。 可以遵循[更改信息](#change-your-info)部分所述的步骤，将其他方法添加到安全信息。 如果没有其他可添加的方法，请联系公司支持人员，让他们清除设置，以便下次登录时可以设置方法。<br><br>如果你经常由于手机信号不良导致问题，我们建议在移动设备上使用 Microsoft Authenticator 应用。 应用可以生成用于登录的随机安全代码，这些代码可在没有任何手机信号或 Internet 连接的情况下生成。 有关 Microsoft Authenticator 应用的详细信息，请参阅 [Microsoft Authenticator 应用入门](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to)一文。|
|此表格中的所有做法都不能解决我的问题|如果已尝试上述故障排除步骤但仍遇到问题，请联系公司支持人员，他们应该可以提供帮助。|

## <a name="next-steps"></a>后续步骤

- 在[安全信息（预览版）概述](user-help-security-info-overview.md)中详细了解安全信息。

- 在[双重验证概述](user-help-two-step-verification-overview.md)一文中了解双重验证。 

- 遵循以下操作说明文章之一，了解如何在安全信息区域设置设备：

    - [设置安全信息以使用 Authenticator 应用](security-info-setup-auth-app.md)

    - [设置安全信息以使用电话呼叫](security-info-setup-phone-number.md)

    - [设置安全信息以使用短信](security-info-setup-text-msg.md)

    - [设置安全信息以使用电子邮件](security-info-setup-email.md)

    - [设置安全信息以使用安全提问](security-info-setup-questions.md)

- 如果你丢失或忘记了密码，请通过[密码重置门户](https://passwordreset.microsoftonline.com/)或遵循[重置工作或学校帐户密码](user-help-reset-password.md)一文中的步骤来重置密码。

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。