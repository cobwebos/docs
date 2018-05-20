---
title: 将 Active Directory 连接到 Azure Active Directory | Microsoft Docs
description: Azure AD Connect 会将本地目录与 Azure Active Directory 集成。 这样，便可以为集成到 Azure AD 的 Office 365、Azure 和 SaaS 应用程序提供一个通用标识。
keywords: Azure AD Connect 介绍, Azure AD Connect 概述, 什么是 Azure AD Connect, 安装 active directory
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/19/2018
ms.author: billmath
ms.openlocfilehash: 73baef03f7b240e7bb33f3d5f827b1fc72c985f2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-your-on-premises-directories-with-azure-active-directory"></a>将本地目录与 Azure Active Directory 集成
Azure AD Connect 会将本地目录与 Azure Active Directory 集成。 这样，便可以为集成到 Azure AD 的 Office 365、Azure 和 SaaS 应用程序的用户提供一个通用标识。 本主题介绍计划、部署和操作步骤。 其中统合了与这些操作相关的主题的链接。

> [!IMPORTANT]
> [Azure AD Connect 是连接本地目录与 Azure AD 和 Office 365 的最佳方式。这是从 Windows Azure Active Directory Sync (DirSync) 或 Azure AD Sync 升级到 Azure AD Connect 的最佳时机，因为这些工具现已弃用，从 2017 年 4 月 13 日起不再受支持。](active-directory-aadconnect-dirsync-deprecated.md)  此外：



> 
> - 将用户同步到 Azure AD 是一项**免费功能**，不需要客户付费购买任何订阅。
>- 同步的用户**不会自动获得***任何*许可证。 管理员仍对许可证分配拥有总体控制权。 
> - Microsoft 建议让 IT 管理员同步其所有用户。 这不仅可以解除阻止用户访问任何 Azure AD 集成资源，而且还能为 IT 管理员提供更宽广的视图，让他们查看其用户正在访问哪些应用程序。 

![什么是 Azure AD Connect](media/active-directory-aadconnect/arch.png)

## <a name="why-use-azure-ad-connect"></a>为何使用 Azure AD Connect
将本地目录与 Azure AD 集成可提供通用标识用于访问云和本地资源，从而提高用户的生产率。 用户和组织可以享受到以下好处：

* 用户可以使用单个标识来访问本地应用程序和云服务，例如 Office 365。
* 单个工具即可提供轻松同步和登录的部署体验。
* 为方案提供最新功能。 Azure AD Connect 取代了 DirSync 和 Azure AD Sync 等早期版本的标识集成工具。有关详细信息，请参阅 [混合标识目录集成工具比较](../active-directory-hybrid-identity-design-considerations-tools-comparison.md)。

### <a name="how-azure-ad-connect-works"></a>Azure AD Connect 工作原理
Azure Active Directory Connect 由三个主要组件构成：同步服务、可选的 Active Directory 联合身份验证服务组件和名为 [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)的监视组件。

<center>![Azure AD Connect 堆栈](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png)
</center>

* 同步 - 此组件负责创建用户、组和其他对象。 它还负责确保本地用户和组的标识信息与云匹配。
* AD FS - 联合身份验证是 Azure AD Connect 的可选部件，可用于使用本地 AD FS 基础结构配置混合环境。 组织可以使用此部件来解决复杂的部署，例如域加入 SSO、实施 AD 登录策略和智能卡或第三方 MFA。
* 运行状况监视 - Azure AD Connect Health 提供可靠监视，并在 Azure 门户中提供一个中心位置用于查看此活动。 有关更多信息，请参阅 [Azure Active Directory Connect Health](../connect-health/active-directory-aadconnect-health.md)。

## <a name="install-azure-ad-connect"></a>安装 Azure AD Connect

> [!IMPORTANT]
> Microsoft 不支持在正式记录的这些操作之外修改或操作 Azure AD Connect 同步。 其中的任何操作都可能会导致 Azure AD Connect 同步出现不一致或不受支持状态。因此，Microsoft 无法提供这种部署的技术支持。

可以在 [Microsoft 下载中心](http://go.microsoft.com/fwlink/?LinkId=615771)找到 Azure AD Connect 的下载文件。

| 解决方案 | 场景 |
| --- | --- |
| 开始之前 - [硬件和先决条件](active-directory-aadconnect-prerequisites.md) |<li>开始安装 Azure AD Connect 之前所要完成的步骤。</li> |
| [快速设置](active-directory-aadconnect-get-started-express.md) |<li>如果只有一个林 AD，我们建议使用此选项。</li> <li>使用密码同步以同一密码进行用户登录。</li> |
| [自定义设置](active-directory-aadconnect-get-started-custom.md) |<li>有多个林时使用。 支持许多本地[拓扑](active-directory-aadconnect-topologies.md)。</li> <li>自定义登录选项，例如直通身份验证、用于联合身份验证的 ADFS，或使用第三方标识提供者。</li> <li>自定义同步功能，例如筛选和写回。</li> |
| [从 DirSync 升级](active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>在已有 DirSync 服务器运行的情况下使用。</li> |
| [从 Azure AD Sync 或 Azure AD Connect 升级](active-directory-aadconnect-upgrade-previous-version.md) |<li>可以根据偏好选择多种不同的方法。</li> |

[安装后](active-directory-aadconnect-whats-next.md)，应该验证程序是否按预期工作，并将许可证分配给用户。

### <a name="next-steps-to-install-azure-ad-connect"></a>Azure AD Connect 安装后续步骤
|主题 |链接|  
| --- | --- |
|下载 Azure AD Connect | [下载 Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|使用快速设置安装 | [Azure AD Connect 的快速安装](./active-directory-aadconnect-get-started-express.md)|
|使用自定义设置安装 | [Azure AD Connect 的自定义安装](./active-directory-aadconnect-get-started-custom.md)|
|从 DirSync 升级 | [从 Azure AD 同步工具 (DirSync) 升级](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|安装后 | [验证安装并分配许可证 ](active-directory-aadconnect-whats-next.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>了解有关安装 Azure AD Connect 的详细信息
还要预先了解 [操作](active-directory-aadconnectsync-operations.md) 注意事项。 可能要部署一台待机服务器，以便在发生[灾难](active-directory-aadconnectsync-operations.md#disaster-recovery)时轻松进行故障转移。 如果要频繁进行配置更改，应该计划部署一台 [暂存模式](active-directory-aadconnectsync-operations.md#staging-mode) 服务器。

|主题 |链接|  
| --- | --- |
|支持的拓扑 | [Azure AD Connect 的拓扑](active-directory-aadconnect-topologies.md)|
|设计概念 | [Azure AD Connect 设计概念](active-directory-aadconnect-design-concepts.md)|
|用于安装的帐户 | [有关 Azure AD Connect 凭据和权限的详细信息](./active-directory-aadconnect-accounts-permissions.md)|
|操作规划 | [Azure AD Connect 同步：操作任务和注意事项](active-directory-aadconnectsync-operations.md)|
|用户登录选项 | [Azure AD Connect 用户登录选项](active-directory-aadconnect-user-signin.md)|

## <a name="configure-sync-features"></a>配置同步功能
Azure AD Connect 随附了多个可以选择启用或已按默认启用的功能。 在某些方案和拓扑中，有些功能可能需要进行其他配置。

[筛选](active-directory-aadconnectsync-configure-filtering.md) 。 默认情况下，同步所有用户、联系人、组和 Windows 10 计算机。 可以根据域、OU 或属性更改筛选。

[密码哈希同步](active-directory-aadconnectsync-implement-password-hash-synchronization.md) 可将 Active Directory 中的密码哈希同步到 Azure AD。 最终用户可以在本地与云中使用相同的密码，且只需在一个位置管理此密码。 由于它将本地 Active Directory 用作颁发机构，因此，还可以使用自己的密码策略。

[密码写回](../authentication/quickstart-sspr.md) 可让用户在云中更改和重置其密码，及应用本地密码策略。

[设备写回](active-directory-aadconnect-feature-device-writeback.md) 可将 Azure AD 中注册的设备写回到本地 Active Directory，以便可以使用该设备进行条件性访问。

[防止意外删除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) 功能默认处于打开状态，它可以保护云目录，避免同时进行多次删除。 默认情况下，允许每次运行执行 500 次删除。 可以更改此设置，具体取决于组织规模。

[自动升级](active-directory-aadconnect-feature-automatic-upgrade.md) ，这可确保 Azure AD Connect 始终保持最新版本。

### <a name="next-steps-to-configure-sync-features"></a>同步功能配置后续步骤
|主题 |链接|  
| --- | --- |
|配置筛选 | [Azure AD Connect 同步：配置筛选](active-directory-aadconnectsync-configure-filtering.md)|
|密码哈希同步 | [Azure AD Connect 同步：实现密码哈希同步](active-directory-aadconnectsync-implement-password-hash-synchronization.md)|
|密码写回 | [密码管理入门](../authentication/quickstart-sspr.md)|
|设备写回 | [在 Azure AD Connect 中启用设备写回](active-directory-aadconnect-feature-device-writeback.md)|
|防止意外删除 | [Azure AD Connect 同步：防止意外删除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)|
|自动升级 | [Azure AD Connect：自动升级](active-directory-aadconnect-feature-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>自定义 Azure AD Connect 同步
Azure AD Connect 同步随附一个适用于大部分客户和拓扑的默认配置。 但总会有一些情况使得默认配置不适用，因此必须进行调整。 可以根据本部分和链接主题中所述进行更改。

如果以前没有用过同步拓扑，请先了解 [技术概念](active-directory-aadconnectsync-technical-concepts.md)中所述的基本概念和术语。 Azure AD Connect 是在 MIIS2003、ILM2007 和 FIM2010 基础上演进而来的。 即使有些功能相同，但改变的部分也有很多。

[默认配置](active-directory-aadconnectsync-understanding-default-configuration.md) 假设配置中可能存在多个林。 在这些拓扑中，用户对象可能表示为另一个林中的联系人。 用户还可能在另一个资源林中具有链接的邮箱。 [用户和联系人](active-directory-aadconnectsync-understanding-users-and-contacts.md)中介绍了默认配置的行为。

同步的配置模型称为 [声明性预配](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)。 高级属性流程使用 [函数](active-directory-aadconnectsync-functions-reference.md) 来表示属性转换。 可以使用 Azure AD Connect 随附的工具来检查整个配置。 如果需要进行配置更改，请确保遵循 [最佳做法](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) ，以便可以更轻松地采用新版本。

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Azure AD Connect 同步自定义后续步骤
|主题 |链接|  
| --- | --- |
|所有 Azure AD Connect 同步文章 | [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md)|
|技术概念 | [Azure AD Connect 同步：技术概念](active-directory-aadconnectsync-technical-concepts.md)|
|了解默认配置 | [Azure AD Connect 同步：了解默认配置](active-directory-aadconnectsync-understanding-default-configuration.md)|
|了解用户和联系人 | [Azure AD Connect 同步：了解用户和联系人](active-directory-aadconnectsync-understanding-users-and-contacts.md)|
|声明性预配 | [Azure AD Connect Sync：了解声明性设置表达式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)|
|更改默认配置 | [更改默认配置的最佳做法](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>配置联合身份验证功能

Azure AD Connect 提供多项功能，简化了使用 AD FS 通过 Azure AD 进行联合身份验证以及管理联合身份验证信任的过程。 Azure AD Connect 支持 Windows Server 2012R2 或更高版本上的 AD FS。

[更新 AD FS 场的 SSL 证书](active-directory-aadconnectfed-ssl-update.md)，即使不使用 Azure AD Connect 来管理联合身份验证信任。

向场[添加 AD FS 服务器](active-directory-aadconnect-federation-management.md#addadfsserver)，以便根据需要扩展场。

[修复信任](active-directory-aadconnect-federation-management.md#repairthetrust)（针对 Azure AD），只需单击数下即可。

可将 ADFS 配置为支持 [多个域](active-directory-aadconnect-multiple-domains.md)。 例如，可能在联合身份验证功能中需要使用多个顶级域。

如果 ADFS 服务器未配置为自动更新 Azure AD 中的证书，或者如果使用非 ADFS 解决方案，则在需要 [更新证书](active-directory-aadconnect-o365-certs.md)时会通知你。

### <a name="next-steps-to-configure-federation-features"></a>配置联合身份验证功能的后续步骤
|主题 |链接|  
| --- | --- |
|所有 AD FS 文章 | [Azure AD Connect 和联合身份验证](active-directory-aadconnectfed-whatis.md)|
|配置带有子域的 ADFS | [与 Azure AD 联合的多域支持](active-directory-aadconnect-multiple-domains.md)|
|管理 AD FS 场 | [使用 Azure AD Connect 管理和自定义 AD FS](active-directory-aadconnect-federation-management.md)|
|手动更新联合身份验证证书 | [续订 Office 365 和 Azure AD 的联合身份验证证书](active-directory-aadconnect-o365-certs.md)|

## <a name="more-information-and-references"></a>详细信息和参考
|主题 |链接|  
| --- | --- |
|版本历史记录 | [版本历史记录](active-directory-aadconnect-version-history.md)|
|比较 DirSync、Azure ADSync 和 Azure AD Connect | [目录集成工具比较](../active-directory-hybrid-identity-design-considerations-tools-comparison.md)|
|Azure AD 的非 ADFS 兼容性列表 | [Azure AD 联合身份验证兼容性列表](active-directory-aadconnect-federation-compatibility.md)|
|配置 SAML 2.0 Idp|[将 SAML 2.0 标识提供者 (IdP) 用于单一登录](active-directory-aadconnect-federation-saml-idp.md)|
|同步的属性 | [同步的属性](active-directory-aadconnectsync-attributes-synchronized.md)|
|使用 Azure AD Connect Health 进行监视 | [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)|
|常见问题 | [Azure AD Connect 常见问题](active-directory-aadconnect-faq.md)|

**其他资源**

有关将本地目录扩展到云的 Ignite 2015 演示文稿。

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3862/player]
> 
> 

