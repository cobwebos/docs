---
title: Azure 多重身份验证版本和消耗计划
description: 了解 Azure 多重身份验证客户端以及可用的不同方法和版本。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e49a07f591731a1deb2838751852c0134548966d
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77521840"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Azure 多重身份验证的功能和许可证

若要保护组织中的用户帐户，应使用多重身份验证。 对资源拥有访问特权的帐户尤其需要此功能。 基本多重身份验证功能适用于 Office 365 和 Azure Active Directory （Azure AD）管理员，无需额外付费。 如果要升级管理员的功能，或将多重身份验证扩展到其他用户，可以通过多种方式购买 Azure 多重身份验证。

> [!IMPORTANT]
> 本文详细介绍了 Azure 多重身份验证可以获得许可和使用的不同方式。 有关定价和计费的具体详细信息，请参阅[Azure 多重身份验证定价页](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。

## <a name="available-versions-of-azure-multi-factor-authentication"></a>可用的 Azure 多重身份验证版本

Azure 多重身份验证可以通过几种不同的方式使用和授权，具体取决于组织的需求。 你可能已有权使用 Azure 多重身份验证，具体取决于你当前拥有 Azure AD、Office 365、EMS 或 Microsoft 365 许可证。 下表详细介绍了实现 Azure 多重身份验证的不同方式，以及每种身份验证的一些功能和用例。

| 如果用户是 | 功能和用例 |
| --- | --- |
| EMS 或 Microsoft 365 E3 和 E5 | EMS E3 或 Microsoft 365 E3 （包括 EMS 和 Office 365）包含 Azure AD Premium P1。 EMS E5 或 Microsoft 365 E5 包括 Azure AD Premium P2。 您可以使用以下各节中所述的条件性访问功能来向用户提供多重身份验证。 |
| Azure AD Premium P1 | 你可以使用[Azure AD 条件性访问](../conditional-access/overview.md)来提示用户在特定方案或事件期间进行多重身份验证，以满足你的业务需求。 |
| Azure AD Premium P2 | 提供最强的安全位置和改进的用户体验。 将[基于风险的条件访问](../conditional-access/howto-conditional-access-policy-risk.md)添加到可适应用户模式的 Azure AD Premium P1 功能，并将多因素身份验证提示降到最低。 |
| Office 365 商业高级版、E3 版或 E5 版 | 为所有用户启用或禁用 Azure 多重身份验证，适用于所有登录事件。 只能为一部分用户或仅在某些情况下启用多重身份验证。 管理是通过 Office 365 门户进行的。 为了改进用户体验，请升级到 Azure AD Premium P1 或 P2 并使用条件性访问。 有关详细信息，请参阅[通过多重身份验证保护 Office 365 资源](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)。 |
| Azure AD 免费版 | 每次进行身份验证请求时，可以使用[安全默认值](../fundamentals/concept-fundamentals-security-defaults.md)为所有用户启用多重身份验证。 你没有对已启用的用户或方案的精细控制，但它确实提供了额外的安全步骤。<br /> 即使安全默认设置不用于为每个人启用多重身份验证，分配了*Azure AD 全局管理员*角色的用户也可以配置为使用多重身份验证。 免费层的这一功能可确保关键管理员帐户受到多重身份验证的保护。 |

## <a name="feature-comparison-of-versions"></a>版本功能比较

下表提供了 Azure 多重身份验证的各个版本中可用的功能列表。 规划保护用户身份验证的需求，然后确定哪些方法满足这些要求。 例如，尽管 Azure AD Free 提供提供 Azure 多重身份验证的安全默认设置，但只有移动身份验证器应用可用于身份验证提示，而不是电话或短信。 如果无法确保将移动身份验证应用安装在用户的个人设备上，此方法可能会受到限制。

| Feature | Azure AD Free-安全默认值 | Azure AD 全局管理员 Azure AD Free | Office 365 商业高级版、E3 版或 E5 版 | Azure AD Premium P1 或 P2 |
| --- |:---:|:---:|:---:|:---:|
| 使用 MFA 保护 Azure AD 管理员帐户 | ● | ●（仅*Azure AD 全局管理员*帐户） | ● | ● |
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
> 从2019年3月起，电话呼叫选项不再可用于 Azure 多重身份验证，以及 Azure AD Free/试用租户中的 Azure 自助服务密码重置用户。 SMS 消息不受此更改的影响。 Azure AD Premium P1 或 P2 租户中的用户仍可使用电话呼叫，或者使用或 Office 365 业务高级版、E3 版或 E5 版。

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>购买并启用 Azure 多重身份验证

若要使用 Azure 多重身份验证，请注册或购买符合条件的 Azure AD 层。 Azure AD 提供了四个版本：免费版、Office 365 应用版（适用于 Office 365 商业高级版 E3 或 E5 客户）、高级 P1 和高级 P2。

免费版包含在 Azure 订阅中。 请参阅[下面的部分](#azure-ad-free-tier)，了解有关如何使用安全默认值或保护具有*Azure AD 全局管理员*角色的帐户的信息。

Azure AD Premium 版本可通过 Microsoft 代表、[开放批量许可计划](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)和[云解决方案提供商计划](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409)获得。 Azure 和 Office 365 订阅者还可以在线购买 Azure Active Directory Premium P1 和 P2。 [登录](https://portal.office.com/Commerce/Catalog.aspx)以购买。

> [!IMPORTANT]
> 自2018年9月1日起，新客户不再能够使用基于消费的许可。 使用基于消耗的模型的现有客户可以继续使用每个启用的用户或按身份验证计费。

购买所需的 Azure AD 层后，请[规划和部署 Azure 多重身份验证](howto-mfa-getstarted.md)。

### <a name="azure-ad-free-tier"></a>Azure AD Free 层

Azure AD Free 租户中的所有用户都可以通过使用安全性默认值来使用 Azure 多重身份验证。 默认情况下，这些安全默认为所有用户启用 Azure 多重身份验证，每次登录时都是如此。 移动身份验证应用是唯一可用于在使用 Azure AD Free 安全默认值时进行 Azure 多重身份验证的方法。

* [详细了解 Azure AD 安全默认值](../fundamentals/concept-fundamentals-security-defaults.md)
* [为 Azure AD Free 中的用户启用安全默认值](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

如果你不想为所有用户和每个登录事件启用 Azure 多重身份验证，则可以改为仅选择使用*Azure AD 全局管理员*角色保护用户帐户。 此方法提供对关键管理员帐户的其他身份验证提示。 可以通过以下方式之一启用 Azure 多重身份验证，具体取决于所使用的帐户类型：

* 如果使用 Microsoft 帐户，请[注册多重身份验证](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)。
* 如果不使用 Microsoft 帐户，请[在 Azure AD 中为用户或组启用多重身份验证](howto-mfa-userstates.md)。

## <a name="next-steps"></a>后续步骤

有关成本的详细信息，请参阅[Azure 多重身份验证定价](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。
