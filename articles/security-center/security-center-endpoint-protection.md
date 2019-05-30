---
title: 终结点保护解决方案发现和运行状况评估在 Azure 安全中心 |Microsoft Docs
description: 如何发现和标识为正常终结点保护解决方案。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2019
ms.author: v-mohabe
ms.openlocfilehash: b17e5f16b988bfa562b00bc6f5b9dfd34be4ca43
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247959"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>终结点保护评估和 Azure 安全中心建议

终结点保护评估和建议在 Azure 安全中心检测，并提供的运行状况评估[支持](https://docs.microsoft.com/azure/security-center/security-center-os-coverage#supported-platforms-for-windows-computers-and-vms)版本的终结点保护解决方案。 本主题说明生成通过 Azure 安全中心的终结点保护解决方案的以下两个建议的方案。

* **在虚拟机上安装终结点保护解决方案**
* **解决在计算机上的终结点保护运行状况问题**

## <a name="windows-defender"></a>Windows Defender

* **"虚拟机上安装终结点保护解决方案"** 生成建议时[Get MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)运行并将该结果是**AMServiceEnabled:False**

* **"解析在计算机上的终结点保护运行状况问题"** 生成建议时[Get MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)运行和一个或两个以下发生：

  * 在至少一个以下属性为 false:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * 如果一个或两个以下属性大于或等于 7。

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center endpoint protection

* **"虚拟机上安装终结点保护解决方案"** 导入时生成建议**SCEPMpModule ("$env: ProgramFiles\Microsoft 安全 Client\MpProvider\MpProvider.psd1")** 并运行**Get MProtComputerStatus**结果**AMServiceEnabled = false**

* **"解析在计算机上的终结点保护运行状况问题"** 生成建议时**Get MprotComputerStatus**运行和一个或两个以下发生：

    * 在至少一个以下属性为 false:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * 如果一个或两个以下的签名更新是大于或等于 7。 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* **"虚拟机上安装终结点保护解决方案"** 生成建议，如果一个或多个以下检查不符合：
    * **HKLM:\SOFTWARE\TrendMicro\Deep 安全代理**存在
    * **HKLM:\SOFTWARE\TrendMicro\Deep 安全 Agent\InstallationFolder**存在
    * **Dsq_query.cmd**安装文件夹中找到文件
    * 运行**dsa_query.cmd**结果**Component.AM.mode： 上的 Trend Micro Deep Security Agent 检测到**

## <a name="symantec-endpoint-protection"></a>Symantec 终结点保护
**"虚拟机上安装终结点保护解决方案"** 如果不满足任何以下检查生成建议：

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

或

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

**"解析在计算机上的终结点保护运行状况问题"** 如果不满足任何以下检查生成建议：  

* 检查 Symantec 版本 > = 12:注册表位置：**HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* 检查实时保护状态：**HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* 检查签名更新状态：**HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 days**

* 检查完全扫描状态：**HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 days**

* 查找 Symantec 12 签名版本号路径到签名版本：**Registry Paths+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 

* Symantec 14 的签名版本的路径：**Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

注册表路径：

**"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;** 
 **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>适用于 Windows 的 McAfee endpoint protection

**"虚拟机上安装终结点保护解决方案"** 如果不满足以下检查，则会生成建议：

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** exists

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

**"解析在计算机上的终结点保护运行状况问题"** 如果不满足以下检查，则会生成建议：

* McAfee 版本：**HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* 查找签名版本：**HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* 找到签名日期：**HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 days**

* 找到扫描日期：**HKLM:\Software\McAfee\Endpoint\AV\ODS -Value "LastFullScanOdsRunTime" >= 7 days**

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

Microsoft 反恶意软件扩展日志位于：  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
