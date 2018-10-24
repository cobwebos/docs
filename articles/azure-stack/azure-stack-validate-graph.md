---
title: 验证 Azure Stack 的 Azure Graph 集成
description: 使用 Azure Stack 就绪性检查器来验证 Azure Stack 的 graph 集成。
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
ms.openlocfilehash: e1c1ba0a065a20874bf51d7464cbcfdfa13a571d
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947384"
---
# <a name="validate-graph-integration-for-azure-stack"></a>验证 Azure Stack 的 graph 集成

使用 Azure Stack 就绪性检查器工具 (AzsReadinessChecker) 来验证你的环境准备好进行 Azure Stack 与 graph 集成。 在开始数据中心集成之前或在 Azure Stack 部署之前，应验证 graph 集成。

就绪性检查器会验证下列项：

* 创建 graph 集成的服务帐户的凭据具有相应的权限查询 Active Directory。
* *全局编录*可以被解析，并且是访问。
* KDC 可以被解析，并且是访问。
* 必要的网络连接已准备就绪。

有关 Azure Stack 数据中心集成的详细信息，请参阅[Azure Stack 数据中心集成-标识](azure-stack-integrate-identity.md)

## <a name="get-the-readiness-checker-tool"></a>获取就绪性检查器工具

请从 [PSGallery](https://aka.ms/AzsReadinessChecker) 下载 Azure Stack 就绪性检查器工具 (AzsReadinessChecker) 的最新版本。

## <a name="prerequisites"></a>必备组件

必须满足以下先决条件。

**运行该工具的计算机：**

* Windows 10 或 Windows Server 2016 中，与域的连接。
* PowerShell 5.1 或更高版本。 若要检查版本，请运行以下 PowerShell cmd，然后查看 *Major* 版本和 *Minor* 版本：  
   > `$PSVersionTable.PSVersion`
* Active Directory PowerShell 模块
* 最新版[Microsoft Azure Stack 就绪性检查器](https://aka.ms/AzsReadinessChecker)工具

**Active Directory 环境：**

* 标识的用户名和密码中现有的 Active Directory 的 graph 服务的帐户
* 确定 Active Directory 林根 FQDN

## <a name="validate-graph"></a>验证关系图

1. 在满足先决条件的计算机上，打开一个管理 PowerShell 提示符，然后运行以下命令来安装 AzsReadinessChecker。

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. 从 PowerShell 提示符处，运行以下命令，设置 *$graphCredential*图形帐户到变量。 替换`contoso\graphservice`与你的帐户使用`domain\username`格式。

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. 从 PowerShell 提示符处，运行以下命令启动对关系图的验证。 指定的值 **-ForestFQDN**目录林根的 fqdn:

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. 运行该工具后，查看输出。 确认，状态为正常的图形集成要求。 成功验证看起来类似于以下：

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

在生产环境中，不能完全表示可用于 Azure Stack 的连接视为测试从运算符工作站的网络连接的对象。 Azure Stack 戳记的公共 VIP 网络将需要执行身份集成的 LDAP 通信的连接。

## <a name="report-and-log-file"></a>报表和日志文件

每次运行验证时，它都会将结果记录到 **AzsReadinessChecker.log** 和 **AzsReadinessCheckerReport.json** 中。 这些文件的位置会随验证结果一起显示在 PowerShell 中。

验证文件可以帮助您在部署 Azure Stack 或调查的验证问题之前共享状态。 这两个文件都会持久保留每个后续验证检查的结果。 报表向你的部署团队提供标识配置确认。 日志文件可以帮助你的部署或支持团队调查验证问题。

默认情况下，这两个文件写入到 `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`

使用：

* **-OutputPath** *路径*末尾运行的命令行，以指定不同的报表位置的参数。
* **-CleanReport**参数运行命令以清除末尾*AzsReadinessCheckerReport.json*的以前的报表信息。 有关详细信息，请参阅[Azure Stack 验证报表](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>验证失败

如果验证检查失败，则有关失败的详细信息将显示在 PowerShell 窗口中。 该工具还将记录到的信息*AzsGraphIntegration.log*。

## <a name="next-steps"></a>后续步骤

[查看就绪性报表](azure-stack-validation-report.md)  
[有关 Azure Stack 集成的一般注意事项](azure-stack-datacenter-integration.md)  
