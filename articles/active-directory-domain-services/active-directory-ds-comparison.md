---
title: "Azure AD 域服务：Azure AD 域服务与 DIY 域控制器的比较 | Microsoft 文档"
description: "Azure Active Directory 域服务与 DIY 域控制器的比较"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 165249d5-e0e7-4ed1-aa26-91a05a87bdc9
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 09a68c7f4e7169a6ca02e33e89e0f048155fa88c
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017

---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>如何确定 Azure AD 域服务是否适合具体的用例
凭借 Azure AD 域服务，可以在 Azure 基础结构服务中部署工作负荷，而无需担心如何维护 Azure 中的标识基础结构。 此托管服务不同于自行部署和管理的典型 Windows Server Active Directory 部署。 此服务易于部署，并提供自动化的运行状况监视和修正。 我们正在持续改进该服务，以求添加对常见部署方案的支持。

若要决定是否使用 Azure AD 域服务，我们建议阅读以下材料：

* 请参阅 [features offered by Azure AD Domain Services](active-directory-ds-features.md)（Azure AD 域服务提供的功能列表）。
* 查看 [Azure AD 域服务的常见部署方案](active-directory-ds-scenarios.md)。
* 最后，[将 Azure AD 域服务与 DIY AD 选项进行比较](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure)。

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Azure AD 域服务与 Azure 中 DIY AD 域的比较
下表可帮助确定是要使用 Azure AD 域服务还是要在 Azure 中管理自己的 AD 基础结构。

