---
title: 自助密码重置部署指南 - Azure Active Directory
description: 提示：如何成功推出 Azure AD 自助密码重置
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 2371ad00728a47af9e96e8e711aa07cc5170266c
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158856"
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>如何成功推出自助密码重置

为了确保顺利推出 Azure Active directory (Azure AD) 自助密码重置 (SSPR) 功能，大多数客户会完成以下步骤：

> [!VIDEO https://www.youtube.com/embed/OZn5btP6ZXw]

1. 使用组织的一小部分完成试点推广。
   * 有关如何试点的信息，请参阅[教程：完成 Azure AD 自助密码重置试点推广](tutorial-sspr-pilot.md)。
1. 培训支持人员。
   * 他们将如何帮助你的用户？
   * 你是否会强制用户使用 SSPR 而不允许支持人员协助用户？
   * 你是否向他们提供了用于注册和重置的 URL？
      * 注册：https://aka.ms/ssprsetup
      * 重置：https://aka.ms/sspr
1. 培训用户。
   * 本文档的以下部分将介绍示例通信、密码门户、如何强制注册和填充身份验证数据。
   * Azure Active Directory 产品组创建了[分步部署计划](https://aka.ms/SSPRDeploymentPlan)，组织可以将该计划与在此站点上找到的文档一起使用，针对自助服务密码重置部署生成业务案例和计划。
1. 为整个组织启用自助密码重置。
   * 准备就绪后，为所有用户启用密码重置，方法是将“启用自助密码重置”开关设置为“所有”。

## <a name="sample-communication"></a>示例通信

许多客户发现，若要让用户使用 SSPR，最简单的方法是开展电子邮件促销活动，为用户提供易用的说明。 [我们创建了简单的电子邮件和其他宣传材料，你可以将其用作模板以帮助你进行推广](https://www.microsoft.com/download/details.aspx?id=56768)：

* **即将推出**：一种电子邮件模板，在推出之前的数周或数天使用，目的是让用户了解他们需要做哪些准备。
* **现已推出**：一种”电子邮件模板，在项目启动当天使用，目的是促使用户注册并确认其身份验证数据。 如果现在就注册，用户可以在需要 SSPR 时使用它。
* **注册提醒**：一种电子邮件模板，在部署后的数天或数周使用，目的是提醒用户注册并确认其身份验证数据。
* **SSPR 海报**：你可以自定义的海报并在推出之前和之后的几天和几周内将其显示在你组织周围。
* **SSPR 桌面帐篷**：可以放在午餐室、会议室或办公桌上的桌卡，以鼓励用户完成注册。
* **SSPR 贴纸**：可以自定义并打印的贴纸模板，用于放置笔记本电脑、显示器、键盘或手机，以记住如何访问 SSPR。

![SSPR 电子邮件示例][Email]

## <a name="create-your-own-password-portal"></a>创建自己的密码门户

许多客户选择托管网页并创建根 DNS 条目，例如 https://passwords.contoso.com。 他们在此页中填充以下信息的链接：

* [Azure AD 密码重置门户 - https://aka.ms/sspr](https://aka.ms/sspr)
* [Azure AD 密码重置注册门户 - https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
* [Azure AD 密码更改门户 - https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* 其他特定于组织的信息

可以在发出的任何电子邮件通信或传单中包括带品牌标记的易记 URL，供用户在需要使用相应服务时访问。 我们为你创建了一个[示例密码重置页](https://github.com/ajamess/password-reset-page)，你可以根据组织需要使用它并进行自定义。

## <a name="use-enforced-registration"></a>使用强制注册

如果想要用户注册密码重置，可要求他们在通过 Azure AD 登录时注册。 可以在“注册”选项卡上启用“要求用户在登录时注册”选项，这样即可从目录的“密码重置”窗格启用此选项。

管理员可以要求用户在特定的一段时间过后重新注册。 他们可以将“用户必须在几天后重新确认其身份验证信息”选项设置为 0-730 天。

启用此选项后，用户在登录时会看到一条消息，指出管理员已要求其验证身份验证信息。

## <a name="populate-authentication-data"></a>填充身份验证数据

你应该考虑[为用户预填充一些身份验证数据](howto-sspr-authenticationdata.md)。 这样用户不注册密码重置也可使用 SSPR。 只要用户提供的身份验证数据符合你定义的密码重置策略，他们便能重置其密码。

## <a name="disable-self-service-password-reset"></a>禁用自助密码重置

如果你的组织决定禁用自助密码重置，则这是一个简单过程。 打开 Azure AD 租户，转到“密码重置” > “属性”，然后在“启用自助密码重置”下选择“无”。 用户将仍保持其

## <a name="next-steps"></a>后续步骤

* [重置或更改密码](../user-help/active-directory-passwords-update-your-own-password.md)
* [注册自助密码重置](../user-help/active-directory-passwords-reset-register.md)
* [是否有许可问题？](concept-sspr-licensing.md)
* [SSPR 使用哪些数据？应为用户填充哪些数据？](howto-sspr-authenticationdata.md)
* [SSPR 有哪些策略选项？](concept-sspr-policy.md)
* [什么是密码写回？我为什么关心它？](howto-sspr-writeback.md)
* [如何报告 SSPR 中的活动？](howto-sspr-reporting.md)
* [SSPR 中的所有选项有哪些？它们有哪些含义？](concept-sspr-howitworks.md)
* [我认为有些功能被破坏。如何对 SSPR 进行故障排除？](active-directory-passwords-troubleshoot.md)
* [我有在别处未涵盖的问题](active-directory-passwords-faq.md)

[Email]: ./media/howto-sspr-deployment/sspr-emailtemplates.png "根据组织需要自定义这些电子邮件模板"
