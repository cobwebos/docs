---
title: Azure 活动目录基于 SMS 的用户登录
description: 了解如何配置和使用户能够使用 SMS 登录到 Azure 活动目录（预览）
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
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770552"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>使用 Azure 活动目录配置和启用用户进行基于 SMS 的身份验证（预览）

为了降低用户登录到应用程序和服务的复杂性和安全风险，Azure 活动目录 （Azure AD） 提供了多个身份验证选项。 基于 SMS 的身份验证当前处于预览状态，允许用户无需提供甚至知道用户名和密码即可登录。 身份管理员创建其帐户后，他们可以在登录提示符处输入其电话号码，并提供通过短信发送给他们的身份验证代码。 此身份验证方法简化了对应用程序和服务的访问，尤其是对于前线工作人员。

本文介绍如何为 Azure AD 中选定的用户或组启用基于 SMS 的身份验证。

|     |
| --- |
| 基于 SMS 的用户身份验证是 Azure 活动目录的公共预览功能。 有关预览的详细信息，请参阅 Microsoft [Azure 预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="before-you-begin"></a>开始之前

要完成本文，您需要以下资源和特权：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure 活动目录租户。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 需要在 Azure AD 租户中具有*全局管理员*权限才能启用基于 SMS 的身份验证。
* 在文本消息身份验证方法策略中启用的每个用户都必须获得许可，即使他们不使用它。 每个启用的用户必须具有以下 Azure AD 或 Microsoft 365 许可证之一：
    * [Azure AD Premium P1 或 P2][azuread-licensing]
    * [微软 365 （M365） F1 或 F3][m365-firstline-workers-licensing]
    * [企业移动性 + 安全 （EMS） E3 或 E5][ems-licensing]或[Microsoft 365 （M365） E3 或 E5][m365-licensing]

## <a name="limitations"></a>限制

在基于 SMS 的身份验证的公共预览期间，以下限制适用：

* 基于 SMS 的身份验证当前与 Azure 多重身份验证不兼容。
* 除 Teams 外，基于 SMS 的身份验证当前与本机 Office 应用程序不兼容。
* 不建议 B2B 帐户使用基于 SMS 的身份验证。
* 联合用户不会在主租户中进行身份验证。 它们仅在云中进行身份验证。

## <a name="enable-the-sms-based-authentication-method"></a>启用基于 SMS 的身份验证方法

您的组织中启用和使用基于 SMS 的身份验证有三个主要步骤：

* 启用身份验证方法策略。
* 选择可以使用基于 SMS 的身份验证方法的用户或组。
* 为每个用户帐户分配电话号码。
    * 此电话号码可以在 Azure 门户（本文中所示）和 *"我的员工*"或 *"我的个人资料*"中分配。

首先，让我们为 Azure AD 租户启用基于 SMS 的身份验证。

1. 以*全局管理员*身份登录到[Azure 门户][azure-portal]。
1. 搜索并选择“Azure Active Directory”  。
1. 从 Azure 活动目录窗口左侧的导航菜单中，选择 **"安全>身份验证方法>身份验证方法策略（预览）。**

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. 从可用身份验证方法列表中，选择 **"文本消息**"。
1. 将**启用设置为***"是*"。

    ![在身份验证方法策略窗口中启用文本身份验证](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    您可以选择为*所有用户*或*选择用户*和组启用基于 SMS 的身份验证。 在下一节中，您可以为测试用户启用基于 SMS 的身份验证。

## <a name="assign-the-authentication-method-to-users-and-groups"></a>将身份验证方法分配给用户和组

在 Azure AD 租户中启用基于 SMS 的身份验证后，现在选择允许使用此身份验证方法的某些用户或组。

1. 在短信身份验证策略窗口中，将 **"目标"** 设置为 *"选择用户*"。
1. 选择添加**用户或组**，然后选择测试用户或组，如*Contoso 用户*或*Contoso SMS 用户*。

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. 选择用户或组后，**选择"选择**"，然后**保存**更新的身份验证方法策略。

在文本消息身份验证方法策略中启用的每个用户都必须获得许可，即使他们不使用它。 请确保为身份验证方法策略中启用的用户提供了适当的许可证，尤其是在为大型用户组启用该功能时。

## <a name="set-a-phone-number-for-user-accounts"></a>设置用户帐户的电话号码

用户现在已启用基于 SMS 的身份验证，但其电话号码必须与 Azure AD 中的用户配置文件关联，然后才能登录。 用户可以在 *"我的配置文件"* 中[自行设置此电话号码](../user-help/sms-sign-in-explainer.md)，也可以使用 Azure 门户分配电话号码。 电话号码可以由*全局管理员*、*身份验证管理员*或*特权身份验证管理员*设置。

当为 SMS 签名设置电话号码时，它也可以与[Azure 多重身份验证][tutorial-azure-mfa]和[自助服务密码重置][tutorial-sspr]一起使用。

1. 搜索并选择“Azure Active Directory”  。
1. 从 Azure 活动目录窗口左侧的导航菜单中，选择 **"用户**"。
1. 在上一节中（如*Contoso User）* 中选择启用基于 SMS 的身份验证的用户，然后选择**身份验证方法**。
1. 输入用户的电话号码，包括国家/地区代码，如 *#1 xxxxxxxxx*。 Azure 门户验证电话号码的格式正确。

    ![为 Azure 门户中的用户设置电话号码，以便与基于 SMS 的身份验证一起使用](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    电话号码在租户中必须是唯一的。 如果尝试对多个用户使用相同的电话号码，将显示一条错误消息。

1. 要将电话号码应用于用户帐户，请选择"**保存**"。

成功预配后，*将显示启用 SMS 登录的*复选标记。

## <a name="test-sms-based-sign-in"></a>测试基于 SMS 的登录

要测试现在启用的基于 SMS 的登录的用户帐户，请完成以下步骤：

1. 打开新的 InPrivate 或隐身 Web 浏览器窗口，[https://www.office.com][office]
1. 在右上角，选择 **"登录**"。
1. 在登录提示符中，在上一节中输入与用户关联的电话号码，然后选择 **"下一步**"。

    ![在测试用户的登录提示处输入电话号码](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. 文本消息发送到提供的电话号码。 要完成登录过程，请在登录提示符的短信中输入 6 位代码。

    ![输入通过短信发送到用户电话号码的确认代码](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. 用户现在登录而无需提供用户名或密码。

## <a name="troubleshoot-sms-based-sign-in"></a>排除基于 SMS 的登录故障

如果在启用和使用基于 SMS 的登录时遇到问题，可以使用以下方案和故障排除步骤。

### <a name="phone-number-already-set-for-a-user-account"></a>已为用户帐户设置的电话号码

如果用户已注册 Azure 多重身份验证和/或自助服务密码重置 （SSPR），则他们已具有与其帐户关联的电话号码。 此电话号码不能自动用于基于 SMS 的登录。

已为其帐户设置电话号码的用户将显示一个按钮，用于在其 **"我的个人资料"** 页*中启用 SMS 登录*。 选择此按钮，该帐户将启用，以便与基于 SMS 的登录和以前的 Azure 多重身份验证或 SSPR 注册一起使用。

有关最终用户体验的详细信息，请参阅[SMS 登录用户体验的电话号码（预览）。](../user-help/sms-sign-in-explainer.md)

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>尝试在用户帐户上设置电话号码时出错

如果在 Azure 门户中尝试为用户帐户设置电话号码时收到错误，请查看以下故障排除步骤：

1. 确保已启用基于 SMS 的登录预览。
1. 确认在*短信*身份验证方法策略中启用了用户帐户。
1. 请确保使用 Azure 门户中验证的正确格式设置电话号码（如 *+1 4251234567）。*
1. 确保电话号码在租户的其他地方未使用。
1. 检查帐户上未设置语音号码。 如果设置了语音号码，请删除并再次尝试电话号码。

## <a name="next-steps"></a>后续步骤

有关在没有密码的情况下登录到 Azure AD 的其他方法，例如 Microsoft 身份验证器应用或 FIDO2 安全密钥，请参阅[Azure AD 的无密码身份验证选项][concepts-passwordless]。

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
