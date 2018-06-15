---
title: Azure Active Directory 域服务：安全 LDAP 配置疑难解答 | Microsoft Docs
description: Azure AD 域服务的安全 LDAP 疑难解答
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: ergreenl
ms.openlocfilehash: 29554230adc9d30f357c4e8d0082c0fe7d8d9035
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588417"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD 域服务 - 安全 LDAP 配置疑难解答

本文提供为 Azure AD 域服务[配置安全 LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md) 时常见问题的解决方法。

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101：安全 LDAP 网络安全组配置

**警报消息：**

已为托管域启用 Internet 上的安全 LDAP。*但是，访问端口 636 未使用网络安全组锁定。这样可能会使托管域上的用户帐户遭到密码暴力攻击。*

### <a name="secure-ldap-port"></a>安全 LDAP 端口

启用安全 LDAP 后，我们建议创建其他规则，以支持仅从特定 IP 地址的入站 LDAPS 访问。 这些规则可以使域免受可能构成安全威胁的暴力攻击。 端口 636 支持对托管域的访问。 下面介绍如何更新 NSG 以允许安全 LDAP 的访问：

1. 导航到 Azure 门户中的[“网络安全组”选项卡](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups)
2. 从表中选择与域相关联的 NSG。
3. 单击“入站安全规则”
4. 创建端口 636 规则
   1. 单击顶部导航栏上的“添加”。
   2. 为源选择一个“IP 地址”。
   3. 指定此规则的源端口范围。
   4. 针对目标端口范围，输入“636”。
   5. 协议为 TCP。
   6. 为规则指定适当的名称、说明和优先级。 此规则的优先级应高于“全部拒绝”规则的优先级（如果有）。
   7. 单击“确定”。
5. 验证是否已创建规则。
6. 在两小时后检查域的运行状况，确保已正确完成相关步骤。

> [!TIP]
> 端口 636 不是 Azure AD 域服务要平稳地运行所需的唯一规则。 若要了解详细信息，请访问[网络准则](active-directory-ds-networking.md)或[解决 NSG 配置问题](active-directory-ds-troubleshoot-nsg.md)文章。
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502：安全 LDAP 证书到期

**警报消息：**

*托管域的安全 LDAP 证书将于 XX 到期。*

**解决方法：**

按照[配置安全 LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md) 一文中所述的步骤创建新的安全 LDAP 证书。

## <a name="contact-us"></a>联系我们
欢迎联系 Azure Active Directory 域服务产品团队[分享看法或请求支持](active-directory-ds-contact-us.md)。
