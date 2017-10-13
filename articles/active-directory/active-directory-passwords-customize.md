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
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 1b6e368df4914e58eb3f8d6481132f25d27312b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="customize-azure-ad-functionality-for-self-service-password-reset"></a>为自助密码重置自定义 Azure AD 功能

想要部署自助服务密码重置的 IT 专业人员可以自定义与其用户相符的体验。

## <a name="customize-the-contact-your-administrator-link"></a>自定义“联系管理员”链接

即使未启用 SSPR，用户也仍会在密码重置门户中看到“联系管理员”链接。  单击此链接会向管理员发送一封电子邮件，请求他们帮忙更改用户的密码。 此电子邮件按以下顺序发送到以下收件人：

1. 如果已分配**密码管理员**角色，则会通知充当此角色的管理员
2. 如果未分配密码管理员，则会通知充当**用户管理员**角色的管理员
3. 如果上述两个角色都未分配，则会通知“全局管理员”

在所有情况下，最多会向 100 个收件人发送通知。

若要了解有关不同管理员角色以及如何分配它们的详细信息，请参阅文档[在 Azure Active Directory 中分配管理员角色](active-directory-assign-admin-roles.md)

### <a name="disable-contact-your-administrator-emails"></a>禁用“联系管理员”电子邮件

如果组织不希望在发生密码重置请求时通知管理员，可启用以下配置

* 为所有最终用户启用自助密码重置。 可在“密码重置”>“属性”下面找到此选项。
    * 如果不希望用户重置其自己的密码，可以将访问权限限制为某个空组，但**我们不建议使用此选项**。
* 自定义帮助台链接，以提供可让用户获得帮助的 Web URL 或 mailto: 地址。 可在“密码重置”>“自定义”>“自定义帮助台电子邮件或 URL”下面找到此选项。

## <a name="customize-adfs-sign-in-page-for-sspr"></a>为 SSPR 自定义 ADFS 登录页

ADFS 管理员可以使用[添加登录页说明](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description)一文中的指导添加登录页链接。

在 ADFS 服务器上使用随后的命令可添加 ADFS 登录页链接，从而使用户可以直接进入自助服务密码重置工作流。

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-and-access-panel-look-and-feel"></a>自定义登录页和访问面板的外观

可以自定义用户访问登录页时显示的徽标以及登录页的图像，使之符合公司品牌形象。

在以下情况下，会显示这些图形：

* 用户键入其用户名后
* 用户访问自定义的 URL
    * 通过将“whr”传递到密码重置页，例如“https://login.microsoftonline.com/?whr=contoso.com”
    * 通过将“username”传递到密码重置页，例如“https://login.microsoftonline.com/?username=admin@contoso.com”

### <a name="graphics-details"></a>图形详细信息

使用以下设置可以更改登录页的视觉特征。可在“Azure Active Directory”>“公司品牌”>“编辑公司品牌”下面找到这些设置

* 登录页图像应是 1420x1200 像素的 PNG 或 JPG 文件，不能大于 500KB。 为获得最佳效果，我们建议使用大约 200 KB 的图像大小。
* 登录页背景色在建立高延迟连接时使用，必须采用 RGB 十六进制格式。
* 横幅图像应是 60x280 像素的 PNG 或 JPG 文件，不能大于 10 KB。
* 方形徽标（正常和深色主题）应是 240x240 像素（大小可调）的 PNG 或 JPG 文件，不能大于 10 KB。

### <a name="sign-in-text-options"></a>登录文本选项

使用以下设置可在组织相关的登录页中添加文本。 可在“Azure Active Directory”>“公司品牌”>“编辑公司品牌”下面找到这些设置

* 在“用户名提示”中，可将示例文本 someone@example.com 替换为更适合用户的某些内容。支持内部和外部用户时，我们建议保留默认值
* “登录页文本”的长度最大为 256 个字符。 此文本会显示在用户在线登录到的任何位置，以及 Windows 10 上的 Azure AD Join 体验中。 通过此文本向用户显示使用条款、说明和提示。 **任何人都可以看到登录页，因此，请不要在此处提供任何敏感信息。**

### <a name="keep-me-signed-in-disabled"></a>已禁用“使我保持登录状态”

选项“已禁用‘使我保持登录状态’”可让用户在关闭再重新打开其浏览器窗口时保持登录状态。 此选项不会影响会话生存期。 可在“Azure Active Directory”>“公司品牌”>“编辑公司品牌”下面找到此设置。

SharePoint Online 和 Office 2010 的某些功能取决于用户能否勾选此框。 如果隐藏此选项，用户可能会看到多余且意外的登录提示。

### <a name="directory-name"></a>目录名称

可以在“Azure Active Directory”>“属性”下面更改名称属性，以便在门户中和自动化通信中显示友好的组织名称。 在采用以下形式的自动化电子邮件表单中，此选项最显眼

* “Microsoft 代表 CONTOSO 演示”电子邮件中的友好名称
* “CONTOSO 演示帐户电子邮件验证码”电子邮件中的主题行

## <a name="next-steps"></a>后续步骤

以下链接提供了有关使用 Azure AD 进行密码重置的其他信息

* [**快速入门**](active-directory-passwords-getting-started.md) - 启动并运行 Azure AD 自助服务密码管理 
* [**授权**](active-directory-passwords-licensing.md) - 配置 Azure AD 授权
* [**数据**](active-directory-passwords-data.md) - 了解所需的数据以及如何使用它进行密码管理
* [**推出**](active-directory-passwords-best-practices.md) - 使用此处提供的指南计划 SSPR 并将其部署到用户
* [**策略**](active-directory-passwords-policy.md) - 了解并设置 Azure AD 密码策略
* [**密码写回**](active-directory-passwords-writeback.md) - 如何对本地目录使用密码写回
* [**报告**](active-directory-passwords-reporting.md) - 了解用户是否访问 SSPR 功能，以及在何时何处进行访问
* [深入技术探究](active-directory-passwords-how-it-works.md) - 了解幕后的工作原理
* [**常见问题**](active-directory-passwords-faq.md) - 如何？ 为什么？ 什么？ 何处？ 谁？ 何时？ - 常见问题的答案
* [**故障排除**](active-directory-passwords-troubleshoot.md) - 了解如何解决使用 SSPR 时遇到的常见问题

