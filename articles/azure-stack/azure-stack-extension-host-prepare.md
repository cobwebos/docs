---
title: 为 Azure Stack 的扩展主机准备 |Microsoft Docs
description: 了解如何准备扩展主机，会自动启用通过将来的 Azure Stack 更新包。
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 09/05/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.openlocfilehash: 02ff83b41af47492a67bea94c5e5deecec42d15e
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025985"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>准备适用于 Azure Stack 扩展主机

扩展主机通过减少所需的 TCP/IP 端口的数量来保护 Azure Stack。 此文章讨论了为 1808年更新后的 Azure Stack 更新包通过自动启用的扩展主机准备 Azure Stack。

## <a name="certificate-requirements"></a>证书要求

扩展主机实现两个新域的命名空间以保证每个门户的扩展插件的唯一的主机条目。 新的域命名空间需要两个其他的通配符证书，以确保通信安全。

表显示了新的命名空间和相关联的证书：

| 部署文件夹 | 所需的证书使用者和使用者可选名称 (SAN) | 范围（按区域） | 子域命名空间 |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| 管理员扩展主机 | *.adminhosting。\<区域 >。\<fqdn > （通配符 SSL 证书） | 管理员扩展主机 | adminhosting。\<区域 >。\<fqdn > |
| 公共扩展主机 | *.hosting。\<区域 >。\<fqdn > （通配符 SSL 证书） | 公共扩展主机 | 承载。\<区域 >。\<fqdn > |

可以在中找到详细的证书要求[Azure Stack 公钥基础结构证书要求](azure-stack-pki-certs.md)一文。

## <a name="create-certificate-signing-request"></a>创建证书签名请求

Azure Stack 就绪状态检查器工具提供的功能来创建证书签名请求的两个新的、 需要 SSL 证书。 按照本文中的步骤[Azure Stack 证书签名请求生成](azure-stack-get-pki-certs.md)。

> [!Note]  
> 您可以跳过此步骤，具体取决于请求 SSL 证书的方式。

## <a name="validate-new-certificates"></a>验证新证书

1. 使用硬件生命周期主机或 Azure Stack 管理工作站上的提升权限打开 PowerShell。
2. 运行以下 cmdlet 以安装 Azure Stack 就绪性检查器工具。

    ```PowerShell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. 运行以下脚本以创建所需的文件夹结构：

    ```PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > 如果使用 Azure Active Directory 联合身份验证服务 (AD FS) 部署在以下目录必须添加到 **$directories**在脚本中： `ADFS`， `Graph`。

4. 运行以下 cmdlet 以启动证书检查：

    ```PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD -ExtensionHostFeature $true
    ```

5. 将证书放在相应目录。

6. 检查输出和所有证书是否通过所有测试。


## <a name="import-extension-host-certificates"></a>导入扩展主机证书

使用的计算机可以连接到 Azure Stack 特权终结点以获取后续措施。 请确保可以访问新的证书文件从该计算机。

1. 使用的计算机可以连接到 Azure Stack 特权终结点以获取后续措施。 请确保你从该计算机访问新的证书文件。
2. 打开 PowerShell ISE，若要执行的下一步的脚本块
3. 导入承载终结点的证书。 调整脚本以匹配你的环境。
4. 为承载终结点管理员导入证书。

    ```PowerShell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]] $AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputeName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
5. 导入托管终结点的证书。
    ```PowerShell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]] $HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputeName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```



### <a name="update-dns-configuration"></a>更新 DNS 配置

> [!Note]  
> 如果使用 DNS 集成 DNS 区域委派，则不需要此步骤。
如果已配置单独的主机 A 记录来发布 Azure Stack 终结点，您需要创建两个额外的主机 A 记录：

| IP | 主机名 | Type |
|----|------------------------------|------|
| \<IP &GT; | Adminhosting。<Region>.<FQDN> | A |
| \<IP &GT; | 承载。<Region>.<FQDN> | A |

可以通过运行 cmdlet 使用特权终结点检索已分配的 Ip **Get AzureStackStampInformation**。

### <a name="ports-and-protocols"></a>端口和协议

本文中， [Azure Stack 数据中心集成-发布终结点](azure-stack-integrate-endpoints.md)，涵盖的端口和协议需要扩展主机推出之前发布 Azure Stack 的入站的通信。

### <a name="publish-new-endpoints"></a>发布新的终结点

有两个新的终结点需通过防火墙进行发布。 可以使用 cmdlet 检索从公共 VIP 池已分配的 Ip **Get AzureStackStampInformation**。

> [!Note]  
> 进行此更改，然后再启用扩展主机。 这样，Azure Stack 门户可以连续访问。

| 终结点 (VIP) | 协议 | 端口 |
|----------------|----------|-------|
| AdminHosting | HTTPS | 443 |
| Hosting | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>更新现有发布规则 （开机自检启用的扩展主机）

> [!Note]  
> Azure Stack 更新包可执行 1808年**不**尚未启用扩展主机。 它允许以准备扩展主机所导入所需的证书。 扩展主机自动启用之前通过 Azure Stack 更新包 1808年更新后，不要关闭任何端口。

在现有防火墙规则中，必须关闭以下现有终结点的端口。

> [!Note]  
> 建议在成功验证后关闭这些端口。

| 终结点 (VIP) | 协议 | 端口 |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| 门户（管理员） | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015 |
| 门户（用户） | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003 |
| Azure 资源管理器（管理员） | HTTPS | 30024 |
| Azure 资源管理器（用户） | HTTPS | 30024 |

## <a name="next-steps"></a>后续步骤

- 了解如何[防火墙集成](azure-stack-firewall.md)。
- 了解有关[Azure Stack 证书签名请求生成](azure-stack-get-pki-certs.md)
