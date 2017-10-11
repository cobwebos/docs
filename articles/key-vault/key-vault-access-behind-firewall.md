---
title: "防火墙的访问密钥保管库 |Microsoft 文档"
description: "了解如何从防火墙后面的应用程序访问 Azure 密钥保管库"
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 50d21774-2ee1-4212-8995-570c9de603c5
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.openlocfilehash: d00c6e0acf437d2bfc3c27e948f4646a6685b08f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>在防火墙后面的访问 Azure 密钥保管库
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-ports-hosts-or-ip-addresses-should-i-open-to-enable-access-to-a-key-vault"></a>问： 我的密钥保管库客户端应用程序需要在防火墙后面。 哪些端口、 主机或 IP 地址应打开以启用对密钥保管库的访问？
若要访问密钥保管库，将密钥保管库客户端应用程序访问的各种功能的多个终结点：

* 身份验证通过 Azure Active Directory (Azure AD)。
* Azure 密钥保管库的管理。 这包括创建、 读取、 更新、 删除和设置访问策略通过 Azure 资源管理器。
* 访问和管理对象 （密钥和机密） 存储在密钥保管库本身，经过特定于密钥保管库的终结点 (例如， [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net))。  

根据你配置和环境中，有一些变化形式。   

## <a name="ports"></a>端口
为所有三个函数 （身份验证、 管理和数据平面访问） 的密钥保管库的所有流量都都经过 HTTPS： 端口 443。 但是，有时将 CRL 的 HTTP （端口 80） 流量。 支持 OCSP 的客户端不应访问 CRL，但有时可能会达到[http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl)。  

## <a name="authentication"></a>身份验证
密钥保管库客户端应用程序将需要访问 Azure Active Directory 进行身份验证的终结点。 使用的终结点取决于在 Azure AD 租户配置中，主体 （用户主体或服务主体） 的类型以及帐户-例如，Microsoft 帐户或工作或学校帐户的类型。  

| 主体类型 | 终结点： 端口 |
| --- | --- |
| 使用 Microsoft 帐户的用户<br> (例如， user@hotmail.com) |**全局：**<br> login.microsoftonline.com:443<br><br> **中国 azure:**<br> login.chinacloudapi.cn:443<br><br>**Azure 美国政府：**<br> 登录名 us.microsoftonline.com:443<br><br>**Azure 德国：**<br> login.microsoftonline.de:443<br><br> 和 <br>login.live.com:443 |
| 用户或服务主体与 Azure AD 中使用工作或学校帐户 (例如， user@contoso.com) |**全局：**<br> login.microsoftonline.com:443<br><br> **中国 azure:**<br> login.chinacloudapi.cn:443<br><br>**Azure 美国政府：**<br> 登录名 us.microsoftonline.com:443<br><br>**Azure 德国：**<br> login.microsoftonline.de:443 |
| 用户或服务主体使用工作或学校帐户，以及 Active Directory 联合身份验证服务 (AD FS) 或其他联合终结点 (例如， user@contoso.com) |所有终结点的工作或学校帐户，以及 AD FS 或其他联合终结点 |

有其他可能的复杂方案。 请参阅[Azure Active Directory 身份验证流](/documentation/articles/active-directory-authentication-scenarios/)，[将应用程序与 Azure Active Directory](/documentation/articles/active-directory-integrating-applications/)，和[Active Directory 身份验证协议](https://msdn.microsoft.com/library/azure/dn151124.aspx)有关其他信息。  

## <a name="key-vault-management"></a>密钥保管库管理
对于密钥保管库管理 （CRUD 并设置访问策略），密钥保管库客户端应用程序需要访问 Azure 资源管理器终结点。  

| 类型的操作 | 终结点： 端口 |
| --- | --- |
| 密钥保管库控制平面操作<br> 通过 Azure 资源管理器 |**全局：**<br> management.azure.com:443<br><br> **中国 azure:**<br> management.chinacloudapi.cn:443<br><br> **Azure 美国政府：**<br> management.usgovcloudapi.net:443<br><br> **Azure 德国：**<br> management.microsoftazure.de:443 |
| Azure Active Directory Graph API |**全局：**<br> graph.windows.net:443<br><br> **中国 azure:**<br> graph.chinacloudapi.cn:443<br><br> **Azure 美国政府：**<br> graph.windows.net:443<br><br> **Azure 德国：**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>密钥保管库操作
对于所有密钥保管库对象 （密钥和机密） 管理和加密操作，密钥保管库客户端需要访问密钥保管库终结点。 根据密钥保管库的位置而异的终结点 DNS 后缀。 密钥保管库终结点的格式为*保管库名称*。*区域特定的 dns 后缀*下, 表中所述。  

| 类型的操作 | 终结点： 端口 |
| --- | --- |
| 操作包括密钥; 上的加密操作创建、 读取、 更新和删除密钥和机密;设置或获取对密钥保管库对象 （密钥或机密） 的标记和其他特性 |**全局：**<br> &lt;保管库名称&gt;。 vault.azure.net:443<br><br> **中国 azure:**<br> &lt;保管库名称&gt;。 vault.azure.cn:443<br><br> **Azure 美国政府：**<br> &lt;保管库名称&gt;。 vault.usgovcloudapi.net:443<br><br> **Azure 德国：**<br> &lt;保管库名称&gt;。 vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>IP 地址范围
密钥保管库服务使用其他 Azure 资源，如 PaaS 基础结构。 因此不可能提供特定的 IP 地址范围，密钥保管库服务终结点将要在任何特定时间。 如果你的防火墙支持仅 IP 地址范围，请参阅[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)文档。 有关身份验证和标识 (Azure Active Directory)，你的应用程序必须能够连接到的终结点中所述[身份验证和标识地址](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)。

## <a name="next-steps"></a>后续步骤
如果你的疑问有关密钥保管库，请访问[Azure 密钥保管库论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)。

