---
title: 使用安全 LDAP (LDAPS) 绑定到 Azure AD 域服务托管域 | Microsoft Docs
description: 使用安全 LDAP (LDAPS) 绑定到 Azure AD 域服务托管域
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: df0b3d27eec478280a33be831a2431eccdf05a74
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483385"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>使用安全 LDAP (LDAPS) 绑定到 Azure AD 域服务托管域

## <a name="before-you-begin"></a>开始之前
完成[任务 4：配置 DNS 以便从 Internet 访问托管域](active-directory-ds-ldaps-configure-dns.md)。


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>任务 5：使用 LDP.exe 通过 LDAP 绑定到托管域
可使用远程服务器管理工具包中包含的 LDP.exe 工具，通过 LDAP 进行绑定和搜索。

首先，打开 LDP 并连接到托管域。 单击“连接”  ，再单击菜单中的“连接...”  。 指定托管域的 DNS 域名。 指定用于连接的端口。 对于 LDAP 连接，请使用端口 389。 对于 LDAPS 连接，请使用端口 636。 单击“确定”  按钮，连接到托管域。

接下来，绑定到托管域。 单击“连接”  ，再单击菜单中的“绑定...”  。 提供属于“AAD DC 管理员”组的用户帐户的凭据。

> [!IMPORTANT]
> 如果已在 Azure AD 域服务实例上禁用 NTLM 密码哈希同步用户 （和服务帐户） 无法执行 LDAP 简单绑定。  禁用 NTLM 密码哈希同步的详细信息，请阅读[确保 Azure AD 域服务托管的域的安全](secure-your-domain.md)。
>
>

选择“视图”  ，然后选择菜单中的“树”  。 将“基 DN”字段留空，再单击“确定”。 导航到想要搜索的容器，右键单击该容器，再选择“搜索”。

> [!TIP]
> - 从 Azure AD 同步的用户和组存储在“AADDC 用户”  组织单位中。 此组织单位的搜索路径类似于 ```OU=AADDC Users,DC=CONTOSO100,DC=COM```。
> - 加入托管域的计算机的计算机帐户存储在“AADDC 计算机”  组织单位中。 此组织单位的搜索路径类似于 ```OU=AADDC Computers,DC=CONTOSO100,DC=COM```。
>
>

更多信息 - [ LDAP 查询基础知识](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>任务 6：通过 Internet 锁定对托管域的安全 LDAP 访问
> [!NOTE]
> 如果尚未启用 LDAPS 来通过 Internet 访问托管域，请跳过此配置任务。
>
>

开始此任务之前，请完成[任务 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md) 中所述的步骤。

如果支持通过 Internet 对托管域进行 LDAPS 访问，则会对安全造成威胁。 可在安全 LDAP 所用的端口处（即端口 636）通过 Internet 访问托管域。 可选择将对托管域的访问限制为特定的已知 IP 地址。 创建网络安全组 (NSG) 并将其与已启用 Azure AD 域服务的子网关联。

下表中的示例 NSG 将锁定通过 Internet 的安全 LDAP 访问。 NSG 中的规则允许仅从指定的一组 IP 地址通过 TCP 端口 636 进行入站安全 LDAP 访问。 默认的“DenyAll”规则适用于来自 Internet 的所有其他入站流量。 NSG 规则允许从指定 IP 地址通过 Internet 进行 LDAPS 访问，此规则的优先级比 DenyAll NSG 规则的优先级高。

![通过 Internet 进行安全 LDAPS 访问的示例 NSG](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**详细信息** - [网络安全组](../virtual-network/security-overview.md)。


## <a name="related-content"></a>相关内容
* [Azure AD 域服务 - 入门指南](create-instance.md)
* [管理 Azure AD 域服务域](manage-domain.md)
* [LDAP 查询基础知识](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)
* [Azure AD 域服务的管理组策略](manage-group-policy.md)
* [网络安全组](../virtual-network/security-overview.md)
* [创建网络安全组](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>后续步骤
[对托管域中的安全 LDAP 进行故障排除](tshoot-ldaps.md)
