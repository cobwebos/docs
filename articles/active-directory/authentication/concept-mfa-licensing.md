---
title: Azure 多重身份验证版本和消费计划
description: 了解有关 Azure 多重身份验证客户端以及可用的不同方法和版本的信息。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 165b84ee6b124d3f6a04c8db177ef17e32784ff9
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83757409"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Azure 多重身份验证的功能和许可证

若要保护组织中的用户帐户，应使用多重身份验证。 对资源拥有访问特权的帐户尤其需要此功能。 Office 365 和 Azure Active Directory (Azure AD) 管理员可使用基本的多重身份验证功能，无需额外费用。 如果想要升级管理员的功能，或者将多重身份验证扩展到其他用户，可以通过多种方式购买 Azure 多重身份验证。

> [!IMPORTANT]
> 本文详细介绍了可以获得 Azure 多重身份验证的许可并使用的不同方式。 有关定价和计费的具体详细信息，请参阅[Azure 多重身份验证定价页](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。

## <a name="available-versions-of-azure-multi-factor-authentication"></a>可用的 Azure 多重身份验证版本

根据组织的需求，可以通过几种不同的方式使用 Azure 多重身份验证和获得许可。 你可能有权使用 Azure 多重身份验证，具体取决于当前拥有 Azure AD、Office 365、EMS 或 Microsoft 365 许可证。 下表详细介绍了获取 Azure 多重身份验证的不同方式，以及每种方式的一些功能和用例。

| 如果你是以下一种用户： | 功能和用例 |
| --- | --- |
| EMS 或 Microsoft 365 E3 和 E5 | EMS E3 或 Microsoft 365 E3（包括 EMS 和 Office 365），包含 Azure AD Premium P1。 EMS E5 或 Microsoft 365 E5，包含 Azure AD Premium P2。 可以使用以下部分中所述的相同条件性访问功能向用户提供多重身份验证。 |
| Azure AD Premium P1 | 在特定的情况下或者发生适合业务要求的事件时，使用 [Azure AD 条件访问](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)提示用户执行多重身份验证。 |
| Azure AD Premium P2 | 提供了最强的安全保障和改进的用户体验。 在 Azure AD Premium P1 功能的基础上增加了[基于风险的条件访问](../conditional-access/howto-conditional-access-policy-risk.md)，以适应用户模式并尽量减少出现多重身份验证提示。 |
| OFFICE 365 商业高级版、E3 或 E5 | 可以[基于每个用户](howto-mfa-userstates.md)启用 Azure 多重身份验证，也可以使用安全默认设置为所有用户、为所有登录事件启用或禁用 Azure 多重身份验证。 Azure 多重身份验证的管理通过 Office 365 门户进行。 若要改进用户体验，请升级到 Azure AD Premium P1 或 P2 并使用条件访问。 有关详细信息，请参阅[使用多重身份验证保护 Office 365 资源](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)。 |
| Azure AD 免费版 | 可以使用[安全默认设置](../fundamentals/concept-fundamentals-security-defaults.md)在每次发出身份验证请求时为所有用户启用多重身份验证。 未对已启用功能的用户或方案进行精细控制，但它确实提供了额外的安全步骤。<br /> 即使安全默认设置不用于为每个人启用多重身份验证，但分配了 *Azure AD 全局管理员* 角色的用户也可以被配置为使用多重身份验证。 免费层的这一功能可确保关键管理员帐户受到多重身份验证的保护。 |

## <a name="feature-comparison-of-versions"></a>版本功能比较

下表提供了 Azure 多重身份验证的各个版本中可用的功能列表。 规划对于保护用户身份验证的需求，然后确定哪些方法满足这些要求。 例如，尽管 Azure AD 免费版提供了 Azure 多重身份验证的安全默认设置，但只有移动身份验证应用可用于身份验证提示，而不是电话或短信。 如果无法确保将移动身份验证应用安装在用户的个人设备上，此方法可能会是一种限制。

| Feature | Azure AD 免费版 - 安全默认设置 | Azure AD 免费版 - Azure AD 全局管理员 | OFFICE 365 商业高级版、E3 或 E5 | Azure AD Premium P1 或 P2 |
| --- |:---:|:---:|:---:|:---:|
| 使用 MFA 保护 Azure AD 租户管理员帐户 | ● | ●（仅适用于 *Azure AD 全局管理员帐户*） | ● | ● |
| 将移动应用用作第二个因素 | ● | ● | ● | ● |
| 将电话呼叫用作第二个因素 | | ● | ● | ● |
| 将短信用作第二个因素 | | ● | ● | ● |
| 管理员控制验证方法 | | ● | ● | ● |
| 欺诈警报 | | | | ● |
| MFA 报告 | | | | ● |
| 通话的自定义问候语 | | | | ● |
| 通话的自定义呼叫方 ID | | | | ● |
| 受信任的 IP | | | | ● |
| 记住受信任的设备的 MFA | | ● | ● | ● |
| 适用于本地应用程序的 MFA | | | | ● |

> [!IMPORTANT]
> 从 2019 年 3 月起，Azure 多重身份验证和 Azure AD 免费版/试用租户中的 Azure 自助服务密码重置用户不再使用电话呼叫选项。 短信不受此更改的影响。 Azure AD Premium P1 或 P2 租户或者使用 Office 365 商业高级版、E3 版或 E5 版中的用户仍可使用电话呼叫。

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>购买和启用 Azure 多重身份验证

若要使用 Azure 多重身份验证，请注册或购买符合条件的 Azure AD 分层。 Azure AD 提供了四个版本：免费版、Office 365 应用版（适用于 Office 365 商业高级版 E3 或 E5 客户）、Premium P1 和 Premium P2。

Azure 订阅随附了免费版。 请参阅[下面的部分](#azure-ad-free-tier)了解如何使用安全默认设置或使用 *Azure AD 全局管理员*角色保护帐户。

Azure AD Premium 版通过 Microsoft 代表、[开放批量许可计划](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)和[云解决方案提供商计划](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409)提供。 Azure 和 Office 365 订阅者还可以在线购买 Active Directory Premium P1 and P2。 [登录](https://portal.office.com/Commerce/Catalog.aspx)购买。

> [!IMPORTANT]
> 自 2018 年 9 月 1 日起，新客户无法再使用基于消费的许可。 现有客户可以继续使用基于消费的模型（按每个启用的用户或按身份验证计费）。

购买所需的 Azure AD 分层后，[规划和部署 Azure 多重身份验证](howto-mfa-getstarted.md)。

### <a name="azure-ad-free-tier"></a>Azure AD 免费版层

Azure AD 免费版租户中的所有用户都可以使用安全默认设置使用 Azure 多重身份验证。 这些安全默认设置在每次用户登录时为所有用户启用 Azure 多重身份验证。 移动身份验证应用是在使用 Azure AD 免费版安全默认设置时可用于进行 Azure 多重身份验证的唯一方法。

* [详细了解 Azure AD 安全默认设置](../fundamentals/concept-fundamentals-security-defaults.md)
* [为 Azure AD 免费版中的用户启用安全默认设置](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

如果不想为所有用户和每次登录事件启用 Azure 多重身份验证，则可以改为选择仅保护使用 *Azure AD 全局管理员*角色的用户帐户。 此方法为关键管理员帐户提供了其他身份验证提示。 通过以下方式之一启用 Azure 多重身份验证，具体取决于所使用的帐户类型：

* 如果使用的是 Microsoft 帐户，[注册多重身份验证](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)。
* 如果使用的不是 Microsoft 帐户，[为 Azure AD 中的用户或组启用多重身份验证](howto-mfa-userstates.md)。

## <a name="next-steps"></a>后续步骤

* 有关费用的详细信息，请参阅[Azure 多重身份验证定价](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。
* [什么是条件访问？](../conditional-access/overview.md)

