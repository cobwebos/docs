---
title: 基于 SMS 的用户登录 Azure Active Directory
description: 了解如何使用 SMS （预览版）配置和允许用户登录到 Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: 845948d9aec28ee79a11fb11aaef4cfbf1b263fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770552"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>使用 Azure Active Directory （预览版）配置和启用基于 SMS 的身份验证的用户

为了降低用户登录到应用程序和服务所带来的复杂性和安全风险，Azure Active Directory （Azure AD）提供了多个身份验证选项。 通过基于 SMS 的身份验证（目前为预览版），用户无需提供甚至知道用户名和密码即可登录。 在身份管理员创建帐户后，他们可以在登录提示中输入其电话号码，并提供通过短信发送给他们的身份验证代码。 此身份验证方法可简化对应用程序和服务的访问，特别是对于前端辅助角色。

本文介绍如何为 Azure AD 中的选择用户或组启用基于 SMS 的身份验证。

|     |
| --- |
| 用户的基于 SMS 的身份验证是 Azure Active Directory 的公共预览功能。 有关预览的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="before-you-begin"></a>在开始之前

若要完成本文，需要具备以下资源和权限：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与你的订阅关联的 Azure Active Directory 租户。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 需要 Azure AD 租户中的*全局管理员*权限才能启用基于 SMS 的身份验证。
* 在短信身份验证方法策略中启用的每个用户都必须获得许可，即使它们不使用它也是如此。 每个启用的用户都必须具有下列 Azure AD 之一或 Microsoft 365 许可证：
    * [Azure AD Premium P1 或 P2][azuread-licensing]
    * [Microsoft 365 （M365） F1 或 F3][m365-firstline-workers-licensing]
    * [企业移动性 + 安全性（EMS） e3 或 e5][ems-licensing]或[Microsoft 365 （M365） e3 或 e5][m365-licensing]

## <a name="limitations"></a>限制

在基于 SMS 的身份验证的公共预览版期间，有以下限制：

* 基于 SMS 的身份验证当前不兼容 Azure 多重身份验证。
* 除了团队以外，基于短信的身份验证当前不与本机 Office 应用程序兼容。
* 对于 B2B 帐户，不建议使用基于 SMS 的身份验证。
* 联合用户不会在 home 租户中进行身份验证。 它们仅在云中进行身份验证。

## <a name="enable-the-sms-based-authentication-method"></a>启用基于 SMS 的身份验证方法

在组织中启用和使用基于 SMS 的身份验证有三个主要步骤：

* 启用身份验证方法策略。
* 选择可以使用基于短信的身份验证方法的用户或组。
* 为每个用户帐户分配一个电话号码。
    * 可以在 "我的工作" 或 "我的工作*人员*" 或 *"我的个人资料*" 中为 Azure 门户（本文中所示）分配此电话号码。

首先，让我们为 Azure AD 租户启用基于 SMS 的身份验证。

