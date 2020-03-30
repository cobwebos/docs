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
ms.openlocfilehash: e74a7ab0c003aaf9d90211484b39f8322cd9c329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647996"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Azure 多重身份验证的功能和许可证

若要保护组织中的用户帐户，应使用多重身份验证。 对资源拥有访问特权的帐户尤其需要此功能。 基本多重身份验证功能适用于 Office 365 和 Azure Active Directory (Azure AD) 管理员，不收取额外的费用。 如果想要升级管理员的功能，或者将多重身份验证扩展到其他用户，可通过多种方式购买 Azure 多重身份验证。

> [!IMPORTANT]
> 本文详细说明 Azure 多重身份验证的不同许可和使用方式。 有关定价和计费的具体详细信息，请参阅[ Azure 多重身份验证定价页](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。

## <a name="available-versions-of-azure-multi-factor-authentication"></a>可用的 Azure 多重身份验证版本

可以根据组织的需求，以几种不同的方式使用和许可 Azure 多重身份验证。 根据你当前拥有的 Azure AD、Office 365、EMS 或 Microsoft 365 许可证，你可能已有权使用 Azure 多重身份验证。 下表详细说明了获取 Azure 多重身份验证和某些功能的不同方式，以及每种方式的用例。

| 如果你是以下产品的用户 | 功能和用例 |
| --- | --- |
| EMS 或 Microsoft 365 E3 和 E5 | EMS E3 或 Microsoft 365 E3（包括 EMS 和 Office 365）包括 Azure AD Premium P1。 EMS E5 或 Microsoft 365 E5 包括 Azure AD Premium P2。 可使用以下部分所述的相同条件访问功能向用户提供多重身份验证。 |
| Azure AD Premium P1 | 可以使用[Azure AD 条件访问](../conditional-access/overview.md)提示用户在某些方案或事件期间进行多重身份验证，以满足您的业务需求。 |
| Azure AD Premium P2 | 提供最强的安全形势和改进的用户体验。 添加[基于风险的条件访问](../conditional-access/howto-conditional-access-policy-risk.md)Azure AD 高级 P1 功能，这些功能可适应用户的模式并最大限度地减少多重身份验证提示。 |
| Office 365 业务高级版、E3 或 E5 | 针对所有用户的所有登录事件启用或禁用 Azure 多重身份验证。 不能只为一部分用户或者只是在特定的方案中启用多重身份验证。 通过 Office 365 门户进行管理。 若要改进用户体验，请升级到 Azure AD Premium P1 或 P2 并使用条件访问。 有关详细信息，请参阅[使用多重身份验证保护 Office 365 资源](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)。 |
| Azure AD 免费版 | 每次发出身份验证请求时，都可以使用[安全默认值](../fundamentals/concept-fundamentals-security-defaults.md)为所有用户启用多重身份验证。 无法精细控制已启用多重身份验证的用户或方案，但此版本确实提供附加的安全措施。<br /> 即使不使用安全默认值来为每个人启用多重身份验证，也可以将分配有“Azure AD 全局管理员”角色的用户配置为使用多重身份验证。** 此免费层功能确保关键的管理员帐户受到多重身份验证的保护。 |

## <a name="feature-comparison-of-versions"></a>版本功能比较

下表提供了 Azure 多重身份验证的各个版本中可用的功能列表。 规划保护用户身份验证的需求，然后确定哪种方法符合这些要求。 例如，尽管 Azure AD Free 提供安全默认值来实现 Azure 多重身份验证，但只能通过手机验证器应用显示身份验证提示，而不能通过电话呼叫或短信来显示。 如果无法确保将手机身份验证应用安装到用户的个人设备上，此方法可能存在限制。

| Feature | Azure AD Free - 安全默认值 | Azure AD Free - Azure AD 全局管理员 | Office 365 业务高级版、E3 或 E5 | Azure AD Premium P1 或 P2 |
| --- |:---:|:---:|:---:|:---:|
| 使用 MFA 保护 Azure AD 租户管理员帐户 | ● | * （仅限*Azure AD 全局管理员*帐户） | ● | ● |
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
> 从 2019 年 3 月起，电话呼叫选项不再适用于 Azure AD Free/试用版租户中的 Azure 多重身份验证和 Azure 自助式密码重置用户。 短信不受此项更改的影响。 电话呼叫继续可供 Azure AD 高级 P1 或 P2 租户或使用或 Office 365 业务高级版、E3 或 E5 中的用户使用。

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>购买和启用 Azure 多重身份验证

若要使用 Azure 多重身份验证，请注册或购买符合条件的 Azure AD 层。 Azure AD 有四个版本 - 免费、Office 365 应用版本（适用于 Office 365 业务高级 E3 或 E5 客户）、高级 P1 和高级 P2。

Free 版本随附在 Azure 订阅中。 有关如何使用安全默认值或者使用“Azure AD 全局管理员”角色保护帐户的信息，请参阅[下面的部分](#azure-ad-free-tier)。**

Azure AD Premium 版本通过 Microsoft 代表、[开放批量许可计划](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)和[云解决方案提供商计划](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409)提供。 Azure 和 Office 365 订阅者还可以在线购买 Azure Active Directory Premium P1 和 P2。 [登录](https://portal.office.com/Commerce/Catalog.aspx)购买。

> [!IMPORTANT]
> 从 2018 年 9 月 1 日起，新客户不再可以使用基于消耗量的许可。 使用基于消耗量的模型的现有客户可以继续使用按启用用户或按身份验证的计费。

购买所需的 Azure AD 层后，请[规划并部署 Azure 多重身份验证](howto-mfa-getstarted.md)。

### <a name="azure-ad-free-tier"></a>Azure AD Free 层

Azure AD Free 租户中的所有用户都可以通过安全默认值来使用 Azure 多重身份验证。 每次用户登录时，这些安全默认值将为这些用户启用 Azure 多重身份验证。 使用 Azure AD Free 安全默认值时，只能使用手机身份验证应用来完成 Azure 多重身份验证。

* [了解有关 Azure AD 安全默认值的更多](../fundamentals/concept-fundamentals-security-defaults.md)
* [为 Azure AD 免费中的用户启用安全默认值](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

如果你不想要为所有用户和每个登录事件启用 Azure 多重身份验证，可以选择仅使用“Azure AD 全局管理员”角色来保护用户帐户。** 此方法针对关键的管理员帐户提供更多的身份验证提示。 您可以根据所使用的帐户类型，以以下方式之一启用 Azure 多重身份验证：

* 如果您使用 Microsoft 帐户，[请注册进行多重身份验证](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)。
* 如果不使用 Microsoft 帐户，[请为 Azure AD 中的用户或组打开多重身份验证](howto-mfa-userstates.md)。

## <a name="next-steps"></a>后续步骤

有关成本的详细信息，请参阅 [Azure 多重身份验证定价](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。
