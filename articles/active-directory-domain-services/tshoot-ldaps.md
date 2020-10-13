---
title: 对 Azure AD 域服务中的安全 LDAP 故障排除 | Microsoft Docs
description: 了解如何对 Azure Active Directory 域服务托管域的安全LDAP (LDAPS) 进行故障排除
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: dc4a8e6783c699cd81ecab342f5611e564577fad
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967318"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>对 Azure Active Directory 域服务托管域的安全 LDAP 连接问题进行故障排除

使用轻型目录访问协议 (LDAP) 与 Azure Active directory 域服务 (Azure AD DS) 进行通信的应用程序和服务可以[配置为使用安全 LDAP](tutorial-configure-ldaps.md)。 必须打开相应的证书和所需的网络端口，才能使安全 LDAP 正常工作。

可借助本文排除 Azure AD DS 中的安全 LDAP 访问问题。

## <a name="common-connection-issues"></a>常见连接问题

如果使用安全 LDAP 连接到 Azure AD DS 托管域时遇到问题，请查看以下故障排除步骤。 在每个故障排除步骤之后，尝试再次连接到托管域：

* 安全 LDAP 证书的证书颁发者链在客户端上必须受信任。 可以将根证书颁发机构 (CA) 添加到客户端上受信任的根证书存储以建立信任。
    * 请确保[导出证书并将其应用于客户端计算机][client-cert]。
* 验证托管域的安全 LDAP 证书具有“使用者”或“使用者可选名称”属性中的 DNS 名称 。
    * 检查[安全 LDAP 证书要求][certs-prereqs]，如果需要，创建一个替换证书。
* 验证 LDAP 客户端（例如 ldp.exe）连接到安全 LDAP 终结点时使用的是 DNS 名称，而不是 IP 地址。
    * 应用到托管域的证书不包括服务的 IP 地址，只含有 DNS 名称。
* 检查 LDAP 客户端连接到的 DNS 名称。 它必须解析为托管域中安全 LDAP 的公共 IP 地址。
    * 如果 DNS 名称解析为内部 IP 地址，则更新 DNS 记录以解析为外部 IP 地址。
* 对于外部连接，网络安全组必须包含允许 internet 到 TCP 端口 636 的流量的规则。
    * 如果可以使用安全 LDAP 从直接连接到虚拟网络的资源（但不是外部连接）连接到托管域，请确保[创建网络安全组规则以允许安全 LDAP 通信][ldaps-nsg]。

## <a name="next-steps"></a>后续步骤

如果仍有问题，请[发起 Azure 支持请求][azure-support]以获得额外的疑难解答帮助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
