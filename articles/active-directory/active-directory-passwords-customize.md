---
title: "自定义：Azure AD SSPR | Microsoft Docs"
description: "用于 Azure AD 自助服务密码重置的自定义选项"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: e8cf0ce8ed154a7e42b885e605dcdf37827a6447
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2017
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>为自助密码重置自定义 Azure AD 功能

想要在 Azure Active directory (Azure AD) 中部署自助服务密码重置 (SSPR) 的 IT 专业人员可以自定义与其用户需求相符的体验。

## <a name="customize-the-contact-your-administrator-link"></a>自定义“联系管理员”链接

即使未启用 SSPR，用户在密码重置门户中也仍可找到“联系管理员”链接。 如果用户选择该链接，它将执行以下任务之一：
   * 向管理员发送一封电子邮件，请求他们帮助更改用户的密码。 
   * 将用户指引到指定的 URL 以获取帮助。 

建议将此联系人设置为用户已用来提问支持问题的电子邮件地址或网站等内容。

![联系人][Contact]

此联系人电子邮件按以下顺序发送到以下收件人：

1. 如果已分配“密码管理员”角色，则会通知充当此角色的管理员。
2. 如果未分配密码管理员，则会通知充当“用户管理员”角色的管理员。
3. 如果上述两个角色都未分配，则会通知“全局管理员”。

在所有情况下，最多会向 100 个收件人发送通知。

若要了解有关不同管理员角色以及如何分配它们的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](active-directory-assign-admin-roles-azure-portal.md)。

### <a name="disable-contact-your-administrator-emails"></a>禁用“联系管理员”电子邮件

如果组织不希望向管理员通知密码重置请求，可启用以下配置：

* 为所有最终用户启用自助密码重置。 可在“密码重置” > “属性”下面找到此选项。
  
  如果不希望用户重置其自己的密码，可以将访问权限限制为某个空组。 我们不建议使用此选项。
* 自定义帮助台链接，以提供可让用户获得帮助的 Web URL 或 mailto: 地址。 可在“密码重置” > “自定义” > “自定义支持人员电子邮件或 URL”下面找到此选项。

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>为 SSPR 自定义 AD FS 登录页

Active Directory 联合身份验证服务 (AD FS) 管理员可以使用[添加登录页说明](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description)一文中的指导将链接添加到登录页。

若要将链接添加到 AD FS 登录页，请在 AD FS 服务器上使用以下命令。 用户可以使用此页输入 SSPR 工作流。

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>自定义登录页和访问面板的外观

可以自定义登录页。 可以添加一个徽标，它将随适合公司品牌的图像一起显示。

在以下情况下会显示所选图形：

* 用户输入其用户名后
* 如果用户通过以下方式访问自定义的 URL：
    * 将“whr”参数传递到密码重置页，例如“https://login.microsoftonline.com/?whr=contoso.com”
    * 将“username”参数传递到密码重置页，例如“https://login.microsoftonline.com/?username=admin@contoso.com”

### <a name="graphics-details"></a>图形详细信息

使用以下设置可更改登录页的视觉特征。 转到“Azure Active Directory” > “公司品牌” > “编辑公司品牌”：

* 登录页图像应是 1420x1200 像素的 .png 或 .jpg 文件，不能大于 500KB。 为获得最佳结果，建议将其保持为大约 200 KB。
* 登录页背景色在高延迟连接上使用，必须采用 RGB 十六进制格式。
* 横幅图像应是 60x280 像素的 .png 或 .jpg 文件，不能大于 10 KB。
* 方形徽标（常规的深色主题）应是 240 x 240（可调整大小）像素的 .png 或 .jpg 文件，不能大于 10 KB。

### <a name="sign-in-text-options"></a>登录文本选项

使用以下设置可将文本添加到与组织相关的登录页中。 转到“Azure Active Directory” > “公司品牌” > “编辑公司品牌”：

* **用户名提示**：将示例文本 *someone@example.com* 替换为更适合于用户的内容。 如果支持内部和外部用户，建议保留默认提示。
* **登录页文本**：长度最大为 256 个字符。 此文本会出现在用户在线登录到的任何位置，以及 Windows 10 上的 Azure AD Workplace Join 体验中。 通过此文本向用户显示使用条款、说明和提示。 

   >[!IMPORTANT]
   >任何人都可以看到登录页，因此，请不要在此处提供任何敏感信息。
   >

### <a name="the-keep-me-signed-in-disabled-setting"></a>“已禁用‘使我保持登录状态’”设置

使用“已禁用‘使我保持登录状态’”选项，用户可以在关闭再重新打开其浏览器窗口时保持登录状态。 此选项不会影响会话生存期。 转到“Azure Active Directory” > “公司品牌” > “编辑公司品牌”。

SharePoint Online 和 Office 2010 的某些功能取决于用户能否选中此复选框。 如果隐藏此选项，用户可能会看到多余且意外的登录提示。

### <a name="directory-name"></a>目录名称

可以在“Azure Active Directory” > “属性”下更改目录名称属性。 可以在门户中以及在自动通信中显示友好的组织名称。 在自动发送的电子邮件中，此选项以下列形式出现时最显眼：

* 电子邮件中的友好名称，例如“Microsoft 代表 CONTOSO 演示”
* 电子邮件中的主题行，例如“CONTOSO 演示帐户电子邮件验证码”

## <a name="next-steps"></a>后续步骤

* [如何成功推出 SSPR？](active-directory-passwords-best-practices.md)
* [重置或更改密码](active-directory-passwords-update-your-own-password.md)
* [注册自助密码重置](active-directory-passwords-reset-register.md)
* [是否有许可问题？](active-directory-passwords-licensing.md)
* [SSPR 使用哪些数据？应为用户填充哪些数据？](active-directory-passwords-data.md)
* [哪些身份验证方法可供用户使用？](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR 有哪些策略选项？](active-directory-passwords-policy.md)
* [什么是密码写回？我为什么关心它？](active-directory-passwords-writeback.md)
* [如何报告 SSPR 中的活动？](active-directory-passwords-reporting.md)
* [SSPR 中的所有选项有哪些？它们有哪些含义？](active-directory-passwords-how-it-works.md)
* [我认为有些功能被破坏。如何对 SSPR 进行故障排除？](active-directory-passwords-troubleshoot.md)
* [我有在别处未涵盖的问题](active-directory-passwords-faq.md)

[Contact]: ./media/active-directory-passwords-customize/sspr-contact-admin.png "联系管理员请求帮忙重置密码的电子邮件示例"
