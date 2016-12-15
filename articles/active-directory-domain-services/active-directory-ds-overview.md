---
title: "Azure Active Directory 域服务概述 | Microsoft 文档"
description: "Azure Active Directory 域服务的概述"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 0d47178f-773e-45f9-9ff4-9e8cffa4ffa2
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2e78382b4cbfe7623eec1b2cd32a9eaea61e4709


---
# <a name="azure-ad-domain-services"></a>Azure AD 域服务
## <a name="overview"></a>概述
使用 Azure 基础结构服务可以灵活部署各种计算解决方案。 借助 Azure 虚拟机，几乎即时就能完成部署，并且只需按分钟付费。 使用 Windows、Linux、SQL Server、Oracle、IBM、SAP 和 BizTalk 的支持，可以在几乎任何操作系统上部署任何语言的任何工作负荷。 凭借这些优势，可以将本地的旧版应用程序迁移到 Azure，从而节省运营开支。

将本地应用程序迁移到 Azure 的一个重要方面是处理这些应用程序的标识需求。 目录感知的应用程序可以依赖于使用 LDAP 对企业目录进行读取或写入访问，或依赖于使用 Windows 集成身份验证（Kerberos 或 NTLM 身份验证）来验证最终用户的身份。 Windows Server 上运行的业务线 (LOB) 应用程序通常部署在已加入域的计算机中，因此可以使用组策略安全地对其进行管理。 若要将本地应用程序“即时转移”到云中，需要解决对企业标识基础结构的依赖关系。

管理员经常采用以下解决方案之一来满足部署在 Azure 中的应用程序的标识需求：

* 在 Azure 基础结构服务和本地企业目录中运行的工作负荷之间部署站点到站点 VPN 连接。
* 通过使用 Azure 虚拟机设置副本域控制器来扩展企业 AD 域/林基础结构。
* 使用部署为 Azure 虚拟机的域控制器在 Azure 中部署独立的域。

所有这些方法都会增加成本和管理开销。 管理员需要在 Azure 中使用虚拟机部署域控制器。 此外，他们需要管理、保护、修补、监视、备份这些虚拟机并对其进行故障排除。 过份依赖使用 VPN 连接访问本地目录，使得 Azure 中部署的工作负荷容易发生暂时性网络问题或中断。 而这些网络中断又会导致运行时间减少，降低这些应用程序的可靠性。

Azure AD 域服务经过精心设计，可以提供一种更简便的替代方法。

## <a name="introducing-azure-ad-domain-services"></a>Azure AD 域服务简介
Azure AD 域服务提供与 Windows Server Active Directory 完全兼容的托管域服务，例如域加入、组策略、LDAP、Kerberos/NTLM 身份验证。 无需在云中部署、管理和修补域控制器即可使用这些域服务。 Azure AD 域服务与现有的 Azure AD 租户集成，因此可让用户使用其企业凭据登录。 此外，可以使用现有的组和用户帐户安全访问资源，确保更顺畅地将本地资源“即时转移”到 Azure 基础结构服务。

无论 Azure AD 租户是在云中还是在本地 Active Directory 中，Azure AD 域服务功能都能顺利运行。

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>仅限云的组织的 Azure AD 域服务
仅限云的 Azure AD 租户（通常称为“托管租户”）不占用任何本地标识。 换而言之，用户帐户、其密码和组成员身份都在云本地 - 也就是说，在 Azure AD 中创建和管理。 假设 Contoso 是一个仅限云的 Azure AD 租户。 如下图所示，Contoso 的管理员已在 Azure 基础结构服务中配置虚拟网络。 应用程序和服务器工作负荷部署在此虚拟网络中的 Azure 虚拟机上。 由于 Contoso 是仅限云的租户，所有用户标识、其凭据和组成员身份都在 Azure AD 中创建和管理。

![Azure AD 域服务概述](./media/active-directory-domain-services-overview/aadds-overview.png)

Contoso 的 IT 管理员可为其 Azure AD 租户启用 Azure AD 域服务，使域服务可以在此虚拟网络中使用。 然后，Azure AD 域服务将预配一个托管域并使它可在虚拟网络中使用。 Contoso 的 Azure AD 租户中的所有用户帐户、组成员身份和用户凭据也在此新建的域中可用。 此功能可让组织中的用户使用其企业凭据登录到域，例如，通过远程桌面远程连接到已加入域的计算机。 管理员可以使用现有的组成员身份在域中预配资源访问权限。 部署在虚拟网络中的虚拟机上的应用程序可以使用域加入、LDAP 读取、LDAP 绑定、NTLM、Kerberos 身份验证、组策略等功能。

