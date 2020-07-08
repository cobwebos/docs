---
title: 适用于 Azure Active Directory 的基于 SMS 的用户登录
description: 了解如何配置和允许用户使用 SMS 登录到 Azure Active Directory（预览版）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55e64fad910aba25354d8d7829fc1189b744ca16
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85550480"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>使用 Azure Active Directory 配置和允许用户基于 SMS 进行身份验证（预览版）

为了降低用户登录应用程序和服务的复杂性及安全风险，Azure Active Directory (Azure AD) 提供多种身份验证选项。 基于 SMS 的身份验证目前以预览版提供，它使用户无需提供甚至无需知晓其用户名和密码即可登录。 标识管理员创建其帐户后，他们可以在登录提示中输入其电话号码，并提供通过短信发送给他们的验证码。 此身份验证方法可简化对应用程序和服务的访问，尤其是对一线工作人员而言。

本文介绍如何在 Azure AD 中为选定用户或组启用基于 SMS 的身份验证。

> [!NOTE]
> 基于 SMS 的用户身份验证是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="before-you-begin"></a>开始之前

需要拥有以下资源和权限才能完成本文中的操作：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 需要在 Azure AD 租户中拥有全局管理员权限才能启用基于 SMS 的身份验证。
* 短信身份验证方法策略中启用的每个用户都必须获得许可，即使他们不使用该方法也是如此。 每个启用的用户都必须具有以下 Azure AD、EMS、Microsoft 365 许可证之一：
    * [Azure AD Premium P1 或 P2][azuread-licensing]
    * [Microsoft 365 (M365) F1 或 F3][m365-firstline-workers-licensing]
    * [企业移动性 + 安全性 (EMS) E3 或 E5][ems-licensing] 或 [Microsoft 365 (M365) E3 或 E5][m365-licensing]

## <a name="limitations"></a>限制

在基于 SMS 的身份验证的公共预览版期间，存在以下限制：

* 基于 SMS 的身份验证目前与 Azure 多重身份验证不兼容。
* 除 Teams 外，基于 SMS 的身份验证目前与本机 Office 应用程序不兼容。
* 不建议 B2B 帐户使用基于 SMS 的身份验证。
* 联合身份验证用户不会在主租户中进行身份验证。 它们仅在云中进行身份验证。

## <a name="enable-the-sms-based-authentication-method"></a>启用基于 SMS 的身份验证方法

在组织中启用和使用基于 SMS 的身份验证有三个主要步骤：

* 启用身份验证方法策略。
* 选择可以使用基于 SMS 的身份验证方法的用户或组。
* 为每个用户帐户分配一个电话号码。
    * 可在 Azure 门户（如本文所示）和“我的员工”或“我的个人资料”中分配此电话号码。

首先，让我们为 Azure AD 租户启用基于 SMS 的身份验证。

