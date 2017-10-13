---
title: "Azure Active Directory 域服务：功能 | Microsoft 文档"
description: "Azure Active Directory 域服务的功能"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 8005be7ded6ea005af086aeaf594963a5f2d4ac2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-domain-services"></a>Azure AD 域服务
## <a name="features"></a>功能
可在 Azure AD 域服务托管域中使用以下功能。

* **简单的部署体验：**只需单击几下鼠标，就能为 Azure AD 租户启用 Azure AD 域服务。 无论 Azure AD 租户是云租户还是与本地目录同步，都能快速预配托管域。
* **支持加入域：**可以在提供托管域的 Azure 虚拟网络中轻松将计算机加入域。 在 Windows 客户端和服务器操作系统上的域加入体验可针对 Azure AD 域服务所服务的域顺畅运行。 还可以针对此类域使用自动化域加入工具。
* **每个 Azure AD 目录都有一个域实例：**可以针对每个 Azure AD 目录创建单个 Active Directory 域。
* **使用自定义名称创建域：**可以使用 Azure AD 域服务创建具有自定义名称的域（例如“contoso100.com”）。 可以使用已验证或未验证的域名。 还可以选择性地使用 Azure AD 目录提供的内置域后缀（即“*.onmicrosoft.com”）来创建域。
* **与 Azure AD 集成：**无需配置或管理向 Azure AD 域服务的复制。 Azure AD 域服务中自动提供来自 Azure AD 目录的用户帐户、组成员身份和用户凭据（密码）。 新用户、组或者对 Azure AD 租户或本地目录中的属性所做的更改会自动同步到 Azure AD 域服务。
* **NTLM 和 Kerberos 身份验证：**借助对 NTLM 和 Kerberos 身份验证的支持，可以部署依赖于 Windows 集成身份验证的应用程序。
* **使用企业凭据/密码：**Azure AD 租户中用户的密码可与 Azure AD 域服务配合使用。 用户可以使用其企业凭据将计算机加入域，以交互方式或通过远程桌面登录，以及针对托管域进行身份验证。
* **LDAP 绑定和 LDAP 读取支持：**可以使用依赖于 LDAP 绑定的应用程序，在 Azure AD 域服务所服务的域中验证用户的身份。 此外，使用 LDAP 读取操作从目录查询用户/计算机属性的应用程序也可以针对 Azure AD 域服务正常运行。
* **安全 LDAP (LDAPS)：**通过安全 LDAP (LDAPS) 启用对目录的访问。 默认情况下，可在虚拟网络中使用安全 LDAP 访问。 但是，也可以选择为 Internet 启用安全 LDAP 访问。
* **组策略：**可为每个用户和计算机容器使用单个内置 GPO，针对用户帐户和已加入域的计算机强制实施符合所需安全策略的规范。 还可以创建自己的自定义 GPO 并将其分配到自定义组织单位来[管理组策略](active-directory-ds-admin-guide-administer-group-policy.md)。
* **管理 DNS：**“AAD DC 管理员”组的成员可以使用熟悉的 DNS 管理工具（例如“DNS 管理 MMC”管理单元）来管理托管域的 DNS。
* **创建自定义的组织单位 (OU)：**“AAD DC 管理员”组的成员可以在托管域中创建自定义 OU。 这些用户已被授予自定义 OU 的完全管理权限，可以在这些自定义 OU 中添加或删除服务帐户、计算机、组等。
* **可在多个 Azure 区域中使用：**请参阅[按区域列出的 Azure 服务](https://azure.microsoft.com/regions/#services/)页，了解已推出 Azure AD 域服务的 Azure 区域。
* **高可用性：**Azure AD 域服务可为域提供高可用性。 此功能可以保证增大服务运行时间，提高弹性应对故障的能力。 内置的运行状况监视功能可以在发生故障时自动补救，因为它可以启动新的实例来取代有故障的实例，持续为域提供服务。
* **使用熟悉的管理工具：**可以使用 Active Directory 管理中心或 PowerShell 的 Active Directory 等熟悉的 Windows Server Active Directory 管理工具来管理托管域。
