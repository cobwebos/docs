---
title: "常见问题 - Azure Active Directory 域服务 | Microsoft 文档"
description: "有关 Azure Active Directory 域服务的常见问题"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 5e6bab265b2b6eabd1a878492588c4eb39d1b332
ms.openlocfilehash: 89dfabb8feafffee2ed8143c372b53d02033d582


---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory 域服务：常见问题 (FAQ)
本页面解答有关 Azure Active Directory 域服务的常见问题。 请随时返回查看更新信息。

### <a name="troubleshooting-guide"></a>故障排除指南
有关配置或管理 Azure AD 域服务时遇到的常见问题的解决方法，请参阅[故障排除指南](active-directory-ds-troubleshooting.md)。

### <a name="configuration"></a>配置
#### <a name="can-i-create-multiple-domains-for-a-single-azure-ad-directory"></a>是否可为单个 Azure AD 目录创建多个域？
不会。 对于单个 Azure AD 目录，只能创建一个由 Azure AD 域服务提供服务的域。  

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>是否可以在 Azure Resource Manager 虚拟网络中启用 Azure AD 域服务？
不会。 只能在经典 Azure 虚拟网络中启用 Azure AD 域服务。 可以使用虚拟网络对等互连将经典虚拟网络连接到 Resource Manager 虚拟网络，然后便可以在 Resource Manager 虚拟网络中使用托管域。

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>是否可以在订阅中的多个虚拟网络内使用 Azure AD 域服务？
域服务本身无法直接支持这种方案。 Azure AD 域服务每次只能在一个虚拟网络中使用。 但是，可以在多个虚拟网络之间配置连接，将 Azure AD 域服务公开到其他虚拟网络。 有关操作方法，请参阅 [connect virtual networks in Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)（在 Azure 中连接虚拟网络）一文。

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>是否可以使用 PowerShell 来启用 Azure AD 域服务？
目前不支持通过 PowerShell/自动化部署 Azure AD 域服务。

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal"></a>是否可以在新 Azure 门户中使用 Azure AD 域服务？
不会。 只能在 [Azure 经典门户](https://manage.windowsazure.com)中配置 Azure AD 域服务。 将来有望添加对 [Azure 门户](https://portal.azure.com)的支持。

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>是否可将域控制器添加到 Azure AD 域服务托管域？
不会。 Azure AD 域服务提供的域是托管域。 无需预配、配置或管理此域的域控制器 - Microsoft 以服务的形式提供这些管理活动。 因此，无法为托管域添加其他域控制器（读写或只读）。

### <a name="administration-and-operations"></a>管理和操作
#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>是否可以使用远程桌面连接到托管域的域控制器？
不会。 你没有权限通过远程桌面连接到托管域的域控制器。 “AAD DC 管理员”组的成员可以使用 AD 管理工具，例如 Active Directory 管理中心 (ADAC) 或 AD PowerShell 来管理托管域。 可使用“远程服务器管理工具”功能在加入托管域的 Windows 服务器上安装这些工具。

#### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>我已启用 Azure AD 域服务。 应使用哪个用户帐户将计算机加入此域？
已添加到管理组（例如“AAD DC 管理员”）的用户都可以将计算机加入域。 此外，此组中的用户有权通过远程桌面访问已加入域的计算机。

#### <a name="can-i-wield-domain-administrator-privileges-for-the-domain-provided-by-azure-ad-domain-services"></a>是否可以针对 Azure AD 域服务提供的域行使管理员权限？
不会。 你在托管域上没有管理权限。 你不可以在该域中使用“域管理员”和“企业管理员”权限。 Azure AD 目录中现有的域管理员或企业管理员组在该域上也没有域/企业管理员权限。

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-domains-provided-by-azure-ad-domain-services"></a>是否可以在 Azure AD 域服务提供的域上使用 LDAP 或其他 AD 管理工具来修改组成员身份？
不会。 无法在 Azure AD 域服务服务的域上修改组成员身份。 这同样适用于用户属性。 但是，可以在 Azure AD 中或本地域上更改组成员身份或用户属性。 此类更改会自动同步到 Azure AD 域服务。

#### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>对 Azure AD 目录的更改需要多长时间才可在托管域中显示？
在 Azure AD 目录中使用 Azure AD UI 或 PowerShell 进行的更改将同步到托管域中。 此同步过程在后台运行。 目录的一次性初始同步完成后，Azure AD 中的更改通常需要约 20 分钟才会在托管域中反映。

#### <a name="can-i-extend-the-schema-of-the-domain-provided-by-azure-ad-domain-services"></a>是否可以扩展 Azure AD 域服务提供的域的架构？
不会。 托管域的架构由 Microsoft 管理。 Azure AD 域服务不支持架构扩展。

#### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>是否可以在托管域中修改或添加 DNS 记录？
是的。 属于“AAD DC 管理员”组的用户已被授予“DNS 管理”权限，可在托管域中修改 DNS 记录。 这些用户可以在已加入托管域且运行 Windows Server 的计算机上，使用 DNS 管理器控制台来管理 DNS。 若要使用 DNS 管理器控制台，请在服务器上安装“远程服务器管理工具”可选功能中包含的“DNS 服务器工具”。 TechNet 上提供了有关[用于管理、监视 DNS 以及对其进行故障排除的实用工具](https://technet.microsoft.com/library/cc753579.aspx)的详细信息。

### <a name="billing-and-availability"></a>计费和可用性
#### <a name="is-azure-ad-domain-services-a-paid-service"></a>Azure AD 域服务是付费服务吗？
是的。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

#### <a name="is-there-a-free-trial-for-the-service"></a>该服务是否有免费试用版？
Azure 免费试用版中包含此服务。 可以注册 [Azure 一个月免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>是否可以从企业移动性套件 (EMS) 获取 Azure AD 域服务？ 是否需要 Azure AD Premium 才能使用 Azure AD 域服务？
不会。 Azure AD 域服务是即用即付的 Azure 服务，并未包含在 EMS 中。 Azure AD 域服务可用于所有版本的 Azure AD（免费版、基本版和高级版）。 按每小时计费，具体取决于使用量。

#### <a name="what-azure-regions-is-the-service-available-in"></a>哪些 Azure 区域提供此服务？
请参阅[按区域列出的 Azure 服务](https://azure.microsoft.com/regions/#services/)页，获取提供 Azure AD 域服务的 Azure 区域列表。



<!--HONumber=Jan17_HO2-->


