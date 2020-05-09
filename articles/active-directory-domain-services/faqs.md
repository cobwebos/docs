---
title: 有关 Azure AD 域服务的常见问题 |Microsoft Docs
description: 阅读并了解 Azure Active Directory 域服务的配置、管理和可用性方面遇到的一些常见问题
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: 92b3fd2453a4fb121c97f8f25f1d3ca129826092
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926963"
---
# <a name="frequently-asked-questions-faqs"></a>常见问题 (FAQ)

本页回答了有关 Azure Active Directory 域服务的常见问题。

## <a name="configuration"></a>Configuration

* [是否可为单个 Azure AD 目录创建多个托管域？](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [能否在经典虚拟网络中启用 Azure AD 域服务？](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [是否可以在 Azure 资源管理器虚拟网络中启用 Azure AD 域服务？](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [是否可以将现有托管域从经典虚拟网络迁移到资源管理器虚拟网络？](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [是否可以在 Azure CSP（云解决方案提供商）订阅中启用 Azure AD 域服务？](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [能否在联合 Azure AD 目录中启用 Azure AD 域服务？我不会将密码哈希同步到 Azure AD。能否为此目录启用 Azure AD 域服务？](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [是否可以在订阅中的多个虚拟网络内使用 Azure AD 域服务？](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [是否可以使用 PowerShell 来启用 Azure AD 域服务？](#can-i-enable-azure-ad-domain-services-using-powershell)
* [能否使用资源管理器模板启用 Azure AD 域服务？](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [是否可将域控制器添加到 Azure AD 域服务托管域？](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [邀请到我的目录中的来宾用户能否使用 Azure AD 域服务？](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [是否可以将现有 Azure AD 域服务托管域移到不同的订阅、资源组、区域或虚拟网络？](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [域服务是否包含高可用性选项 Azure AD？](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>是否可为单个 Azure AD 目录创建多个托管域？
不是。 对于单个 Azure AD 目录，只能创建一个由 Azure AD 域服务提供服务的托管域。

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>能否在经典虚拟网络中启用 Azure AD 域服务？
新部署不支持经典虚拟网络。 在2023年3月1日之前，将继续支持经典虚拟网络中部署的现有托管域。 对于现有部署，可以将[Azure AD 的域服务从经典虚拟网络模型迁移到资源管理器](migrate-from-classic-vnet.md)。

有关详细信息，请参阅[官方弃用通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)。

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>是否可以在 Azure 资源管理器虚拟网络中启用 Azure AD 域服务？
是的。 可以在 Azure 资源管理器虚拟网络中启用 Azure AD 域服务。 创建托管域时，经典 Azure 虚拟网络不再可用。

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>是否可以将现有托管域从经典虚拟网络迁移到资源管理器虚拟网络？
是的。 有关详细信息，请参阅[将 Azure AD 域服务从经典虚拟网络模型迁移到资源管理器](migrate-from-classic-vnet.md)。

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>是否可以在 Azure CSP（云解决方案提供商）订阅中启用 Azure AD 域服务？
是的。 有关详细信息，请参阅[如何在 AZURE CSP 订阅中启用 Azure AD 域服务](csp.md)。

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>能否启用联合 Azure AD 目录中的 Azure AD 域服务？ 我不会将密码哈希同步到 Azure AD。 能否为此目录启用 Azure AD 域服务？
不是。 若要通过 NTLM 或 Kerberos 对用户进行身份验证，Azure AD 域服务需要访问用户帐户的密码哈希。 在联合目录中，密码哈希不会存储在 Azure AD 目录中。 因此，Azure AD 域服务不能与此类 Azure AD 目录一起使用。

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>是否可以在订阅中的多个虚拟网络内使用 Azure AD 域服务？
服务本身不会直接支持这种情况。 托管域每次只能在一个虚拟网络中使用。 但是，你可以配置多个虚拟网络之间的连接，以向其他虚拟网络公开 Azure AD 域服务。 有关详细信息，请参阅如何使用 VPN 网关或[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)[在 Azure 中连接虚拟网络](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)。

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>是否可以使用 PowerShell 来启用 Azure AD 域服务？
是的。 有关详细信息，请参阅[如何使用 PowerShell 启用 Azure AD 域服务](powershell-create-instance.md)。

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>是否可以使用资源管理器模板来启用 Azure AD 域服务？
是的，你可以使用资源管理器模板创建 Azure AD 域服务托管域。 在部署模板之前，必须使用 Azure 门户或 Azure PowerShell 创建用于管理的服务主体和 Azure AD 组。 有关详细信息，请参阅[使用 Azure 资源管理器模板创建 AZURE AD DS 托管域](template-create-instance.md)。 在 Azure 门户中创建 Azure AD 域服务托管域时，还可以选择导出该模板以便与其他部署一起使用。

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>是否可将域控制器添加到 Azure AD 域服务托管域？
不是。 Azure AD 域服务提供的域是托管域。 不需要设置、配置或以其他方式管理此域的域控制器。 这些管理活动是由 Microsoft 提供的服务。 因此，不能添加托管域的其他域控制器（读写或只读）。

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>邀请到我的目录中的来宾用户能否使用 Azure AD 域服务？
不是。 使用 [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) 邀请进程邀请到 Azure AD 目录的来宾用户会同步到 Azure Active Directory 域服务托管域。 但是，这些用户的密码未存储在 Azure AD 目录中。 因此，Azure AD 域服务无法将这些用户的 NTLM 和 Kerberos 哈希同步到托管域。 此类用户无法登录或将计算机加入托管域。

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>是否可以将现有 Azure AD 域服务托管域移到不同的订阅、资源组、区域或虚拟网络？
不是。 创建 Azure AD 域服务托管域后，不能将实例移动到不同的资源组、虚拟网络、订阅等。部署 Azure AD DS 实例时，请注意选择最适当的订阅、资源组、区域和虚拟网络。

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>域服务是否包含高可用性选项 Azure AD？

是的。 每个 Azure AD 域服务托管域包括两个域控制器。 不管理或连接到这些域控制器，它们是托管服务的一部分。 如果将 Azure AD 域服务部署到支持可用性区域的区域，则域控制器跨区域分布。 在不支持可用性区域的区域中，域控制器分布于多个可用性集。 你没有针对此分发的配置选项或管理控制。 有关详细信息，请参阅[Azure 中虚拟机的可用性选项](../virtual-machines/windows/availability.md)。

## <a name="administration-and-operations"></a>管理和操作

* [是否可以使用远程桌面连接到托管域的域控制器？](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [我已启用 Azure AD 域服务。使用哪种用户帐户将计算机加入到此域中？](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [我是否具有 Azure AD 域服务提供的托管域的域管理员特权？](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [能否在托管域上使用 LDAP 或其他 AD 管理工具修改组成员身份？](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [对 Azure AD 目录的更改需要多长时间才可在托管域中显示？](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [能否扩展 Azure AD 域服务提供的托管域的架构？](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [是否可以在托管域中修改或添加 DNS 记录？](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [什么是托管域上的密码生存期策略？](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Azure AD 域服务是否提供 AD 帐户锁定保护？](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [能否在 Azure AD 域服务中配置分布式文件系统（DFS）和复制？](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>是否可以使用远程桌面连接到托管域的域控制器？
不是。 你没有权限使用远程桌面连接到托管域的域控制器。 *AAD DC 管理员*组的成员可以使用 ad 管理工具（例如 Active Directory 管理中心（ADAC）或 ad PowerShell）来管理托管域。 这些工具是使用已加入托管域的 Windows 服务器上的*远程服务器管理工具*功能安装的。 有关详细信息，请参阅[创建管理 VM 以配置和管理 Azure AD 域服务托管域](tutorial-create-management-vm.md)。

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>我已启用 Azure AD 域服务。 应使用哪个用户帐户将计算机加入此域？
属于 Azure AD DS 托管域的任何用户帐户都可以加入 VM。 *AAD DC 管理员*组的成员被授予对已加入托管域的计算机的远程桌面访问权限。

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>我是否具有 Azure AD 域服务提供的托管域的域管理员特权？
不是。 你没有获得托管域的管理权限。 *域管理员*和*企业管理员*权限不可用于域中。 本地 Active Directory 中的域管理员或企业管理员组的成员也未被授予托管域上的域/企业管理员权限。

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>能否在托管域上使用 LDAP 或其他 AD 管理工具修改组成员身份？
无法修改从 Azure Active Directory 同步到 Azure AD 域服务的用户和组，因为其源源 Azure Active Directory。 可能会修改托管域中的任何用户或组。

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>对 Azure AD 目录的更改需要多长时间才可在托管域中显示？
使用 Azure AD UI 或 PowerShell 在 Azure AD 目录中所做的更改将自动同步到托管域。 此同步过程在后台运行。 没有定义用于完成所有对象更改的时间段。

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>能否扩展 Azure AD 域服务提供的托管域的架构？
不是。 托管域的架构由 Microsoft 管理。 Azure AD 域服务不支持架构扩展。

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>是否可以在托管域中修改或添加 DNS 记录？
是的。 *AAD DC Administrators*组的成员被授予*DNS 管理员*权限，以修改托管域中的 dns 记录。 这些用户可以使用已加入托管域的运行 Windows Server 的计算机上的 DNS 管理器控制台来管理 DNS。 若要使用 DNS 管理器控制台，请在服务器上安装*远程服务器管理工具*可选功能的一部分 " *dns 服务器工具*"。 有关详细信息，请参阅[管理 Azure AD 域服务托管域中的 DNS](manage-dns.md)。

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>什么是托管域上的密码生存期策略？
Azure AD 域服务托管域上的默认密码生存期为 90 天。 此密码生存期与在 Azure AD 中配置的密码生存期不同步。 因此，可能会出现用户密码在托管域中已过期，但在 Azure AD 中仍然有效的情况。 在这种情况下，用户需要更改 Azure AD 中的密码，并且将新密码同步到托管域。 此外，用户帐户的*密码不会过期*，用户帐户的 "*下次登录*" 属性不会同步到托管域。

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure AD 域服务是否提供 AD 帐户锁定保护？
是的。 在托管域上于 2 分钟内尝试五个无效密码将导致用户帐户锁定 30 分钟。 30 分钟后用户帐户将自动解锁。 托管域上的无效密码尝试不会 Azure AD 中锁定用户帐户。 仅在 Azure AD 域服务托管域中锁定用户帐户。 有关详细信息，请参阅[托管域上的密码和帐户锁定策略](password-policy.md)。

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>能否在 Azure AD 域服务内配置分布式文件系统和复制？
不是。 使用 Azure AD 域服务时，分布式文件系统（DFS）和复制不可用。

## <a name="billing-and-availability"></a>计费和可用性

* [Azure AD 域服务是付费服务吗？](#is-azure-ad-domain-services-a-paid-service)
* [该服务是否有免费试用版？](#is-there-a-free-trial-for-the-service)
* [我能否暂停 Azure AD 域服务托管域？](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [对于 DR 事件，是否可以将 Azure AD 域服务故障转移到另一个区域？](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [是否可以作为企业移动性套件（EMS）的一部分获取 Azure AD 域服务？是否需要 Azure AD Premium 才能使用 Azure AD 域服务？](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [哪些 Azure 区域提供此服务？](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Azure AD 域服务是付费服务吗？
是的。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

### <a name="is-there-a-free-trial-for-the-service"></a>该服务是否有免费试用版？
Azure 的免费试用版中包含 Azure AD 域服务。 可以注册 [Azure 一个月免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>我能否暂停 Azure AD 域服务托管域？
不是。 启用 Azure AD 域服务托管域后，该服务将在所选虚拟网络内可用，直到你删除托管域。 无法暂停服务。 删除托管域前，会按小时对服务计费。

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>对于 DR 事件，是否可以将 Azure AD 域服务故障转移到另一个区域？
不是。 Azure AD 域服务当前不提供地域冗余部署模型。 它仅限于 Azure 区域中的单个虚拟网络。 如果希望利用多个 Azure 区域，需要在 Azure IaaS VM 上运行 Active Directory 域控制器。 有关体系结构指南，请参阅将[本地 Active Directory 域扩展到 Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain)。

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>是否可以从企业移动性套件 (EMS) 获取 Azure AD 域服务？ 是否需要 Azure AD Premium 才能使用 Azure AD 域服务？
不是。 Azure AD 域服务是即用即付 Azure 服务，不属于 EMS。 Azure AD 域服务可用于所有版本的 Azure AD （免费和高级）。 按小时计费，具体取决于使用情况。

### <a name="what-azure-regions-is-the-service-available-in"></a>哪些 Azure 区域提供此服务？
请参阅[按区域列出的 Azure 服务](https://azure.microsoft.com/regions/#services/)页，获取提供 Azure AD 域服务的 Azure 区域列表。

## <a name="troubleshooting"></a>疑难解答

有关配置或管理 Azure AD 域服务的常见问题的解决方法，请参阅[疑难解答指南](troubleshoot.md)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure AD 域服务的详细信息，请参阅[什么是 Azure Active Directory 域服务？](overview.md)。

若要开始，请参阅[创建和配置 Azure Active Directory 域服务实例](tutorial-create-instance.md)。
