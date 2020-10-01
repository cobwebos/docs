---
title: 有关 Azure AD 域服务的常见问题解答 | Microsoft Docs
description: 阅读并了解有关 Azure Active Directory 域服务的配置、管理和可用性方面的一些常见问题解答
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 09/30/2020
ms.author: iainfou
ms.openlocfilehash: 6e2daa60e99eb7aab34b11f240a2e2fb03c98582
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612398"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>有关 Azure Active Directory (AD) 域服务的常见问题 (FAQ)

本页面解答了有关 Azure Active Directory 域服务的常见问题。

## <a name="configuration"></a>配置

* [是否可为单个 Azure AD 目录创建多个托管域？](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [是否可在经典虚拟网络中启用 Azure AD 域服务？](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [是否可在 Azure 资源管理器虚拟网络中启用 Azure AD 域服务？](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [是否可将现有托管域从经典虚拟网络迁移到资源管理器虚拟网络？](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [是否可在 Azure CSP（云解决方案提供商）订阅中启用 Azure AD 域服务？](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [是否可以启用联合 Azure AD 目录中的 Azure AD 域服务？我不会将密码哈希同步到 Azure AD。是否可为此目录启用 Azure AD 域服务？](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [是否可以在订阅中的多个虚拟网络内使用 Azure AD 域服务？](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [是否可以使用 PowerShell 来启用 Azure AD 域服务？](#can-i-enable-azure-ad-domain-services-using-powershell)
* [是否可以使用资源管理器模板来启用 Azure AD 域服务？](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [是否可将域控制器添加到 Azure AD 域服务托管域？](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [是否可以邀请来宾用户使用 Azure AD 域服务的目录？](#can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services)
* [是否可将现有 Azure AD 域服务托管域移动到不同的订阅、资源组、区域或虚拟网络？](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Azure AD 域服务是否包含高可用性选项？](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>是否可为单个 Azure AD 目录创建多个托管域？
否。 对于单个 Azure AD 目录，只能创建一个由 Azure AD 域服务提供服务的托管域。

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>是否可在经典虚拟网络中启用 Azure AD 域服务？
新部署不支持经典虚拟网络。 经典虚拟网络中部署的现有托管域将继续受支持，直到 2023 年 3 月 1 日停用。 对于现有部署，你可以[将 Azure AD 域服务从经典虚拟网络模型迁移到资源管理器](migrate-from-classic-vnet.md)。

有关详细信息，请查看[正式的弃用通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)。

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>是否可以在 Azure 资源管理器虚拟网络中启用 Azure AD 域服务？
是的。 可以在 Azure 资源管理器虚拟网络中启用 Azure AD 域服务。 创建托管域时，经典 Azure 虚拟网络不再可用。

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>是否可将现有托管域从经典虚拟网络迁移到资源管理器虚拟网络？
是的。 有关详细信息，请参阅[将 Azure AD 域服务从经典虚拟网络模型迁移到资源管理器](migrate-from-classic-vnet.md)。

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>是否可以在 Azure CSP（云解决方案提供商）订阅中启用 Azure AD 域服务？
是的。 有关详细信息，请参阅[如何在 Azure CSP 订阅中启用 Azure AD 域服务](csp.md)。

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>能否启用联合 Azure AD 目录中的 Azure AD 域服务？ 我不会将密码哈希同步到 Azure AD。 能否为此目录启用 Azure AD 域服务？
否。 若要通过 NTLM 或 Kerberos 对用户进行身份验证，Azure AD 域服务需要访问用户帐户的密码哈希。 在联合目录中，密码哈希未存储于 Azure AD 目录中。 因此，Azure AD 域服务不适用于此类 Azure AD 目录。

但是，如果使用 Azure AD Connect 进行密码哈希同步，则可以使用 Azure AD 域服务，因为密码哈希值存储在 Azure AD 中。

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>是否可以在订阅中的多个虚拟网络内使用 Azure AD 域服务？
域服务本身无法直接支持这种方案。 托管域每次只能在一个虚拟网络中使用。 但是，可以在多个虚拟网络之间配置连接，将 Azure AD 域服务公开到其他虚拟网络。 有关详细信息，请参阅[如何使用 VPN 网关](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md)或[虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)连接 Azure 中的虚拟网络。

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>是否可以使用 PowerShell 来启用 Azure AD 域服务？
是的。 有关详细信息，请参阅[如何使用 PowerShell 启用 Azure AD 域服务](powershell-create-instance.md)。

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>是否可以使用资源管理器模板来启用 Azure AD 域服务？
是的，可以使用资源管理器模板创建 Azure AD 域服务托管域。 在部署模板之前，必须使用 Azure 门户或 Azure PowerShell 创建用于管理的服务主体和 Azure AD 组。 有关详细信息，请参阅[使用 Azure 资源管理器模板创建 Azure AD DS 托管域](template-create-instance.md)。 在 Azure 门户中创建 Azure AD 域服务托管域时，还可以选择导出模板以用于其他部署。

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>是否可将域控制器添加到 Azure AD 域服务托管域？
否。 Azure AD 域服务提供的域是托管域。 你不需要预配、配置或以其他方式管理此域的域控制器。 这些管理活动由 Microsoft 以服务形式提供。 因此，你无法为托管域添加其他域控制器（读写或只读）。

### <a name="can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services"></a>是否可以邀请来宾用户使用 Azure AD 域服务的目录？
否。 使用 [Azure AD B2B](../active-directory/external-identities/what-is-b2b.md) 邀请进程邀请到 Azure AD 目录的来宾用户会同步到 Azure Active Directory 域服务托管域。 但这些用户的密码不会存储在 Azure AD 目录中。 因此，Azure AD 域服务无法将这些用户的 NTLM 和 Kerberos 哈希同步到托管域。 这类用户可以登录到或者将计算机加入到托管域。

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>是否可将现有 Azure AD 域服务托管域移动到不同的订阅、资源组、区域或虚拟网络？
否。 创建 Azure AD 域服务托管域后，无法将托管域移到其他资源组、虚拟网络、订阅等位置。部署托管域时，请注意选择最合适的订阅、资源组、区域和虚拟网络。

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Azure AD 域服务是否包含高可用性选项？

是的。 每个 Azure AD 域服务托管域都包括两个域控制器。 你不需要管理或连接到这些域控制器，它们是托管服务的一部分。 如果将 Azure AD 域服务部署到支持可用性局部区域的区域中，则域控制器将分布到各个局部区域中。 在不支持可用性局部区域的区域中，域控制器将分布在多个可用性集中。 对于此分布，你无法通过选项进行配置，也无法进行管理控制。 有关详细信息，请参阅 [Azure 中虚拟机的可用性选项](../virtual-machines/availability.md)。

## <a name="administration-and-operations"></a>管理和操作

* [是否可以使用远程桌面连接到托管域的域控制器？](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [我已启用 Azure AD 域服务。应使用哪个用户帐户通过域加入方式将计算机加入此域？](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [我是否具有 Azure AD 域服务提供的托管域的域管理员特权？](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [是否可在托管域上使用 LDAP 或其他 AD 管理工具修改组成员身份？](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [对 Azure AD 目录的更改需要多长时间才可在托管域中显示？](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [能否扩展 Azure AD 域服务提供的托管域的架构？](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [是否可以在托管域中修改或添加 DNS 记录？](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [什么是托管域上的密码生存期策略？](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Azure AD 域服务是否提供 AD 帐户锁定保护？](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [是否可在 Azure AD 域服务中配置分布式文件系统 (DFS) 和复制？](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)
* [Windows 更新在 Azure AD 域服务中的应用方式是什么？](#how-are-windows-updates-applied-in-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>是否可以使用远程桌面连接到托管域的域控制器？
否。 你没有权限使用远程桌面连接到托管域的域控制器。 “AAD DC 管理员”组的成员可以使用 AD 管理工具（例如 Active Directory 管理中心 (ADAC) 或 AD PowerShell）来管理托管域。 可使用“远程服务器管理工具”功能在加入托管域的 Windows 服务器上安装这些工具。 有关详细信息，请参阅[创建一个管理 VM 来配置和管理 Azure AD 域服务托管域](tutorial-create-management-vm.md)。

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>我已启用了 Azure AD 域服务。 应使用哪个用户帐户将计算机加入此域？
属于该托管域的任何用户帐户都可以将 VM 加入域。 “AAD DC 管理员”组的成员有权通过远程桌面访问已加入托管域的计算机。

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>我是否具有 Azure AD 域服务提供的托管域的域管理员特权？
否。 你在托管域上没有管理权限。 你不可以在该域中使用“域管理员”和“企业管理员”权限。  本地 Active Directory 中的域管理员或企业管理员组成员在该托管域上也没有域/企业管理员权限。

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>能否在托管域上使用 LDAP 或其他 AD 管理工具修改组成员身份？
无法修改从 Azure Active Directory 同步到 Azure AD 域服务的用户和组，因为其来源是 Azure Active Directory。 可以修改源自托管域的任何用户或组。

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>对 Azure AD 目录的更改需要多长时间才可在托管域中显示？
在 Azure AD 目录中使用 Azure AD UI 或 PowerShell 所做的更改将自动同步到托管域中。 此同步过程在后台运行。 没有规定此同步完成所有对象更改的时间段。

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>能否扩展 Azure AD 域服务提供的托管域的架构？
否。 托管域的架构由 Microsoft 管理。 Azure AD 域服务不支持架构扩展。

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>是否可以在托管域中修改或添加 DNS 记录？
是的。 “AAD DC 管理员”组的成员具有“DNS 管理员”权限，可在托管域中修改 DNS 记录。  这些用户可以在运行已加入托管域的 Windows Server 的计算机上使用 DNS 管理器控制台来管理 DNS。 若要使用 DNS 管理器控制台，请在服务器上安装*远程服务器管理工具*可选功能的一部分的*dns 服务器工具*。 有关详细信息，请参阅[管理 Azure AD 域服务托管域中的 DNS](manage-dns.md)。

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>什么是托管域上的密码生存期策略？
Azure AD 域服务托管域上的默认密码生存期为 90 天。 此密码生存期与在 Azure AD 中配置的密码生存期不同步。 因此，可能会出现用户密码在托管域中已过期，但在 Azure AD 中仍然有效的情况。 在这种情况下，用户需要更改 Azure AD 中的密码，并且将新密码同步到托管域。 如果要更改托管域中的默认密码生存期，可以[创建并配置自定义密码策略](password-policy.md)。

此外，DisablePasswordExpiration 的 Azure AD 密码策略将同步到托管域。 当将 DisablePasswordExpiration 应用于 Azure AD 中的用户时，托管域中已同步用户的 UserAccountControl 值已应用 DONT_EXPIRE_PASSWORD  。

当用户在 Azure AD 中重置密码时，将应用 forceChangePasswordNextSignIn=True 属性。 托管域从 Azure AD 同步此属性。 当托管域检测到为来自 Azure AD 的同步用户设置了 forceChangePasswordNextSignIn 时，托管域中的 pwdLastSet 属性设置为 0，这将使当前设置的密码无效  。

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure AD 域服务是否提供 AD 帐户锁定保护？
是的。 在托管域上于 2 分钟内尝试五个无效密码将导致用户帐户锁定 30 分钟。 30 分钟后用户帐户将自动解锁。 在 Azure AD 中，在托管域上尝试无效密码不会锁定用户帐户。 仅在 Azure AD 域服务托管域中锁定用户帐户。 有关详细信息，请参阅[托管域中的密码和帐户锁定策略](password-policy.md)。

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>是否可在 Azure AD 域服务中配置分布式文件系统和复制？
否。 使用 Azure AD 域服务时，分布式文件系统 (DFS) 和复制不可用。

### <a name="how-are-windows-updates-applied-in-azure-ad-domain-services"></a>Windows 更新在 Azure AD 域服务中的应用方式是什么？
托管域中的域控制器会自动应用必需的 Windows 更新。 无需在此处进行配置或管理。 请确保不创建阻止到 Windows 更新的出站流量的网络安全组规则。 对于已加入托管域的自己的 Vm，你负责配置和应用任何所需的操作系统和应用程序更新。

## <a name="billing-and-availability"></a>计费和可用性

* [Azure AD 域服务是付费服务吗？](#is-azure-ad-domain-services-a-paid-service)
* [该服务是否有免费试用版？](#is-there-a-free-trial-for-the-service)
* [我能否暂停 Azure AD 域服务托管域？](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [是否可以将 Azure AD 域服务故障转移到灾难恢复事件的其他区域？](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [是否可以从企业移动性套件 (EMS) 获取 Azure AD 域服务？是否需要 Azure AD Premium 才能使用 Azure AD 域服务？](#can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [哪些 Azure 区域提供此服务？](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Azure AD 域服务是付费服务吗？
是的。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

### <a name="is-there-a-free-trial-for-the-service"></a>该服务是否有免费试用版？
Azure 的免费试用版中包含 Azure AD 域服务。 可以注册 [Azure 一个月免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>我能否暂停 Azure AD 域服务托管域？
否。 一旦启用 Azure AD 域服务托管域，即可在选定的虚拟网络中使用该服务，直到删除托管域为止。 无法暂停该服务。 删除托管域前，会按小时对服务计费。

### <a name="can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>是否可以将 Azure AD 域服务故障转移到灾难恢复事件的其他区域？
否。 Azure AD 域服务当前未提供异地冗余部署模型。 它被限制到 Azure 区域中的单个虚拟网络。 如果希望利用多个 Azure 区域，需要在 Azure IaaS VM 上运行 Active Directory 域控制器。 有关体系结构指南，请参阅将 [本地 Active Directory 域扩展到 Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain)。

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>是否可以从企业移动性套件 (EMS) 获取 Azure AD 域服务？ 是否需要 Azure AD Premium 才能使用 Azure AD 域服务？
否。 Azure AD 域服务是即用即付的 Azure 服务，未包含在 EMS 中。 Azure AD 域服务可用于所有版本的 Azure AD（免费版和高级版）。 它按小时计费，具体取决于使用量。

### <a name="what-azure-regions-is-the-service-available-in"></a>哪些 Azure 区域提供此服务？
请参阅[按区域列出的 Azure 服务](https://azure.microsoft.com/regions/#services/)页，获取提供 Azure AD 域服务的 Azure 区域列表。

## <a name="troubleshooting"></a>故障排除

有关配置或管理 Azure AD 域服务的常见问题的解决方法，请参阅[疑难解答指南](troubleshoot.md)。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure AD 域服务，请参阅[什么是 Azure Active Directory 域服务？](overview.md)。

若要开始使用，请参阅[创建并配置 Azure Active Directory 域服务托管域](tutorial-create-instance.md)。
