---
title: 对 Azure AD 域服务中的安全 LDAP (LDAPS) 故障排除 | Microsoft Docs
description: 对 Azure AD 域服务中的安全 LDAP (LDAPS) 故障排除
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: maheshu
ms.openlocfilehash: 9713a06bbf6a61b316e061cb851721a3554cd632
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503666"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>对 Azure AD 域服务中的安全 LDAP (LDAPS) 故障排除

## <a name="connection-issues"></a>连接问题
如果在使用安全 LDAP 连接到托管域时遇到问题：

* 安全 LDAP 证书的证书颁发者链在客户端上必须受信任。 要建立信任，可选择向客户端上受信任的根证书存储添加根证书颁发机构。
* 验证 LDAP 客户端（例如 ldp.exe）连接到安全 LDAP 终结点时使用的是 DNS 名称，而不是 IP 地址。
* 检查 LDAP 客户端连接到的 DNS 名称。 它必须解析为托管域中安全 LDAP 的公共 IP 地址。
* 验证托管域的安全 LDAP 证书具有“使用者”或“使用者可选名称”属性中的 DNS 名称。
* 虚拟网络的 NSG 设置必须允许从 Internet 到端口 636 的流量。 仅当已启用通过 Internet 进行安全 LDAP 访问时，此步骤才适用。


## <a name="need-help"></a>需要帮助？
如果在使用安全 LDAP 连接托管域的过程中仍遇到问题，请[联系产品团队](active-directory-ds-contact-us.md)寻求帮助。 包括以下有助于优化问题诊断的信息：
* ldp.exe 建立连接并失败的屏幕截图。
* Azure AD 租户 ID，以及托管域的 DNS 域名。
* 尝试绑定为的完全匹配用户名。


## <a name="related-content"></a>相关内容
* [Azure AD 域服务 - 入门指南](active-directory-ds-getting-started.md)
* [管理 Azure AD 域服务托管域](active-directory-ds-admin-guide-administer-domain.md)
* [LDAP 查询基础知识](https://technet.microsoft.com/library/aa996205.aspx)
* [管理 Azure AD 域服务托管域上的组策略](active-directory-ds-admin-guide-administer-group-policy.md)
* [网络安全组](../virtual-network/security-overview.md)
* [创建网络安全组](../virtual-network/tutorial-filter-network-traffic.md)
