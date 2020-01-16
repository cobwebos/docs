---
title: Azure MFA 版本和消耗计划-Azure Active Directory
description: 有关多重身份验证客户端以及可用的方法和版本的信息。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1528bffc613d2e8ab2c0150095d90791b649198a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979501"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>如何获取 Azure 多重身份验证

如果想要保护帐户，应该在组织中标配双重验证。 对资源拥有访问特权的帐户尤其需要此功能。 为此，Microsoft 为 Office 365 和 Azure Active Directory (Azure AD) 管理员提供了基本的双重验证功能，无需额外费用。 如果想要升级管理员的功能，或者将双重验证扩展到其他用户，可以用多种方式购买 Azure 多重身份验证。

> [!IMPORTANT]
> 本文旨在指导用户如何以不同的方式购买 Azure 多重身份验证。 有关定价和计费的具体详细信息，请始终参阅[多重身份验证定价页](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>可用的 Azure 多重身份验证版本

下表描述了多重身份验证的各个版本之间的差异：

| 版本 | Description |
| --- | --- |
| 免费选项 | 利用 Azure AD 的免费权益的客户可以使用[安全默认值](../fundamentals/concept-fundamentals-security-defaults.md)在其环境中启用多重身份验证。 |
| 针对 Office 365 的多重身份验证 | 此版本是从 Office 365 或 Microsoft 365 门户管理的。 管理员可以[使用双重验证来保护 Office 365 资源](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)。 此版本是 Office 365 订阅的一部分。 |
| 面向 Azure AD 管理员的多重身份验证 | Azure AD 租户中被分配了 Azure AD 全局管理员角色的用户可以免费启用双重验证。 |
| Azure 多重身份验证 | Azure 多重身份验证（通常称为“完整”版本）提供了最丰富的功能集。 它通过 [Azure 门户](https://portal.azure.com)、高级报告及支持一系列本地和云应用程序来提供其他配置选项。 Azure 多重身份验证是[Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features)和[Microsoft 365 商业版](https://www.microsoft.com/microsoft-365/business)的一项功能。 |

> [!NOTE]
> 自 2018 年 9 月 1 日起，新客户无法再将 Azure 多重身份验证作为独立产品进行购买。 多重身份验证将继续作为 Azure AD Premium 或 Microsoft 365 商业版许可证的功能提供。

## <a name="feature-comparison-of-versions"></a>版本功能比较

下表提供了 Azure 多重身份验证的各个版本中可用的功能列表。

> [!NOTE]
> 此比较表讨论了每个版本的多重身份验证的部分功能。 如果拥有完整的 Azure 多重身份验证服务，某些功能可能不可用，具体取决于是否在云中使用 [MFA 或本地 MFA](concept-mfa-whichversion.md)。
>

| 功能 | 针对 Office 365 的多重身份验证 | 面向 Azure AD 管理员的多重身份验证 | Azure 多重身份验证 | 安全默认值 |
| --- |:---:|:---:|:---:|:---:|
| 使用 MFA 保护 Azure AD 管理员帐户 |● |●（仅适用于 Azure AD 全局管理员帐户） |● |● |
| 将移动应用用作第二个因素 |● |● |● |● |
| 将电话呼叫用作第二个因素 |● |● |● |   |
| 将短信用作第二个因素 |● |● |● |   |
| 不支持 MFA 的客户端的应用密码 |● |● |● |   |
| 管理员控制验证方法 |● |● |● |   |
| 使用 MFA 保护非管理员帐户 |● | |● |● |
| PIN 模式 | | |● |   |
| 欺诈警报 | | |● |   |
| MFA 报告 | | |● |   |
| 一次性绕过 | | |● |   |
| 通话的自定义问候语 | | |● |   |
| 通话的自定义呼叫方 ID | | |● |   |
| 受信任的 IP | | |● |   |
| 记住受信任的设备的 MFA |● |● |● |   |
| 适用于本地应用程序的 MFA | | |● |   |

> [!IMPORTANT]
> 从2019年3月开始，将无法在免费/试用 Azure AD 租户中向 MFA 和 SSPR 用户提供电话呼叫选项。 SMS 消息不受此更改的影响。 电话呼叫将继续为付费 Azure AD 租户中的用户提供。 此更改仅影响免费/试用 Azure AD 租户。

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>如何为 Azure AD 管理员启用 Azure 多重身份验证

Azure AD 租户中被分配了全局管理员角色的用户可以免费为其 Azure AD 全局管理员帐户启用双重验证。 如果使用的是 Microsoft 帐户，则可以使用 Microsoft 帐户支持文章[关于双重验证](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)中的指南注册多重身份验证。 如果未使用 Microsoft 帐户，请使用文章[如何对用户或组要求双重验证](howto-mfa-userstates.md)中的指南为全局管理员启用多重身份验证。

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>如何购买 Azure 多重身份验证

购买包含 Azure 多重身份验证的许可证（如 Azure Active Directory Premium）或包含 Azure AD Premium 或条件性访问的许可证捆绑，并将其分配到 Azure Active Directory 中的用户。

### <a name="consumption-based-licensing"></a>基于消耗的许可

自2018年9月1日起，新客户不再能够使用基于消费的许可。

2018年9月1日生效，可能无法再创建新的身份验证提供程序。 可以继续使用和更新现有的身份验证提供程序。 多重身份验证将继续成为 Azure AD Premium 许可证中的可用功能。

使用 Azure 多重身份验证提供程序时，有两种使用模式（通过 Azure 订阅计费）可用：

1. **按启用的用户** - 适用于想要为人数固定、需要定期进行身份验证的员工启用双重验证的企业。 按用户计费基于 Azure AD 租户和 Azure MFA 服务器中启用了 MFA 的用户数。 如果同时在 Azure AD 和 Azure MFA 服务器中为用户启用 MFA 并启用域同步 (Azure AD Connect)，我们会根据更多的用户计费。 如果未启用域同步，我们会根据 Azure AD 和 Azure MFA 服务器中启用 MFA 的所有用户总人数计费。 费用按比例计算，每日在商务系统中报告。

   > [!NOTE]
   > 计费示例 1：今天你为 5,000 个用户启用了 MFA。 MFA 系统将该数字除以 31，报告当日有 161.29 个用户产生了费用。 明天要为另外 15 个用户启用 MFA，到时 MFA 系统将报告该日期有 161.77 个用户产生费用。 在计费周期结束时，根据 Azure 订阅计费的用户总数累计为大约 5,000。
   >
   > 计费示例 2：某些用户持有许可证，而有些用户则没有，因此，使用了按用户计费的 Azure MFA 提供程序来区分这些用户。 租户中有 4,500 个企业移动性 + 安全性许可证，但有 5,000 个用户启用了 MFA。 在这种情况下，将按比例针对 500 个用户计收 Azure 订阅费用，每日报告 16.13 个用户的费用。
   >

1. **按身份验证** - 适用于想要为不定期需要身份验证的大量用户启用双重验证的企业。 费用会根据双重验证请求数计算，不管这些验证是成功还是被拒绝。 此笔费用以 10 次身份验证为一组显示在 Azure 用量结算单中，并且每日报告。

   > [!NOTE]
   > 计费示例 3：Azure MFA 服务今天收到了 3,105 个双重验证请求。 将按 310.5 次身份验证计收 Azure 订阅费用。
   >

请务必注意，即使有许可证，也仍要基于使用量支付配置费用。 如果设置了每个身份验证 Azure MFA 提供程序，则将为每个双重验证请求付费，甚至会为拥有许可证的用户执行的请求付费。 如果在未链接到 Azure AD 租户的域中设置了按用户计费的 Azure MFA 提供程序，则需要为每个启用 MFA 的用户付费，即使这些用户在 Azure AD 中持有许可证。

## <a name="next-steps"></a>后续步骤

- 有关定价详细信息，请参阅 [Azure MFA 定价](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。