1. 以*全局管理员身份*登录到[Azure 门户][azure-portal]。
1. 搜索并选择“Azure Active Directory”  。
1. 从 "Azure Active Directory" 窗口左侧的导航菜单中，选择 "**安全 > 身份验证方法" > 身份验证方法策略（预览）**。

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. 从可用的身份验证方法列表中，选择 "**短信**"。
1. 将 "**启用**" 设置为 *"是"*。

    ![在身份验证方法策略窗口中启用文本身份验证](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    你可以选择为*所有用户*启用基于 SMS 的身份验证，或*选择 "用户*和组"。 在下一部分中，将为测试用户启用基于 SMS 的身份验证。

## <a name="assign-the-authentication-method-to-users-and-groups"></a>向用户和组分配身份验证方法

在 Azure AD 租户中启用基于 SMS 的身份验证后，请选择要允许其使用此身份验证方法的某些用户或组。

1. 在 "短信身份验证策略" 窗口中，将 "**目标**" 设置为 "*选择用户*"。
1. 选择 "**添加用户或组**"，然后选择 "测试用户" 或 "组"，如 " *contoso 用户*" 或 " *contoso SMS 用户*"。

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. 选择用户或组后，选择 "**选择**"，并**保存**更新的身份验证方法策略。

在短信身份验证方法策略中启用的每个用户都必须获得许可，即使它们不使用它也是如此。 请确保你为在身份验证方法策略中启用的用户提供了相应的许可证，尤其是为大型用户组启用此功能时。

## <a name="set-a-phone-number-for-user-accounts"></a>设置用户帐户的电话号码

现在用户已启用基于 SMS 的身份验证，但必须将其电话号码与 Azure AD 中的用户配置文件相关联，然后才能登录。 用户可以在*我的个人资料*中[设置此电话号码](../user-help/sms-sign-in-explainer.md)，也可以使用 Azure 门户分配电话号码。 电话号码可以由*全局管理员*、*身份验证管理员*或*特权身份验证管理员*设置。

当电话号码设置为 "短信" 时，它也可用于[Azure 多重身份验证][tutorial-azure-mfa]和[自助密码重置][tutorial-sspr]。

1. 搜索并选择“Azure Active Directory”  。
1. 从 "Azure Active Directory" 窗口左侧的导航菜单中，选择 "**用户**"。
1. 选择在上一部分中为基于 SMS 的身份验证启用的用户，如*Contoso user*，然后选择**身份验证方法**。
1. 输入用户的电话号码，包括国家/地区代码，如 *+ 1 xxxxxxxxx*。 Azure 门户验证电话号码的格式是否正确。

    ![为 Azure 门户中的用户设置电话号码，以便将其用于基于短信的身份验证](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    电话号码在你的租户中必须是唯一的。 如果尝试为多个用户使用相同的电话号码，将显示一条错误消息。

1. 若要将电话号码应用到用户的帐户，请选择 "**保存**"。

成功预配后，会出现一个*启用了 SMS 登录*的复选标记。

## <a name="test-sms-based-sign-in"></a>测试基于 SMS 的登录

若要测试现在为基于 SMS 的登录而启用的用户帐户，请完成以下步骤：

1. 打开新的 InPrivate 或 Incognito web 浏览器窗口以[https://www.office.com][office]
1. 在右上角，选择 "**登录**"。
1. 在登录提示符下，输入与上一节中的用户关联的电话号码，然后选择 "**下一步**"。

    ![在测试用户的登录提示中输入电话号码](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. 短信将发送到提供的电话号码。 若要完成登录过程，请在登录提示中输入文本消息中提供的6位代码。

    ![输入通过短信发送到用户的电话号码的确认代码](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. 用户现已登录，不需要提供用户名或密码。

## <a name="troubleshoot-sms-based-sign-in"></a>排查基于 SMS 的登录问题

如果你在启用和使用基于短信的登录时遇到问题，则可以使用以下方案和故障排除步骤。

### <a name="phone-number-already-set-for-a-user-account"></a>已为用户帐户设置的电话号码

如果用户已注册 Azure 多重身份验证和/或自助服务密码重置（SSPR），则他们已经有了与其帐户关联的电话号码。 此电话号码不能自动用于基于短信的登录。

已为其帐户设置了电话号码的用户显示在其 **"我的配置文件**" 页中*启用了 SMS 登录的*按钮。 选择此按钮，该帐户可用于基于 SMS 的登录和之前的 Azure 多重身份验证或 SSPR 注册。

有关最终用户体验的详细信息，请参阅[电话号码的 SMS 登录用户体验（预览版）](../user-help/sms-sign-in-explainer.md)。

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>尝试设置用户帐户的电话号码时出现错误

如果在 Azure 门户中尝试为用户帐户设置电话号码时收到错误，请查看以下故障排除步骤：

1. 请确保已为基于 SMS 的登录预览启用了。
1. 确认在*短信*身份验证方法策略中启用了用户帐户。
1. 请确保按照 Azure 门户（例如 *+ 1 4251234567*）中的验证设置了正确格式的电话号码。
1. 请确保未在租户中的其他地方使用电话号码。
1. 检查帐户上没有设置任何语音号码。 如果设置了语音号码，请删除并再次尝试拨打电话号码。

## <a name="next-steps"></a>后续步骤

有关无需密码即可登录 Azure AD 的其他方法，如 Microsoft Authenticator 应用或 FIDO2 安全密钥，请参阅[Azure AD 的无密码 authentication 选项][concepts-passwordless]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
