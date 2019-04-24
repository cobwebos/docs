---
title: Azure Active Directory 域服务：排查警报问题 | Microsoft Docs
description: 排查 Azure AD 域服务的警报问题
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2018
ms.author: ergreenl
ms.openlocfilehash: 7d99f5a5d027c825fa1145328bb9576229ce39b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416511"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD 域服务 - 排查警报问题
本文提供有关排查托管域中可能出现的任何警报问题的指南。


选取警报中的 ID 或消息所对应的故障排除步骤。

| **警报 ID** | **警报消息** | **解决方法** |
| --- | --- | :--- |
| AADDS001 | 已为托管域启用 Internet 上的安全 LDAP。*但是，访问端口 636 未使用网络安全组锁定。这样可能会使托管域上的用户帐户遭到密码暴力攻击。* | [不正确的安全 LDAP 配置](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | 与托管域关联的 Azure AD 目录可能已被删除。*托管域不再处于受支持的配置中。Microsoft 无法监视、管理、修补和同步托管域。* | [缺少目录](#aadds100-missing-directory) |
| AADDS101 | 无法在 Azure AD B2C 目录中启用 Azure AD 域服务。 | [Azure AD B2C 在此目录中运行](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | 已从 Azure AD 目录中删除 Azure AD 域服务正常工作所需的服务主体。此配置影响 Microsoft 监视、管理、修补和同步托管域的功能。 | [缺少服务主体](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | 已启用 Azure AD 域服务的虚拟网络的 IP 地址范围在公共 IP 范围内。*必须在具有专用 IP 地址范围的虚拟网络中启用 Azure AD 域服务。此配置影响 Microsoft 监视、管理、修补和同步托管域的功能。* | [地址在公共 IP 范围内](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | Microsoft 程序无法访问此托管域的域控制器。*如果虚拟网络上配置的网络安全组 (NSG) 阻止访问托管域，则可能会发生这种情况。另一个可能的原因为，如果有用户定义的路由阻止来自 Internet 的传入流量。* | [网络错误](active-directory-ds-troubleshoot-nsg.md) |
| AADDS105 | *应用程序 ID 为“d87dcbc6-a371-462e-88e3-28ad15ec4e64”的服务主体已删除并重新创建。重新创建会导致在为托管域提供服务所需的 Azure AD 域服务资源上出现不一致的权限。托管域上的密码同步可能会受影响。* | [密码同步应用程序已过期](active-directory-ds-troubleshoot-service-principals.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS106 | 与托管域关联的 Azure 订阅已删除。Azure AD 域服务需要有效的订阅才能继续正常工作。 | [找不到 Azure 订阅](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | 与托管域关联的 Azure 订阅处于非活动状态。Azure AD 域服务需要有效的订阅才能继续正常工作。 | [Azure 订阅已禁用](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | Azure AD 域服务使用的订阅已移到另一个目录。Azure AD 域服务需在同一目录中具有活动的订阅才能正常运行。 | [在目录中移动了订阅](#aadds108-subscription-moved-directories) |
| AADDS109 | 用于托管域的资源已删除。需要此资源才能让 Azure AD 域服务正常运行。 | [资源已删除](#aadds109-resources-for-your-managed-domain-cannot-be-found) |
| AADDS110 | 选择用于部署 Azure AD 域服务的子网已满，没有空间用于保留需要创建的其他域控制器。 | [子网已满](#aadds110-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS111 | Azure AD 域服务用来为域提供服务的服务主体无权管理 Azure 订阅中的资源。该服务主体需要获取权限才能为托管域提供服务。 | [服务主体未经授权](#aadds111-service-principal-unauthorized) |
| AADDS112 | *我们发现，此域中虚拟网络的子网可能没有足够的 IP 地址。Azure AD 域服务在启用它的子网中至少需要两个可用的 IP 地址。我们建议在该子网中至少提供 3-5 个备用 IP 地址。如果在该子网中部署了其他虚拟机，从而耗尽了可用的 IP 地址，或者子网中可用 IP 地址数量有限制，则可能会发生这种情况。* | [IP 地址不足](#aadds112-not-enough-ip-address-in-the-managed-domain) |
| AADDS113 | 检测到 Azure AD 域服务使用的资源处于意外状态，且无法恢复。 | [资源不可恢复](#aadds113-resources-are-unrecoverable) |
| AADDS114 | 为 Azure AD 域服务部署选择的子网无效，且不可用。 | [子网无效](#aadds114-subnet-invalid) |
| AADDS115 | 由于目标范围已锁定，托管域使用的一个或多个网络资源无法运行。 | [资源已锁定](#aadds115-resources-are-locked) |
| AADDS116 | 由于策略限制，托管域使用的一个或多个网络资源无法运行。 | [资源不可用](#aadds116-resources-are-unusable) |
| AADDS500 | *托管域上次于 [date] 与 Azure AD 进行同步。用户可能无法登录到托管域，或者组成员身份可能未与 Azure AD 同步。* | [已在一段时间内未进行同步](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *托管域上次于 [date] 进行备份。* | [已在一段时间内未执行备份](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | 托管域的安全 LDAP 证书将于 [date] 到期。 | [安全 LDAP 证书即将到期](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *由于与域关联的 Azure 订阅未处于活动状态，托管域已暂停。* | [由于订阅禁用而暂停](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *由于配置无效，托管域已暂停。服务已很长时间无法为托管域管理、修补或更新域控制器。* | [由于配置无效而暂停](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100：缺少目录
**警报消息：**

与托管域关联的 Azure AD 目录可能已被删除。*托管域不再处于受支持的配置中。Microsoft 无法监视、管理、修补和同步托管域。*

**解决方法：**

出现此错误通常因为错误地将 Azure 订阅移动到新的 Azure AD 目录，并删除了仍与 Azure AD 域服务关联的旧 Azure AD 目录。

此错误无法恢复。 要解决此问题，必须[删除现有托管域](active-directory-ds-disable-aadds.md)，并在新目录中重新创建。 如果在进行删除时遇到问题惑，请联系 Azure Active Directory 域服务产品团队[请求支持](active-directory-ds-contact-us.md)。

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101：Azure AD B2C 在此目录中运行
**警报消息：**

无法在 Azure AD B2C 目录中启用 Azure AD 域服务。

**解决方法：**

>[!NOTE]
>要继续使用 Azure AD 域服务，必须在非 Azure AD B2C 目录中重新创建 Azure AD 域服务实例。

要还原服务，请按照以下步骤操作：

1. 从现有 Azure AD 目录中[删除托管域](active-directory-ds-disable-aadds.md)。
2. 创建并非 Azure AD B2C 目录的新目录。
3. 按照[入门](active-directory-ds-getting-started.md)指南重新创建托管域。

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103：地址在公共 IP 范围内

**警报消息：**

已启用 Azure AD 域服务的虚拟网络的 IP 地址范围在公共 IP 范围内。*必须在具有专用 IP 地址范围的虚拟网络中启用 Azure AD 域服务。此配置影响 Microsoft 监视、管理、修补和同步托管域的功能。*

**解决方法：**

> [!NOTE]
> 要解决此问题，必须删除现有托管域，然后在属于专用 IP 地址范围的虚拟网络中重新创建。 此过程是中断的。

开始之前，请阅读[本文](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)的“专用 IP v4 地址空间”部分。

在虚拟网络内部，计算机可能会向为子网配置的资源在同一 IP 地址范围的 Azure 资源发出请求。 但是，由于为此范围配置了虚拟网络，因此这些请求将在虚拟网络中进行路由，而不会到达预期的 Web 资源。 此配置可能导致 Azure AD 域服务出现不可预知的错误。

**如果你在 Internet 中拥有为虚拟网络配置的 IP 地址范围，则可以忽略此警报。但是，Azure AD 域服务使用此配置无法承诺 [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)，因为它可能会导致不可预知的错误。**


1. 从目录中[删除托管域](active-directory-ds-disable-aadds.md)。
2. 修复子网的 IP 地址范围
   1. 导航到 [Azure 门户中的虚拟网络页](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks)。
   2. 选择要用于 Azure AD 域服务的虚拟网络。
   3. 单击“设置”下的“地址空间”
   4. 单击并编辑现有地址范围，或添加其他地址范围，以更新地址范围。 请确保新的地址范围在专用 IP 范围内。 保存所做更改。
   5. 在左侧导航栏中，单击“子网”。
   6. 单击要在表中编辑的子网。
   7. 更新地址范围并保存更改。
3. 按照[开始使用 Azure AD 域服务指南](active-directory-ds-getting-started.md)重新创建托管域。 请确保选取属于专用 IP 地址范围的虚拟网络。
4. 要将虚拟机加入新的域，请按照[本指南](active-directory-ds-admin-guide-join-windows-vm-portal.md)操作。
8. 若要确保警报已解决，请在两个小时内检查域的运行状况。

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106：找不到 Azure 订阅

**警报消息：**

与托管域关联的 Azure 订阅已删除。Azure AD 域服务需要有效的订阅才能继续正常工作。

**解决方法：**

Azure AD 域服务需要订阅功能，并且无法移到其他订阅。 由于删除了与托管域关联的 Azure 订阅，需要重新创建 Azure 订阅和 Azure AD 域服务。

1. 创建 Azure 订阅
2. 从现有 Azure AD 目录中[删除托管域](active-directory-ds-disable-aadds.md)。
3. 按照[入门](active-directory-ds-getting-started.md)指南重新创建托管域。

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107：Azure 订阅已禁用

**警报消息：**

与托管域关联的 Azure 订阅处于非活动状态。Azure AD 域服务需要有效的订阅才能继续正常工作。

**解决方法：**


1. [续订 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)。
2. 续订订阅后，Azure AD 域服务将从 Azure 收到重新启用托管域的通知。

## <a name="aadds108-subscription-moved-directories"></a>AADDS108：订阅已移至其他目录

**警报消息：**

Azure AD 域服务使用的订阅已移到另一个目录。Azure AD 域服务需在同一目录中具有活动的订阅才能正常运行。

**解决方法：**

可将与 Azure AD 域服务关联的订阅移回到以前的目录，或者从现有目录中[删除托管域](active-directory-ds-disable-aadds.md)并在所选的目录中重新创建它（使用新订阅，或更改 Azure AD 域服务实例所在的目录）。

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109：找不到托管域的资源

**警报消息：**

用于托管域的资源已删除。需要此资源才能让 Azure AD 域服务正常运行。

**解决方法：**

Azure AD 域服务在部署时会创建特定的资源（包括公共 IP 地址、NIC 和负载均衡器），以便正常运行。 如果删除了上述任一资源，则会导致托管域处于不受支持的状态，并阻止管理你的域。 当有权编辑 Azure AD 域服务资源的某个用户删除了所需的资源时，便会出现此警报。 以下步骤概述了如何还原托管域。

1. 导航到 Azure AD 域服务运行状况页
   1.    在 Azure 门户中导航到 [Azure AD 域服务页](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)。
   2.    在左侧导航栏中，单击“运行状况”
2. 检查警报发送时间是否不超过 4 小时
   1.    在运行状况页上，单击 ID 为 **AADDS109** 的警报
   2.    警报包含一个时间戳，指出了首次发出该警报的时间。 如果该时间戳不超过 4 小时，则 Azure AD 域服务有机会重新创建已删除的资源。
3. 如果警报超过 4 小时，则托管域将处于不可恢复状态。 必须删除 Azure AD 域服务并重新创建。


## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110：与托管域关联的子网已满

**警报消息：**

选择用于部署 Azure AD 域服务的子网已满，没有空间用于保留需要创建的其他域控制器。

**解决方法：**

此错误无法恢复。 若要解决此问题，必须[删除现有托管域](active-directory-ds-disable-aadds.md)并[重新创建托管域](active-directory-ds-getting-started.md)。

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111：服务主体未经授权

**警报消息：**

Azure AD 域服务用来为域提供服务的服务主体无权管理 Azure 订阅中的资源。该服务主体需要获取权限才能为托管域提供服务。

**解决方法：**

服务主体需要访问权限才能在托管域中管理和创建资源。 某人拒绝了服务主体访问权限，该服务主体现在无法管理资源。 遵循向服务主体授予访问权限的步骤。

1. 了解 [RBAC 控制以及如何在 Azure 门户中授予对应用程序的访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
2. 评审 ID 为 ```abba844e-bc0e-44b0-947a-dc74e5d09022``` 的服务主体的访问权限，并授予在以前的某个日期拒绝的访问权限。


## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112：托管域中没有足够的 IP 地址

**警报消息：**

*我们发现，此域中虚拟网络的子网可能没有足够的 IP 地址。Azure AD 域服务在启用它的子网中至少需要两个可用的 IP 地址。我们建议在该子网中至少提供 3-5 个备用 IP 地址。如果在该子网中部署了其他虚拟机，从而耗尽了可用的 IP 地址，或者子网中可用 IP 地址数量有限制，则可能会发生这种情况。*

**解决方法：**

1. 从租户中删除托管域。
2. 修复子网的 IP 地址范围
   1. 导航到 [Azure 门户中的虚拟网络页](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks)。
   2. 选择要用于 Azure AD 域服务的虚拟网络。
   3. 单击“设置”下的“地址空间”
   4. 单击并编辑现有地址范围，或添加其他地址范围，以更新地址范围。 保存所做更改。
   5. 在左侧导航栏中，单击“子网”。
   6. 单击要在表中编辑的子网。
   7. 更新地址范围并保存更改。
3. 按照[开始使用 Azure AD 域服务指南](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)重新创建托管域。 请确保选取属于专用 IP 地址范围的虚拟网络。
4. 要将虚拟机加入新的域，请按照[本指南](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal)操作。
5. 在两小时后检查域的运行状况，确保已正确完成相关步骤。

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113：资源不可恢复

**警报消息：**

检测到 Azure AD 域服务使用的资源处于意外状态，且无法恢复。

**解决方法：**

此错误无法恢复。 若要解决此问题，必须[删除现有托管域](active-directory-ds-disable-aadds.md)并[重新创建托管域](active-directory-ds-getting-started.md)。

## <a name="aadds114-subnet-invalid"></a>AADDS114：子网无效

**警报消息：**

为 Azure AD 域服务部署选择的子网无效，且不可用。

**解决方法：**

此错误无法恢复。 若要解决此问题，必须[删除现有托管域](active-directory-ds-disable-aadds.md)并[重新创建托管域](active-directory-ds-getting-started.md)。

## <a name="aadds115-resources-are-locked"></a>AADDS115：资源已锁定

**警报消息：**

由于目标范围已锁定，托管域使用的一个或多个网络资源无法运行。

**解决方法：**

1.  查看有关网络资源的资源管理器操作日志（其中应会提供有关哪个锁正在阻止修改的信息）。
2.  删除资源中的锁，使 Azure AD 域服务服务主体能够在这些资源上运行。

## <a name="aadds116-resources-are-unusable"></a>AADDS116：资源不可用

**警报消息：**

由于策略限制，托管域使用的一个或多个网络资源无法运行。

**解决方法：**

1.  查看有关托管域网络资源的资源管理器操作日志。
2.  降低对资源的策略限制，使 AAD-DS 服务主体可在这些资源上运行。



## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500：同步在一段时间内未完成

**警报消息：**

*托管域上次于 [date] 与 Azure AD 进行同步。用户可能无法登录到托管域，或者组成员身份可能未与 Azure AD 同步。*

**解决方法：**

[检查域的运行状况](active-directory-ds-check-health.md)，看是否有任何警报指示托管域的配置有问题。 有时，配置问题可能会阻止 Microsoft 对托管域进行同步操作。 如果能够解决任何警报，请等待两个小时再返回检查同步是否已已完成。

以下是托管域停止同步的一些常见原因：
- 托管域上的网络连接受阻。 若要了解有关检查网络是否存在问题的详细信息，请参阅如何[对网络安全组进行故障排除](active-directory-ds-troubleshoot-nsg.md)以及 [Azure AD 域服务的网络要求](active-directory-ds-networking.md)。
-  从未设置或完成密码同步。 若要设置密码同步，请阅读[本文](active-directory-ds-getting-started-password-sync.md)。

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501：已在一段时间内未执行备份

**警报消息：**

*托管域上次于 [date] 进行备份。*

**解决方法：**

[检查域的运行状况](active-directory-ds-check-health.md)，看是否有任何警报指示托管域的配置有问题。 有时，配置问题可能会阻止 Microsoft 备份托管域。 如果能够解决任何警报，请等待两个小时再返回检查备份是否已完成。


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503：由于订阅禁用而暂停

**警报消息：**

*由于与域关联的 Azure 订阅未处于活动状态，托管域已暂停。*

**解决方法：**

> [!WARNING]
> 如果托管域挂起很长一段时间，则很有可能会将其删除。 最好尽快解决挂起问题。 若要了解详细信息，请访问[本文](active-directory-ds-suspension.md)。

若要还原服务，请[续订与托管域关联的 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable)。

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504：由于配置无效而暂停

**警报消息：**

*由于配置无效，托管域已暂停。服务已很长时间无法为托管域管理、修补或更新域控制器。*

**解决方法：**

> [!WARNING]
> 如果托管域挂起很长一段时间，则很有可能会将其删除。 最好尽快解决挂起问题。 若要了解详细信息，请访问[本文](active-directory-ds-suspension.md)。

[检查域的运行状况](active-directory-ds-check-health.md)，看是否有任何警报指示托管域的配置有问题。 如果可以解决其中任何警报，请这样做。 之后，请联系支持人员以重新启用订阅。


## <a name="contact-us"></a>联系我们
欢迎联系 Azure Active Directory 域服务产品团队[分享看法或请求支持](active-directory-ds-contact-us.md)。
