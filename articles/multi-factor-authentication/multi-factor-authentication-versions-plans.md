---
title: "Azure MFA 版本和使用计划 | Microsoft 文档"
description: "有关多重身份验证客户端以及可用的方法和版本的信息。 有关每个使用计划的详细信息"
keywords: 
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: joflore
ms.openlocfilehash: c7e880b6632e3a1456cf2fa535e6218d3bb31e4a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>如何获取 Azure 多重身份验证

如果想要保护帐户，应该在组织中标配双重验证。 对资源拥有访问特权的管理帐户尤其需要此功能。 为此，Microsoft 为 Office 365 和 Azure 管理员提供了基本的双重验证功能，无需额外费用。 如果想要升级管理员的功能，或者将双重验证扩展到其他用户，可以购买 Azure 多重身份验证。 

本文介绍提供给管理员的版本与完整 Azure MFA 版本之间的差别。 如果已准备好部署完整的 Azure MFA 产品，可在后续部分中了解实施选项，以及 Microsoft 如何计算使用量。


>[!IMPORTANT]
>本文旨在指导用户如何以不同的方式购买 Azure 多重身份验证。 有关定价和计费的具体详细信息，请始终参阅[多重身份验证定价页](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。

## <a name="available-versions-of-azure-multi-factor-authentication"></a>可用的 Azure 多重身份验证版本

下表介绍了多重身份验证的三个版本之间的差别：

| 版本 | 说明 |
| --- | --- |
| 适用于 Office 365 的多重身份验证 |此版本专门与 Office 365 应用程序配合使用，可以从 Office 365 门户进行管理。 管理员可以[使用双重验证来保护 Office 365 资源](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)。 此版本是 Office 365 订阅的一部分。 |
| 面向 Azure 管理员的多重身份验证 | Azure 租户的全局管理员可以免费为其全局管理员帐户启用双重验证。|
| Azure 多重身份验证 | Azure 多重身份验证（通常称为“完整”版本）提供了最丰富的功能集。 它通过 [Azure 经典门户](https://manage.windowsazure.com)、高级报告及支持一系列本地和云应用程序来提供其他配置选项。 Azure 多重身份验证已随附在 [Azure Active Directory Premium 计划](https://www.microsoft.com/cloud-platform/azure-active-directory-features)和[企业移动性 + 安全性计划](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing)中，可在云中或本地部署。 |

## <a name="feature-comparison-of-versions"></a>版本功能比较
下表提供了 Azure 多重身份验证的各个版本中可用的功能列表。

> [!NOTE]
> 此比较表讨论了每个版本的多重身份验证的部分功能。 如果拥有完整的 Azure 多重身份验证服务，某些功能可能不可用，具体取决于是否在云中使用 [MFA 或本地 MFA](multi-factor-authentication-get-started.md)。


| 功能 | 适用于 Office 365 的多重身份验证 | 面向 Azure 管理员的多重身份验证 | Azure 多重身份验证 |
| --- |:---:|:---:|:---:|
| 使用 MFA 保护管理员帐户 |● |●（仅适用于全局管理员帐户） |● |
| 将移动应用用作第二个因素 |● |● |● |
| 将电话呼叫用作第二个因素 |● |● |● |
| 将短信用作第二个因素 |● |● |● |
| 不支持 MFA 的客户端的应用密码 |● |● |● |
| 管理员控制验证方法 |● |● |● |
| 使用 MFA 保护非管理员帐户 |●（仅适用于 Office 365 应用程序） | |● |
| PIN 模式 | | |● |
| 欺诈警报 | | |● |
| MFA 报告 | | |● |
| 一次性跳过 | | |● |
| 通话的自定义问候语 | | |● |
| 通话的自定义呼叫方 ID | | |● |
| 受信任的 IP | | |● |
| 记住受信任的设备的 MFA |● |● |● |
| MFA SDK | | |●（已弃用） | 
| 适用于本地应用程序的 MFA | | |● |

## <a name="how-to-get-azure-multi-factor-authentication"></a>如何获取 Azure 多重身份验证
如果想要获取 Azure 多重身份验证提供的完整功能，可通过多种方法实现：

### <a name="option-1---mfa-licenses"></a>选项 1 - MFA 许可证

购买 Azure 多重身份验证许可证并将其分配给 Azure Active Directory 中的用户。 

如果使用此选项，则仅当需要向没有许可证的某些用户提供双重验证时，才创建 Azure 多重身份验证提供程序。 否则，可能会向你计费两次。

### <a name="option-2---bundled-licenses-that-include-mfa"></a>选项 2 - 包含 MFA 的捆绑许可证

购买包含 Azure 多重身份验证的许可证（例如 Azure Active Directory Premium 或企业移动性 + 安全性），并将其分配给 Azure Active Directory 中的用户。 

如果使用此选项，则仅当需要向没有许可证的某些用户提供双重验证时，才要创建 Azure 多重身份验证提供程序。 否则，可能会向你计费两次。 

### <a name="option-3---mfa-consumption-based-model"></a>选项 3 - 基于 MFA 使用量的模型

在 Azure 订阅中创建 Azure 多重身份验证提供程序。 与其他所有 Azure 资源一样，Azure MFA 提供程序是根据企业协议、Azure 资金承诺或信用卡收费的 Azure 资源。 只能在完整 Azure 订阅中创建这些提供程序，而不能在支出限制为 $0 的受限 Azure 订阅中创建。 与选项 1 和 2 一样，受限的订阅是在激活许可证时创建的。 

使用 Azure 多重身份验证提供程序时，有两种使用模式（通过 Azure 订阅计费）可用：  

1. **按启用的用户** - 适用于想要为人数固定、需要定期进行身份验证的员工启用双重验证的企业。 按用户计费基于 Azure AD 租户和 Azure MFA 服务器中启用了 MFA 的用户数。 如果同时在 Azure AD 和 Azure MFA 服务器中为用户启用 MFA 并启用域同步 (Azure AD Connect)，我们会根据更多的用户计费。 如果未启用域同步，我们会根据 Azure AD 和 Azure MFA 服务器中启用 MFA 的所有用户总人数计费。 费用按比例计算，每日在商务系统中报告。 

  > [!NOTE]
  > 计费示例 1：今天你为 5,000 个用户启用了 MFA。 MFA 系统将该数字除以 31，报告当日有 161.29 个用户产生了费用。 明天要为另外 15 个用户启用 MFA，到时 MFA 系统将报告该日期有 161.77 个用户产生费用。 在计费周期结束时，根据 Azure 订阅计费的用户总数累计为大约 5,000。 
  >
  > 计费示例 2：某些用户持有许可证，而有些用户则没有，因此，使用了按用户计费的 Azure MFA 提供程序来区分这些用户。 租户中有 4,500 个企业移动性 + 安全性许可证，但有 5,000 个用户启用了 MFA。 在这种情况下，将按比例针对 500 个用户计收 Azure 订阅费用，每日报告 16.13 个用户的费用。 

2. **按身份验证** - 适用于想要为不定期需要身份验证的大量用户启用双重验证的企业。 费用会根据双重验证请求数计算，不管这些验证是成功还是被拒绝。 此笔费用以 10 次身份验证为一组显示在 Azure 用量结算单中，并且每日报告。 

  > [!NOTE]
  > 计费示例 3：Azure MFA 服务今天收到了 3,105 个双重验证请求。 将按 310.5 次身份验证计收 Azure 订阅费用。 

请务必注意，即使有 Azure MFA 许可证，也仍要基于使用量支付配置费用。 如果设置了按身份验证计费的 Azure MFA 提供程序，需要支付每次双重验证请求的费用，即使这些请求是持有许可证的用户发出的。 如果在未链接到 Azure AD 租户的域中设置了按用户计费的 Azure MFA 提供程序，则需要为每个启用 MFA 的用户付费，即使这些用户在 Azure AD 中持有许可证。 

## <a name="next-steps"></a>后续步骤

- 有关定价详细信息，请参阅 [Azure MFA 定价](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。

- 选择是要将 Azure MFA 部署[在云中还是本地](multi-factor-authentication-get-started.md)
