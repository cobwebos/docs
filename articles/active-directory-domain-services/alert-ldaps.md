---
title: 解除 Azure AD 域服务中的安全 LDAP 警报 | Microsoft Docs
description: 了解如何排查并解除 Azure Active Directory 域服务安全 LDAP 的常见警报。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86220246"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>已知问题：Azure Active Directory 域服务中的安全 LDAP 警报

使用轻型目录访问协议 (LDAP) 与 Azure Active directory 域服务 (Azure AD DS) 进行通信的应用程序和服务可以[配置为使用安全 LDAP](tutorial-configure-ldaps.md)。 必须打开相应的证书和所需的网络端口，才能使安全 LDAP 正常工作。

本文可帮助你了解和解决 Azure AD DS 中的安全 LDAP 访问的常见警报。

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101：安全 LDAP 网络配置

### <a name="alert-message"></a>警报消息

已为托管域启用 Internet 上的安全 LDAP。*但是，访问端口 636 未使用网络安全组锁定。这样可能会使托管域上的用户帐户遭到密码暴力攻击。*

### <a name="resolution"></a>解决方法

启用安全 LDAP 时，建议创建可将入站 LDAPS 访问限制为特定 IP 地址的其他规则。 这些规则可保护托管域免受暴力攻击。 若要更新网络安全组以限制对安全 LDAP 的 TCP 端口 636 访问，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“网络安全组”。
1. 选择与托管域相关联的网络安全组，例如 AADDS-contoso.com-NSG，然后选择“入站安全规则”
1. 选择“+添加”，为 TCP 端口 636 创建规则。 如果需要，请在窗口中选择“高级”以创建规则。
1. 对于“源”，请从下拉菜单中选择“IP 地址”。 输入要为安全 LDAP 流量授予访问权限的源 IP 地址。
1. 选择“任意”作为目标，然后为目标端口范围输入“636”。
1. 将协议设置为“TCP”，将操作设置为“允许”。
1. 指定规则的优先级，然后输入名称，例如 RestrictLDAPS。
1. 准备就绪后，选择“添加”以创建规则。

托管域的运行状况会在两小时内自动更新，并删除警报。

> [!TIP]
> TCP 端口 636 不是 Azure AD DS 平稳地运行所需的唯一规则。 若要了解详细信息，请参阅 [Azure AD DS 网络安全组和必需端口](network-considerations.md#network-security-groups-and-required-ports)。

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502：安全 LDAP 证书即将到期

### <a name="alert-message"></a>警报消息

托管域的安全 LDAP 证书将于 [date] 到期。

### <a name="resolution"></a>解决方法

通过执行[创建安全 LDAP 证书](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)的步骤来创建替换安全 LDAP 证书。 向 Azure AD DS 应用替换证书，并将证书分发给使用安全 LDAP 连接的任何客户端。

## <a name="next-steps"></a>后续步骤

如果仍有问题，请[发起 Azure 支持请求][azure-support]以获得额外的疑难解答帮助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
