---
title: "验证 Azure 堆栈公钥基础结构的集成的 Azure 堆栈系统部署的证书 |Microsoft 文档"
description: "描述如何验证 Azure 堆栈集成系统的 Azure 堆栈 PKI 证书。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: 86f1b889d83905abfb5ddab2e82f32922409ff5f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="validate-azure-stack-pki-certificates"></a>验证 Azure 堆栈 PKI 证书
本文中所述使用 Azure 堆栈证书检查器工具提供的 OEM 附带 deploymentdata.json 文件，以便验证[生成 PKI 证书](azure-stack-get-pki-certs.md)适合于部署前。 证书应具有足够的时间来测试，并且获取如有必要重新颁发的证书验证。 

证书检查器工具 (Certchecker) 执行以下检查：

- **读取 PFX**。 检查有效的 PFX 文件，正确的密码，并会发出警告如果公共信息不受密码保护密码。 
- **签名算法**。 检查并非 SHA1 签名算法。 
- **私钥**。 检查私钥为存在并具有本地计算机属性导出。 
- **证书链**。 检查证书链，则在原封不动包括为自签名证书。 
- **DNS 名称**。 检查 SAN 包含每个终结点相关的 DNS 名称或支持通配符是否存在。 
- **密钥用法**。 检查密钥用法包含数字签名和密钥加密和增强型密钥用法包含服务器身份验证和客户端身份验证。 
- **密钥大小**。 检查密钥大小为 2048年或更大 
- **链接顺序**。 检查的顺序进行链的其他证书正确。 
- **其他证书**。 确保任何其他证书获取已打包在 PFX 中相关叶证书和其链以外。 
- **没有配置文件**。 检查新的用户可以加载 PFX 数据而用户配置文件加载，在证书维护期间模仿 gMSA 帐户的行为。   

> [!IMPORTANT]
> PKI 证书 PFX 文件和密码应视为敏感信息。

## <a name="prerequisites"></a>必备组件
验证 Azure 堆栈部署的 PKI 证书之前，你的系统应满足以下先决条件：
- （在下 \utils\certchecker PartnerToolKit) CertChecker
- SSL 证书导出以下[准备说明](prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 或 Windows Server 2016

## <a name="perform-certificate-validation"></a>执行证书验证
使用以下步骤来准备和验证 Azure 堆栈 PKI 证书： 

1. 内容提取<partnerToolkit>到新目录，例如，\utils\certchecker **c:\certchecker**。

2. 以管理员身份打开 PowerShell 并将目录更改为 certchecker 文件夹：

  ```powershell
  cd c:\certchecker
  ```
 
3. 通过运行以下 PowerShell 命令创建证书的目录结构：

  ```powershell 
  $directories = "ACS","ADFS","Admin Portal","ARM Admin","ARM Public","Graph","KeyVault","KeyVaultInternal","Public Portal" 
  $destination = '.\certs' 
  $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}  
  ```

  >  [!NOTE]
  >  如果 Azure 堆栈部署的标识提供程序，Azure AD 删除**ADFS**和**Graph**目录。 

4. 将你的证书放在相应的目录，例如在上一步中创建： 
  - c:\certchecker\Certs\ACS\CustomerCertificate.pfx,  
  - c:\certchecker\Certs\Admin Portal\CustomerCertificate.pfx  
  - c:\certchecker\Certs\ARM Admin\CustomerCertificate.pfx  
  - 等等... 

5. 复制**deploymentdata.json**到**c:\certchecker**目录。

6. 在 PowerShell 窗口中运行以下命令： 

  ```powershell
  $password = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
  .\CertChecker.ps1 -CertificatePath .\Certs\ -pfxPassword $password -deploymentDataJSONPath .\DeploymentData.json  
  ```

7. 输出应包含所有证书和检查的所有属性的确定: 

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
**症状**: Certchecker 过早地退出，并收到以下错误： 
> 已失败

> 详细信息: 此命令不能运行由于错误: 目录名称无效。 

**可能的原因**： 从限制性的文件夹，例如运行 certchecker.ps1、 c:\temp 或 %temp% 

**解析**： 将 certchecker 工具移动到新目录，例如，C:\CertChecker 


**症状**: Certchecker 会对有关 （如上面步骤 7 中的示例） 使用前 1803年发出警告：

> [!WARNING]
> Pre 1803 证书结构。 文件夹结构，为 Azure 堆栈 1803年和更高版本： ACSBlob，ACSQueue，ACSTable 而不是 ACS 文件夹。 有关详细信息请参阅部署文档。

**可能的原因**: CertChecker 检测到了利用单个 ACS 文件夹，这是正确的部署之前 1803年。 Azure 堆栈版本 1803年及更高版本部署，文件夹结构更改为 ACSTable，ACSQueue，ACSBlob。 Certchecker 已更新为支持此功能。

**解析**： 如果部署 1802年，不需要任何操作。 如果部署 1803年及以上版本，将替换为 ACSTable、 ACSQueue、 ACSBlob ACS，然后将 ACS 证书复制到这些文件夹。

**症状**： 测试将跳过

**可能的原因**: CertChecker 跳过某些测试，如果不满足依赖关系：
- 如果证书链将失败，则将跳过其他证书。
- 没有配置文件才会跳过：
  - 没有限制的能力来创建临时用户并以该用户身份运行 powershell 的安全策略。
  - 私钥检查失败。

**解析**： 按照每个证书的一组测试下的详细信息部分中的工具指导。


## <a name="prepare-deployment-script-certificates"></a>准备部署脚本证书 
作为最后一步，需要在部署主机上的相应目录中放置你已经准备的所有证书。 你部署在主机上，创建名为的文件夹。 证书 * * 和位置你导出的证书文件中指定相应的子文件夹中[必需证书](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates)部分：

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

<sup>*</sup> 证书标有星号 * AD FS 使用作为标识存储区时才需要。


## <a name="next-steps"></a>后续步骤
[数据中心标识集成](azure-stack-integrate-identity.md)