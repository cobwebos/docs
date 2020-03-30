---
title: 解决 Azure AD 域服务中的安全 LDAP 警报 |微软文档
description: 了解如何使用 Azure 活动目录域服务的安全 LDAP 排除和解决常见警报。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: iainfou
ms.openlocfilehash: 06b0fa1979f18981ec5cf78dc9a9dbad8b196394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "71258053"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>已知问题：Azure 活动目录域服务中的安全 LDAP 警报

使用轻量级目录访问协议 （LDAP） 与 Azure 活动目录域服务 （Azure AD DS） 通信的应用程序和服务可以[配置为使用安全的 LDAP](tutorial-configure-ldaps.md)。 必须打开适当的证书和所需的网络端口，才能安全 LDAP 正常工作。

本文可帮助您了解和解决在 Azure AD DS 中具有安全 LDAP 访问权限的常见警报。

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101：安全 LDAP 网络配置

### <a name="alert-message"></a>警报消息

*为托管域启用了 Internet 上的安全 LDAP。但是，不会使用网络安全组锁定对端口 636 的访问。这可能将托管域上的用户帐户暴露为密码暴力攻击。*

### <a name="resolution"></a>解决方法

启用安全 LDAP 时，建议创建其他规则，限制对特定 IP 地址的入站 LDAPS 访问。 这些规则保护 Azure AD DS 托管域免受暴力攻击。 要更新网络安全组以限制 TCP 端口 636 访问的安全 LDAP，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择**网络安全组**。
1. 选择与托管域关联的网络安全组，例如*AADDS-contoso.com-NSG，* 然后选择**入站安全规则**
1. **• 为**TCP 端口 636 添加规则。 如果需要，请在窗口中选择 **"高级"** 以创建规则。
1. 对于**源**，从下拉菜单*中选择 IP 地址*。 输入要授予安全 LDAP 流量访问权限的源 IP 地址。
1. 选择 *"任意"* 作为**目标**，然后输入*636*表示**目标端口范围**。
1. 将**协议**设置为*TCP* *和允许***的操作**。
1. 指定规则的优先级，然后输入名称，如*限制LDAPS*。
1. 准备就绪后，选择 **"添加"** 以创建规则。

Azure AD DS 托管域的运行状况会在两小时内自动更新自身并删除警报。

> [!TIP]
> TCP 端口 636 不是 Azure AD DS 平稳运行所需的唯一规则。 要了解更多信息，请参阅[Azure AD DS 网络安全组和所需端口](network-considerations.md#network-security-groups-and-required-ports)。

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502：安全 LDAP 证书到期

### <a name="alert-message"></a>警报消息

托管域的安全 LDAP 证书将于 [date] 到期。**

### <a name="resolution"></a>解决方法

通过按照为[安全 LDAP 创建证书](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)的步骤创建替换安全 LDAP 证书。 将替换证书应用于 Azure AD DS，并将证书分发到使用安全 LDAP 连接的任何客户端。

## <a name="next-steps"></a>后续步骤

如果仍有问题，[请打开 Azure 支持请求][azure-support]以获取其他故障排除帮助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
