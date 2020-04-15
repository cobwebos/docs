---
title: Azure 多因素身份验证提供程序 - Azure 活动目录
description: 何时应使用 Azure MFA 的身份验证提供程序？
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf50a8f58978a010fe3d8228ace8579fcf52eb38
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309904"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>何时使用 Azure 多重身份验证提供程序

> [!IMPORTANT]
> 自 2018 年 9 月 1 日起，可能无法再创建新的身份验证提供程序。 现有身份验证提供程序可以继续使用和更新，但迁移不再可能。 多重身份验证将继续以 Azure AD Premium 许可证中的功能的形式提供。

双重验证对于具有 Azure Active Directory 和 Office 365 用户的全局管理员默认可用。 但是，如果想充分利用[高级功能](howto-mfa-mfasettings.md)，则应购买完整版的 Azure 多重身份验证 (MFA)。

使用 Azure 多重身份验证提供程序以便利用 Azure 多重身份验证为**没有许可证的用户**提供的功能。

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>与 Azure MFA SDK 相关的注意事项

请注意，该 SDK 已弃用，并将仅继续工作到 2018 年 11 月 14 日。 之后将无法再调用该 SDK。

## <a name="what-is-an-mfa-provider"></a>什么是 MFA 提供程序？

有两种类型的身份验证提供程序，区别主要在于 Azure 订阅的收费方式。 基于身份验证的选项会计算一个月内对租户执行的身份验证次数。 此选项最适用于仅偶尔对大量用户进行身份验证的情况。 基于用户的选项会计算租户中一个月内执行双重验证的用户数量。 此选项最适用于部分用户有许可证，但需要为超出许可限制的更多用户扩展 MFA 的情况。

## <a name="manage-your-mfa-provider"></a>管理 MFA 提供程序

创建 MFA 提供程序以后，不能更改使用模型（按启用的用户或按身份验证）。

如果购买了足够多的许可证，可以覆盖启用 MFA 的所有用户，则可以删除整个 MFA 提供程序。

如果 MFA 提供程序未与 Azure AD 租户相关联，或者你将新的 MFA 提供程序与其他 Azure AD 租户相关联，则用户设置和配置选项不会进行转移。 此外，需要使用通过 MFA 提供程序生成的激活凭据，重新激活现有 Azure MFA 服务器。

### <a name="removing-an-authentication-provider"></a>删除身份验证提供程序

> [!CAUTION]
> 删除身份验证提供程序时没有确认。 选择 **"删除**"是一个永久过程。

身份验证提供程序可以在**Azure 门户** > **Azure 活动目录** > **安全** > **MFA** > **提供程序中找到**。 单击列出的提供程序以查看与该提供程序关联的详细信息和配置。

在删除身份验证提供程序之前，请注意提供程序中配置的任何自定义设置。 决定需要从提供商迁移到常规 MFA 设置的设置，并完成这些设置的迁移。 

连接到提供程序的 Azure MFA 服务器需要使用**Azure 门户** > **Azure 活动目录** > **安全** > **MFA** > **服务器设置**下生成的凭据重新激活。 在重新激活之前，必须从环境中 Azure MFA`\Program Files\Multi-Factor Authentication Server\Data\`服务器上的目录中删除以下文件：

- 卡塞尔特
- cert
- 组CACert
- 组键
- groupName
- 许可证密钥
- 皮基

![从 Azure 门户中删除身份验证提供程序](./media/concept-mfa-authprovider/authentication-provider-removal.png)

确认已迁移所有设置后，可以浏览到 Azure**门户** > **Azure 活动目录** > **安全** >  **Delete****MFA** > **提供程序**并选择省略号 **...**

> [!WARNING]
> 删除身份验证提供程序将删除与该提供程序关联的任何报告信息。 您可能需要在删除提供程序之前保存活动报告。

> [!NOTE]
> 具有旧版本的 Microsoft 身份验证器应用和 Azure MFA 服务器的用户可能需要重新注册其应用。

## <a name="next-steps"></a>后续步骤

[配置多重身份验证设置](howto-mfa-mfasettings.md)
