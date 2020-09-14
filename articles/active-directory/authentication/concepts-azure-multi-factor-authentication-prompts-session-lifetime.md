---
title: Azure 多重身份验证提示和会话生存期
description: 了解有关使用 Azure 多重身份验证进行身份验证的建议配置和会话生存期的应用方式。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d5ffaf996f51348334f4adeeae150db9eb0defa
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052658"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-multi-factor-authentication"></a>优化重新验证的提示并了解 Azure 多重身份验证的会话生存期

Azure Active Directory (Azure AD) 具有多个设置，用于确定用户需要重新进行身份验证的频率。 这种重新验证可能会导致首个因素（如 password、FIDO 或无密码 Microsoft Authenticator）或 (MFA) 执行多重身份验证。 你可以根据自己的环境和所需的用户体验来配置这些身份验证设置。

用户登录频率 Azure AD 默认配置是90天的滚动窗口。 要求用户提供凭据通常是一项明智的事情，但它可以 backfire。 如果已训练用户在未考虑的情况下输入其凭据，则他们可能会无意中将其提供给恶意凭据提示。

即使任何违反 IT 策略的情况都吊销会话，它也可能会发出警报。 一些示例包括密码更改、incompliant 设备或帐户禁用操作。 还可以 [使用 PowerShell 显式撤销用户会话](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)。

本文详细介绍了推荐的配置，以及不同的设置的工作方式和相互交互。

## <a name="recommended-settings"></a>建议的设置

若要为用户授予正确的安全性和易用性，并要求他们以适当的频率登录，我们建议使用以下配置：

* 如果有 Azure AD Premium：
    * 使用 [托管设备](../devices/overview.md) 或 [无缝 SSO](../hybrid/how-to-connect-sso.md)跨应用程序启用单一登录 (SSO) 。
    * 如果需要重新进行身份验证，请使用条件访问 [登录频率策略](../conditional-access/howto-conditional-access-session-lifetime.md)。
    * 对于从非托管设备或移动设备方案登录的用户，使用条件访问来启用持久性浏览器会话和登录频率策略。
* 如果有 Microsoft 365 应用许可证或免费 Azure AD 层：
    * 使用 [托管设备](../devices/overview.md) 或 [无缝 SSO](../hybrid/how-to-connect-sso.md)跨应用程序启用单一登录 (SSO) 。
    * 保持启用 " *保持登录* " 选项，并指导用户接受该选项。
* 对于移动设备方案，请确保用户使用 Microsoft Authenticator 应用。 此应用程序用作其他 Azure AD 联合应用程序的代理，并减少设备上的身份验证提示。

我们的研究表明，这些设置适用于大多数租户。 这些设置的一些组合（如 " *记住 MFA* " 和 " *签名*"）可能会导致提示用户进行身份验证。 一般的身份验证提示对于用户工作效率是不正确的，并且可以使其更易受到攻击。

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Azure AD 会话生存期配置设置

若要优化用户身份验证提示的频率，可以配置 Azure AD 会话生存期选项。 了解你的业务和用户的需求，并配置为你的环境提供最佳平衡的设置。

### <a name="evaluate-session-lifetime-policies"></a>评估会话生存期策略