| **功能** | **Azure AD 域服务** | **Azure VM 中的 DIY AD** |
| --- |:---:|:---:|
| [**托管服务**](active-directory-ds-comparison.md#managed-service) |**&#x2713;** |**&#x2715;** |
| [**安全部署**](active-directory-ds-comparison.md#secure-deployments) |**&#x2713;** |管理员需要保护部署。 |
| [**DNS 服务器**](active-directory-ds-comparison.md#dns-server) |**&#x2713;**（托管服务） |**&#x2713;** |
| [**域管理员或企业管理员权限**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges) |**&#x2715;** |**&#x2713;** |
| [**域加入**](active-directory-ds-comparison.md#domain-join) |**&#x2713;** |**&#x2713;** |
| [**使用 NTLM 和 Kerberos 进行域身份验证**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;** |**&#x2713;** |
| [**Kerberos 约束委派**](active-directory-ds-comparison.md#kerberos-constrained-delegation)|基于资源|基于资源和基于帐户|
| [**自定义 OU 结构**](active-directory-ds-comparison.md#custom-ou-structure) |**&#x2713;** |**&#x2713;** |
| [**架构扩展**](active-directory-ds-comparison.md#schema-extensions) |**&#x2715;** |**&#x2713;** |
| [**AD 域/林信任**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts) |**&#x2715;** |**&#x2713;** |
| [**LDAP 读取**](active-directory-ds-comparison.md#ldap-read) |**&#x2713;** |**&#x2713;** |
| [**安全 LDAP (LDAPS)**](active-directory-ds-comparison.md#secure-ldap) |**&#x2713;** |**&#x2713;** |
| [**LDAP 写入**](active-directory-ds-comparison.md#ldap-write) |**&#x2715;** |**&#x2713;** |
| [**组策略**](active-directory-ds-comparison.md#group-policy) |**&#x2713;** |**&#x2713;** |
| [**地理分散的部署**](active-directory-ds-comparison.md#geo-dispersed-deployments) |**&#x2715;** |**&#x2713;** |

#### <a name="managed-service"></a>托管服务
Azure AD 域服务域由 Microsoft 管理。 用户无需考虑如何修补、更新、监视、备份域，以及确保域的可用性。 Microsoft Azure 将以服务的形式针对托管域提供这些管理任务。

#### <a name="secure-deployments"></a>安全部署
托管域是根据 Microsoft 针对 AD 部署的安全建议被安全锁定的。 这些建议源于 AD 产品团队数十年的 AD 部署工程设计和支持经验。 在 DIY 部署中，需要执行特定的部署步骤才能锁定/保护部署。

#### <a name="dns-server"></a>DNS 服务器
Azure AD 域服务管理的域包含 DNS 托管服务。 “AAD DC 管理员”组的成员可以管理托管域上的 DNS。 此组的成员在托管域上拥有完全 DNS 管理权限。 可以使用远程服务器管理工具 (RSAT) 包中包含“DNS 管理控制台”执行 DNS 管理。
[详细信息](active-directory-ds-admin-guide-administer-dns.md)

#### <a name="domain-or-enterprise-administrator-privileges"></a>域管理员或企业管理员权限
AAD DS 托管域上未提供这些提升的权限。 无法针对 AAD-DS 托管域部署需要这些提升的权限的应用程序。 委托的管理组（称为“AAD DC 管理员”）的成员拥有少量的管理权限。 这些权限包括配置 DNS、配置组策略、获取已加入域的计算机上的管理员权限，等等。

#### <a name="domain-join"></a>域加入
可以将虚拟机加入托管域，方法类似于将计算机加入 AD 域。

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>使用 NTLM 和 Kerberos 进行域身份验证
通过 Azure AD 域服务，可以使用企业凭据向托管域进行身份验证。 证书与 Azure AD 租户保持同步。 对于已同步的租户，Azure AD Connect 可确保本地的凭据更改同步到 Azure AD。 使用 DIY 域设置时，可能需要设置与你的本地 AD 之间的 AD 域信任关系，使用户能够使用其企业凭据进行身份验证。 或者，可能需要设置 AD 复制，确保将用户密码同步到 Azure 域控制器虚拟机。

#### <a name="kerberos-constrained-delegation"></a>Kerberos 约束委派
你没有 Active Directory 域服务托管域的“域管理员”权限。 因此无法配置基于帐户的（传统）Kerberos 约束委派。 但可配置更安全的基于资源的约束委派。
[详细信息](active-directory-ds-enable-kcd.md)

#### <a name="custom-ou-structure"></a>自定义 OU 结构
“AAD DC 管理员”组的成员可以在托管域中创建自定义 OU。 创建自定义 OU 的用户将获取 OU 的完全管理权限。
[详细信息](active-directory-ds-admin-guide-create-ou.md)

#### <a name="schema-extensions"></a>架构扩展
无法扩展 Azure AD 域服务托管域的基本架构。 因此，依赖于 AD 架构扩展（例如，用户对象下面的新属性）的应用程序无法即时转移到 AAD DS 域。

#### <a name="ad-domain-or-forest-trusts"></a>AD 域或林信任
无法通过配置托管域来设置与其他域的信任关系（入站/出站）。 因此资源林部署方案无法使用 Azure AD 域服务。 同样，在不想将密码同步到 Azure AD 的部署中无法使用 Azure AD 域服务。

#### <a name="ldap-read"></a>LDAP 读取
托管域支持 LDAP 读取工作负荷。 因此，可以部署针对托管域执行 LDAP 读取操作的应用程序。

#### <a name="secure-ldap"></a>安全 LDAP
可以配置 Azure AD 域服务，提供对托管域的安全 LDAP 访问访问，包括通过 Internet 访问。
[详细信息](active-directory-ds-admin-guide-configure-secure-ldap.md)

#### <a name="ldap-write"></a>LDAP 写入
托管域对用户对象而言是只读的。 因此，对用户对象的属性执行 LDAP 写入操作的应用程序不适用于托管域。 此外，无法在托管域内部更改用户密码。 另一个不允许的操作示例是在托管域中修改组成员身份或组属性。 但是，在 Azure AD（通过 PowerShell/Azure 门户）或本地 AD 中所做的用户属性或密码更改将同步到 AAD-DS 托管域。

#### <a name="group-policy"></a>组策略
“AADDC 计算机”和“AADDC 用户”容器各有内置的 GPO。 可以自定义这些内置的 GPO 以配置组策略。 “AAD DC 管理员”组的成员还可创建自定义 GPO 并将其链接到现有的 OU（包括自定义 OU）。
[详细信息](active-directory-ds-admin-guide-administer-group-policy.md)

#### <a name="geo-dispersed-deployments"></a>地理分散的部署
Azure AD 域服务托管域可在 Azure 的单个虚拟网络中使用。 对于需要在世界各地的多个 Azure 区域中提供域控制器的方案，更好的做法可能是在 Azure IaaS VM 中设置域控制器。


## <a name="do-it-yourself-diy-ad-deployment-options"></a>DIY（“自己动手”）AD 部署选项
在某些部署用例中，可能需要用到 Windows Server AD 安装所提供的一些功能。 在这种情况下，请考虑以下 DIY 选项之一：

* **独立云域：**可以使用配置为域控制器的 Azure 虚拟机来设置独立的“云域”。 此基础结构不会与本地 AD 环境集成。 此选项要求使用一组不同的“云凭据”来登录/管理云中的 VM。
* **资源林部署：**可以使用配置为域控制器的 Azure 虚拟机在资源林拓扑中设置域。 接下来，可以配置与本地 AD 环境的 AD 信任关系。 可以将计算机 (Azure VM) 的域加入云中的此资源林。 用户身份验证通过与本地目录建立的 VPN/ExpressRoute 连接发生。
* **将本地域扩展到 Azure：**可以使用 VPN/ExpressRoute 连接将 Azure 虚拟网络连接到本地网络。 此设置使 Azure VM 能够加入本地 AD。 另一种做法是将 Azure 中本地域的副本域控制器升级为 VM。 然后，将它设置为通过与本地目录建立的 VPN/ExpressRoute 连接进行复制。 此部署模式可以有效地将本地域扩展到 Azure。

> [!NOTE]
> 你可能觉得 DIY 选项更适合你的部署用例。 欢迎各位[分享反馈](active-directory-ds-contact-us.md)，让我们了解哪些功能可在你们今后选择 Azure AD 域服务时提供帮助。 此外，这些反馈可帮助我们改进服务，使其更好地满足部署需求，适合具体的用例。
>
>

我们已发布[在 Azure 虚拟机上部署 Windows Server Active Directory 的指南](https://msdn.microsoft.com/library/azure/jj156090.aspx)，帮助客户完成 DIY 安装。

## <a name="related-content"></a>相关内容
* [功能 - Azure AD 域服务](active-directory-ds-features.md)
* [部署方案 - Azure AD 域服务](active-directory-ds-scenarios.md)
* [在 Azure 虚拟机上部署 Windows Server Active Directory 的指南](https://msdn.microsoft.com/library/azure/jj156090.aspx)

