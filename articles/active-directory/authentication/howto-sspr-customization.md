---
title: 自定义自助式密码重置 - Azure Active Directory
description: 了解如何自定义 Azure AD 自助服务密码重置的用户显示和体验选项
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a45602b80db965e3cc79d188dd40034a320b79
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81394249"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>自定义 Azure Active Directory 自助服务密码重置的用户体验

自助服务密码重置（SSPR）允许 Azure Active Directory （Azure AD）的用户更改或重置其密码，而不需要管理员或支持人员。 如果用户的帐户被锁定或用户忘记了自己的密码，他们可以按照提示取消对自己的阻止，回到工作状态。 当用户无法登录到其设备或应用程序时，此功能可减少呼叫支持人员的次数，降低生产力损失。

若要改善用户的 SSPR 体验，你可以自定义密码重置页面、电子邮件通知或登录页面的外观。 利用这些自定义选项，您可以清楚地将其放在正确的位置，并使他们确信他们访问公司资源。
    
本文介绍如何自定义用户、公司品牌和 AD FS 登录页面链接的 SSPR 电子邮件链接。

## <a name="customize-the-contact-your-administrator-link"></a>自定义“联系管理员”链接

为了帮助用户通过自助密码重置获得帮助，在密码重置门户中显示 "联系你的管理员" 链接。 如果用户选择此链接，它将执行以下两项操作之一：

* 如果此联系人链接处于默认状态，则会向管理员发送一封电子邮件，要求他们在更改用户密码时提供帮助。 下面的示例电子邮件显示此默认电子邮件：

    ![用于重置发送到管理员的电子邮件的示例请求](./media/howto-sspr-customization/sspr-contact-admin.png)

* 如果已自定义，则会将用户发送到管理员指定的网页或电子邮件地址以获得帮助。
    * 如果你自定义此功能，我们建议将其设置为用户已熟悉支持的内容。

    > [!WARNING]
    > 如果你使用需要密码重置的电子邮件地址和帐户自定义此设置，则用户可能无法请求协助。

### <a name="default-email-behavior"></a>默认电子邮件行为

默认的联系人电子邮件按以下顺序发送到收件人：

1. 如果已分配“支持管理员”** 角色或“密码管理员”** 角色，则会通知具有这些角色的管理员。
1. 如果未分配任何支持人员管理员或密码管理员，则会通知具有 "*用户管理员*" 角色的管理员。
1. 如果上述角色均未分配，则会通知“全局管理员”**。

在所有情况下，最多会向 100 个收件人发送通知。

若要了解有关不同管理员角色以及如何分配它们的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](../users-groups-roles/directory-assign-admin-roles.md)。

### <a name="disable-contact-your-administrator-emails"></a>禁用“联系管理员”电子邮件

如果你的组织不希望通知管理员有关密码重置的请求，则可以使用以下配置选项：

* 自定义帮助台链接，以提供可让用户获得帮助的 Web URL 或 mailto: 地址。 此选项位于**密码重置** > **Customization** > 自**定义自定义支持人员电子邮件或 URL**下。
* 为所有用户启用自助密码重置。 此选项位于**密码重置** > **属性**下。 如果不希望用户重置其自己的密码，可以将访问权限限制为某个空组。 *不建议采用此选项。*

## <a name="customize-the-sign-in-page-and-access-panel"></a>自定义登录页和访问面板

你可以自定义登录页，例如添加一个徽标，该徽标连同符合公司品牌的图像一起显示。 有关如何配置公司品牌的详细信息，请参阅[Azure AD 中的将公司品牌添加到登录页](../fundamentals/customize-branding.md)。

在以下情况下会显示所选图形：

* 用户输入其用户名后
* 如果用户通过以下方式访问自定义的 URL：
   * 通过将 `whr` 参数传递到密码重置页，如 `https://login.microsoftonline.com/?whr=contoso.com`
   * 通过将 `username` 参数传递到密码重置页，如 `https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>目录名称

为了使某些功能更易于用户了解，你可以在门户中和自动化通信中更改组织名称。 若要在 Azure 门户中更改目录名称属性，请浏览到**Azure Active Directory** > **属性**"。 此友好的组织名称选项在自动电子邮件中最明显，如以下示例中所示：

* 电子邮件中的友好名称，例如 "*Microsoft 代表 CONTOSO 演示*"
* 电子邮件中的 "主题" 行，例如 "*CONTOSO demo 帐户电子邮件验证码*"

## <a name="customize-the-ad-fs-sign-in-page"></a>自定义 AD FS 登录页

如果对用户登录事件使用 Active Directory 联合身份验证服务（AD FS），则可以使用[添加登录页说明](/windows-server/identity/ad-fs/operations/add-sign-in-page-description)一文中的指导来添加指向登录页的链接。

向用户提供页面的链接，以供用户输入 SSPR 工作流，例如*https://passwordreset.microsoftonline.com*。 若要将链接添加到 AD FS 登录页，请在 AD FS 服务器上使用以下命令：

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>后续步骤

若要了解在你的环境中使用 SSPR，请参阅[Azure AD 密码管理的报告选项](howto-sspr-reporting.md)。

如果你或用户遇到 SSPR 问题，请参阅[排查自助密码重置](active-directory-passwords-troubleshoot.md)问题
