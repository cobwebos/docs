---
title: 解决 Azure AD 域服务中的安全 LDAP 警报 |Microsoft Docs
description: 了解如何针对 Azure Active Directory 域服务的安全 LDAP 排除和解决常见警报。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: d987bbbe2a35dd24341b75d5663bab33d8c3bdb9
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86220246"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>已知问题： Azure Active Directory 域服务中的安全 LDAP 警报

使用轻型目录访问协议 (LDAP) 的应用程序和服务与 Azure Active Directory 域服务通信 (Azure AD DS) 可以[配置为使用安全 LDAP](tutorial-configure-ldaps.md)。 为了使安全 LDAP 正常工作，必须打开相应的证书和所需的网络端口。

本文可帮助你了解和解决 Azure AD DS 中的安全 LDAP 访问的常见警报。

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101：安全 LDAP 网络配置

### <a name="alert-message"></a>警报消息

*已为托管域启用通过 internet 的安全 LDAP。但是，使用网络安全组对端口636的访问未被锁定。这可能会使托管域上的用户帐户遭受密码暴力攻击。*

### <a name="resolution"></a>解决方案

启用安全 LDAP 时，建议创建其他规则，以限制对特定 IP 地址的入站 LDAPS 访问。 这些规则保护托管域免受暴力攻击。 若要更新网络安全组以限制安全 LDAP 的 TCP 端口636访问，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择 "**网络安全组**"。
1. 选择与托管域相关联的网络安全组，例如*AADDS-contoso.com-NSG*，然后选择 "**入站安全规则**"
1. 选择 " **+ 添加**" 创建 TCP 端口636的规则。 如果需要，请在窗口中选择 "**高级**" 来创建规则。
1. 对于 "**源**"，请从下拉菜单中选择 " *IP 地址*"。 输入要为安全 LDAP 流量授予访问权限的源 IP 地址。
1. 选择 "*任何*" 作为**目标**，然后为 "**目标端口范围**" 输入*636* 。
1. 将**协议**设置为*TCP* ，并将**操作**设置为 "*允许*"。
1. 指定规则的优先级，然后输入名称（如*RestrictLDAPS*）。
1. 准备就绪后，选择 "**添加**" 创建规则。

托管域的运行状况在两小时内自动更新，并删除警报。

> [!TIP]
> TCP 端口636不是 Azure AD DS 平稳运行所需的唯一规则。 若要了解详细信息，请参阅[AZURE AD DS 网络安全组和所需端口](network-considerations.md#network-security-groups-and-required-ports)。

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502：安全 LDAP 证书到期

### <a name="alert-message"></a>警报消息

托管域的安全 LDAP 证书将于 [date] 到期。**

### <a name="resolution"></a>解决方案

按照[创建安全 ldap 证书的](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)步骤创建替换安全 ldap 证书。 向 Azure AD DS 应用替换证书，并将证书分发到使用安全 LDAP 连接的任何客户端。

## <a name="next-steps"></a>后续步骤

如果仍有问题，请[发起 Azure 支持请求][azure-support]以获得额外的疑难解答帮助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
