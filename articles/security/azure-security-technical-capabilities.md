---
title: "Azure 安全技术功能 | Microsoft Docs"
description: "了解基于云的计算服务，包括大量计算实例和服务，它们可根据应用程序或企业的需求自动增加或减少。"
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/26/2017
ms.author: TomSh
ms.openlocfilehash: a32693a274c2615a515838306d72dbe64a5d1763
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-technical-capabilities"></a>Azure 安全技术功能

为了帮助当前和潜在的 Azure 客户了解和利用 Azure 平台中和围绕 Azure 平台提供的各种安全相关功能，Microsoft 制定了一系列白皮书、安全概述、最佳实践和清单。 这些主题涵盖各类不同宽度和深度的内容，并且定期更新。 本文档属于下面“摘要”部分中总结的系列的一部分。 有关此 Azure 安全系列的更多信息，请访问 (URL)。

## <a name="azure-platform"></a>Azure 平台

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) 是托管于 Microsoft 公有云数据中心的云平台，由基础结构和应用程序服务组成，并且集成了数据服务、高级分析以及开发人员工具和服务。 客户可将 Azure 用于许多不同的容量和方案，从基本计算、网络和存储，到移动和 Web 应用服务，再到物联网等完整云方案，并且可将 Azure 与开源技术配合使用，作为混合云进行部署或托管在客户的数据中心内。 Azure 以构建基块的形式提供云技术，帮助公司节省成本、快速创新和主动管理系统。 构建 IT 资产或将其迁移到云提供商处时，需要借助该组织的能力来保护应用程序和数据，并使用该组织提供的服务和控件来管理基于云的资产的安全性。

Microsoft Azure 是唯一一个提供安全一致的应用程序平台和服务架构的云计算提供商，让团队可以使用各种云技能组合应对各种级别的项目复杂性；它集成了数据服务和分析，可以跨 Microsoft 和非 Microsoft 平台、开放框架和工具，从现有数据中挖掘信息；它允许用户选择是将云与本地集成，还是在本地数据中心部署 Azure 云服务。 作为 Microsoft 受信任云的一部分，客户可依赖 Azure 行业领先的安全性、可靠性、合规性、隐私以及庞大的人员、合作伙伴和流程网络，为云中的组织提供支持。

借助 Microsoft Azure，可以：

- 通过云加快创新。

- 深入了解业务决策和应用。

- 随时生成，随地部署。

- 保护业务。

## <a name="scope"></a>范围