如果没有任何会话生存期设置，则浏览器会话中不存在永久性 cookie。 每次用户关闭并打开浏览器时，都会收到重新进行身份验证的提示。 在 Office 客户端中，默认时间段为90天的滚动窗口。 如果用户已重置了其密码，或在超过90天处于非活动状态，则对于此默认 Office 配置，用户需要通过)  (第一个和第二个因素的所有必需因素进行身份验证。

用户可能会在没有 Azure AD 标识的设备上看到多个 MFA 提示。 当每个应用程序都有自己的与其他客户端应用共享的 OAuth 刷新令牌时，会产生多个提示。 在这种情况下，MFA 会在每个应用程序请求使用 MFA 验证 OAuth 刷新令牌时多次提示。

在 Azure AD 中，对会话生存期最严格的策略决定用户需要重新进行身份验证的时间。 请参考以下方案：

* 启用 *仍登录*，这将使用永久性浏览器 cookie，并
* 你还可以在*14 天内启用 "记住 MFA* "

在此示例方案中，用户需要每14天进行一次身份验证。 此行为遵循最严格的策略，即使 " *使我保持登录* "，也不需要用户在浏览器中进行身份验证。

### <a name="managed-devices"></a>托管设备

使用 Azure AD 联接或混合 Azure AD 联接加入到 Azure AD 的设备接收 [主刷新令牌 (PRT) ](../devices/concept-primary-refresh-token.md) ，以便在应用程序之间使用单一登录 (SSO) 。 此 PRT 允许用户在设备上登录一次，并允许 IT 人员确保满足安全和合规性标准。 如果用户需要在已联接的设备上针对某些应用或方案更频繁地登录，可以使用 [条件性访问登录频率](../conditional-access/howto-conditional-access-session-lifetime.md)来实现此目的。

### <a name="show-option-to-remain-signed-in"></a>显示保持登录的选项

当用户在登录期间在 "*保持登录"* 选项中选择 **"是"** 时，将在浏览器上设置持久性 cookie。 此永久性 cookie 同时记住第一个和第二个因素，并且仅应用于浏览器中的身份验证请求。

![保持登录状态的示例提示屏幕截图](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

如果有 Azure AD Premium 1 许可证，我们建议使用条件性访问策略来实现 *持久的浏览器会话*。 此策略会覆盖 " *保持登录"* 设置，并提供改进的用户体验。 如果没有 Azure AD Premium 1 许可证，我们建议为用户启用 "保持登录" 设置。

有关配置选项以使用户保持登录的详细信息，请参阅 [自定义 Azure AD 登录页](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page)。

### <a name="remember-multi-factor-authentication"></a>记住多重身份验证  

此设置允许你配置介于1-365 天之间的值，并在用户选择 "在登录时 **不再次询问 X 天** " 选项时在浏览器上设置持久性 cookie。

![批准登录请求的示例提示屏幕截图](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

尽管此设置可减少 web 应用的身份验证次数，但会增加新式身份验证客户端的身份验证次数，如 Office 客户端。 通常，在密码重置或非活动状态为90天后，这些客户端通常会提示。 但是，将此值设置为小于90天会缩短 Office 客户端的默认 MFA 提示，并增加重新验证的频率。 当结合使用时，将与 **保持登录** 或条件性访问策略一起使用时，它可能会增加身份验证请求的数目。

如果你使用 " *记住 MFA* " 并有 Azure AD Premium 1 个许可证，请考虑将这些设置迁移到条件访问登录频率。 否则，请考虑使用 *"使我保持登录"* ，而不是。

有关详细信息，请参阅 [记住多重身份验证](howto-mfa-mfasettings.md#remember-multi-factor-authentication)。

### <a name="authentication-session-management-with-conditional-access"></a>使用条件性访问的身份验证会话管理

**登录频率** 允许管理员选择适用于客户端和浏览器中第一个和第二个因素的登录频率。 如果需要限制身份验证会话（例如关键业务应用程序），则建议使用这些设置以及使用托管设备。

**持久性浏览器会话** 允许用户在关闭并重新打开其浏览器窗口后保持登录。 与 " *保持登录* " 设置相似，它在浏览器上设置持久性 cookie。 但是，由于它是由管理员配置的，因此不要求用户在 "*保持登录"* 选项中选择 **"是"** ，因此提供了更好的用户体验。 如果使用 " *保持登录"* 选项，则建议改为启用 **永久浏览器会话** 策略。

获取详细信息。 请参阅 [使用条件访问配置身份验证会话管理](../conditional-access/howto-conditional-access-session-lifetime.md)。

### <a name="configurable-token-lifetimes"></a>可配置的令牌生存期

此设置允许配置 Azure Active Directory 颁发的令牌的生存期。 此策略由 *具有条件访问的身份验证会话管理*替换。 如果现在使用 *可配置的令牌生存期* ，则建议启动到条件性访问策略的迁移。

## <a name="review-your-tenant-configuration"></a>查看租户配置  

现在，你已了解不同设置的工作原理和推荐的配置，可以检查租户配置并相应地进行更改：

若要配置或查看 *保持登录* 选项，请完成以下步骤：

1. 在 Azure AD 门户中，搜索并选择 " *Azure Active Directory*"。
1. 选择 " **公司品牌**"，然后对每个区域设置选择 " **显示" 选项以保持登录**。
1. 选择 *"是*"，然后选择 " **保存**"。

若要记住多重身份验证设置，请完成以下步骤：

1. 在 Azure AD 门户中，搜索并选择 " *Azure Active Directory*"。
1. 选择 " **安全性**"，然后选择 " **MFA**"。
1. 在 " **配置**" 下，选择 " **其他基于云的 MFA 设置**"。
1. 在 " *多重身份验证服务设置* " 页中，滚动以 **记住多重身份验证设置**。 通过取消选中相应的复选框来禁用此设置。

若要配置条件性访问策略以登录频率和持续浏览器会话，请完成以下步骤：

1. 在 Azure AD 门户中，搜索并选择 " *Azure Active Directory*"。
1. 选择 " **安全性**"，然后选择 " **条件访问**"。
1. 使用本文详细介绍的建议会话管理选项配置策略。

若要查看令牌生存期，请 [使用 Azure AD PowerShell 来查询任何 Azure AD 策略](../develop/active-directory-configurable-token-lifetimes.md#prerequisites)。 禁用已有的任何策略。

如果在租户中启用了多个设置，则建议你根据可用的许可来更新设置。 例如，如果你有 Azure AD premium 许可证，则应仅使用 *登录频率* 和 *持续浏览器会话*的条件性访问策略。 如果有 Microsoft 365 应用或 Azure AD 免费许可证，则应使用 " *保持登录"* 配置。

如果已启用可配置的令牌生存期，则很快会删除此功能。 规划迁移到条件性访问策略。

下表汇总了基于许可证的建议：

|              | Azure AD Free 和 Microsoft 365 应用 | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| **SSO**                      | 对于非托管设备， [Azure AD 联接](../devices/concept-azure-ad-join.md)或[混合 Azure AD 联接](../devices/concept-azure-ad-join-hybrid.md)或[无缝 SSO](../hybrid/how-to-connect-sso.md) 。 | Azure AD 加入<br />混合 Azure AD 加入 |
| **重新身份验证设置** | 保持登录                  | 使用条件性访问策略进行登录频率和持续浏览器会话 |

## <a name="next-steps"></a>后续步骤

若要开始，请完成教程以 [使用 Azure 多重身份验证保护用户登录事件](tutorial-enable-azure-mfa.md) ，或 [使用用户登录的风险检测来触发 Azure 多重身份验证](tutorial-risk-based-sspr-mfa.md)。
