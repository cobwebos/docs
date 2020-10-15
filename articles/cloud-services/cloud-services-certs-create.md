---
title: 云服务和管理证书 | Microsoft Docs
description: 了解如何创建和部署云服务的证书，以及如何在 Azure 中通过管理 API 进行身份验证。
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 8650b8670c61cab15b26163dd5108145b8509434
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072418"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Azure 云服务证书概述
证书在 Azure 中用于云服务（[服务证书](#what-are-service-certificates)）以及用于通过管理 API 进行身份验证（[管理证书](#what-are-management-certificates)）。 本主题同时提供了有关这两种证书类型的一般概述，并说明了如何[创建](#create)并将其部署到 Azure。

Azure 中使用的证书是 x.509 v3 证书，可自签名或由另一个受信任的证书签名。 自签名证书由其创建者签名，因此，默认情况下不受信任。 大多数浏览器可以忽略此问题。 自签名证书应仅在开发和测试云服务时使用。 

Azure 使用的证书可以包含一个公钥。 证书具有指纹，它提供了一种明确识别证书的方法。 该指纹在 Azure [配置文件](cloud-services-configure-ssl-certificate-portal.md)中用于识别云服务应使用的证书。 

>[!Note]
>Azure 云服务不接受 AES256-SHA256 加密证书。

## <a name="what-are-service-certificates"></a>什么是服务证书？
将服务证书添加到云服务后可在服务之间实现安全通信。 例如，如果部署了 Web 角色，则需要提供可对公开 HTTPS 终结点进行身份验证的证书。 在服务定义中定义的服务证书会自动部署到运行角色实例的虚拟机。 

可使用 Azure 门户或使用经典部署模型将服务证书上传到 Azure。 服务证书与特定的云服务相关联。 它们将分配给服务定义文件中的部署。

服务证书可与服务分开管理，且可由不同的人员管理。 例如，开发人员上传的服务包可以使用 IT 管理员以前上传到 Azure 的证书。 IT 管理员可以管理并续订该证书（更改服务配置）而无需上传新的服务包。 在没有新服务包的情况下更新之所以可能是因为证书的逻辑名称、存储名称和存储位置是在服务定义文件中指定的，而证书指纹则是在服务配置文件中指定的。 若要更新证书，只需上传新证书并更改服务配置文件中的指纹值。

>[!Note]
>[云服务常见问题解答 - 配置和管理](cloud-services-configuration-and-management-faq.md)一文包含有关证书的一些有用信息。

## <a name="what-are-management-certificates"></a>什么是管理证书？
使用管理证书可以通过经典部署模型进行身份验证。 许多程序和工具（如 Visual Studio 或 Azure SDK）会使用这些证书来自动配置和部署各种 Azure 服务。 实际上，这些证书与云服务并无关系。 

> [!WARNING]
> 请注意！ 这些类型的证书允许使用它们进行身份验证的任何人管理与其相关联的订阅。 
> 
> 

### <a name="limitations"></a>限制
每个订阅最多只能有 100 个管理证书。 特定服务管理员用户 ID 下的所有订阅同样最多只能有 100 个管理证书。 如果帐户管理员的用户 ID 下已添加 100 个管理证书，但还需要更多证书，则可以添加共同管理员，从而增加额外的证书。 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>创建新的自签名证书
任何可用工具均可用于创建自签名证书，前提是符合以下设置：

* X.509 证书。
* 包含公钥。
* 为密钥交换（.pfx 文件）而创建。
* 使用者名称必须与用于访问云服务的域匹配。

    > 你无法获取 cloudapp.net (或任何 Azure 相关) 域的 TLS/SSL 证书;证书的使用者名称必须与用于访问应用程序的自定义域名匹配。 例如，**contoso.net**，而不是 **contoso.cloudapp.net**。

* 至少采用 2048 位加密。
* **仅服务证书**：客户端证书必须驻留在“个人”证书存储中。

在 Windows 上有两种简单方法可以创建证书，分别是使用 `makecert.exe` 实用程序或 IIS。

### <a name="makecertexe"></a>Makecert.exe
此实用工具已被弃用，此处不再赘述。 有关详细信息，请参阅 [此 MSDN 文章](/windows/desktop/SecCrypto/makecert)。

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> 如果要将证书用于 IP 地址而不是域，请在 -DnsName 参数中使用 IP 地址。


如果要将此[证书用于管理门户](/previous-versions/azure/azure-api-management-certs)，请将其导出到 .cer 文件：

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Internet 上有许多关于如何使用 IIS 实现此操作的信息。 [此页面](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) 就是示例之一，其阐述非常清楚。 

### <a name="linux"></a>Linux
[本文](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)介绍如何通过 SSH 创建证书。

## <a name="next-steps"></a>后续步骤
[将服务证书上传到 Azure 门户](cloud-services-configure-ssl-certificate-portal.md)。

将[管理 API 证书](/previous-versions/azure/azure-api-management-certs)上传到 Azure 门户。