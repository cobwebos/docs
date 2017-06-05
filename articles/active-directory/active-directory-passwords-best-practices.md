---
title: "推出：Azure AD SSPR | Microsoft Docs"
description: "提示：如何成功推出 Azure AD 自助密码重置"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/25/2017
ms.author: joflore
ms.editor: gahug
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 5bd17fc68447b8387dfaad33fe2d0055596ec086
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017


---
# <a name="roll-out-password-reset-for-users"></a>为用户推出密码重置

大多数客户按以下步骤操作即可确保顺利推出 SSPR 功能。

1. [在目录中启用密码重置](active-directory-passwords-getting-started.md)
2. [配置进行密码写回所需的本地 AD 权限](active-directory-passwords-how-it-works.md#active-directory-permissions)
3. [配置密码写回](active-directory-passwords-writeback.md#configuring-password-writeback)，将密码从 Azure AD 写回到本地目录
4. [分配并验证所需许可证](active-directory-passwords-licensing.md)
5. 若要逐渐推出，可以选择仅限一组用户进行密码重置，以便在一段时间内缓慢推出该功能。 为此，请将“已启用自助密码重置”切换开关从“所有人”设置为“一个组”，然后选择一个允许其进行密码重置的安全组。 必须为该组的所有成员分配许可证，以便[根据组进行授权](active-directory-passwords-licensing.md#enable-group-or-user-based-licensing)。
6. 根据策略填充最小的[身份验证数据](active-directory-passwords-data.md)集。
7. 指导用户使用 SSPR，为其发送注册和重置说明。
    > [!NOTE]
    > 请使用用户而不是管理员来测试 SSPR，因为 Microsoft 对 Azure 管理员类型帐户强制实施强身份验证要求。 有关管理员密码策略的详细信息，请参阅我们的[深入分析文章](active-directory-passwords-how-it-works.md)。

8. 你可以随时选择强制要求用户注册，并要求用户在特定时间过后重新确认其身份验证信息。 如果不要求用户必须注册，则可[在无需最终用户注册的情况下部署密码重置](active-directory-passwords-data.md)。
9. 一段时间过后，可以通过查看 [Azure AD 提供的报告](active-directory-passwords-reporting.md)来了解用户注册和使用此功能的情况。

## <a name="email-based-rollout"></a>基于电子邮件的启用

许多客户发现，若要让用户使用 SSPR，最简单的方法是开展电子邮件促销活动，为用户提供易用的说明。 [我们撰写了三封简单的电子邮件，你可以在推出此功能时将其用作模板。](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* “即将推出”电子邮件模板：在推出之前的数周或数天使用，目的是让用户了解他们需要做哪些准备。
* “现已推出”电子邮件模板：在产品发布会当天使用，目的是促使用户注册并确认其身份验证数据，使之在需要 SSPR 时就能够马上使用它。
* “注册提醒”电子邮件模板：在部署后的数天或数周使用，目的是提醒用户注册并确认其身份验证数据。

## <a name="creating-your-own-password-portal"></a>创建自己的密码门户

较大的客户很多选择托管网页并创建根 DNS 条目，例如 https://passwords.contoso.com。 他们会在该页中填充 Azure AD 密码重置链接、密码重置注册链接、密码更改门户链接，以及其他组织特定信息链接。 然后，你就可以在需要发出的任何电子邮件通信或传单中包括带品牌标记的易记 URL，供用户在需要使用相应服务时访问。

* 密码重置门户 - https://passwordreset.microsoftonline.com/
* 密码重置注册门户 - http://aka.ms/ssprsetup
* 密码更改门户 - https://account.activedirectory.windowsazure.com/ChangePassword.aspx

## <a name="using-enforced-registration"></a>使用强制注册

如果你想要用户注册密码重置，则可在他们使用 Azure AD 登录时强制其注册。 可以在“注册”选项卡上启用“要求用户在登录时注册”选项，这样即可从目录的“密码重置”边栏选项卡启用此选项。

管理员可以要求用户在一段时间过后重新注册，只需将“要求用户在特定天数过后重新确认其身份验证信息”设置为 0-730 天即可。

启用此选项后，系统会向登录的用户显示一条消息，通知他们管理员已要求其验证身份验证信息。

## <a name="populate-authentication-data"></a>填充身份验证数据

如果[为用户填充身份验证数据](active-directory-passwords-data.md)，则用户不需注册密码重置即可使用 SSPR。 只要用户定义的身份验证数据符合你定义的密码重置策略，用户便能重置其密码。

## <a name="disabling-self-service-password-reset"></a>禁用自助密码重置

禁用自助密码重置很简单，只需打开 Azure AD 租户，转到“密码重置”>“属性”，然后在“已启用自助密码重置”下选择“无人”即可。

## <a name="next-steps"></a>后续步骤

以下链接提供了有关使用 Azure AD 进行密码重置的其他信息

* [**快速入门**](active-directory-passwords-getting-started.md) - 启动并运行 Azure AD 自助服务密码管理 
* [**授权**](active-directory-passwords-licensing.md) - 配置 Azure AD 授权
* [**数据**](active-directory-passwords-data.md) - 了解所需的数据以及如何使用它进行密码管理
* [**自定义**](active-directory-passwords-customize.md) - 自定义公司的 SSPR 体验的外观。
* [**策略**](active-directory-passwords-policy.md) - 了解并设置 Azure AD 密码策略
* [**密码写回**](active-directory-passwords-writeback.md) - 如何对本地目录使用密码写回
* [**报告**](active-directory-passwords-reporting.md) - 了解用户是否访问 SSPR 功能，以及在何时何处进行访问
* [**深入技术探究**](active-directory-passwords-how-it-works.md) - 了解幕后的工作原理
* [**常见问题**](active-directory-passwords-faq.md) - 如何？ 为什么？ 什么？ 何处？ 谁？ 何时？ - 常见问题的答案
* [**故障排除**](active-directory-passwords-troubleshoot.md) - 了解如何解决使用 SSPR 时遇到的常见问题
