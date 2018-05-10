---
title: 有关 Azure 堆栈修正证书问题 |Microsoft 文档
description: 使用 Azure 堆栈准备情况检查器以查看并解决证书问题。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 0d2c4d848f861e4e07dbd0de4609344955ca26f7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>修正常见的 Azure 堆栈 PKI 证书问题
这篇文章中的信息可以帮助您了解和解决常见的 Azure 堆栈 PKI 证书问题。 使用 Azure 堆栈准备情况检查程序工具，可以发现问题[验证 Azure 堆栈 PKI 证书](azure-stack-validate-pki-certs.md)。 该工具会检查以确保证书满足 Azure 堆栈部署和 Azure 堆栈机密旋转的 PKI 要求和结果记录[report.json 文件](azure-stack-validation-report.md)。  

## <a name="read-pfx"></a>读取 PFX
**警告**-密码仅保护证书中的私有信息。  
**修正**-我们建议您将 PFX 文件导出的可选设置**启用证书隐私**。  

**失败**-PFX 文件无效。  
**修正**-再次导出证书的步骤以使用[的部署准备 Azure 堆栈 PKI 证书](azure-stack-prepare-pki-certs.md)。

## <a name="signature-algorithm"></a>签名算法
**失败**的签名算法是 SHA1。    
**修正**-使用 Azure 堆栈证书签名请求生成重新生成证书签名请求 (CSR) 利用签名 SHA256 算法中的步骤。 然后重新提交至证书颁发机构，以重新颁发证书的 CSR。

## <a name="private-key"></a>专用密钥
**失败**-的私钥缺少或不包含本地机属性。  
**修正**-从生成 CSR 的计算机的重新导出步骤中准备 Azure 堆栈 PKI 证书用于部署的证书。 这些步骤包括从本地计算机证书存储中导出。

## <a name="certificate-chain"></a>证书链
**失败**-证书链不完整。  
**修正**-证书应包含完整证书链。  重新导出证书的步骤以使用[的部署准备 Azure 堆栈 PKI 证书](azure-stack-prepare-pki-certs.md)并选择的选项**如果可能包括证书路径中的所有证书。**

## <a name="dns-names"></a>DNS 名称
**失败**-证书上的 DNSNameList 不包含 Azure 堆栈服务终结点名称或有效的通配符匹配。  通配符匹配项是仅有效的 DNS 名称的最左边的命名空间。 例如， _*。 region.domain.com_仅可用于*portal.region.domain.com*，而不 _*。 table.region.domain.com_。  
**修正**-Azure 堆栈证书签名请求生成重新生成具有正确的 DNS 名称 CSR 中使用的步骤，以支持 Azure 堆栈终结点。 重新提交到证书颁发机构，CSR，然后按照中的步骤[的部署准备 Azure 堆栈 PKI 证书](azure-stack-prepare-pki-certs.md)生成 CSR 的计算机中导出的证书。  

## <a name="key-usage"></a>密钥用法
**失败**-密钥用法缺少数字签名或密钥加密，orEnhanced 密钥用法缺少服务器身份验证或客户端身份验证。  
**修正**-使用中的步骤[Azure 堆栈证书签名请求生成](azure-stack-get-pki-certs.md)重新生成具有正确的密钥用法属性 CSR。  重新提交至证书颁发机构的 CSR 并确认证书模板不覆盖请求中的密钥用法。

## <a name="key-size"></a>密钥大小
**失败**的密钥大小小于 2048年    
**修正**-使用中的步骤[Azure 堆栈证书签名请求生成](azure-stack-get-pki-certs.md)以重新生成 CSR 与正确的密钥长度 (2048)，然后重新提交至证书颁发机构的 CSR。

## <a name="chain-order"></a>链顺序
**失败**-证书链的顺序不正确。  
**修正**-再次导出证书的步骤以使用[的部署准备 Azure 堆栈 PKI 证书](azure-stack-prepare-pki-certs.md)并选择的选项**如果可能包括证书路径中的所有证书。** 确保只有叶证书选择要导出。 

## <a name="other-certificates"></a>其他证书
**失败**-PFX 包包含不是叶证书或证书链的一部分的证书。  
**修正**-再次导出证书的步骤以使用[的部署准备 Azure 堆栈 PKI 证书](azure-stack-prepare-pki-certs.md)，并选择的选项**如果可能包括证书路径中的所有证书。** 确保只有叶证书选择要导出。

## <a name="fix-common-packaging-issues"></a>修复打包常见问题
AzsReadinessChecker 可以导入，然后导出 PFX 文件以修复常见打包问题，包括： 
 - *私钥*缺少本地机属性。
 - *证书链*不完整或不正确。 （本地计算机必须包含的证书链如果不存在 PFX 包。） 
 - *其他证书*。
但是，如果你需要生成新的 CSR 并重新颁发证书，不能帮助 AzsReadinessChecker。 

### <a name="prerequisites"></a>必备组件
将计算机上运行该工具必须下列先决条件： 
 - Windows 10 或 Windows Server 2016 中，在具有 internet 连接。
 - PowerShell 5.1 或更高版本。 若要检查你的版本，运行以下 PowerShell cmd，然后查看*主要*版本和*次要*版本。

   > `$PSVersionTable.PSVersion`
 - 配置[PowerShell for Azure 堆栈](azure-stack-powershell-install.md)。 
 - 下载最新版本的[Microsoft Azure 堆栈准备情况检查程序](https://aka.ms/AzsReadinessChecker)工具。

### <a name="import-and-export-an-existing-pfx-file"></a>导入和导出现有的 PFX 文件
1. 在满足先决条件的计算机，打开的管理的 PowerShell 提示符，然后运行以下命令以安装 AzsReadinessChecker  
   > `Install-Module Microsoft.AzureStack.ReadinessChecker- Force`

2. 在 PowerShell 提示符下，运行以下命令以设置 PFX 密码。 替换*PFXpassword*使用实际密码。 
   > `$password = Read-Host -Prompt PFXpassword -AsSecureString`

3. 在 PowerShell 提示符下，运行下列命令以导出新的 PFX 文件。
   - 有关 *-PfxPath*，指定你正在使用的 PFX 文件的路径。  在下面的示例中，该路径是 *.\certificates\ssl.pfx*。
   - 有关 *-ExportPFXPath*，指定的位置和导出的 PFX 文件的名称。  在下面的示例中，该路径是 *.\certificates\ssl_new.pfx*

   > `Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx`  

4. 此工具完成后，请查看成功的输出：![结果](./media/azure-stack-remediate-certs/remediate-results.png)

## <a name="next-steps"></a>后续步骤
[详细了解 Azure Stack 安全性](azure-stack-rotate-secrets.md)