1. 以全局管理员身份登录到 [Azure 门户][azure-portal]。
1. 搜索并选择“Azure Active Directory”。
1. 从“Azure Active Directory”窗口左侧的导航菜单中，选择“安全性”>“身份验证方法”>“身份验证方法策略(预览版)”。

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. 从可用身份验证方法列表中，选择“短信”。
1. 将“启用”设置为“是”。

    ![在“身份验证方法策略”窗口中启用短信身份验证](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    可选择为“所有用户”或“选择用户”和组启用基于 SMS 的身份验证。 在下一部分中，为测试用户启用基于 SMS 的身份验证。

## <a name="assign-the-authentication-method-to-users-and-groups"></a>为用户和组分配身份验证方法

在 Azure AD 租户中启用基于 SMS 的身份验证后，现在可选择允许使用此身份验证方法的一些用户或组。

1. 在“短信身份验证策略”窗口中，将“目标”设置为“选择用户”。
1. 选择“添加用户或组”，然后选择测试用户或组，例如“Contoso 用户”或“Contoso SMS 用户”。

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. 选择用户或组后，选择“选择”，然后“保存”更新的身份验证方法策略。

短信身份验证方法策略中启用的每个用户都必须获得许可，即使他们不使用该方法也是如此。 确保为身份验证方法策略中启用的用户提供适当的许可证，尤其在为大型用户组启用该功能时。

## <a name="set-a-phone-number-for-user-accounts"></a>设置用户帐户的电话号码

现在，已为用户启用了基于 SMS 的身份验证，接下来必须先将其电话号码与 Azure AD 中的用户配置文件关联，然后他们才能登录。 用户可以在“我的个人资料”中[自己设置此电话号码](../user-help/sms-sign-in-explainer.md)，你也可以使用 Azure 门户分配电话号码。 电话号码可由全局管理员、身份验证管理员或特权身份验证管理员进行设置。

设置用于 SMS 登录的电话号码后，该电话号码还可用于 [Azure 多重身份验证][tutorial-azure-mfa]和[自助式密码重置][tutorial-sspr]。

1. 搜索并选择“Azure Active Directory”。
1. 在“Azure Active Directory”窗口左侧的导航菜单中，选择“用户”。
1. 选择在上一部分中启用了基于 SMS 身份验证的用户（例如“Contoso 用户”），然后选择“身份验证方法”。
1. 输入用户的电话号码，包括国家/地区代码，例如 +1 xxxxxxxxx。 Azure 门户会验证电话号码的格式是否正确。

    ![在 Azure 门户中为用户设置用于基于 SMS 的身份验证的电话号码](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    该电话号码在租户中必须唯一。 如果尝试为多个用户使用相同的电话号码，则会显示错误消息。

1. 若要将电话号码应用于用户的帐户，请选择“保存”。

成功预配后，“SMS 登录已启用”将显示一个复选标记。

## <a name="test-sms-based-sign-in"></a>测试基于 SMS 的登录

若要测试现已启用基于 SMS 的登录的用户帐户，请完成以下步骤：

1. 在新的 InPrivate 或 Incognito Web 浏览器窗口中打开 [https://www.office.com][office]
1. 选择右上角的“登录”。
1. 在登录提示中，输入上一部分中与用户关联的电话号码，然后选择“下一步”。

    ![在登录提示中输入测试用户的电话号码](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. 系统会向提供的电话号码发送一条短信。 若要完成登录过程，请在登录提示中输入短信中提供的 6 位代码。

    ![输入通过短信发送到用户电话号码的确认代码](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. 现在，用户无需提供用户名或密码即可登录。

## <a name="troubleshoot-sms-based-sign-in"></a>排查基于 SMS 的登录问题

如果在启用和使用基于 SMS 的登录时遇到问题，可以使用以下方案和故障排除步骤。

### <a name="phone-number-already-set-for-a-user-account"></a>已为用户帐户设置电话号码

如果用户已经注册 Azure 多重身份验证和/或自助式密码重置 (SSPR)，则他们已拥有与其帐户关联的电话号码。 此电话号码不会自动用于基于 SMS 的登录。

帐户已设置电话号码的用户的“我的个人资料””页中的“为 SMS 登录启用”旁会显示一个按钮。 选择此按钮，即会启用该帐户用于基于 SMS 的登录和之前的 Azure 多重身份验证或 SSPR 注册。

有关最终用户体验的详细信息，请参阅[电话号码 SMS 登录用户体验（预览版）](../user-help/sms-sign-in-explainer.md)。

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>尝试在用户帐户上设置电话号码时出错

如果尝试在 Azure 门户中为用户帐户设置电话号码时收到错误，请查看以下故障排除步骤：

1. 确保已启用基于 SMS 的登录预览版。
1. 确认在“短信”身份验证方法策略中启用了用户帐户。
1. 确保使用正确的格式设置电话号码并已在 Azur e门户中验证（例如 +1 4251234567）。
1. 确保租户中的其他位置未使用该电话号码。
1. 检查帐户中是否设置了语音号码。 如果设置了语音号码，请删除并尝试重新输入电话号码。

## <a name="next-steps"></a>后续步骤

有关不使用密码登录 Azure AD 的其他方法（例如 Microsoft Authenticator 应用或 FIDO2 安全密钥），请参阅 [Azure AD 无密码身份验证选项][concepts-passwordless]。

你还可以使用 Microsoft Graph REST API beta 来[启用][rest-enable]或[禁用][rest-disable]基于 SMS 的登录。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?view=graph-rest-beta&tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?view=graph-rest-beta&tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab
