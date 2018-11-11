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
ms.date: 11/02/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2aca42c23cc213d5d7e451105052d5d5d697b77d
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979465"
---
# <a name="hybrid-identity-and-microsoft-identity-solutions"></a>混合标识和 Microsoft 标识解决方案
[Microsoft Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 混合标识解决方案使你能够将本地目录与 Azure AD 同步，同时仍可在本地管理用户。 如果计划将本地 Windows Server Active Directory 与 Azure AD 进行同步，首先需要决定是使用托管标识还是使用联合标识。 

- **托管标识** - 从本地 Active Directory 同步的用户帐户和组以及用户身份验证由 Azure 进行管理。   
- **联合标识**可以对用户进行更多的控制，其方法是将用户身份验证与 Azure 隔离开，将身份验证委托给受信任的本地标识提供者。 

有多个选项可用于配置混合标识。 当考虑哪个标识模型最适合组织需求时，还需考虑时间、现有基础结构、复杂性和成本。 这些因素对每个组织都不同，并可能随时间变化。 但是，如果需求确实发生更改，你还可灵活切换到不同的标识模型。

## <a name="managed-identity"></a>托管标识 

托管标识是将本地目录对象（用户和组）与 Azure AD 同步的最简单方法。 

![同步混合标识](./media/whatis-hybrid-identity/managed.png)

尽管托管标识是最简单且最快速的方法，你的用户将仍需针对基于云的资源维护一个单独的密码。 若要避免此问题，你还可以（可选）将[用户密码哈希同步](how-to-connect-password-hash-synchronization.md)到 Azure AD 目录中。 同步密码哈希使用户能够使用与本地使用的相同用户名和密码登录基于云的组织资源。 Azure AD Connect 将定期检查本地目录是否发生更改，并保持 Azure AD 目录同步。 如果本地 Active Directory 的用户属性或密码已更改，则将在 Azure AD 中自动更新此信息。 

由于大多数组织只想让用户登录 Office 365、SaaS 应用程序和其他基于 Azure AD 的资源，因此建议使用默认的密码哈希同步选项。 如果这对你不适应，请在传递身份验证和 AD FS 之间进行选择。

> [!TIP]
> 用户密码以表示实际用户密码的哈希值形式存储在本地 Windows Server Active Directory 中。 哈希值是单向数学函数（哈希算法）的计算结果。 没有任何方法可将单向函数的结果还原为纯文本版本的密码。 无法使用密码哈希来登录本地网络。 如果选择同步密码，Azure AD Connect 将从本地 Active Directory 提取密码哈希，并在同步到 Azure AD 之前将额外安全处理应用于密码哈希。 还可将密码哈希同步与密码写回一起使用，以便在 Azure AD 中启用自助密码重置。 此外，还可以对连接到公司网络中的已加入域的计算机上的用户启用单一登录 (SSO)。 通过单一登录，受支持的用户只需输入用户名即可安全访问云资源。 
>

## <a name="pass-through-authentication"></a>直通身份验证

[Azure AD 传递身份验证](how-to-connect-pta.md)使用本地 Acitve Directory 为基于 Azure AD 的服务提供简单的密码验证解决方案。 如果组织的安全和符合性策略不允许发送用户密码（即使以哈希格式发送也不允许），则只需对加入域的设备提供桌面 SSO 支持，建议使用传递身份验证进行评估。 传递身份验证不需要在 DMZ 中进行任何部署，因此在与 AD FS 进行比较时可以简化部署基础结构。 如果用户使用 Azure AD 进行登录，此身份验证方法可直接针对本地 Active Directory 验证用户的密码。

![直通身份验证](./media/whatis-hybrid-identity/pass-through-authentication.png)

使用传递身份验证，无需复杂的网络基础结构，也不需要将本地密码存储在云中。 结合单一登录，传递身份验证可在登录 Azure AD 或其他云服务时提供真正集成的体验。

传递身份验证可以通过 Azure AD Connect 进行配置，利用一个简单的本地代理来侦听密码验证请求。 可轻松地将此代理部署到多台计算机，以提供高可用性和负载均衡。 由于所有通信均为出站，因此无需在 DMZ 中安装连接器。 服务器计算机的连接器要求如下所示：

- Windows Server 2012 R2 或更高版本
- 加入通过其验证用户的林中的域

## <a name="federated-identity-ad-fs"></a>联合标识 (AD FS)

若要更好地控制用户访问 Office 365 和其他云服务的方式，可使用 [Active Directory 联合身份验证服务 (AD FS)](how-to-connect-fed-whatis.md) 设置与单一登录 (SSO) 的目录同步。 使用 AD FS 联合验证用户的登录时，可将身份验证委托给验证用户凭据的本地服务器。 在此模型中，本地 Active Directory 凭据永远不会传递到 Azure AD 中。

![联合标识](./media/whatis-hybrid-identity/federated-identity.png)

也称为“联合身份验证”，这种登录方法可确保所有用户身份验证均在本地得以控制，并且允许管理员实施更严格的访问控制。 使用 AD FS 的联合身份验证是最复杂的选项，需要在本地环境中部署其他服务器。 联合身份验证还承诺为 Active Directory 和 AD FS 基础结构提供全天候支持。 如果本地 Internet 访问、域控制器或 AD FS 服务器不可用，用户无法登录云服务，此时就需要这种高级支持。

> [!TIP]
> 如果决定使用 Active Directory 联合身份验证服务 (AD FS) 进行联合身份验证，则可以选择性地设置密码哈希同步，作为在 AD FS 基础结构发生故障时的备用身份验证方式。
>

## <a name="common-scenarios-and-recommendations"></a>常见方案和建议

下面是一些常见的混合标识和访问管理方案，其中每个方案都包含有关适合的混合标识选项的建议。

|我需要：|PHS 和 SSO<sup>1</sup>| PTA 和 SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|将本地 Active Directory 中创建的新用户、联系人和组帐户自动同步到云。|![建议](./media/whatis-hybrid-identity/ic195031.png)| ![建议](./media/whatis-hybrid-identity/ic195031.png) |![建议](./media/whatis-hybrid-identity/ic195031.png)|
|为 Office 365 混合方案设置我的租户|![建议](./media/whatis-hybrid-identity/ic195031.png)| ![建议](./media/whatis-hybrid-identity/ic195031.png) |![建议](./media/whatis-hybrid-identity/ic195031.png)|
|使用户能够使用其本地密码登录并访问云服务|![建议](./media/whatis-hybrid-identity/ic195031.png)| ![建议](./media/whatis-hybrid-identity/ic195031.png) |![建议](./media/whatis-hybrid-identity/ic195031.png)|
|使用公司凭据实现单一登录|![建议](./media/whatis-hybrid-identity/ic195031.png)| ![建议](./media/whatis-hybrid-identity/ic195031.png) |![建议](./media/whatis-hybrid-identity/ic195031.png)|
|确保未在云中存储密码哈希| |![建议](./media/whatis-hybrid-identity/ic195031.png)|![建议](./media/whatis-hybrid-identity/ic195031.png)|
|启用云多重身份验证解决方案| |![建议](./media/whatis-hybrid-identity/ic195031.png)|![建议](./media/whatis-hybrid-identity/ic195031.png)|
|启用本地多重身份验证解决方案| | |![建议](./media/whatis-hybrid-identity/ic195031.png)|
|支持用户使用智能卡身份验证<sup>4</sup>| | |![建议](./media/whatis-hybrid-identity/ic195031.png)|
|在 Office 门户和 Windows 10 桌面上显示密码到期通知| | |![建议](./media/whatis-hybrid-identity/ic195031.png)|

> <sup>1</sup> 使用单一登录的密码哈希同步。
>
> <sup>2</sup> 传递身份验证和单一登录。 
>
> <sup>3</sup> 使用 AD FS 的联合单一登录。
>
> <sup>4</sup> AD FS 可与企业 PKI 集成，允许使用证书登录。 这些证书可以是通过受信任预配通道（如 MDM、GPO 或智能卡证书（包括 PIV/CAC 卡）或 Hello 企业版（信任证书））部署的软证书。 有关智能卡身份验证支持的详细信息，请参阅[此博客](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)。
>

## <a name="what-is-azure-ad-connect"></a>什么是 Azure AD Connect？

Azure AD Connect 专用于满足和完成混合标识目标的 Microsoft 工具。  这样，便可以为集成到 Azure AD 的 Office 365、Azure 和 SaaS 应用程序的用户提供一个通用标识。  它提供以下功能：
    
- [同步](how-to-connect-sync-whatis.md) - 此组件负责创建用户、组和其他对象。 它还负责确保本地用户和组的标识信息与云匹配。  它负责将密码哈希与 Azure AD 进行同步。
- [密码哈希同步](how-to-connect-password-hash-synchronization.md) - 一个可选组件，可以将用户密码哈希与 Azure AD 同步，这样用户就可以在本地和云中使用相同的密码。
-   [AD FS 和联合身份验证集成](how-to-connect-fed-whatis.md) - 联合身份验证是 Azure AD Connect 的可选部件，可用于使用本地 AD FS 基础结构配置混合环境。 它还提供了 AD FS 管理功能，例如证书续订和其他 AD FS 服务器部署。
-   [直通身份验证](how-to-connect-pta.md) - 另一个可选组件，它允许用户在本地和云中使用相同的密码，但不要求额外提供联合环境的基础结构。
-   [PingFederate 和联合身份验证集成](how-to-connect-install-custom.md#configuring-federation-with-pingfederate) - 另一联合身份验证选项，允许你使用 PingFederate 作为标识提供者。
-   [运行状况监视](whatis-hybrid-identity-health.md) - Azure AD Connect Health 提供可靠监视，并在 Azure 门户中提供一个中心位置，用于查看此活动。 


![什么是 Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>什么是 Azure AD Connect Health？

Azure Active Directory (Azure AD) Connect Health 可帮助你监视和深入了解本地标识基础结构和同步服务。 它针对重要的标识组件 - 例如 Active Directory 联合身份验证服务 (AD FS) 服务器、Azure AD Connect 服务器（也称为同步引擎）、Active Directory 域控制器等 - 提供监视功能，以便与 Office 365 和 Microsoft Online Services 建立可靠的连接。它还可以让用户轻松访问有关这些组件的关键数据点，获取使用情况信息和其他重要见解来做出明智的决策。

这些信息显示在 [Azure AD Connect Health 门户](https://aka.ms/aadconnecthealth)中。 在 Azure AD Connect Health 门户中，可以查看警报、性能监视、使用情况分析和其他信息。 Azure AD Connect Health 在一个集中的位置提供重要标识组件的运行状况单一可重用功能区。

![什么是 Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)


随着 Azure AD Connect Health 中的功能日益增多，门户通过标识可重用功能区提供单个仪表板。 可以创建一个更稳健、更健全的集成环境，让用户提高其工作效率。


## <a name="why-use-azure-ad-connect"></a>为何使用 Azure AD Connect？
将本地目录与 Azure AD 集成可提供通用标识用于访问云和本地资源，从而提高用户的生产率。 用户和组织可以享受到以下好处：

* 用户可以使用单个标识来访问本地应用程序和云服务，例如 Office 365。
* 单个工具即可提供轻松同步和登录的部署体验。
* 为方案提供最新功能。 Azure AD Connect 取代了 DirSync 和 Azure AD Sync 等早期版本的标识集成工具。有关详细信息，请参阅 [混合标识目录集成工具比较](plan-hybrid-identity-design-considerations-tools-comparison.md)。

## <a name="why-use-azure-ad-connect-health"></a>为何使用 Azure AD Connect Health？
将本地目录与 Azure AD 集成可以提高用户的工作效率，因为他们可以使用一个通用标识来访问云和本地资源。 但是，这种集成带来的挑战是，必须确保此环境正常运行，才能让用户从任何设备可靠地访问本地和云中的资源。 Azure AD Connect Health 可帮助你监视和深入分析用于访问 Office 365 或其他 Azure AD 应用程序的本地标识基础结构。 这种方法就像在每个本地标识服务器上安装代理那样简单。

### <a name="azure-ad-connect-health-for-ad-fshow-to-connect-health-adfsmd"></a>[适用于 AD FS 的 Azure AD Connect Health](how-to-connect-health-adfs.md)
适用于 AD FS 的 Azure AD Connect Health 支持 Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 和 Windows Server 2016 中的 AD FS 2.0。 它还支持监视为 Extranet 访问提供身份验证支持的 AD FS 代理或 Web 应用程序代理服务器。 轻松快速地安装 Health 代理后，适用于 AD FS 的 Azure AD Connect Health 可提供以下一组重要功能：

#### <a name="key-benefits-and-best-practices"></a>主要优点和最佳做法

- *增强的安全性*
  - [Extranet 锁定趋势](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)
  - [失败的登录报告](how-to-connect-health-adfs.md#risky-ip-report-public-preview) 
  - [符合隐私标准](reference-connect-health-user-privacy.md)    
- *[出现严重 ADFS 系统问题](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)时会收到通知*
    - 服务器配置和可用性 
    - [性能和连接](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs) 
  - 定期维护    
- *易于部署和管理*
  - 快速的[代理安装](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs) 
  - 代理自动升级到最新版本 
  - 数据在几分钟后便可出现在门户中    
- *丰富的[使用情况指标](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)* 
  - 排名靠前的应用程序使用情况
  - 网络位置和 TCP 连接
  - 每个服务器的令牌请求    
- 出色的用户体验 
  - Azure 门户中的仪表板方式
  - [通过电子邮件提供警报](how-to-connect-health-adfs.md#alerts-for-ad-fs)    

#### <a name="feature-highlight"></a>功能亮点

*   使用警报进行监视，以便在 AD FS 和 AD FS 代理服务器状况不正常时知道这一点
*   关键警报电子邮件通知
*   查看性能数据的趋势，这在规划 AD FS 的容量时非常有用
*   使用透视针对 AD FS 登录进行使用情况分析（应用、用户、网络位置，等等）
*   针对 AD FS 的报告，例如，使用不当的用户名/密码尝试登录的前 50 个用户和使用的最后一个 IP 地址
*   针对失败的 AD FS 登录的危险 IP 报告

在此处详细了解如何[在 AD FS 中使用 Azure AD Connect Health](how-to-connect-health-adfs.md)

### <a name="azure-ad-connect-health-for-synchow-to-connect-health-syncmd"></a>[用于同步的 Azure AD Connect Health](how-to-connect-health-sync.md)
用于同步的 Azure AD Connect Health 可以监视和提供有关本地 Active Directory 与 Azure AD 之间发生的同步的信息。 用于同步的 Azure AD Connect Health 提供以下关键功能集：

* 使用警报进行监视，以便在 Azure AD Connect 服务器（也称为同步引擎）状况不正常时知道这一点
* 关键警报电子邮件通知
* 包括延迟图表以不同操作和趋势保持同步的同步操作见解操作如窗体添加的更新、删除
* 速览有关同步属性以及上次成功导出到 Azure AD 的信息
* 有关对象级同步错误的报告\(不需要 Azure AD Premium\)

在此处详细了解如何[使用 Azure AD Connect Health 进行同步](how-to-connect-health-sync.md)

### <a name="azure-ad-connect-health-for-ad-dshow-to-connect-health-addsmd"></a>[用于 AD DS 的 Azure AD Connect Health](how-to-connect-health-adds.md)
用于 Active Directory 域服务 (AD DS) 的 Azure AD Connect Health 负责监视 Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 和 Windows Server 2016 上已安装的域控制器。 使用 Health 代理安装可以从云监视本地 AD DS 环境。 用于 AD DS 的 Azure AD Connect Health 提供以下关键功能集：

* 检测到域控制器不正常时的监视警报以及关键警报的电子邮件通知
* 域控制器仪表板，提供对域控制器运行状况和运行状态的快速视图
* 复制状态仪表板，显示最新复制信息以及指向故障排除指南的链接（检测到错误时）
* 需要从任何位置快速访问常用性能计数器的性能数据关系图，才能实现故障排除和监视目的

在此处详细了解如何[在 AD DS 中使用 Azure AD Connect Health](how-to-connect-health-adds.md)

## <a name="next-steps"></a>后续步骤


- [硬件和先决条件](how-to-connect-install-prerequisites.md) 
- [快速设置](how-to-connect-install-express.md)
- [自定义设置](how-to-connect-install-custom.md)
- [密码哈希同步](how-to-connect-password-hash-synchronization.md)|
- [直通身份验证](how-to-connect-pta.md)
- [Azure AD Connect 和联合身份验证](how-to-connect-fed-whatis.md)
- [安装 Azure AD Connect Health 代理](how-to-connect-health-agent-install.md) 
- [Azure AD Connect 同步](how-to-connect-sync-whatis.md)
- [版本历史记录](reference-connect-version-history.md)
- [目录集成工具比较](plan-hybrid-identity-design-considerations-tools-comparison.md)
- [Azure AD Connect 常见问题](reference-connect-faq.md)









