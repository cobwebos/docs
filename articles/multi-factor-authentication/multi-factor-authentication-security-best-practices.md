---
title: "MFA 安全最佳实践 | Microsoft 文档"
description: "本文档提供有关配合使用 Azure MFA 与 Azure 帐户的最佳实践"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 3be7d968-96bb-4320-8701-869fd04a2595
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 2be36bce1b4cffdab2d25d150bd5a0e8451e422d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>将 Azure 多重身份验证与 Azure AD 帐户配合使用时的安全最佳实践

对于希望增强其身份验证过程的大多数组织来说，双重验证是其首选。 Azure 多重身份验证 (MFA) 可帮助公司满足其安全和法规遵从要求，同时为用户提供简单的登录体验。 本文介绍了计划采用 Azure MFA 时应考虑的一些提示。

## <a name="deploy-azure-mfa-in-the-cloud"></a>在云中部署 Azure MFA

可通过两种方法为所有用户启用 Azure MFA。

* 为每个用户购买许可证（Azure MFA、Azure AD Premium 或企业移动性 + 安全性）
* 创建一个多重身份验证提供程序并按用户或按身份验证付费

### <a name="licenses"></a>许可证
![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

如果拥有 Azure AD Premium 或企业移动性 + 安全性许可证，则已拥有了 Azure MFA。 组织无需使用任何其他组件便可将双重验证功能扩展到所有用户。 只需将许可证分配给用户，然后便可以启用 MFA。

设置多重身份验证时，请注意以下提示：

* 不要创建按身份验证的多重身份验证提供程序。 否则，最终可能要为已有许可证的用户的验证请求付费。
* 如果没有足够的许可证供所有用户使用，则可以创建按用户的多重身份验证提供程序来涵盖组织的其余人员。 
* 仅当要将本地 Active Directory 环境与 Azure AD 目录同步时，才需要 Azure AD Connect。 如果使用不与 Active Directory 的本地实例同步的 Azure AD 目录，则不需要 Azure AD Connect。

### <a name="multi-factor-auth-provider"></a>Multi-Factor Auth 提供程序
![Multi-Factor Auth 提供程序](./media/multi-factor-authentication-security-best-practices/authprovider.png)

如果没有包含 Azure MFA 的许可证，可以创建 MFA 身份验证提供程序。 

创建身份验证提供程序时，需要选择一个目录并考虑以下详细信息：

* 无需拥有 Azure AD 目录，即可创建多重身份验证提供程序；但如果有，可以使用更多功能。 将身份验证提供程序与 Azure AD 目录关联后会实现以下功能：  
  * 将双重验证功能扩展到所有用户  
  * 为全局管理员提供其他功能，如管理门户、自定义问候语和报告。
* 若要将本地 Active Directory 环境与 Azure AD 目录同步，需要有 DirSync 或 AAD Sync。如果使用不与 Active Directory 的本地实例同步的 Azure AD 目录，则不需要 DirSync 或 AAD 同步。
* 选择最适合业务的消耗模型。 使用模型一旦选择之后，就无法对其更改。 有以下两个模型：
  * 按身份验证：针对每次验证收费。 如果要对访问某些应用的任何人（而不是特定用户）进行双重验证，请使用此模型。
  * 基于启用的用户：对启用 Azure MFA 的每个用户收费。 如果拥有一些使用 Azure AD Premium 或企业移动性套件许可证的用户，请使用此模型。

### <a name="supportability"></a>可支持性
由于大多数用户习惯只使用密码进行身份验证，因此，公司必须让所有用户了解此过程。 如果用户熟悉该过程，则他们就不会在出现 MFA 相关的小问题时经常呼叫技术支持。 但是，在某些情况下，需要暂时禁用 MFA。 使用以下指导原则了解如何处理这种情况：

* 为技术支持人员提供培训，以便处理用户因为移动应用或电话未收到通知或来电而无法登录的情况。 技术支持团队可以[启用免验证一次](multi-factor-authentication-whats-next.md#one-time-bypass)，让用户通过“免除”双重验证的方式来进行身份验证，不过只会免除一次。 免验证是暂时性的，会在指定的秒数后过期。
* 可以考虑使用 Azure MFA 中的[受信任的 IP 功能](multi-factor-authentication-whats-next.md#trusted-ips)来尽量减少双重验证。 使用此功能，托管或联合租户的管理员可以对从公司本地 Intranet 登录的用户免除双重验证。 这些功能适用于拥有 Azure AD Premium、Enterprise Mobility Suite 或 Azure 多重身份验证许可证的 Azure AD 租户。

## <a name="best-practices-for-an-on-premises-deployment"></a>本地部署的最佳做法
如果公司决定利用自己的基础结构来启用 MFA，则需要在本地部署 Azure 多重身份验证服务器。 下图显示了 MFA 服务器的组件：

![默认 MFA 服务器组件：控制台、同步引擎、管理门户、云服务](./media/multi-factor-authentication-security-best-practices/server.png) \*默认未安装 \**默认安装但未启用

Azure 多重身份验证服务器可以使用联合身份验证来保护云资源和本地资源。 必须安装 AD FS，并将它与 Azure AD 租户联合。
设置多重身份验证服务器时，请注意以下详细信息：

* 如果使用 Active Directory 联合身份验证服务 (AD FS) 来保护 Azure AD 资源，则第一个验证步骤是使用 AD FS 在本地执行的。 遵循声明在本地执行第二步。
* 不需要在 AD FS 联合服务器上安装 Azure 多重身份验证服务器。 但是，必须在运行 AD FS 的 Windows Server 2012 R2 上安装用于 AD FS 的多重身份验证适配器。 可以将服务器安装在其他计算机上（只要它是受支持的版本），并将 AD FS 适配器单独安装在 AD FS 联合服务器上。 
* 多重身份验证 AD FS 适配器安装向导会在 Active Directory 中创建名为 PhoneFactor Admins 的安全组，然后将 AD FS 服务帐户添加到此组。 检查是否在域控制器上创建了 PhoneFactor Admins 组，以及 AD FS 服务帐户是否是此组的成员。 需要时，在域控制器上手动将 AD FS 服务帐户添加到 PhoneFactor 管理员组。

### <a name="user-portal"></a>用户门户
用户门户提供自助服务功能，以及一套完整的用户管理功能。 它在 Internet Information Server (IIS) 网站中运行。 请使用以下指导原则来配置此组件：

* 使用 IIS 6 或更高版本
* 安装并注册 ASP.NET v2.0.507207
* 确保此服务器可以部署在外围网络中

### <a name="app-passwords"></a>应用密码
如果组织通过 Azure AD 进行 SSO 联合验证，并且打算使用 Azure MFA，请注意以下详细信息：

* 应用密码由 Azure AD 进行验证，因此绕过了联合。 仅在设置应用密码时，才会使用联合。
* 对于联合 (SSO) 用户，密码存储在组织 ID 中。如果用户离开公司，相应信息必须通过 DirSync 流向组织 ID。 帐户禁用/删除可能需要长达三个小时才能同步，从而延迟了 Azure AD 中应用密码的禁用/删除。
* 应用密码不遵循“本地客户端访问控制”设置。
* 没有为应用密码提供本地身份验证日志记录/审核功能。
* 在对客户端使用双重验证时，某些先进的体系结构设计可能需要将组织用户名和密码与应用密码结合使用，具体取决于进行身份验证的位置。 对于针对本地基础结构进行身份验证的客户端，会使用组织用户名和密码。 对于针对 Azure AD 进行身份验证的客户端，会使用应用密码。
* 默认情况下，用户无法创建应用密码。 如果需要允许用户创建应用密码，请选择“允许用户创建应用密码来登录非浏览器应用程序”选项。

## <a name="additional-considerations"></a>其他注意事项
使用以下列表，了解将在本地部署的每个组件的其他注意事项和相关指导：

- 设置使用 [Active Directory 联合服务](multi-factor-authentication-get-started-adfs.md) 的多重身份验证
- 设置和配置使用 [RADIUS 身份验证](multi-factor-authentication-get-started-server-radius.md)的 Azure MFA 服务器。
- 设置和配置使用 [IIS 身份验证](multi-factor-authentication-get-started-server-iis.md)的 Azure MFA 服务器。
- 设置和配置使用 [Windows 身份验证](multi-factor-authentication-get-started-server-windows.md)的 Azure MFA 服务器。
- 设置和配置使用 [LDAP 身份验证](multi-factor-authentication-get-started-server-ldap.md)的 Azure MFA 服务器。
- 设置和配置[使用远程桌面网关的 Azure MFA 服务器和使用 RADIUS 的 Azure 多重身份验证服务器](multi-factor-authentication-get-started-server-rdg.md)。
- 设置和配置 Azure MFA 服务器与 [Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md) 之间的同步。
- [部署 Azure 多重身份验证服务器移动应用 Web 服务](multi-factor-authentication-get-started-server-webservice.md)。
- [包含 Azure 多重身份验证的高级 VPN 配置](multi-factor-authentication-advanced-vpn-configurations.md)（对于使用 LDAP 或 RADIUS 的 Cisco ASA、Citrix Netscaler 和 Juniper/Pulse Secure VPN 设备）。

## <a name="next-steps"></a>后续步骤
尽管本文重点介绍了 Azure MFA 的一些最佳实践，但其他一些资源也可以帮助你规划 MFA 的部署。 以下列表提供了在此过程中也许能够帮到一些重要文章：

* [Azure 多重身份验证中的报告](multi-factor-authentication-manage-reports.md)
* [双重验证注册体验](multi-factor-authentication-end-user-first-time.md)
* [Azure 多重身份验证常见问题](multi-factor-authentication-faq.md)

