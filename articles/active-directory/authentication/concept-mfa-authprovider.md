---
title: Azure 多重身份验证提供程序-Azure Active Directory
description: 何时应使用 Azure MFA 的身份验证提供程序？
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d4b89f7416847e01cad8cb4f9bc52248d09170d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382001"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>何时使用 Azure 多重身份验证提供程序

双重验证对于具有 Azure Active Directory 和 Office 365 用户的全局管理员默认可用。 但是，如果想充分利用[高级功能](howto-mfa-mfasettings.md)，则应购买完整版的 Azure 多重身份验证 (MFA)。

使用 Azure 多重身份验证提供程序以便利用 Azure 多重身份验证为**没有许可证的用户**提供的功能。

> [!NOTE]
> 自 2018 年 9 月 1 日起，可能无法再创建新的身份验证提供程序。 现有的身份验证提供程序可能继续使用和更新，但不可能再进行迁移。 多重身份验证将继续以 Azure AD Premium 许可证中的功能的形式提供。

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>与 Azure MFA SDK 相关的注意事项

请注意，该 SDK 已弃用，并将仅继续工作到 2018 年 11 月 14 日。 之后将无法再调用该 SDK。

## <a name="what-is-an-mfa-provider"></a>什么是 MFA 提供程序？

有两种类型的身份验证提供程序，区别主要在于 Azure 订阅的收费方式。 基于身份验证的选项会计算一个月内对租户执行的身份验证次数。 此选项最适用于仅偶尔对大量用户进行身份验证的情况。 基于用户的选项会计算租户中一个月内执行双重验证的用户数量。 此选项最适用于部分用户有许可证，但需要为超出许可限制的更多用户扩展 MFA 的情况。

## <a name="manage-your-mfa-provider"></a>管理 MFA 提供程序

创建 MFA 提供程序以后，不能更改使用模型（按启用的用户或按身份验证）。

如果购买了足够多的许可证，可以覆盖启用 MFA 的所有用户，则可以删除整个 MFA 提供程序。

如果 MFA 提供程序未与 Azure AD 租户相关联，或者你将新的 MFA 提供程序与其他 Azure AD 租户相关联，则用户设置和配置选项不会进行转移。 此外，需要使用通过 MFA 提供程序生成的激活凭据，重新激活现有 Azure MFA 服务器。 重新激活 MFA 服务器并将其链接到 MFA 提供程序不会影响电话和短信身份验证，但所有用户将不再会收到移动应用通知，除非他们重新激活移动应用。

### <a name="removing-an-authentication-provider"></a>删除身份验证提供程序

> [!CAUTION]
> 删除身份验证提供程序时，不会出现任何确认。 选择 "**删除**" 是永久性过程。

可在**Azure 门户** > **Azure Active Directory** > **MFA** > **提供程序**中找到身份验证提供程序。 单击列出的提供程序，查看与该提供程序关联的详细信息和配置。

在删除身份验证提供程序之前，请记下在提供程序中配置的任何自定义设置。 确定需要从提供商迁移到常规 MFA 设置的设置，并完成这些设置的迁移。 

链接到提供程序的 Azure MFA 服务器需要使用**Azure 门户** > **Azure Active Directory** > **MFA** > **服务器设置**中生成的凭据重新激活。 在重新激活之前，必须从你的环境中的 Azure MFA 服务器上的 `\Program Files\Multi-Factor Authentication Server\Data\` 目录中删除以下文件：

- caCert
- cert
- groupCACert
- groupKey
- groupName
- licenseKey
- pkey

![从 Azure 门户删除身份验证提供程序](./media/concept-mfa-authprovider/authentication-provider-removal.png)

确认所有设置都已迁移后，你可以浏览到**Azure 门户** > **Azure Active Directory** > **MFA** > **提供程序**，然后选择 "省略号 **...** " 并选择 "**删除**"。

> [!WARNING]
> 删除身份验证提供程序将删除与该提供程序关联的任何报表信息。 你可能需要在删除提供程序之前保存活动报告。

> [!NOTE]
> 具有较旧版本 Microsoft Authenticator 应用和 Azure MFA 服务器的用户可能需要重新注册其应用。

## <a name="next-steps"></a>后续步骤

[配置多重身份验证设置](howto-mfa-mfasettings.md)
