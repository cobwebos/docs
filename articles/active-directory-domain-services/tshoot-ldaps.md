---
title: 在 Azure AD 域服务中排除安全 LDAP 故障 |微软文档
description: 了解如何为 Azure 活动目录域服务托管域排除安全 LDAP （LDAPS） 的故障
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 22d1b6e2344256b52cfdbc48720a680a770a4216
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132164"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>解决 Azure 活动目录域服务托管域的安全 LDAP 连接问题

使用轻量级目录访问协议 （LDAP） 与 Azure 活动目录域服务 （Azure AD DS） 通信的应用程序和服务可以[配置为使用安全的 LDAP](tutorial-configure-ldaps.md)。 必须打开适当的证书和所需的网络端口，才能安全 LDAP 正常工作。

本文可帮助您解决 Azure AD DS 中安全 LDAP 访问的问题。

## <a name="common-connection-issues"></a>常见连接问题

如果使用安全 LDAP 连接到 Azure AD DS 托管域时遇到问题，请查看以下故障排除步骤。 每次故障排除步骤后，请尝试再次连接到 Azure AD DS 托管域：

* 安全 LDAP 证书的证书颁发者链在客户端上必须受信任。 您可以将根证书颁发机构 （CA） 添加到客户端上的受信任根证书存储，以建立信任。
    * 请确保[导出证书并将证书应用于客户端计算机][client-cert]。
* 验证托管域的安全 LDAP 证书在 *"主题"* 或 *"主题替代名称"* 属性中具有 DNS 名称。
    * 查看[安全的 LDAP 证书要求][certs-prereqs]，并在需要时创建替换证书。
* 验证 LDAP 客户端（如*ldp.exe）* 是否使用 DNS 名称而不是 IP 地址连接到安全的 LDAP 终结点。
    * 应用于 Azure AD DS 托管域的证书不包括服务的 IP 地址，仅包括 DNS 名称。
* 检查 LDAP 客户端连接到的 DNS 名称。 它必须解析为 Azure AD DS 托管域上的安全 LDAP 的公共 IP 地址。
    * 如果 DNS 名称解析为内部 IP 地址，请更新 DNS 记录以解析为外部 IP 地址。
* 对于外部连接，网络安全组必须包含一个规则，允许流量从 Internet 到 TCP 端口 636。
    * 如果可以使用安全 LDAP 从直接连接到虚拟网络的资源而不是外部连接连接到 Azure AD DS 托管域，请确保[创建网络安全组规则以允许安全的 LDAP 流量][ldaps-nsg]。

## <a name="next-steps"></a>后续步骤

如果仍有问题，[请打开 Azure 支持请求][azure-support]以获取其他故障排除帮助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