本白皮书的焦点包括用于支持 Microsoft Azure 核心组件（即 [Microsoft Azure 存储](https://docs.microsoft.com/azure/storage/storage-introduction)、[Microsoft Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/)、[Microsoft Azure 虚拟机模型](https://docs.microsoft.com/azure/virtual-machines/    )以及用于管理这些组件的工具和基础结构）的安全功能。 它重点介绍了客户可用来履行保护数据安全和隐私职责的 Microsoft Azure 技术功能。

了解此共同责任模式对于迁移到云的客户至关重要。 云提供商为安全和合规性工作的实施带来了极大的优势，但这些优势并不表示客户就不用保护其用户、应用程序和服务产品。

使用 IaaS 解决方案的客户需要负责或共同负责保护和管理操作系统、网络配置、应用程序、标识、客户端及数据。  PaaS 解决方案以 IaaS 部署为基础构建，客户仍需负责或共同负责保护和管理应用程序、标识、客户端及数据。 使用 SaaS 解决方案的客户也还是需要负责这些。 他们必须确保数据正确分类，并且共同负责管理其用户和终结点设备。

本文档未详细介绍任何相关的 Microsoft Azure 平台组件，如 Azure 网站、Azure Active Directory、HDInsight、媒体服务，以及位于核心组件顶层的其他服务。 尽管提供了少许常规信息，但本白皮书假定读者熟悉 Microsoft 提供的其他参考资料中所描述的以及本白皮书提供的链接中所包含的 Azure 基本概念。


## <a name="available-security-technical-capabilities-to-fulfil-user-customer-responsibility---big-picture"></a>可用于履行用户（客户）职责的安全技术功能 — 大图

Microsoft Azure 提供可帮助客户满足安全性、隐私和合规性需求的服务。 下图帮助阐释了各种 Azure 服务，用户可以使用这些服务根据行业标准来构建安全合规的应用程序基础结构。

![可用的安全技术功能 — 大图](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access-protect"></a>管理和控制标识与用户访问（保护）

可使用 Azure 管理用户标识和凭据以及控制访问，帮助保护企业信息和个人信息。

### <a name="azure-active-directory"></a>Azure Active Directory

Microsoft 标识和访问管理解决方案可帮助 IT 保护对企业数据中心和云中的应用程序和资源的访问，从而支持附加的验证级别，比如多重身份验证和条件性访问策略。 通过高级安全报告、审核和警报来监视可疑活动，有助于减少潜在的安全问题。 [Azure Active Directory Premium](https://docs.microsoft.com/azure/active-directory/active-directory-editions) 向数千个云 (SaaS) 应用提供单一登录，并提供对你在本地运行的 Web 应用的访问。

Azure Active Directory (AD) 在安全方面的益处包括以下能力：

- 为混合企业中的每个用户创建和管理单一标识，从而保持用户、组和设备同步。

- 提供对应用程序（包括数千个预先集成的 SaaS 应用）的单一登录访问。

- 通过对本地应用程序和云应用程序实施基于规则的多重身份验证，启用应用程序访问安全措施。

- 通过 Azure AD 应用程序代理预配对本地 Web 应用程序的安全远程访问。

[Azure Active Directory 门户](http://aad.portal.azure.com/)作为 Azure 门户的一部分提供。 通过此仪表板，可以大致了解组织的状态，轻松地深入管理目录、用户或应用程序访问。

![Azure Active Directory](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig2.png)

以下是 Azure 标识管理核心功能：

- 单一登录

- 多重身份验证

- 安全监控、警报和基于机器学习的报告

- 消费者标识和访问管理

- 设备注册

- Privileged identity management

- 标识保护

#### <a name="single-sign-on"></a>单一登录

[单一登录 (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) 是指只需使用单个用户帐户登录一次，就能访问开展业务所需的全部应用程序和资源。 登录之后，用户可以访问所需的全部应用程序，而无需再次进行身份验证（例如键入密码）。

许多组织依赖服务型软件 (SaaS) 应用程序（如 Office 365、Box 和 Salesforce）来提高最终用户生产力。 从历史上看，IT 人员需要在每个 SaaS 应用程序中单独创建和更新用户帐户，而用户需要记住每个 SaaS 应用程序的密码。

[Azure AD 将本地 Active Directory 扩展到云](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)，让用户不仅能够使用主要组织帐户登录到已加入域的设备和公司资源，而且还能登录到完成工作所需的全部 Web 和 SaaS 应用程序。

优势是不仅用户无需管理多组用户名和密码，而且还可根据组织组以及其身为员工的状态，自动预配或取消预配应用程序的访问权限。 [Azure AD 引入了安全和访问管理控件](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps)，支持跨 SaaS 应用程序集中管理用户的访问权限。

#### <a name="multi-factor-authentication"></a>多重身份验证

[Azure 多重身份验证 (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) 是需要使用多个验证方法的身份验证方法，为用户登录和事务额外提供一层重要的安全保障。 [MFA 可帮助保护](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)对数据和应用程序的访问，同时满足用户对简单登录过程的需求。 它通过各种验证选项（例如电话、短信、移动应用通知或验证码和第三方 OAuth 令牌）来提供强身份验证。

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>安全监控、警报和基于机器学习的报告

安全监控、警报和基于机器学习的报告（它们识别不一致的访问模式）可以帮助保护业务。 可以使用 Azure Active Directory 的访问和使用情况报告来监控你所在组织的目录的完整性和安全性。 使用此信息，目录管理员可以更好地确定哪里可能存在安全风险，以便制定相应的计划来降低风险。

在 Azure 经典门户或 [Azure Active Directory 门户](http://aad.portal.azure.com/)中，[报告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide)按以下方式分类：

- 异常报告 - 包含我们发现存在异常的登录事件。 我们的目标是让用户知道这类活动并使用户能够就事件是否可疑做出决定。

- 集成应用程序报告 - 就你所在的组织如何使用云应用程序提供见解。 Azure Active Directory 提供与数千个云应用程序的集成。

- 错误报告 - 指示在为外部应用程序预配帐户时可能发生的错误。

- 用户特定的报告 - 显示特定用户的设备/登录活动数据。

- 活动日志 - 包含过去 24 小时、过去 7 天或过去 30 天内的所有已审核事件的记录，以及组活动更改记录、密码重置和注册活动记录。

#### <a name="consumer-identity-and-access-management"></a>消费者标识和访问管理

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) 是一个高度可用的全局性标识管理服务，该服务适用于面向用户且可通过缩放来处理数以亿计标识的应用程序。 它可以跨移动平台和 Web 平台进行集成。 用户只需使用现有社交帐户或创建新凭据，即可通过可自定义的体验登录到所有应用程序。

过去，想要在自己的应用程序中[注册用户并使用户登录](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)的应用程序开发人员会编写自己的代码。 他们使用本地数据库或系统存储用户名和密码。 Azure Active Directory B2C 通过基于标准的安全平台和大量的可扩展策略，向组织提供一种更好的方式将用户标识管理集成到应用程序中。

使用 Azure Active Directory B2C 时，用户可通过使用他们现有的社交帐户（Facebook、Google、Amazon、LinkedIn）或创建新的凭据（电子邮件地址和密码，或者用户名和密码）在应用程序中注册。

设备注册

[Azure AD 设备注册](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-device-registration-overview)是基于设备的[条件访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-device-registration-overview)方案的基础。 在注册设备时，Azure Active Directory 设备注册会为设备提供一个标识，用于在用户登录时对设备进行身份验证。 然后，可以使用已经过身份验证的设备和设备的属性，对云中和本地托管的应用程序实施条件性访问策略。

当与 Intune 之类的[移动设备管理 (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) 解决方案结合使用时，Azure Active Directory 中的设备属性会使用关于设备的更多信息进行更新。 这会允许创建条件性访问规则，用于从设备强制访问满足安全和合规性标准。

#### <a name="privileged-identity-management"></a>Privileged Identity Management

利用 [Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)，可以管理、控制和监视特权标识以及对 Azure AD 中和 Office 365 或 Microsoft Intune 等其他 Microsoft Online Services 中资源的访问。

用户有时候需要在 Azure 或 Office 365 资源或者其他 SaaS 应用中执行特权操作。 这通常意味着，组织必须授予他们永久的 Azure AD 访问特权。 这会给云中托管的资源不断增大安全风险，因为组织无法充分监视这些用户正在使用管理特权执行哪些操作。 此外，如果有访问特权的用户帐户被泄露，这个缺口可能会影响其总体云安全性。 Azure AD 特权标识管理可帮助解决这一风险。

利用 Azure AD Privileged Identity Management，可以：

- 查看哪些用户是 Azure AD 管理员

- 按需启用对 Office 365 和 Intune 等 Microsoft Online Services 的“实时”管理访问权限

- 获取有关管理员访问历史记录以及管理员分配更改的报告

- 获取有关访问特权角色的警报

#### <a name="identity-protection"></a>标识保护

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) 是一种安全服务，它提供一个整合的视图来让用户查看影响组织标识的风险事件和潜在漏洞。 Identity Protection 使用现有的 Azure Active Directory 异常情况检测功能（可通过 Azure AD 的异常活动报告得到），并引入了可以实时检测异常情况的新风险事件类型。

## <a name="secured-resource-access-in-azure"></a>Azure 中受保护的资源访问

Azure 中的访问控制首先体现在计费方面。 Azure 帐户的所有者（可通过访问 [Azure 帐户中心](https://account.windowsazure.com/subscriptions)进行访问）是帐户管理员 (AA)。 订阅是计费容器，但它们也可充当安全边界：每个订阅都有一个服务管理员 (SA)，此管理员可以使用 [Azure 经典门户](https://manage.windowsazure.com/)来添加、删除和修改该订阅中的 Azure 资源。 新订阅的默认 SA 是 AA，但 AA 可以在 Azure 帐户中心更改 SA。

![Azure 中受保护的资源访问](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig3.png)

订阅也与目录相关联。 目录定义一组用户。 这些用户可以是创建该目录的公司或学校的用户，也可以是外部用户（即 Microsoft 帐户）。 订阅可由这些已被指定为服务管理员 (SA) 或共同管理员 (CA) 的目录用户的子集来访问；唯一的例外是，为了保持向后兼容，可以将 Microsoft 帐户（以前称为 Windows Live ID）指定为 SA 或 CA，而这些帐户不必存在于目录中。

面向安全的公司应侧重于向员工提供他们所需的确切权限。 权限过多，可能会向攻击者公开帐户。 权限太少，员工无法有效完成工作。 [Azure 基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) 通过对 Azure 提供细致的访问管理帮助解决此问题。

![受保护的资源访问 ](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig4.png)

使用 RBAC，可以在团队中对职责进行分配，仅向用户授予执行作业所需的访问权限。 而不是向每个人提供对 Azure 订阅或资源的无限权限，可以仅允许某些操作。 例如，使用 RBAC 允许一个员工管理订阅中的虚拟机，而允许另一个员工管理同一订阅中的 SQL 数据库。

![Azure 中受保护的资源访问 (RBAC)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="azure-data-security-and-encryption-protect"></a>Azure 数据安全与加密（保护）

在云中保护数据的关键问题之一是考虑数据可能将发生的状态，以及哪些控件适用于该状态。 根据 Azure 数据安全与加密最佳实践，将针对以下数据状态提供建议。

- 静态：包括物理媒体（磁盘或光盘）上以静态方式存在的所有信息存储对象、容器和类型。

- 传输中：数据在组件、位置或程序之间发送时，例如通过网络、通过服务总线（从本地到云，反之亦然，包括诸如 ExpressRoute 的混合连接），或在输入/输出过程中，会被视为动态数据。

### <a name="encryption--rest"></a>静态加密

实现静态加密

支持使用下表详述的至少一个推荐加密模型来加密数据。

| 加密模型 |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| 服务器加密 | 服务器加密 | 服务器加密 | 客户端加密
| 使用服务托管密钥的服务器端加密 | 使用 Azure Key Vault 中客户托管密钥的服务器端加密 | 使用本地客户托管密钥的服务器端加密 |
| • Azure 资源提供程序执行加密和解密操作 <br> •    Microsoft 管理密钥 <br>•    完整云功能 | •    Azure 资源提供程序执行加密和解密操作<br>•  客户通过 Azure Key Vault 控制密钥<br>• 完整云功能 | •    Azure 资源提供程序执行加密和解密操作 <br>• 客户在本地控制密钥 <br> •   完整云功能| • Azure 服务无法看到已解密的数据 <br>•  客户将密钥保留在本地（或其他安全存储中）。 Azure 服务无法使用密钥 <br>• 精简云功能|

### <a name="enabling-encryption-at-rest"></a>启用静态加密

**标识用于存储数据的所有位置**

静态加密的目标是加密所有数据。 这样做可以避免丢失重要数据或所有持久位置。请清点应用程序存储的所有数据。 

> [!Note] 
> 不只是“应用程序数据”或“PII”，还包括与应用程序相关的所有数据，其中包括帐户元数据（订阅映射、联系信息、PII）。

考虑使用哪些存储来存储数据。 例如：

- 外部存储（例如，SQL Azure、Document DB、HDInsights、Data Lake 等。）

- 临时存储（包含租户数据的任何本地缓存）

- 内存中缓存（可放入页面文件中。）

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>充分利用 Azure 中现有的静态加密支持

对于所使用的每个存储，充分利用现有的静态加密支持。

- Azure 存储：请参阅[静态数据的 Azure 存储服务加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)

- SQL Azure：请参阅[透明数据加密 (TDE)、SQL Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)

- VM 和本地磁盘存储（[Azure 磁盘加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)）

对于 VM 和本地磁盘存储，使用 Azure 磁盘加密（如果支持）：

IaaS

包含 IaaS VM（Windows 或 Linux）的服务应使用 [Azure 磁盘加密](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx)对包含客户数据的卷进行加密。

PaaS v2

在使用 Service Fabric 的 PaaS v2 上运行的服务可以对虚拟机规模集 [VMSS] 使用 Azure 磁盘加密来加密其 PaaS v2 VM。

PaaS v1

PaaS v1 当前不支持 Azure 磁盘加密。 因此，必须使用应用程序级加密对持久的静态数据进行加密。  这包括但不限于应用程序数据、临时文件、日志和故障转储。

大多数服务应尝试利用存储资源提供程序的加密功能。 某些服务必须进行显式加密，例如，所有持久化密钥材料（证书、根密钥/主密钥）都必须存储在 Key Vault 中。

如果你支持使用客户托管密钥进行服务端加密，则需要让客户能够通过某种方式向我们提供密钥。 支持的推荐方式是与 Azure Key Vault (AKV) 集成。 在此情况下，客户可以在 Azure Key Vault 中添加和管理其密钥。 客户可以通过 [Key Vault 入门](http://go.microsoft.com/fwlink/?linkid=521402)了解如何使用 AKV。

若要与 Azure Key Vault 集成，可以添加代码，以便从 AKV 请求需要用于解密的密钥。

- 有关如何与 AKV 集成的信息，请参阅 [Azure Key Vault – Step by Step](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/)（Azure Key Vault — 分步指南）。

如果支持客户托管密钥，则需要为客户提供 UX 来指定要使用的 Key Vault（或 Key Vault URI）。

由于静态加密涉及主机、基础结构和租户数据的加密，因此，因系统故障或恶意活动导致密钥丢失可能意味着丢失所有加密数据。 因此，静态加密解决方案必须具备能够弹性应对系统故障和恶意活动的全面灾难恢复方案，这一点很重要。

通常，实施静态加密的服务仍然容易受到加密密钥或主机驱动器上（例如，在主机 OS 的页面文件中）未加密数据的影响。因此，服务必须确保对用于其服务的主机卷进行加密。 为了简化此过程，计算团队启用了主机加密部署，该部署使用 [Bitlocker](https://technet.microsoft.com/library/dn306081.aspx) NKP 以及 DCM 服务和代理的扩展对主机卷进行加密。

大多数服务在标准 Azure VM 上实施。 此类服务应在计算团队启用[主机加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)时自动获取该功能。 在已推出的 Windows Server 2016 中，会自动为计算托管群集中运行的服务启用主机加密。

### <a name="encryption-in-transit"></a>传输中加密

保护传输中的数据应该是数据保护策略中不可或缺的部分。 由于数据将从许多位置来回移动，一般建议始终使用 SSL/TLS 协议来交换不同位置的数据。 在某些情况下，建议使用虚拟专用网络 (VPN) 隔离本地与云基础结构之间的整个通信通道。

对于在本地基础结构与 Azure 之间移动的数据，应该考虑适当的防护措施，例如 HTTPS 或 VPN。

对于需要从位于本地的多个工作站安全访问 Azure 的组织而言，请使用 [Azure 站点到站点 VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create)。

对于需要从位于本地的一个工作站安全访问 Azure 的组织而言，请使用[点到站点 VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create)。

可以通过专用高速 WAN 链路（例如 [ExpressRoute](https://azure.microsoft.com/services/expressroute/)）移动较大的数据集。 如果选择使用 ExpressRoute，则还可以使用 [SSL/TLS](https://support.microsoft.com/kb/257591) 或其他协议，在应用程序级别加密数据，以提供额外的保护。

如果通过 Azure 门户与 Azure 存储交互，则所有事务都将通过 HTTPS 发生。 也可以使用基于 HTTPS 的[存储 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) 来与 [Azure 存储](https://azure.microsoft.com/services/storage/)和 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)交互。

无法保护传输中数据的组织更容易遭受[中间人攻击](https://technet.microsoft.com/library/gg195821.aspx)、[窃听](https://technet.microsoft.com/library/gg195641.aspx)和会话劫持。 这些攻击可能是获取机密数据访问权限的第一步。

有关 Azure VPN 选项的详细信息，请阅读[规划和设计 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)一文。

### <a name="enforce-file-level-data-encryption"></a>实施文件级数据加密

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) 使用加密、标识和授权策略帮助保护文件与电子邮件。 Azure RMS 可跨多个设备工作 — 手机、平板电脑和台式电脑保护组织内部和外部的数据。 因为 Azure RMS 添加了数据所属的保护级别，所以即使数据离开组织边界，此功能仍然可行。

使用 Azure RMS 保护文件时，意味着使用行业标准加密并配合 [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html) 的完全支持。 使用 Azure RMS 进行数据保护时，即使文件被复制到不受 IT 控制的存储（例如云存储服务），也可保证该文件持续受到保护。 同样的情况将出现在通过电子邮件共享的文件，文件以电子邮件的附件形式受到保护，并提供如何打开受保护附件的说明。
规划 Azure RMS 采用时，建议执行以下操作：

- 安装 [RMS 共享应用](https://technet.microsoft.com/library/dn339006.aspx)。 此应用通过安装 Office 外挂程序来与 Office 应用程序集成，使用户可以轻松地直接保护文件。

- 配置应用程序和服务以支持 Azure RMS

- 创建可反映业务要求的[自定义模板](https://technet.microsoft.com/library/dn642472.aspx)。 例如：最高机密数据的模板应在所有最高机密相关的电子邮件中应用。

[数据分类](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf)和文件保护能力不佳的组织可能更容易遭到数据泄漏。 没有适当的文件保护，组织将无法获取业务见解、监控滥用，以及防止文件被恶意访问。

> [!Note]
> 有关 Azure RMS 的详细信息，请阅读 [Getting Started with Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx)（Azure Rights Management 入门）一文。

## <a name="secure-your-application-protect"></a>保护应用程序（保护）
Azure 负责保护运行应用程序的基础结构和平台，而你负责保护应用程序本身。 换而言之，需要以安全方式开发、部署和管理应用程序代码和内容。 无此安全性，应用程序代码或内容仍然容易受到威胁。

### <a name="web-application-firewall-waf"></a>Web 应用程序防火墙 (WAF)
[Web 应用程序防火墙 (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) 是[应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction)的功能，可以对 Web 应用程序进行集中保护，避免其受到常见的攻击和漏洞危害。

Web 应用程序防火墙基于 [OWASP 核心规则集](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 或 2.2.9 中的规则。 Web 应用程序已逐渐成为利用常见已知漏洞的恶意攻击的目标。 这些攻击中最常见的攻击包括 SQL 注入攻击、跨站点脚本攻击等。 防止应用程序代码遭受此类攻击颇具挑战性，并且可能需要对应用程序拓扑的多个层进行严格的维护、修补和监视。 集中式 Web 应用程序防火墙有助于大幅简化安全管理，为抵卸威胁或入侵的应用程序管理员提供更好的保障。 相较保护每个单独的 Web 应用程序，WAF 解决方案还可通过在中央位置修补已知漏洞，更快地响应安全威胁。 可将现有应用程序网关轻松转换为支持 Web 应用程序防火墙的应用程序网关。

Web 应用程序防火墙防范的某些常见 Web 安全漏洞包括：

- SQL 注入保护

- 跨站点脚本保护

- 常见 Web 攻击保护，例如命令注入、HTTP 请求走私、HTTP 响应拆分和远程文件包含攻击

- 防止 HTTP 协议违反行为

- 防止 HTTP 协议异常行为，例如缺少主机用户代理和接受标头

- 防止自动程序、爬网程序和扫描程序

- 检测常见应用程序错误配置（即 Apache、IIS 等）

> [!Note]
> 有关规则及其保护措施的更详细的列表，请参阅下面的[核心规则集](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview#core-rule-sets)：

Azure 还提供多种易用的功能，帮助保护应用的入站和出站流量。 此外，Azure 还提供外部来源的功能来扫描 Web 应用程序的漏洞，帮助客户保护其应用程序代码。

- [为应用设置 Azure Active Directory 身份验证](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)


- [启用传输层安全性 (TLS/SSL) - HTTPS 保护应用流量安全](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)

    - [强制所有传入流量通过 HTTPS 连接](http://microsoftazurewebsitescheatsheet.info/)

  - [启用严格传输安全性 (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)


- [按客户端 IP 地址限制应用访问](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [按客户端行为（请求频率和并发）限制应用访问](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [使用 Tinfoil 安全性扫描对 Web 应用代码进行扫描以确定是否存在漏洞](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [配置 TLS 相互身份验证来要求将客户端证书连接到 Web 应用](https://docs.microsoft.com/azure/app-service/app-service-web-configure-tls-mutual-auth)

- [配置从应用中使用的客户端证书以安全连接到外部资源](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [删除标准服务器标头以避免工具对应用进行指纹识别](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [使用点到站点 VPN 安全连接应用与专用网络资源](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

- [使用混合连接安全连接应用与专用网络资源](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

Azure 应用服务所使用的反恶意软件解决方案与 Azure 云服务和虚拟机使用的相同。 若要了解此方面的详细信息，请参阅[反恶意软件文档](https://docs.microsoft.com/azure/security/azure-security-antimalware)。

## <a name="secure-your-network-protect"></a>保护网络（保护）
Microsoft Azure 包括可靠的网络基础结构以支持应用程序和服务连接要求。 Azure 中的资源之间、本地资源与 Azure 托管的资源之间以及 Internet 与 Azure 之间都可能存在网络连接。

利用 [Azure 网络基础结构](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines)，可以安全地将 Azure 资源通过[虚拟网络 (VNet)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 相互连接。 VNet 是自己的网络在云中的表示形式。 VNet 是对专用于订阅的 Azure 云网络进行的逻辑隔离。 可将 VNet 连接到本地网络。

![保护网络（保护）](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig6.png)

如果需要基本的网络级别访问控制（基于 IP 地址和 TCP 或 UDP 协议），则可以使用[网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)。 网络安全组 (NSG) 是基本的静态数据包筛选防火墙，它使你能够基于 [5 元组](https://www.techopedia.com/definition/28190/5-tuple)控制访问。

Azure 网络支持在 Azure 虚拟网络上为网络流量自定义路由行为的功能。 可以通过在 Azure 中配置[用户定义路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)实现此操作。

[强制隧道](https://www.petri.com/azure-forced-tunneling)是一种机制，可用于确保不允许服务启动到 Internet 上的设备的连接。

Azure 支持通过 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 使用专用 WAN 链路连接本地网络和 Azure 虚拟网络。 Azure 和站点之间的链接使用专用连接，不需要通过公共 Internet。 如果 Azure 应用程序在多个数据中心运行，则可以使用 [Azure 流量管理器](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)智能地跨应用程序实例路由来自用户的请求。 如果可以通过 Internet 访问未在 Azure 中运行的服务，还可以将流量路由到这些服务。

## <a name="virtual-machine-security-protect"></a>虚拟机安全性（保护）

借助 [Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/)，可以采用灵活的方式部署各种计算解决方案。 通过对 Microsoft Windows、Linux、Microsoft SQL Server、Oracle、IBM、SAP 和 Azure BizTalk 服务的支持，可以在几乎所有操作系统上部署任何工作负荷和任何语言。

借助 Azure，可以使用来自 Microsoft、Symantec、Trend Micro 和 Kaspersky 等安全性供应商的[反恶意软件](https://docs.microsoft.com/azure/security/azure-security-antimalware)，保护虚拟机免受恶意文件、广告软件和其他威胁的侵害。

适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件是一种实时保护功能，可帮助识别并删除病毒、间谍软件和其他恶意软件。 Microsoft 反恶意软件提供了已知恶意或不需要的软件试图安装自身或在 Azure 系统上运行时的可配置警报。

[Azure 备份](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup)是一种可缩放的解决方案，无需资本投资便可保护应用程序数据，从而最大限度降低运营成本。 应用程序错误可能损坏数据，人为错误可能将 bug 引入应用程序。 借助 Azure 备份，可以保护运行 Windows 和 Linux 的虚拟机。

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 可帮助协调工作负荷和应用的复制、故障转移及恢复，因此能够在主要位置发生故障时通过辅助位置来提供工作负荷和应用。

## <a name="ensure-compliance-cloud-services-due-diligence-checklist-protect"></a>确保合规性：云服务审慎调查清单（保护）

Microsoft 制定了[云服务审慎调查清单](https://aka.ms/cloudchecklist.download)，帮助组织在考虑迁移到云时执行审慎调查。 它为所有规模、所有类型的组织（私有企业和公共部门组织，包括所有级别的政府部门和非盈利组织）提供了一种结构，用于确定他们自己的性能、服务、数据管理以及监管目标和要求。 这样，他们就可以对不同云服务提供商的服务/产品进行比较，最终构成云服务协议的基础。

该清单提供一个框架，该框架与新的云服务协议国际标准 ISO/IEC 19086 逐条保持一致。 此标准为组织提供一系列统一的考虑事项，帮助他们就云采用做出决策，并为云服务产品的比较确立一个共同的基础。

该清单促使云迁移得到全面审核，并为云服务提供商的选择提供结构化指导以及一致且可重复的方法。

云采用不再只是技术决策。 由于清单要求涉及组织的方方面面，因此它们可以让内部所有关键决策者（CIO、CISO 以及法律、风险管理、采购和合规性专业人员）聚集在一起。 这样可以通过合理的推论提高决策过程和基本决策的效率，从而降低出现影响云采用的意外障碍的可能性。

此外，该清单：

- 在云采用流程开始时公开决策者的关键议题。

- 支持就法律法规以及组织自身的隐私、个人身份信息 (PII) 和数据安全目标展开深入的业务讨论。

- 帮助组织识别任何可能影响云项目的问题。

- 针对各提供商提供一系列一致的问题，以及相同的术语、定义、指标和可交付结果，从而简化不同云服务提供商产品/服务的比较过程。

## <a name="azure-infrastructure-and-application-security-validation-detect"></a>Azure 基础结构和应用程序安全性验证（检测）

[Azure 操作安全性](https://docs.microsoft.com/azure/security/azure-operational-security)是指用户可用于在 Microsoft Azure 中保护其数据、应用程序和其他资产的服务、控件和功能。

![安全性验证（检测）](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig7.png)

Azure 操作安全性建立在一个框架上，该框架融合了通过 Microsoft 独有的各种功能获得的知识，包括 Microsoft 安全开发生命周期 (SDL)、Microsoft 安全响应中心计划以及对网络安全威胁形态的深刻认识。

### <a name="microsoft-operations-management-suiteoms"></a>Microsoft Operations Management Suite (OMS)

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) 是混合云的 IT 管理解决方案。 OMS 可单独使用，也可用于扩展现有 System Center 部署，为用户基于云管理基础结构提供了最大的灵活性和控制度。

![Microsoft Operations Management Suite (OMS)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig8.png)

使用 OMS，可以在任何云中（包括本地、Azure、AWS、Windows Server、Linux、VMware 和 OpenStack）管理任何实例，且成本低于其他有竞争力的解决方案。 OMS 专为云优先的环境而设计，为管理企业提供了一种新方法，能最快且最经济高效地应对新的业务挑战并适应新的工作负载、应用程序和云环境。

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) 通过将托管资源的数据收集到中心存储库来为 OMS 提供监视服务。 这些数据可能包括事件、性能数据或通过 API 提供的自定义数据。 收集后，可以分析、导出数据或针对它们发出警报。

![Log Analytics](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig9.png)

使用这种方法可以整合来自各种来源的数据，因此可将 Azure 服务中的数据合并到现有的本地环境。 此外，它还能将数据收集与针对该数据执行的操作明确区分开来，以便能够针对所有类型的数据执行所有操作。

### <a name="azure-security-center"></a>Azure 安全中心

[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)有助于预防、检测和响应威胁，同时增加 Azure 资源的可见性和安全可控性。 它提供 Azure 订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。

安全中心将分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 会有一列建议对所需控件的整个配置过程提供指导。

示例包括：

- 设置反恶意软件可帮助识别和删除恶意软件

- 配置网络安全组和规则来控制发送到 VM 的流量

- 设置 web 应用程序防火墙，帮助抵御针对 web 应用程序的攻击

- 部署缺少的系统更新

- 解决与推荐基线不匹配的操作系统配置

安全中心自动从 Azure 资源、网络和合作伙伴解决方案（例如恶意软件程序和防火墙）收集、分析和整合数据。 检测到威胁时会创建安全警报。 示例中包括的检测项：

- 与已知的恶意 IP 地址通信的不符合安全性的 VM

- 使用 Windows 错误报告检测到的高级恶意软件

- 对 VM 的暴力破解攻击

- 来自集成的反恶意软件程序和防火墙的安全警报

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) 提供一系列指针，指向特定资源类型的相关信息。 它对来自 Azure 基础结构（活动日志）和每个单独 Azure 资源（诊断日志）的数据提供可视化、查询、路由、警报、自动缩放和自动化功能。

云应用程序很复杂，包含很多移动部件。 监视可以为用户提供数据，确保应用程序始终处于健康运行状态。 监视还有助于避免潜在问题，或者解决过去的问题。

![Azure Monitor](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig10.png) 此外，还可以利用监视数据深入了解应用程序的情况。 了解这些情况有助于改进应用程序的性能或可维护性，或者实现本来需要手动干预的操作的自动化。

审核网络安全性对于检测网络漏洞以及确保符合 IT 安全和监管治理模型至关重要。 使用安全组视图，可以检索配置的网络安全组和安全规则，以及有效的安全规则。 应用规则列表后，可以确定打开的端口并评估网络漏洞。

### <a name="network-watcher"></a>网络观察程序

[网络观察程序](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher)是一个区域性服务，可用于在网络级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 借助网络观察程序随附的网络诊断和可视化工具，可以了解、诊断和洞察 Azure 中的网络。 此服务包括数据包捕获、下一跃点、IP 流验证、安全组视图和 NSG 流日志。 与单独网络资源的监视不同，方案级监视提供网络资源的端到端视图。

### <a name="storage-analytics"></a>存储分析

[存储分析](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)可存储一些指标，这些指标包括有关存储服务请求的聚合事务统计信息和容量数据。 在 API 操作级别以及存储服务级别报告事务，并在存储服务级别报告容量。 度量值数据可用于分析存储服务使用情况，诊断对存储服务所发出请求的问题以及提高使用服务的应用程序的性能。

### <a name="application-insights"></a>Application insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 使用它可以监视实时 Web 应用程序。 它会自动检测性能异常。 Application Insights 内含强大的分析工具，有助于诊断问题并了解用户在应用中执行的操作。 Application Insights 有助于持续提高性能与可用性。 它适用于本地或云中托管的各种平台（包括 .NET、Node.js 和 J2EE）中的应用。 它与 devOps 流程集成，并具有与各种开发工具的连接点。

监视：

- **请求率、响应时间和失败率** - 了解最受欢迎的页面、时段以及用户的位置。 查看哪些页面效果最好。 当有较多请求时，如果响应时间长且失败率高，则可能存在资源问题。

- **依赖项速率、响应时间和失败率** - 了解外部服务是否正拖慢速度。

- 异常 - 分析聚合的统计信息，或选择特定实例并钻取堆栈跟踪和相关请求。 报告服务器和浏览器异常。

- **页面查看次数和负载性能** - 由用户的浏览器报告。

- **来自网页的 AJAX 调用**：速率、响应时间和失败率。

- **用户和会话计数。**

- Windows 或 Linux 服务器计算机中的**性能计数器**，例如 CPU、内存和网络使用情况。

- Docker 或 Azure 中的**主机诊断**。

- 应用中的**诊断跟踪日志**- 可以将跟踪事件与请求相关联。

- 在客户端或服务器代码中自行编写的**自定义事件和指标**，用于跟踪业务事件（例如销售的商品或赢得的游戏）。
应用程序的体系结构通常由许多组件构成 – 其中可能包括虚拟机、存储帐户、虚拟网络、Web 应用、数据库、数据库服务器和第三方服务。 这些组件不会以独立的实体出现，而是以单个实体的相关部件和依赖部件出现。 如果希望以组的方式部署、管理和监视这些这些组件， 可以使用 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 以组的方式处理解决方案中的资源。

可以通过一个协调的操作为解决方案部署、更新或删除所有资源。 可以使用一个模板来完成部署，该模板适用于不同的环境，例如测试、过渡和生产。 Resource Manager 提供安全、审核和标记功能，以帮助你在部署后管理资源。

**使用 Resource Manager 的优势**

Resource Manager 提供多种优势：

- 可以以组的形式部署、管理和监视解决方案的所有资源，而不是单独处理这些资源。

- 可以在整个开发生命周期内重复部署解决方案，并确保以一致的状态部署资源。

- 可以通过声明性模板而非脚本来管理基础结构。

- 可以定义各资源之间的依赖关系，使其按正确的顺序进行部署。

- 可以将访问控制应用到资源组中的所有服务，因为基于角色的访问控制 (RBAC) 已在本机集成到管理平台。

- 可以将标记应用到资源，以逻辑方式组织订阅中的所有资源。

- 可以通过查看一组共享相同标记的资源的成本来理清组织的帐单。

> [!Note]
> Resource Manager 提供了一种新方法来部署和管理解决方案。 如果使用早期的部署模型并想了解这些更改，请参阅[了解 Resource Manager 部署和经典部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)。

## <a name="next-steps"></a>后续步骤

阅读一些深度安全性主题，了解有关安全性的更多信息：

- [审核和日志记录](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [网络犯罪](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [设计和运营安全性](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [加密](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [标识和访问管理](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [网络安全](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [威胁管理](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
