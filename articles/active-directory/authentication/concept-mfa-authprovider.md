---
title: Azure Multi-Factor Auth Providers - Azure Active Directory
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
> 自 2018 年 9 月 1 日起，可能无法再创建新的身份验证提供程序。 Existing auth providers may continue to be used and updated, but migration is no longer possible. 多重身份验证将继续以 Azure AD Premium 许可证中的功能的形式提供。

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>与 Azure MFA SDK 相关的注意事项

请注意，该 SDK 已弃用，并将仅继续工作到 2018 年 11 月 14 日。 之后将无法再调用该 SDK。

## <a name="what-is-an-mfa-provider"></a>什么是 MFA 提供程序？

有两种类型的身份验证提供程序，区别主要在于 Azure 订阅的收费方式。 基于身份验证的选项会计算一个月内对租户执行的身份验证次数。 此选项最适用于仅偶尔对大量用户进行身份验证的情况。 基于用户的选项会计算租户中一个月内执行双重验证的用户数量。 此选项最适用于部分用户有许可证，但需要为超出许可限制的更多用户扩展 MFA 的情况。

## <a name="manage-your-mfa-provider"></a>管理 MFA 提供程序

创建 MFA 提供程序以后，不能更改使用模型（按启用的用户或按身份验证）。

如果购买了足够多的许可证，可以覆盖启用 MFA 的所有用户，则可以删除整个 MFA 提供程序。

如果 MFA 提供程序未与 Azure AD 租户相关联，或者你将新的 MFA 提供程序与其他 Azure AD 租户相关联，则用户设置和配置选项不会进行转移。 此外，需要使用通过 MFA 提供程序生成的激活凭据，重新激活现有 Azure MFA 服务器。 重新激活 MFA 服务器并将其链接到 MFA 提供程序不会影响电话和短信身份验证，但所有用户将不再会收到移动应用通知，除非他们重新激活移动应用。

### <a name="removing-an-authentication-provider"></a>Removing an authentication provider

> [!CAUTION]
> There is no confirmation when deleting an authentication provider. Selecting **Delete** is a permanent process.

Authentication providers can be found in the **Azure portal** > **Azure Active Directory** > **MFA** > **Providers**. Click on listed providers to see details and configurations associated with that provider.

Before removing an authentication provider, take note of any customized settings configured in your provider. Decide what settings need to be migrated to general MFA settings from your provider and complete the migration of those settings. 

Azure MFA Servers linked to providers will need to be reactivated using credentials generated under **Azure portal** > **Azure Active Directory** > **MFA** > **Server settings**. Before reactivating, the following files must be deleted from the `\Program Files\Multi-Factor Authentication Server\Data\` directory on Azure MFA Servers in your environment:

- caCert
- cert
- groupCACert
- groupKey
- groupName
- licenseKey
- pkey

![Delete an auth provider from the Azure portal](./media/concept-mfa-authprovider/authentication-provider-removal.png)

When you have confirmed that all settings have been migrated, you can browse to the **Azure portal** > **Azure Active Directory** > **MFA** > **Providers** and select the ellipses **...** and select **Delete**.

> [!WARNING]
> Deleting an authentication provider will delete any reporting information associated with that provider. You may want to save activity reports before deleting your provider.

> [!NOTE]
> Users with older versions of the Microsoft Authenticator app and Azure MFA Server may need to re-register their app.

## <a name="next-steps"></a>后续步骤

[配置多重身份验证设置](howto-mfa-mfasettings.md)
