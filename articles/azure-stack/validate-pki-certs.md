---
title: 验证用于 Azure Stack 集成系统部署的 Azure Stack 公钥基础结构证书 | Microsoft Docs
description: 介绍如何验证 Azure Stack 集成系统的 Azure Stack PKI 证书。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: b38e3cc45d14645611c0cd804f2bfa66047810f0
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>验证 Azure Stack PKI 证书
本文中所述的 Azure Stack 证书检查器工具由 OEM 提供，它附带 deploymentdata.json 文件，用于验证[生成的 PKI 证书](azure-stack-get-pki-certs.md)是否适用于前期部署。 应该花费足够的时间来验证证书，以测试证书并在必要时重新颁发证书。 

证书检查器工具 (Certchecker) 执行以下检查：

- **读取 PFX**。 检查 PFX 文件是否有效且密码正确，在公开的信息不受到密码保护时发出警告。 
- **签名算法**。 检查签名算法是否不是 SHA1 
- **私钥**。 检查私钥是否存在，并且已连同“本地计算机”属性一起导出。 
- **证书链**。 检查自签名证书的证书链是否完整。 
- **DNS 名称**。 检查 SAN 是否包含每个终结点的相关 DNS 名称，或支持性的通配符是否存在。 
- **密钥用法**。 检查密钥用法是否包含数字签名和密钥加密，增强型密钥用法是否包含服务器身份验证和客户端身份验证。 
- **密钥大小**。 检查密钥大小是否为 2048 或更大 
- **链序**。 检查其他证书的顺序是否能使链保持正常。 
- **其他证书**。 确保除了相关叶证书及其链以外，PFX 中未打包其他证书。 
- **无配置文件**。 检查新用户是否无需加载用户配置文件即可加载 PFX 数据，可在证书有效期内模拟 gMSA 帐户的行为。   

> [!IMPORTANT]
> PKI 证书的 PFX 文件和密码应被视为敏感信息。

## <a name="prerequisites"></a>必备组件
在验证用于 Azure Stack 部署的 PKI 证书之前，系统应符合以下先决条件：
- CertChecker（在 \utils\certchecker 下的 PartnerToolKit 中）
- 遵照[准备说明](prepare-pki-certs.md)导出的 SSL 证书
- DeploymentData.json
- Windows 10 或 Windows Server 2016

## <a name="perform-certificate-validation"></a>执行证书验证
使用以下步骤来准备和验证 Azure Stack PKI 证书： 

1. 将 <partnerToolkit>\utils\certchecker 的内容提取到新目录，例如 **c:\certchecker**。

2. 以管理员身份打开 PowerShell，并将目录切换到 certchecker 文件夹：

  ```powershell
  cd c:\certchecker
  ```
 
3. 运行以下 PowerShell 命令创建证书的目录结构：

  ```powershell 
  $directories = "ACS","ADFS","Admin Portal","ARM Admin","ARM Public","Graph","KeyVault","KeyVaultInternal","Public Portal" 
  $destination = '.\certs' 
  $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
  ```

  >  [!NOTE]
  >  如果 Azure Stack 部署的标识提供者为 Azure AD，请删除 **ADFS** 和 **Graph** 目录。 

4. 将证书放入上一步骤中创建的相应目录，例如： 
  - c:\certchecker\Certs\ACS\CustomerCertificate.pfx  
  - c:\certchecker\Certs\Admin Portal\CustomerCertificate.pfx  
  - c:\certchecker\Certs\ARM Admin\CustomerCertificate.pfx  
  - 等等 

5. 将 **deploymentdata.json** 复制到 **c:\certchecker** 目录。

6. 在 PowerShell 窗口中运行以下命令： 

  ```powershell
  $password = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
  .\CertChecker.ps1 -CertificatePath .\Certs\ -pfxPassword $password -deploymentDataJSONPath .\DeploymentData.json  
  ```

7. 针对所有证书运行该命令并检查所有属性后，输出中应该包含 OK： 

  ```powershell
  Starting Azure Stack Certificate Validation 1.1802.221.1
  Testing: ADFS\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: KeyVaultInternal\ContosoSSL.pfx
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Testing: ACS\ContosoSSL.pfx
  WARNING: Pre-1803 certificate structure. The folder structure for Azure Stack 1803 and above is: ACSBlob, ACSQueue, ACSTable instead of ACS folder. Refer to deployment documentation for further informat
  ion.
    Read PFX: OK
    Signature Algorithm: OK
    Private Key: OK
    Cert Chain: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Chain Order: OK
    Other Certificates: OK
    No Profile: OK
  Detailed log can be found C:\CertChecker\CertChecker.log 
  ```

### <a name="known-issues"></a>已知问题 
**症状**：Certchecker 提前退出，并出现以下错误： 
> 已失败

> 详细信息: 由于发生错误，无法运行此命令: 目录名称无效。 

**原因**：从受限的文件夹（例如 c:\temp 或 %temp%）运行 certchecker.ps1 

**解决方法**：将 certchecker 工具移到新目录，例如 C:\CertChecker 


**症状**：Certchecker 发出了有关使用 Pre-1803 的警告（如上述步骤 7 中的示例所示）：

> [!WARNING]
> Pre-1803 证书结构。 Azure Stack 1803 和更高版本的文件夹结构为: ACSBlob、ACSQueue、ACSTable，而不是 ACS 文件夹。 有关详细信息，请参阅部署文档。

**原因**：CertChecker 检测到使用了单个 ACS 文件夹，这在版本低于 1803 的部署是正确的。 对于 Azure Stack 1803 和更高版本的部署，文件夹结构已更改为 ACSTable、ACSQueue、ACSBlob。 Certchecker 已更新为支持此功能。

**解决方法**：如果部署的是 1802，则无需采取任何措施。 如果部署的是 1803 和更高版本，请将 ACS 替换为 ACSTable、ACSQueue、ACSBlob，并将 ACS 证书复制到这些文件夹。

**症状**：已跳过测试

**原因**：如果不符合依赖关系，CertChecker 会跳过某些测试：
- 如果证书链出错，则会跳过其他证书。
- 在以下情况下，不会跳过任何配置文件：
  - 安全策略限制了创建临时用户的功能，并以该用户的身份运行 Powershell。
  - 私钥检查失败。

**解决方法**：遵循每个证书测试集下的详细信息部分中的工具指导。


## <a name="prepare-deployment-script-certificates"></a>准备部署脚本证书 
最后一个步骤是将已准备好的所有证书放入部署主机上的相应目录。 在部署主机上，创建名为 Certificates** 的文件夹，并将导出的证书文件放入[必需的证书](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates)部分中指定的相应子文件夹：

```
\Certificates
\ACS\ssl.pfx
\Admin Portal\ssl.pfx
\ARM Admin\ssl.pfx
\ARM Public\ssl.pfx
\KeyVault\ssl.pfx
\KeyVaultInternal\ssl.pfx
\Public Portal\ssl.pfx
\ADFS\ssl.pfx*
\Graph\ssl.pfx*
```

<sup>*</sup> 仅当使用 AD FS 作为标识存储时，才需要带有星号 * 标记的证书。


## <a name="next-steps"></a>后续步骤
[数据中心标识集成](azure-stack-integrate-identity.md)