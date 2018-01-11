---
title: "推出：Azure AD SSPR | Microsoft Docs"
description: "提示：如何成功推出 Azure AD 自助密码重置"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 79089f09342f520f7d43115cc606d794db6c1602
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>如何成功推出自助密码重置

为了确保顺利推出 Azure Active directory (Azure AD) 自助密码重置 (SSPR) 功能，大多数客户会完成以下步骤：

1. [在目录中启用密码重置](active-directory-passwords-getting-started.md)。
2. [配置进行密码写回所需的本地 Active Directory 权限](active-directory-passwords-writeback.md#active-directory-permissions)。
3. [配置密码写回](active-directory-passwords-writeback.md#configure-password-writeback)，将密码从 Azure AD 写回到本地目录。
4. [分配并验证所需许可证](active-directory-passwords-licensing.md)。
5. 确定是否要逐渐推出。 若要逐渐推出 SSPR，可以只允许一组用户进行访问，以便使用特定的组对项目进行试验。 若要推出到特定的组，请将“启用自助密码重置”开关设置为“选定”，然后选择允许其使用密码重置的安全组。 
6. 填充用户注册所需的[身份验证数据](active-directory-passwords-data.md)，例如办公室电话、移动电话以及备用电子邮件地址。
7. [自定义 Azure AD 登录体验，在其中包括公司品牌](active-directory-passwords-customize.md)。
8. 指导用户使用 SSPR。 向用户发送说明，指导其注册并重置密码。
9. 确定何时需强制执行注册。 可以选择随时强制执行注册。 也可以要求用户在特定时间过后重新确认其身份验证信息。
10. 使用报告功能。 一段时间过后，即可使用 [Azure AD 提供的报告功能](active-directory-passwords-reporting.md)查看用户的注册和使用情况。
11. 启用密码重置。 准备就绪后，为所有用户启用密码重置，方法是将“启用自助密码重置”开关设置为“所有”。 

   > [!NOTE]
   > 将此选项从“选定组”更改为“所有人”不会导致现有的身份验证数据失效，这些数据是用户以测试组成员身份注册的。 只要用户经过了配置并注册了有效的身份验证数据，就可以继续使用该用户。

12. [允许 Windows 10 用户在登录屏幕上重置其密码](active-directory-passwords-login.md)。

   > [!IMPORTANT]
   > 请使用用户而不是管理员来测试 SSPR，因为 Microsoft 对 Azure 管理员帐户强制实施强身份验证要求。 有关管理员密码策略的详细信息，请参阅[密码策略](active-directory-passwords-policy.md#administrator-password-policy-differences)文章。

## <a name="email-based-rollout"></a>基于电子邮件的启用

许多客户发现，若要让用户使用 SSPR，最简单的方法是开展电子邮件促销活动，为用户提供易用的说明。 [我们撰写了三封简单的电子邮件，可以在推出此功能时将其用作模板](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)：

* **即将推出**：一种电子邮件模板，在推出之前的数周或数天使用，目的是让用户了解他们需要做哪些准备。
* **现已推出**：一种”电子邮件模板，在项目启动当天使用，目的是促使用户注册并确认其身份验证数据。 如果现在就注册，用户可以在需要 SSPR 时使用它。
* **注册提醒**：一种电子邮件模板，在部署后的数天或数周使用，目的是提醒用户注册并确认其身份验证数据。

![电子邮件][Email]

## <a name="create-your-own-password-portal"></a>创建自己的密码门户

许多客户选择托管网页并创建根 DNS 条目，例如 https://passwords.contoso.com。他们在此页中填充以下信息的链接：

* [Azure AD 密码重置门户 - https://aka.ms/sspr](https://aka.ms/sspr)
* [Azure AD 密码重置注册门户 - http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
* [Azure AD 密码更改门户 - https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* 其他特定于组织的信息

可以在发出的任何电子邮件通信或传单中包括带品牌标记的易记 URL，供用户在需要使用相应服务时访问。 我们为你创建了一个[示例密码重置页](https://github.com/ajamess/password-reset-page)，你可以根据组织需要使用它并进行自定义。

## <a name="use-enforced-registration"></a>使用强制注册

如果想要用户注册密码重置，可要求他们在通过 Azure AD 登录时注册。 可以在“注册”选项卡上启用“要求用户在登录时注册”选项，这样即可从目录的“密码重置”窗格启用此选项。

管理员可以要求用户在特定的一段时间过后重新注册。 他们可以将“用户必须在几天后重新确认其身份验证信息”选项设置为 0-730 天。

启用此选项后，用户在登录时会看到一条消息，指出管理员已要求其验证身份验证信息。

## <a name="populate-authentication-data"></a>填充身份验证数据

应该[为用户填充身份验证数据](active-directory-passwords-data.md)。 这样用户不注册密码重置也可使用 SSPR。 只要用户提供的身份验证数据符合你定义的密码重置策略，他们便能重置其密码。

## <a name="disable-self-service-password-reset"></a>禁用自助密码重置

禁用自助密码重置很容易。 打开 Azure AD 租户，转到“密码重置” > “属性”，然后在“启用自助密码重置”下选择“无”。

## <a name="next-steps"></a>后续步骤

* [重置或更改密码](active-directory-passwords-update-your-own-password.md)
* [注册自助密码重置](active-directory-passwords-reset-register.md)
* [是否有许可问题？](active-directory-passwords-licensing.md)
* [SSPR 使用哪些数据？应为用户填充哪些数据？](active-directory-passwords-data.md)
* [SSPR 有哪些策略选项？](active-directory-passwords-policy.md)
* [什么是密码写回？我为什么关心它？](active-directory-passwords-writeback.md)
* [如何报告 SSPR 中的活动？](active-directory-passwords-reporting.md)
* [SSPR 中的所有选项有哪些？它们有哪些含义？](active-directory-passwords-how-it-works.md)
* [我认为有些功能被破坏。如何对 SSPR 进行故障排除？](active-directory-passwords-troubleshoot.md)
* [我有在别处未涵盖的问题](active-directory-passwords-faq.md)

[Email]: ./media/active-directory-passwords-best-practices/sspr-emailtemplates.png "根据组织需要自定义这些电子邮件模板"
