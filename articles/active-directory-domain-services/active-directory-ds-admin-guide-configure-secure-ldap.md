---
title: "在 Azure AD 域服务中配置安全 LDAP (LDAPS) | Microsoft 文档"
description: "为 Azure AD 域服务托管域配置安全 LDAP (LDAPS)"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: 0d2e7e6f17fecb9809ac76fbfa0db860b7948a7e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>为 Azure AD 域服务托管域配置安全 LDAP (LDAPS)
本文说明如何为 Azure AD 域服务托管域启用安全轻量目录访问协议 (LDAPS)。 安全 LDAP 也称为基于安全套接字层 (SSL)/传输层安全性 (TLS) 的轻量目录访问协议 (LDAP)。

## <a name="before-you-begin"></a>开始之前
若要执行本文中所列的任务，需要：

1. 一个有效的 **Azure 订阅**。
2. 一个 **Azure AD 目录** - 已与本地目录或仅限云的目录同步。
3. 必须为 Azure AD 目录启用 **Azure AD 域服务**。 如果未启用，请遵循[入门指南](active-directory-ds-getting-started.md)中所述的所有任务。
4. **用于启用安全 LDAP 的证书**。

   * **建议** - 从信任的公共证书颁发机构获取证书。 这是更安全的配置选项。
   * 或者，也可以按本文稍后所述选择[创建自签名证书](#task-1---obtain-a-certificate-for-secure-ldap)。

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>安全 LDAP 证书的要求
根据以下指导原则获取有效证书，并启用安全 LDAP。 如果尝试使用无效/不正确的证书来为托管域启用安全 LDAP，操作会失败。

1. **受信任的颁发者** - 证书必须由使用安全 LDAP 连接到托管域的计算机所信任的颁发机构颁发。 此颁发机构可能是这些计算机信任的公共证书颁发机构。
2. **生存期** - 证书必须至少在接下来的 3 到 6 个月内保持有效。 证书过期后，安全 LDAP 不再可以访问托管域。
3. **使用者名称** - 在托管域中，证书的使用者名称必须是通配符。 例如，如果域名为“contoso100.com”，则证书的使用者名称必须是“*.contoso100.com”。 请将 DNS 名称（使用者替代名称）设置为此通配符名称。
4. **密钥用途** - 必须将证书设置为以下用途：数字签名和密钥加密。
5. **证书目的** - 证书必须有效，可用于 SSL 服务器身份验证。

> [!NOTE]
> **企业证书颁发机构：**Azure AD 域服务不支持使用组织的企业证书颁发机构颁发的安全 LDAP 证书。 限制的原因是：此服务不信任企业 CA 作为根证书颁发机构。 
>
>

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>任务 1 - 获取安全 LDAP 的证书
第一个任务涉及到获取证书，该证书用于对托管域进行安全 LDAP 访问。 可以使用两个选项：

* 从公共证书颁发机构获取证书。
* 创建自签名证书。

> [!NOTE]
> 需要使用安全 LDAP 连接到托管域的客户端计算机必须信任安全 LDAP 证书的颁发者。
>

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>选项 A（建议）- 从证书颁发机构获取安全 LDAP 证书
如果组织从公共证书颁发机构获取其证书，则请从该公共证书颁发机构获取安全 LDAP 证书。

> [!TIP]
> 为具有“onmicrosoft.com”域后缀的托管域使用自签名证书。
> 如果托管域的 DNS 域名以“.onmicrosoft.com”结尾，则无法从公共证书颁发机构获取安全 LDAP 证书。 由于 Microsoft 拥有“onmicrosoft.com”域，因此公共证书颁发机构将拒绝向具有此后缀的域颁发安全 LDAP 证书。 在此方案中将创建自签名证书，并使用它来配置安全 LDAP。
>

确保由公共证书颁发机构颁发的证书满足[安全 LDAP 证书要求](#requirements-for-the-secure-ldap-certificate)中列出的全部要求。


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>选项 B - 为安全 LDAP 创建自签名证书
如果不想使用公共证书颁发机构颁发的证书，可以选择为安全 LDAP 创建自签名证书。 如果托管域的 DNS 域名以“.onmicrosoft.com”结尾，请选择此选项。

**使用 PowerShell 创建自签名证书**

在 Windows 计算机上以**管理员**身份打开新的 PowerShell 窗口，并输入以下命令创建新的自签名证书。

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject *.contoso100.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.com
```

在上述示例中，请将“*.contoso100.com”替换为托管域的 DNS 域名。例如，如果创建了名为“contoso100.onmicrosoft.com”的托管域，则将上述脚本中的“*.contoso100.com”替换为“*.contoso100.onmicrosoft.com”。

![选择 Azure AD 目录](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

新建的自签名证书放在本地计算机的证书存储中。


## <a name="next-step"></a>后续步骤
[任务 2 - 将安全 LDAP 证书导出到 .PFX 文件](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
