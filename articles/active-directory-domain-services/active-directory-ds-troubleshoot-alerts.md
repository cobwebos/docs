---
title: "Azure Active Directory 域服务：排查警报问题 | Microsoft Docs"
description: "排查 Azure AD 域服务的警报问题"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: ergreenl
ms.openlocfilehash: 8a0b30e6c975bd8f3bfbe70a64c085b729115f24
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD 域服务 - 排查警报问题
本文提供有关排查托管域中可能出现的任何警报问题的指南。


选择对应于所遇到的警报 ID 或消息的故障排除步骤。

| **警报 ID** | **警报消息** | **解决方法** |
| --- | --- | :--- |
| AADDS001 | 已为托管域启用 Internet 上的安全 LDAP。*但是，访问端口 636 未使用网络安全组锁定。这样可能会使托管域上的用户帐户遭到密码暴力攻击。* | [不正确的安全 LDAP 配置](active-directory-ds-troubleshoot-ldaps.md) |
| AADDS100 | 与托管域关联的 Azure AD 目录可能已被删除。*托管域不再处于受支持的配置中。Microsoft 无法监视、管理、修补和同步托管域。* | [缺少目录](#aadds100-missing-directory) |
| AADDS101 | 无法在 Azure AD B2C 目录中启用 Azure AD 域服务。 | [Azure AD B2C 在此目录中运行](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | 已从 Azure AD 目录中删除 Azure AD 域服务正常工作所需的服务主体。此配置影响 Microsoft 监视、管理、修补和同步托管域的功能。 | [缺少服务主体](active-directory-ds-troubleshoot-service-principals.md) |
| AADDS103 | 已启用 Azure AD 域服务的虚拟网络的 IP 地址范围在公共 IP 范围内。*必须在具有专用 IP 地址范围的虚拟网络中启用 Azure AD 域服务。此配置影响 Microsoft 监视、管理、修补和同步托管域的功能。* | [地址在公共 IP 范围内](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | Microsoft 程序无法访问此托管域的域控制器。*如果虚拟网络上配置的网络安全组 (NSG) 阻止访问托管域，则可能会发生这种情况。另一个可能的原因为，如果有用户定义的路由阻止来自 Internet 的传入流量。* | [网络错误](active-directory-ds-troubleshoot-nsg.md) |

## <a name="aadds100-missing-directory"></a>AADDS100：缺少目录
**警报消息：**

与托管域关联的 Azure AD 目录可能已被删除。*托管域不再处于受支持的配置中。Microsoft 无法监视、管理、修补和同步托管域。*

**补救方法：**

出现此错误通常因为错误地将 Azure 订阅移动到新的 Azure AD 目录，并删除了仍与 Azure AD 域服务关联的旧 Azure AD 目录。

此错误无法恢复。 要解决此问题，必须[删除现有托管域](active-directory-ds-disable-aadds.md)，并在新目录中重新创建。 如果在进行删除时遇到问题惑，请联系 Azure Active Directory 域服务产品团队[请求支持](active-directory-ds-contact-us.md)。

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101：Azure AD B2C 在此目录中运行
**警报消息：**

无法在 Azure AD B2C 目录中启用 Azure AD 域服务。

**补救方法：**

>[!NOTE]
>要继续使用 Azure AD 域服务，必须在非 Azure AD B2C 目录中重新创建 Azure AD 域服务实例。

要还原服务，请按照以下步骤操作：

1. 从现有 Azure AD 目录中[删除托管域](active-directory-ds-disable-aadds.md)。
2. 创建并非 Azure AD B2C 目录的新目录。
3. 按照[入门](active-directory-ds-getting-started.md)指南重新创建托管域。

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103：地址在公共 IP 范围内

**警报消息：**

已启用 Azure AD 域服务的虚拟网络的 IP 地址范围在公共 IP 范围内。*必须在具有专用 IP 地址范围的虚拟网络中启用 Azure AD 域服务。此配置影响 Microsoft 监视、管理、修补和同步托管域的功能。*

**补救方法：**

> [!NOTE]
> 要解决此问题，必须删除现有托管域，然后在属于专用 IP 地址范围的虚拟网络中重新创建。 此过程是中断的。

开始之前，请阅读[本文](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)的“专用 IP v4 地址空间”部分。

在虚拟网络内部，计算机可能会向为子网配置的资源在同一 IP 地址范围的 Azure 资源发出请求。 但是，由于为此范围配置了虚拟网络，因此这些请求将在虚拟网络中进行路由，而不会到达预期的 Web 资源。 这可能导致 Azure AD 域服务出现不可预知的错误。

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


## <a name="contact-us"></a>联系我们
欢迎联系 Azure Active Directory 域服务产品团队[分享看法或请求支持](active-directory-ds-contact-us.md)。
