---
title: Azure Active Directory 域服务：排查警报问题 | Microsoft Docs
description: 排查 Azure AD 域服务的警报问题
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: ergreenl
ms.openlocfilehash: e4b8f31fe3eb79f9b38ae01af598290582a2cde3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
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
| AADDS105 | *应用程序 ID 为“d87dcbc6-a371-462e-88e3-28ad15ec4e64”的服务主体已删除，Microsoft 无法重新创建它。此服务主体管理另一个服务主体和用于密码同步的应用程序。托管的服务主体和应用程序未在新创建的服务主体下授权，在同步证书过期时将会过期。这意味着新创建的服务主体将无法更新旧的托管应用程序，从 AAD 同步对象也会受影响。* | [密码同步应用程序已过期](active-directory-ds-troubleshoot-service-principals.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS500 | *托管域上次于 [date] 与 Azure AD 进行同步。用户可能无法登录到托管域，或者组成员身份可能未与 Azure AD 同步。* | [已在一段时间内未进行同步](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *托管域上次于 [date] 进行备份。* | [已在一段时间内未执行备份](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *托管域的安全 LDAP 证书将于 XX 到期。* | [安全 LDAP 证书即将到期](active-directory-ds-troubleshoot-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
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

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500：同步在一段时间内未完成

**警报消息：**

*托管域上次于 [date] 与 Azure AD 进行同步。用户可能无法登录到托管域，或者组成员身份可能未与 Azure AD 同步。*

**解决方法：**

[检查域的运行状况](active-directory-ds-check-health.md)，看是否有任何警报指示托管域的配置有问题。 有时，配置问题可能会阻止 Microsoft 对托管域进行同步操作。 如果能够解决任何警报，请等待两个小时再返回检查同步是否已已完成。


## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501：已在一段时间内未执行备份

**警报消息：**

*托管域上次于 [date] 进行备份。*

**解决方法：**

[检查域的运行状况](active-directory-ds-check-health.md)，看是否有任何警报指示托管域的配置有问题。 有时，配置问题可能会阻止 Microsoft 对托管域进行同步操作。 如果能够解决任何警报，请等待两个小时再返回检查同步是否已已完成。


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503：由于订阅禁用而暂停

**警报消息：**

*由于与域关联的 Azure 订阅未处于活动状态，托管域已暂停。*

**解决方法：**

若要还原服务，请[续订与托管域关联的 Azure 订阅](https://docs.microsoft.com/en-us/azure/billing/billing-subscription-become-disable)。

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504：由于配置无效而暂停

**警报消息：**

*由于配置无效，托管域已暂停。服务已很长时间无法为托管域管理、修补或更新域控制器。*

**解决方法：**

[检查域的运行状况](active-directory-ds-check-health.md)，看是否有任何警报指示托管域的配置有问题。 如果可以解决其中任何警报，请这样做。 之后，请联系支持人员以重新启用订阅。

## <a name="contact-us"></a>联系我们
欢迎联系 Azure Active Directory 域服务产品团队[分享看法或请求支持](active-directory-ds-contact-us.md)。
