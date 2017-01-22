---
title: "访问防火墙后的密钥保管库 | Microsoft 文档"
description: "了解如何从防火墙后的应用程序访问 Azure 密钥保管库"
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
translationtype: Human Translation
ms.sourcegitcommit: da4d156132fba9efc98b3af441b6d095a4bb60ea
ms.openlocfilehash: e0bc6e75fef1f3567940e30acf6f9f429258be12


---
# <a name="access-azure-key-vault-behind-a-firewall"></a>访问防火墙保护下的 Azure 密钥保管库
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-ports-hosts-or-ip-addresses-should-i-open-to-enable-access-to-a-key-vault"></a>问：我的密钥保管库客户端应用程序需位于防火墙之后。 我应该打开哪些端口、主机或 IP 地址才可启用对密钥保管库的访问？
要访问密钥保管库，密钥保管库客户端应用程序必须访问多个终结点才可使用各种功能：

* 通过 Azure Active Directory (Azure AD) 进行身份验证。
* Azure 密钥保管库的管理。 这包括通过 Azure Resource Manager 创建、读取、更新、删除和设置访问策略。
* 通过密钥保管库特定的终结点（例如 [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)），访问和管理密钥保管库本身存储的对象（密钥和密码）。  

根据配置和环境，会有一些不同。   

## <a name="ports"></a>端口
针对所有 3 项功能（身份验证、管理和数据平面访问）的所有密钥保管库流量都会通过 HTTPS（端口 443）传递。 但是，对于 CRL，偶尔会有 HTTP（端口 80）流量。 支持 OCSP 的客户端不应到达 CRL，但有时可能会到达 [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl)。  

## <a name="authentication"></a>身份验证
密钥保管库客户端应用程序需要访问 Azure Active Directory 终结点才能进行身份验证。 使用的终结点取决于 Azure AD 租户配置、主体类型（用户主体或服务主体）以及帐户类型（如 Microsoft 帐户或者工作或学校帐户）。  

| 主体类型 | 终结点：端口 |
| --- | --- |
| 使用 Microsoft 帐户的用户<br> （例如 user@hotmail.com) |**全局：**<br> login.microsoftonline.com:443<br><br> **Azure China：**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government：**<br> login-us.microsoftonline.com:443<br><br>**Azure Germany：**<br>  login.microsoftonline.de:443<br><br> 和 <br>login.live.com:443 |
| 使用 Azure AD 的工作或学校帐户的用户或服务主体（如 user@contoso.com) |**全局：**<br> login.microsoftonline.com:443<br><br> **Azure China：**<br> login.chinacloudapi.cn:443<br><br>**Azure US Government：**<br> login-us.microsoftonline.com:443<br><br>**Azure Germany：**<br>  login.microsoftonline.de:443 |
| 使用工作或学校帐户，以及 Active Directory 联合身份验证服务 (AD FS) 或其他联合终结点的用户或服务主体（如 user@contoso.com) |工作或学校帐户的所有终结点，以及 AD FS 或其他联合终结点 |

还可能存在一些其他复杂情况。 有关其他信息，请参阅 [Azure Active Directory 身份验证流](/documentation/articles/active-directory-authentication-scenarios/)、[将应用程序与 Azure Active Directory 集成](/documentation/articles/active-directory-integrating-applications/)和 [Active Directory 身份验证协议](https://msdn.microsoft.com/library/azure/dn151124.aspx)。  

## <a name="key-vault-management"></a>密钥保管库管理
对于密钥保管库管理（CRUD 和设置访问策略），密钥保管库客户端应用程序需要访问 Azure Resource Manager 终结点。  

| 操作类型 | 终结点：端口 |
| --- | --- |
| 密钥保管库控制平面操作<br> 密钥保管库控制平面操作 |**全局：**<br> management.azure.com:443<br><br> **Azure China：**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government：**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany：**<br> management.microsoftazure.de:443 |
| Azure Active Directory 图形 API |**全局：**<br> graph.windows.net:443<br><br> **Azure China：**<br> graph.chinacloudapi.cn:443<br><br> **Azure US Government：**<br> graph.windows.net:443<br><br> **Azure Germany：**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>密钥保管库操作
对于所有密钥保管库对象（密钥和密码）管理和加密操作，密钥保管库客户端需要访问密钥保管库终结点。 终结点 DNS 后缀因密钥保管库位置而异。 密钥保管库终结点的格式是 vault-name.region-specific-dns-suffix，如下表所示。  

| 操作类型 | 终结点：端口 |
| --- | --- |
| 操作包括对密钥的加密操作；创建、读取、更新和删除密钥和密码；设置或获取密钥保管库对象（密钥或密码）上的标记和其他属性 |**全局：**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China：**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government：**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany：**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>IP 地址范围
密钥保管库服务使用其他 Azure 资源，如 PaaS 基础结构。 因此，不可能提供密钥保管库服务终结点在任何特定时间具有的 IP 地址的特定范围。 如果防火墙仅支持 IP 地址范围，请参阅 [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)文档。 对于身份验证和标识 (Azure Active Directory)，应用程序必须能够连接到[身份验证和标识地址](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)中所述的终结点。

## <a name="next-steps"></a>后续步骤
如果对密钥保管库有任何疑问，请访问 [Azure 密钥保管库论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)。




<!--HONumber=Dec16_HO3-->


