---
title: Azure 安全中心中的 Endpoint protection 解决方案发现和运行状况评估 |Microsoft Docs
description: 如何发现 endpoint protection 解决方案并将其识别为正常。
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
ms.date: 08/08/2019
ms.author: v-mohabe
ms.openlocfilehash: a5cd0f88173abb65a120aa305206505af51d9f9e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861374"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Azure 安全中心的 Endpoint protection 评估和建议

Azure 安全中心的 endpoint protection 评估和建议检测并提供[支持](https://docs.microsoft.com/azure/security-center/security-center-os-coverage)的 Endpoint protection 解决方案版本的运行状况评估。 本主题介绍 Azure 安全中心为 Endpoint protection 解决方案生成以下两个建议的方案。

* **在虚拟机上安装 endpoint protection 解决方案**
* **解决计算机上的 endpoint protection 运行状况问题**

## <a name="windows-defender"></a>Windows Defender

* 当[MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)运行且结果为**AMServiceEnabled 时，会生成 **"在虚拟机上安装 endpoint protection 解决方案"** 建议：False**

* 当[MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)运行时，将生成 **"解决计算机上的终结点保护运行状况问题"** 建议，这两种情况都将生成：

  * 以下至少一个属性为 false：

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * 如果以下一个或两个属性大于或等于7。

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center endpoint protection

* 导入**SCEPMpModule （"$env:P Rogramfiles\microsoft Security Client\MpProvider\MpProvider.psd1"）** 并运行 **时，会生成 "在虚拟机上安装 endpoint protection 解决方案" 建议** **AMServiceEnabled = False**的 MProtComputerStatus 结果

* 当**MprotComputerStatus**运行时，将生成 **"解决计算机上的终结点保护运行状况问题"** 建议，这两种情况都将生成：

    * 以下至少一个属性为 false：

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * 如果以下一个或两个签名更新大于或等于7。 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* 如果不满足以下一项或多项检查，则会生成 **"在虚拟机上安装 endpoint protection 解决方案"** 建议：
    * **HKLM： \ SOFTWARE\TrendMicro\Deep 安全代理**已存在
    * **HKLM： \ SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** exists
    * **Dsq_query**文件位于安装文件夹中
    * 运行带**dsa_query 的命令**的结果。**模式：检测到趋势微 Deep Security 代理**

## <a name="symantec-endpoint-protection"></a>Symantec endpoint protection
如果不满足以下任何检查，则会生成 **"在虚拟机上安装 endpoint protection 解决方案"** 建议：

* **HKLM： \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

或

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

如果未满足以下任何检查，则会生成 **"解决计算机上的终结点保护运行状况问题"** 建议：  

* 检查 Symantec 版本 > = 12：注册表位置：**HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* 检查实时保护状态：**HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* 检查签名更新状态：**HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate < = 7 天**

* 查看完全扫描状态：**HKLM： \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime < = 7 天**

* 查找 Symantec 12 的签名版本的签名版本号路径：**Registry Paths+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 

* Symantec 14 的签名版本的路径：**Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

注册表路径：

* **"HKLM： \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM： \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>适用于 Windows 的 McAfee endpoint protection

如果不满足以下检查，则会生成 **"在虚拟机上安装 endpoint protection 解决方案"** 建议：

* **HKLM： \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion**存在

* **HKLM： \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

如果不满足以下检查，则会生成 **"解决计算机上的终结点保护运行状况问题"** 建议：

* McAfee 版本：**HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* 查找签名版本：**HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* 查找签名日期：**HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 days**

* 查找扫描日期：**HKLM:\Software\McAfee\Endpoint\AV\ODS -Value "LastFullScanOdsRunTime" >= 7 days**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>适用于 Linux 的 McAfee 终结点安全威胁防护 

如果不满足以下一项或两项检查，则会生成**在虚拟机上安装 endpoint protection 解决方案**建议：  

- 文件 **/opt/isec/ens/threatprevention/bin/isecav**退出 

- **"/opt/isec/ens/threatprevention/bin/isecav--version"** 输出是：**McAfee name = 适用于 Linux 的 McAfee 终结点安全威胁防护和迈克菲版本 > = 10**

如果不满足以下一项或多项检查，则会生成 "**解决终结点保护运行状况问题**" 建议：

- **"/opt/isec/ens/threatprevention/bin/isecav--listtask"** 返回 "**快速扫描"、"完全扫描**" 和两个扫描 < = 7 天

- **"/opt/isec/ens/threatprevention/bin/isecav--listtask"** 返回**DAT 和引擎更新时间**，并且两者都 < = 7 天

- **"/opt/isec/ens/threatprevention/bin/isecav--getoasconfig--summary"** 返回**访问扫描**状态

## <a name="sophos-antivirus-for-linux"></a>适用于 Linux 的 Sophos 防病毒 

如果不满足以下一项或两项检查，则会生成**在虚拟机上安装 endpoint protection 解决方案**建议：

- 文件 **/opt/sophos-av/bin/savdstatus**退出或搜索自定义位置 **"readlink $ （savscan）"**

- **"/opt/sophos-av/bin/savdstatus--version"** 返回 sophos 名称 = **sophos 反病毒，sophos 版本 > = 9**

如果不满足以下一项或多项检查，则会生成 "**解决终结点保护运行状况问题**" 建议：

- **"/opt/sophos-av/bin/savlog--maxage = 7 |grep-i "计划的扫描。已\*完成 "| tail-1"** ，返回值   

- **"/opt/sophos-av/bin/savlog--maxage = 7 |grep "扫描已完成"** |tail-1 "，返回值   

- **"/opt/sophos-av/bin/savdstatus--lastupdate"** 返回应 < = 7 天的 lastupdate 

- **"/opt/sophos-av/bin/savdstatus-v"** 等于 **"访问时扫描正在运行"** 

- **"/opt/sophos-av/bin/savconfig Get LiveProtection"** 返回已启用  

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

Microsoft 反恶意软件扩展日志位于：  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你可以为 Azure 支持事件提供支持。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
