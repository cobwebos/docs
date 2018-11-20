---
title: 验证 Azure Stack 的 AD FS 集成
description: 使用 Azure Stack 就绪性检查器来验证 Azure Stack 的 AD FS 集成。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: patricka
ms.reviewer: jerskine
ms.openlocfilehash: 87e3f03ce5d4c65d5c4b1754300f5d57feca2a49
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416505"
---
# <a name="validate-ad-fs-integration-for-azure-stack"></a>验证 Azure Stack 的 AD FS 集成

使用 Azure Stack 就绪性检查器工具 (AzsReadinessChecker) 来验证你的环境准备好与 Azure Stack 的 Active Directory 联合身份验证服务 (AD FS) 集成。 在开始数据中心集成之前或在 Azure Stack 部署之前验证 AD FS 集成。

就绪性检查器会验证下列项：

* *联合身份验证元数据*包含联合身份验证的有效 XML 元素。
* *AD FS SSL 证书*可以检索，而信任可以生成链。 模具上 AD FS 必须信任的 SSL 证书链。 该证书必须由同一个签名*证书颁发机构*作为 Azure Stack 部署证书或由受信任的根颁发机构合作伙伴。 有关受信任的根颁发机构的合作伙伴的完整列表，请参阅[TechNet](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)。
* *AD FS 签名证书*是受信任和不即将过期。

有关 Azure Stack 数据中心集成的详细信息，请参阅[Azure Stack 数据中心集成-标识](azure-stack-integrate-identity.md)。

## <a name="get-the-readiness-checker-tool"></a>获取就绪性检查器工具

从下载 Azure Stack 就绪性检查器工具 (AzsReadinessChecker) 的最新版本[PowerShell 库](https://aka.ms/AzsReadinessChecker)。  

## <a name="prerequisites"></a>必备组件

必须满足以下先决条件。

**运行该工具的计算机：**

* Windows 10 或 Windows Server 2016 中，与域的连接。
* PowerShell 5.1 或更高版本。 若要检查你的版本，运行以下 PowerShell 命令，然后查看*主要*版本和*次要*版本：  
   > `$PSVersionTable.PSVersion`
* 最新版本的[Microsoft Azure Stack 就绪性检查器](https://aka.ms/AzsReadinessChecker)工具。

**Active Directory 联合身份验证服务环境：**

您需要至少一个以下形式的元数据：

* AD FS 联合身份验证元数据的 URL。 例如 `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`。
* 联合身份验证元数据 XML 文件。 例如，FederationMetadata.xml。

## <a name="validate-ad-fs-integration"></a>验证 AD FS 集成

1. 上满足的先决条件的计算机，打开管理 PowerShell 提示符并运行以下命令以安装 AzsReadinessChecker:

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. 从 PowerShell 提示符处，运行以下命令以启动验证。 指定的值 **-CustomADFSFederationMetadataEndpointUri**作为联合身份验证元数据的 URI。

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. 运行该工具后，查看输出。 确认，状态为正常的 AD FS 集成要求。 成功验证是类似于下面的示例：

    ```
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

在生产环境中测试的操作员的工作站从信任的证书链不是 Azure Stack 基础结构中的 PKI 信任状况完全表示。 Azure Stack 戳记的公共 VIP 网络需要 PKI 基础结构连接到 CRL。

## <a name="report-and-log-file"></a>报表和日志文件

每次运行验证时，它都会将结果记录到 **AzsReadinessChecker.log** 和 **AzsReadinessCheckerReport.json** 中。 在 PowerShell 中的验证结果将显示这些文件的位置。

验证文件可以帮助您在部署 Azure Stack 或调查的验证问题之前共享状态。 这两个文件都会持久保留每个后续验证检查的结果。 该报表提供您的标识配置的部署团队确认信息。 日志文件可以帮助你的部署或支持团队调查验证问题。

默认情况下，这两个文件写入到`C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`。

使用：

* **-OutputPath**:*路径*参数来指定不同的报表位置的运行命令的末尾。
* **-CleanReport**： 运行命令清除以前的报表信息 AzsReadinessCheckerReport.json 末尾处的参数。 有关详细信息，请参阅[Azure Stack 验证报表](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败

如果验证检查失败，请在 PowerShell 窗口中显示有关失败的详细信息。 该工具还将记录到的信息*AzsReadinessChecker.log*。

下面的示例针对常见的验证失败提供了指导。

### <a name="command-not-found"></a>找不到命令

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**原因**: PowerShell Autoload 未能正确加载就绪性检查器模块。

**解析**： 显式导入就绪性检查器模块。 复制并将以下代码粘贴到 PowerShell 并更新\<版本\>与当前安装的版本数。

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>后续步骤

[查看就绪性报表](azure-stack-validation-report.md)  
[有关 Azure Stack 集成的一般注意事项](azure-stack-datacenter-integration.md)  
