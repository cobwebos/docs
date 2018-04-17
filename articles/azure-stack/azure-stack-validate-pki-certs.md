---
title: 验证用于 Azure Stack 集成系统部署的 Azure Stack 公钥基础结构证书 | Microsoft Docs
description: 介绍如何验证 Azure Stack 集成系统的 Azure Stack PKI 证书。 介绍如何使用 Azure 堆栈证书检查程序工具。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: cd917165804314f6ee4ee006e3f29263d8d4b4c5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>验证 Azure Stack PKI 证书

在本文中所述的 Azure 堆栈准备情况检查程序工具可[从 PowerShell 库](https://aka.ms/AzsReadinessChecker)。 你可以使用该工具以验证[生成 PKI 证书](azure-stack-get-pki-certs.md)适合于部署前。 留出足够的时间来测试和重新颁发证书，如有必要，应验证证书。

准备情况检查程序工具执行以下证书验证：

- **读取 PFX**  
    检查有效的 PFX 文件，正确的密码，并会发出警告如果公共信息不受密码保护密码。 
- **签名算法**  
    检查的签名算法不是 SHA1。
- **私钥**  
    检查私钥存在并具有本地计算机属性导出。 
- **证书链**  
    检查证书链，则包括自签名证书的检查不变。
- **DNS 名称**  
    检查 SAN 是否包含每个终结点的相关 DNS 名称，或支持性的通配符是否存在。
- **密钥用法**  
    如果密钥用法包含数字签名和密钥加密和增强型密钥用法包含服务器身份验证和客户端身份验证检查。
- **密钥大小**  
    检查是否 2048年或更大的密钥大小。
- **链顺序**  
    检查其他证书验证的顺序正确的顺序。
- **其他证书**  
    确保除了相关叶证书及其链以外，PFX 中未打包其他证书。

> [!IMPORTANT]  
> PKI 证书为 PFX 文件和密码应被视为敏感信息。

## <a name="prerequisites"></a>必备组件

验证 Azure 堆栈部署的 PKI 证书之前，你的系统应满足以下先决条件：

- Microsoft Azure 堆栈准备情况检查程序
- 遵照[准备说明](azure-stack-prepare-pki-certs.md)导出的 SSL 证书
- DeploymentData.json
- Windows 10 或 Windows Server 2016

## <a name="perform-certificate-validation"></a>执行证书验证

若要准备和若要验证 Azure 堆栈 PKI 证书，请使用以下步骤：

1. 通过运行以下 cmdlet 从 PowerShell 提示符下 （5.1 或更高版本），安装 AzsReadinessChecker:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker 
    ````

2. 创建证书目录结构。 在下面的示例中，你可以更改`<c:\certificates>`到所选的新的目录路径。

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal' 

    $destination = 'c:\certificates' 

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
    ````

 - 将你的证书放在上一步中创建的相应目录。 例如：  
    - c:\certificates\ACSBlob\CustomerCertificate.pfx 
    - c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx 
    - c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx 
    - 等等 

3. 在 PowerShell 窗口中运行：

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ````

4. 检查输出，以验证所有证书都通过的测试。 例如：

    ````PowerShell
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Validation

    Starting Azure Stack Certificate Validation 1.1803.405.3
    Testing: ARM Public\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ACSBlob\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessReport.json
    AzsReadinessChecker Completed
    ````

### <a name="known-issues"></a>已知问题

**症状**：已跳过测试

**可能的原因**: AzsReadinessChecker 跳过某些测试，如果不满足依赖关系：

 - 如果证书链将失败，则将跳过其他证书。

    ````PowerShell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

**解析**： 请按照下每个组的测试的每个证书的详细信息部分中的工具的指导。

## <a name="using-validated-certificates"></a>使用已验证的证书

一旦通过 AzsReadinessChecker 验证了你的证书，你就可以使用它们在你的 Azure 堆栈部署或对 Azure 堆栈密钥轮换。 

 - 对于部署，将安全传送你的证书到你部署的工程师，以便它们可以将它们复制到指定在部署主机[Azure 堆栈 PKI 要求文档](azure-stack-pki-certs.md)。
 - 对密钥轮换，你可以使用证书通过以下更新 Azure 堆栈环境的公共基础结构终结点的旧证书[Azure 堆栈机密旋转文档](azure-stack-rotate-secrets.md)。

## <a name="next-steps"></a>后续步骤

[数据中心标识集成](azure-stack-integrate-identity.md)