Azure AD 域服务预配的托管域的几个重要方面如下所述：

* Contoso 的 IT 管理员无需管理、修补或监视此域，或此托管域的任何域控制器。
* 无需管理此域的 AD 复制。 Contoso 的 Azure AD 租户中的用户帐户、组成员身份和凭据自动在此托管域中可用。
* 由于域由 Azure AD 域服务管理，Contoso 的 IT 管理员在此域上没有域管理员或企业管理员权限。

### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>混合型组织的 Azure AD 域服务
采用混合 IT 基础结构的组织可以混合使用云资源和本地资源。 此类组织将标识信息从其本地目录同步到其 Azure AD 租户。 如果混合型组织想要将更多的本地应用程序（尤其是传统的目录感知应用程序）迁移到云中，Azure AD 域服务可以派上用场。

Litware Corporation 已部署 [Azure AD Connect](../active-directory/active-directory-aadconnect.md)，以便将标识信息从其本地目录同步到其 Azure AD 租户。 同步的标识信息包括用户帐户、用于身份验证的凭据哈希（密码同步）和组成员身份。

> [!NOTE]
> **混合型组织必须通过密码同步使用 Azure AD 域服务**。 这是因为在 Azure AD 域服务提供的托管域中，需要使用用户的凭据才能通过 NTLM 或 Kerberos 身份验证方法来验证这些用户的身份。
> 
> 

![Litware Corporation 的 Azure AD 域服务](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

上图演示了采用混合 IT 基础结构的组织（例如 Litware Corporation）如何使用 Azure AD 域服务。 需要域服务的 Litware 应用程序和服务器工作负荷部署在 Azure 基础结构服务中的虚拟网络内。 Litware 的 IT 管理员可为其 Azure AD 租户启用 Azure AD 域服务，使托管域可以在此虚拟网络中使用。 由于 Litware 是使用混合 IT 基础结构的组织，用户帐户、组和凭据将从其本地目录同步到其 Azure AD 租户。 此功能可让用户使用其企业凭据登录域，例如，通过远程桌面远程连接到已加入域的计算机。 管理员可以使用现有的组成员身份在域中预配资源访问权限。 部署在虚拟网络中的虚拟机上的应用程序可以使用域加入、LDAP 读取、LDAP 绑定、NTLM、Kerberos 身份验证、组策略等功能。

Azure AD 域服务预配的托管域的几个重要方面如下所述：

* 托管域是独立的域。 它不是 Litware 本地域的扩展。
* Litware 的 IT 管理员无需管理、修补或监视此托管域的域控制器。
* 无需管理对此域的 AD 复制。 Litware 本地目录中的用户帐户、组成员身份和凭据通过 Azure AD Connect 同步到 Azure AD。 这些用户帐户、组成员身份和凭据在托管域中自动可用。
* 由于域由 Azure AD 域服务管理，Litware 的 IT 管理员在此域上没有域管理员或企业管理员权限。

## <a name="benefits"></a>优点
使用 Azure AD 域服务可以享受以下优势：

* **简单** – 只需按几下鼠标，就能将虚拟机部署到 Azure 基础结构服务。 无需在 Azure 中部署和管理标识基础结构，或者与本地标识基础结构建立连接。
* **集成** - Azure AD 域服务与 Azure AD 租户深度集成。 可以使用 Azure AD 作为基于云的集成式企业目录，同时迎合现代应用程序和传统的目录感知应用程序的需求。
* **兼容** - Azure AD 域服务建立在 Windows Server Active Directory 的经过证实的企业级基础结构之上。 因此，与 Windows Server Active Directory 功能的更高程度的兼容可为应用程序提供保障。 并非 Windows Server AD 中的所有功能目前都可在 Azure AD 域服务中使用。 但是，可用的功能与本地基础结构中依赖的相应 Windows Server AD 功能兼容。 LDAP、Kerberos、NTLM、组策略和域加入功能构成了一款成熟的产品，并且已在各种 Windows Server 版本经过测试和优化。
* **经济高效** – 使用 Azure AD 域服务免除了管理标识基础结构、为传统的目录感知应用程序提供支持所带来的基础结构和管理负担。 可以将这些应用程序转移到 Azure 基础结构服务，受益于运营开支的大幅节减。




<!--HONumber=Nov16_HO3-->


