<properties
	pageTitle="访问防火墙后的密钥保管库 | Microsoft Azure"
	description="了解如何从防火墙后的应用程序访问密钥保管库"
	services="key-vault"
	documentationCenter=""
	authors="amitbapat"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="09/13/2016"
	ms.author="ambapat"/>

# 访问防火墙后的密钥保管库
### 问：密钥保管库客户端应用程序位于防火墙之后，要访问密钥保管库，需要打开哪些端口/主机/IP 地址？

要访问密钥保管库，密钥保管库客户端应用程序应能够访问各种功能的多个终结点。

- 身份验证（通过 Azure Active Directory）
- 通过 Azure Resource Manager 管理密钥保管库（包括创建/读取/更新/删除以及设置访问策略）
- 访问和管理密钥保管库中存储的对象（密钥和密码），穿过密钥保管库特定的终结点（例如 [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)）。

根据配置和环境，会有一些不同。

## 端口

流入密钥保管库的所有流量都通过 HTTPS（端口 443）传输，这些流量用于实现全部 3 种功能（身份验证、管理和数据平面访问）。但是对于 CRL，偶尔会出现 HTTP（端口 80）流量。支持 OCSP 的客户端不应到达 CRL，但有时可能会到达 [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl)。

## 身份验证

密钥保管库客户端应用程序需要访问 Azure Active Directory 终结点才能进行身份验证。使用的终结点取决于 AAD 租户配置、主体类型（用户主体、服务主体）以及帐户类型（如 Microsoft 帐户或组织 ID）。

| 主体类型 | 终结点：端口 |
|----------------|---------------|
| 使用 Microsoft 帐户的用户<br>（例如 user@hotmail.com） | **全球：**<br>login.microsoftonline.com:443<br><br>**Azure 中国：**<br>login.chinacloudapi.cn:443<br><br>**Azure 美国政府：**<br>login-us.microsoftonline.com:443<br><br>**Azure 德国：**<br> login.microsoftonline.de:443<br><br> 和 <br>login.live.com:443 |
| 使用具有 AAD 的组织 ID 的用户/服务主体（例如 user@contoso.com） | **全球：**<br>login.microsoftonline.com:443<br><br>**Azure 中国：**<br>login.chinacloudapi.cn:443<br><br>**Azure 美国政府：**<br>login-us.microsoftonline.com:443<br><br>**Azure 德国：**<br> login.microsoftonline.de:443 |
| 使用组织 ID+ADFS 或其他联合终结点的用户/服务主体（例如 user@contoso.com） | 组织 ID+ADFS 或其他联合终结点的上述所有终结点 |

还可能存在一些其他复杂情况。有关其他信息，请参阅 [Azure Active Directory 身份验证流](/documentation/articles/active-directory-authentication-scenarios/)、[将应用程序与 Azure Active Directory 集成](/documentation/articles/active-directory-integrating-applications/)以及 [Active Directory Authentication Protocols](https://msdn.microsoft.com/library/azure/dn151124.aspx)（Active Directory 身份验证协议）。

## 密钥保管库管理

密钥保管库客户端应用程序需要访问 Azure Resource Manager 终结点才能进行密钥保管库管理（CRUD 以及设置访问策略）。

| 操作类型 | 终结点：端口 |
|----------------|---------------|
| 通过 Azure Resource Manager 进行的<br>密钥保管库控制平面操作 | **全球：**<br>management.azure.com:443<br><br>**Azure 中国：**<br>management.chinacloudapi.cn:443<br><br>**Azure 美国政府：**<br>management.usgovcloudapi.net:443<br><br>**Azure 德国：**<br>management.microsoftazure.de:443 |
| Azure Active Directory 图形 API | **全球：**<br>graph.windows.net:443<br><br>**Azure 中国：**<br>graph.chinacloudapi.cn:443<br><br>**Azure 美国政府：**<br>graph.windows.net:443<br><br>**Azure 德国：**<br>graph.cloudapi.de:443 |

## 密钥保管库操作

密钥保管库客户端需要访问密钥保管库终结点才能进行所有密钥保管库对象（密钥和密码）管理和加密操作。根据密钥保管库的位置，终结点 DNS 后缀不同。密钥保管库终结点的格式为：<保管库名称>.<区域特定的 DNS 后缀>，如下表中所述。

| 操作类型 | 终结点：端口 |
|----------------|---------------|
| 密钥保管库操作，如密钥的加密操作、创建/读取/更新/删除密钥和密码、设置/获取标记和密钥保管库对象的其他属性（密钥/密码） | **全球：**<br>&lt;vault-name&gt;.vault.azure.net:443<br><br>**Azure 中国：**<br>&lt;vault-name&gt;.vault.azure.cn:443<br><br>**Azure 美国政府：**<br>&lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br>**Azure 德国：**<br>&lt;vault-name&gt;.vault.microsoftazure.de:443 |

## IP 地址范围

密钥保管库服务反过来使用其他 Azure 资源（如 PaaS 基础结构），因此无法提供密钥保管库服务终结点在任何给定时间具有的特定 IP 地址范围。但是，如果防火墙仅支持 IP 地址范围，请参阅[Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653)（Microsoft Azure 数据中心 IP 范围）文档。应用程序必须能够连接到[身份验证和标识地址](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)中所述的终结点才能进行身份验证和标识 (Azure Active Directory)。

## 后续步骤

- 如果对密钥保管库有任何疑问，请访问 [Azure 密钥保管库论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

<!---HONumber=AcomDC_0921_2016-->