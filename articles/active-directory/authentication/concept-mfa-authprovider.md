---
title: 何时以及如何使用 Azure 多重身份验证提供程序？
description: 何时应使用 Azure MFA 的身份验证提供程序？
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 8e77a33667bd6794f667348958e0edb9c6a8fb0d
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094971"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>何时使用 Azure 多重身份验证提供程序

双重验证对于具有 Azure Active Directory 和 Office 365 用户的全局管理员默认可用。 但是，如果想充分利用[高级功能](howto-mfa-mfasettings.md)，则应购买完整版的 Azure 多重身份验证 (MFA)。

使用 Azure 多重身份验证提供程序以便利用 Azure 多重身份验证为**没有许可证的用户**提供的功能。

如果你拥有涵盖组织中所有用户的许可证，则不需要 Azure 多重身份验证提供程序。 仅当你还需要为没有许可证的某些用户提供双重验证时，才需要创建 Azure 多重身份验证提供程序。

> [!NOTE]
> 自 2018 年 9 月 1 日起，可能无法再创建新的身份验证提供程序。 可以继续使用和更新现有的身份验证提供程序。 多重身份验证将继续成为 Azure AD Premium 许可证中的可用功能。

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>与 Azure MFA SDK 相关的注意事项

需要 Azure 多重身份验证提供程序才能下载 SDK。 请注意，该 SDK 已弃用，不再对新客户支持，并将仅继续工作到 2018 年 11 月 14 日。 之后将无法再调用该 SDK。

若要下载该 SDK，请创建 Azure 多重身份验证提供程序，即使拥有 Azure MFA、AAD Premium 或其他捆绑许可证，也是如此。 如果处于此目的而创建 Azure 多重身份验证提供程序且已拥有许可证，请务必使用**按启用的用户**模型创建提供程序。 然后，将该提供程序链接到包含 Azure MFA、Azure AD Premium 或其他捆绑许可证的目录。 该配置将确保仅当执行双重验证的唯一用户数大于所拥有的许可证数时才进行计费。

## <a name="what-is-an-mfa-provider"></a>什么是 MFA 提供程序？

有两种类型的身份验证提供程序，区别主要在于 Azure 订阅的收费方式。 基于身份验证的选项会计算一个月内对租户执行的身份验证次数。 此选项最适用于仅偶尔对大量用户进行身份验证的情况。 基于用户的选项会计算租户中一个月内执行双重验证的用户数量。 此选项最适用于部分用户有许可证，但需要为超出许可限制的更多用户扩展 MFA 的情况。

## <a name="manage-your-mfa-provider"></a>管理 MFA 提供程序

创建 MFA 提供程序以后，不能更改使用模型（按启用的用户或按身份验证）。

如果购买了足够多的许可证，可以覆盖启用 MFA 的所有用户，则可以删除整个 MFA 提供程序。

如果 MFA 提供程序未与 Azure AD 租户相关联，或者你将新的 MFA 提供程序与其他 Azure AD 租户相关联，则用户设置和配置选项不会进行转移。 此外，需要使用通过 MFA 提供程序生成的激活凭据，重新激活现有 Azure MFA 服务器。 重新激活 MFA 服务器并将其链接到 MFA 提供程序不会影响电话和短信身份验证，但所有用户将不再会收到移动应用通知，除非他们重新激活移动应用。

## <a name="next-steps"></a>后续步骤

[配置多重身份验证设置](howto-mfa-mfasettings.md)
