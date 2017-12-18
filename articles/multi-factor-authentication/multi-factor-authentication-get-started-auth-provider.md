---
title: "Azure 多重身份验证提供程序 | Microsoft Docs"
description: "了解如何创建 Azure 多重身份验证提供程序。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/08/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: b04fd6f969461cf39016df951007c59047c8857a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="getting-started-with-an-azure-multi-factor-authentication-provider"></a>Azure 多重身份验证提供程序入门
双重验证对于具有 Azure Active Directory 和 Office 365 用户的全局管理员默认可用。 但是，如果想充分利用[高级功能](multi-factor-authentication-whats-next.md)，则应购买完整版的 Azure 多重身份验证 (MFA)。

Azure 多重身份验证提供程序用于利用 Azure MFA 完整版提供的功能。 它适用于没有通过 Azure MFA、Azure AD Premium 或企业移动性 + 安全性 (EMS) 获得许可证的用户。  默认情况下，Azure MFA、Azure AD Premium 和 EMS 包括 Azure MFA 完整版。 若有许可证，则无需 Azure 多重身份验证提供程序。

需要 Azure 多重身份验证提供程序才能下载 SDK。

> [!IMPORTANT]
> 已宣布弃用 Azure 多重身份验证软件开发工具包 (SDK)。 对新客户不再支持此功能。 当前客户可以继续使用 SDK 到 2018 年 11 月 14 日。 之后将无法再调用该 SDK。

> [!IMPORTANT]
>即使拥有 Azure MFA、AAD 高级版或 EMS 许可证，若要下载 SDK，也需要创建 Azure 多重身份验证提供程序。  如果处于此目的而创建 Azure 多重身份验证提供程序且已拥有许可证，请务必使用**按启用的用户**模型创建提供程序。 然后，将该提供程序链接到包含 Azure MFA、Azure AD Premium 或 EMS 许可证的目录。 该配置将确保仅当执行双重验证的唯一用户数大于所拥有的许可证数时才进行计费。 

## <a name="what-is-an-mfa-provider"></a>什么是 MFA 提供程序？

如果不具有 Azure 多重身份验证的许可证，则可以创建身份验证提供程序要求用户进行双重验证。

有两种身份验证提供程序，区别主要在于 Azure 订阅的收费方式。 基于身份验证的选项会计算一个月内对租户执行的身份验证次数。 此选项最适用于仅偶尔对大量用户进行身份验证的情况。 基于用户的选项会计算租户中一个月内执行双重验证的用户数量。 此选项最适用于部分用户有许可证，但需要为超出许可限制的更多用户扩展 MFA 的情况。

## <a name="create-an-mfa-provider"></a>创建 MFA 提供程序

在 Azure 门户中使用以下步骤创建 Azure 多重身份验证提供程序：

1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。 
2. 选择“Azure Active Directory” > “MFA 服务器” > “提供程序”。

   ![提供程序][Providers]

3. 选择“添加”。
4. 填充以下字段，然后选择“添加”：
   - **名称** – 提供程序的名称。
   - **使用模型** - 可选择以下两个选项之一：
      * 按身份验证 - 购买按身份验证收费的模型。 通常用于在面向使用者的应用程序中使用 Azure 多重身份验证的方案。
      * 按启用的用户 - 购买按每个启用的用户收费的模型。 通常用于员工访问 Office 365 等应用程序的方案。 若有已拥有 Azure MFA 许可证的用户，请选择此选项。
   - **订阅** – Azure 订阅，通过提供程序对其收取双重验证活动费用。 
   - **目录** – 与提供程序关联的 Azure Active Directory 租户。
      * 无需 Azure AD 目录即可创建提供程序。 如果仅计划下载 Azure 多重身份验证服务器，请将此框留空。
      * 提供程序必须与 Azure AD 目录相关联才能利用高级功能。
      * 只能将一个提供程序与任意一个 Azure AD 目录相关联。

## <a name="manage-your-mfa-provider"></a>管理 MFA 提供程序

创建 MFA 提供程序以后，不能更改使用模型（按启用的用户或按身份验证）。 不过，可以删除 MFA 提供程序，再创建采用不同使用模型的新提供程序。

如果当前多重身份验证提供程序已与 Azure AD 目录（也称 Azure AD 租户）相关联，可以安全地删除该 MFA 提供程序，再创建与同一 Azure AD 租户关联的新 MFA 提供程序。 或者，如果购买了足够多的 MFA、Azure AD Premium 或企业移动性 + 安全性 (EMS) 许可证，可以覆盖启用 MFA 的所有用户，则可以删除整个 MFA 提供程序。

如果 MFA 提供程序未与 Azure AD 租户相关联，或者你将新的 MFA 提供程序与其他 Azure AD 租户相关联，则用户设置和配置选项不会进行转移。 此外，需要使用通过新 MFA 提供程序生成的激活凭据，重新激活现有 Azure MFA 服务器。 重新激活 MFA 服务器并将其链接到新 MFA 提供程序不会影响电话和短信身份验证，但所有用户不再会收到移动应用通知，除非他们重新激活移动应用。

## <a name="next-steps"></a>后续步骤

[配置多重身份验证设置](multi-factor-authentication-whats-next.md)

[Providers]: ./media/multi-factor-authentication-get-started-auth-provider/add-providers.png "添加 MFA 提供程序"
