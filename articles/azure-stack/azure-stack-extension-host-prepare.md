---
title: 为 Azure Stack 准备扩展主机 | Microsoft Docs
description: 了解如何准备扩展主机，它是通过将来的一个 Azure Stack 更新程序包自动启用的。
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 11/02/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.openlocfilehash: 4376b9e89aeef32987f7a3bb29ca6815e941ba00
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2018
ms.locfileid: "50960236"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>为 Azure Stack 准备扩展主机

扩展主机通过减少所需的 TCP/IP 端口数来保护 Azure Stack。 本文讨论了为 Azure Stack 准备扩展主机，扩展主机是通过 1808 更新之后的一个 Azure Stack 更新程序包自动启用的。

## <a name="certificate-requirements"></a>证书要求

扩展主机实施了两个新的域命名空间来为每个门户扩展保证主机条目的唯一性。 新的域命名空间需要两个额外的通配符证书来确保安全的通信。

下表显示了新的命名空间和关联的证书：

| 部署文件夹 | 所需的证书使用者和使用者可选名称 (SAN) | 范围（按区域） | 子域命名空间 |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| 管理扩展主机 | *.adminhosting.\<region>.\<fqdn>（通配符 SSL 证书） | 管理扩展主机 | adminhosting.\<region>.\<fqdn> |
| 公共扩展主机 | *.hosting.\<region>.\<fqdn>（通配符 SSL 证书） | 公共扩展主机 | hosting.\<region>.\<fqdn> |

可以在 [Azure Stack 公钥基础结构证书要求](azure-stack-pki-certs.md)一文中找到详细的证书要求。

## <a name="create-certificate-signing-request"></a>创建证书签名请求

Azure Stack 就绪性检查器工具能够为两个新的必需 SSL 证书创建证书签名请求。 请按照 [Azure Stack 证书签名请求生成](azure-stack-get-pki-certs.md)一文中的步骤进行操作。

> [!Note]  
> 你可以跳过此步骤，具体取决于你请求 SSL 证书的方式。

## <a name="validate-new-certificates"></a>验证新证书

1. 使用提升的权限在硬件生命周期主机或 Azure Stack 管理工作站上打开 PowerShell。
2. 运行以下 cmdlet 来安装 Azure Stack 就绪性检查器工具。

    ```PowerShell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. 运行以下脚本来创建必需的文件夹结构：

    ```PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > 如果使用 Azure Active Directory 联合身份验证服务 (AD FS) 进行部署，则必须在脚本中的 **$directories** 中添加以下目录：`ADFS`、`Graph`。

4. 运行以下 cmdlet 来启动证书检查：

    ```PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

5. 将证书放在合适的目录中。

6. 检查输出和所有证书是否通过所有测试。


## <a name="import-extension-host-certificates"></a>导入扩展主机证书

使用可以连接到 Azure Stack 特权终结点的计算机执行后续步骤。 请确保可以从该计算机访问新的证书文件。

1. 使用可以连接到 Azure Stack 特权终结点的计算机执行后续步骤。 请确保可以从该计算机访问新的证书文件。
2. 打开 PowerShell ISE 以执行接下来的脚本块
3. 导入用于托管终结点的证书。 调整脚本以匹配你的环境。
4. 导入用于管理托管终结点的证书。

    ```PowerShell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
5. 导入用于托管终结点的证书。
    ```PowerShell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
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
> 如果使用了 DNS 区域委派进行 DNS 集成，则此步骤不是必需的。
如果已配置了单独的主机 A 记录来发布 Azure Stack 终结点，则需要创建两个额外的主机 A 记录：

| IP | 主机名 | 类型 |
|----|------------------------------|------|
| \<IP> | Adminhosting.<Region>.<FQDN> | A |
| \<IP> | Hosting.<Region>.<FQDN> | A |

可以通过运行 cmdlet **Get-AzureStackStampInformation** 使用特权终结点检索已分配的 IP。

### <a name="ports-and-protocols"></a>端口和协议

[Azure Stack 数据中心集成 - 发布终结点](azure-stack-integrate-endpoints.md)一文介绍了在推出扩展主机之前进行入站通信以发布 Azure Stack 所需的端口和协议。

### <a name="publish-new-endpoints"></a>发布新的终结点

需要通过防火墙发布两个新的终结点。 可以使用 cmdlet **Get-AzureStackStampInformation** 从公共 VIP 池检索已分配的 IP。

> [!Note]  
> 请在启用扩展主机前进行此更改。 这使得 Azure Stack 门户持续可访问。

| 终结点 (VIP) | 协议 | 端口 |
|----------------|----------|-------|
| AdminHosting | HTTPS | 443 |
| Hosting | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>更新现有的发布规则（在启用扩展主机后）

> [!Note]  
> 1808 Azure Stack 更新包**尚未**启用扩展主机。 它允许通过导入所需的证书来准备扩展主机。 在通过 1808 更新之后的 Azure Stack 更新包自动启用扩展主机之前，请不要关闭任何端口。

在现有的防火墙规则中，必须关闭以下终结点端口。

> [!Note]  
> 建议在成功验证后关闭这些端口。

| 终结点 (VIP) | 协议 | 端口 |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| 门户（管理员） | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015 |
| 门户（用户） | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003 |
| Azure 资源管理器（管理员） | HTTPS | 30024 |
| Azure 资源管理器（用户） | HTTPS | 30024 |

## <a name="next-steps"></a>后续步骤

- 了解[防火墙集成](azure-stack-firewall.md)。
- 了解 [Azure Stack 证书签名请求生成](azure-stack-get-pki-certs.md)
