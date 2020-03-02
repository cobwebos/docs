---
title: Azure 安全中心中的 Endpoint protection 建议
description: 如何发现 endpoint protection 解决方案并将其识别为正常。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: dcf7df501665ea3885d00b9f7668a95cbbf02428
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208536"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Azure 安全中心的 Endpoint protection 评估和建议

Azure 安全中心为 Endpoint protection 解决方案[支持](security-center-services.md#endpoint-supported)的版本提供运行状况评估。 本文介绍领导安全中心生成以下两个建议的方案：

* **在虚拟机上安装 endpoint protection 解决方案**
* **解决计算机上的 endpoint protection 运行状况问题**

## <a name="windows-defender"></a>Windows Defender

* 安全中心建议在[MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)运行且结果为 AMServiceEnabled 时， **"在虚拟机上安装 endpoint protection 解决方案"** **： False**

* 安全中心建议在[MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)运行和以下任何情况时， **"解决计算机上的 endpoint protection 运行状况问题"** ：

  * 以下任何属性均为 false：

    **AMServiceEnabled**

    **AntispywareEnabled**

    **RealTimeProtectionEnabled**

    **BehaviorMonitorEnabled**

    **IoavProtectionEnabled**

    **OnAccessProtectionEnabled**

  * 如果以下一个或两个属性为7或更多。

    **AntispywareSignatureAge**

    **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center endpoint protection

* 安全中心建议你在导入**SCEPMpModule （"$env:P Rogramfiles\microsoft Security Client\MpProvider\MpProvider.psd1"）** 时 **"在虚拟机上安装 endpoint protection 解决方案**"，并通过**AMServiceEnabled = false**运行**MProtComputerStatus**结果

* 安全中心建议在**MprotComputerStatus**运行和以下任何情况时， **"解决计算机上的 endpoint protection 运行状况问题"** ：

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

## <a name="trend-micro"></a>走向微

* 当不满足以下任何检查时，安全中心建议你 **"在虚拟机上安装 endpoint protection 解决方案"** ：
    * **HKLM： \ SOFTWARE\TrendMicro\Deep 安全代理**已存在
    * **HKLM： \ SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** exists
    * 在安装文件夹中找到**dsa_query .cmd**文件
    * 运行带 Component 的**dsa_query .cmd**结果 **：检测到趋势微 Deep Security 代理**

## <a name="symantec-endpoint-protection"></a>Symantec endpoint protection
当不满足以下任何检查时，安全中心建议你 **"在虚拟机上安装 endpoint protection 解决方案"** ：

* **HKLM： \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM： \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

或

* **HKLM： \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM： \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

安全中心建议在不满足以下任何检查时， **"解决计算机上的 endpoint protection 运行状况问题"** ：

* 检查 Symantec 版本 > = 12：注册表位置： **HKLM： \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion "-Value" PRODUCTVERSION "**

* 检查实时保护状态： **HKLM： \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff = = 1**

* 检查签名更新状态： **HKLM\Software\Symantec\Symantec 终结点 Protection\CurrentVersion\public-opstate\LatestVirusDefsDate < = 7 天**

* 检查完全扫描状态： **HKLM： \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime < = 7 天**

* 查找 Symantec 12 的签名版本的签名版本号路径：**注册表路径 + "CurrentVersion\SharedDefs"-值 "SRTSP"** 

* Symantec 14 的签名版本路径：**注册表路径 + "CurrentVersion\SharedDefs\SDSDefs"-Value "SRTSP"**

注册表路径：

* **"HKLM： \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM： \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>适用于 Windows 的 McAfee endpoint protection

当不满足以下任何检查时，安全中心建议你 **"在虚拟机上安装 endpoint protection 解决方案"** ：

* **HKLM： \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion**存在

* **HKLM： \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

安全中心建议在不满足以下任何检查时， **"解决计算机上的 endpoint protection 运行状况问题"** ：

* McAfee 版本： **HKLM： \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion > = 10**

* 查找签名版本： **HKLM： \ Software\McAfee\AVSolution\DS\DS 值 "dwContentMajorVersion"**

* 查找签名日期： **HKLM： \ Software\McAfee\AVSolution\DS\DS-Value "szContentCreationDate" > = 7 天**

* 查找扫描日期： **HKLM： \ Software\McAfee\Endpoint\AV\ODS-Value "LastFullScanOdsRunTime" > = 7 天**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>适用于 Linux 的 McAfee 终结点安全威胁防护 

当不满足以下任何检查时，安全中心建议你 **"在虚拟机上安装 endpoint protection 解决方案"** ：

- 文件 **/opt/isec/ens/threatprevention/bin/isecav**退出 

- **"/opt/isec/ens/threatprevention/bin/isecav--version"** 输出是： **mcafee name = 适用于 Linux 的 Mcafee Endpoint Security 威胁防护和迈克菲版本 > = 10**

安全中心建议在不满足以下任何检查时， **"解决计算机上的 endpoint protection 运行状况问题"** ：

- **"/opt/isec/ens/threatprevention/bin/isecav--listtask"** 返回 "**快速扫描"、"完全扫描**" 和两个扫描 < = 7 天

- **"/opt/isec/ens/threatprevention/bin/isecav--listtask"** 返回**DAT 和引擎更新时间**，并且两者都 < = 7 天

- **"/opt/isec/ens/threatprevention/bin/isecav--getoasconfig--summary"** 返回**访问扫描**状态

## <a name="sophos-antivirus-for-linux"></a>适用于 Linux 的 Sophos 防病毒 

当不满足以下任何检查时，安全中心建议你 **"在虚拟机上安装 endpoint protection 解决方案"** ：

- 文件 **/opt/sophos-av/bin/savdstatus**退出或搜索自定义位置 **"readlink $ （savscan）"**

- **"/opt/sophos-av/bin/savdstatus--version"** 返回 sophos 名称 = **sophos 反病毒，sophos 版本 > = 9**

安全中心建议在不满足以下任何检查时， **"解决计算机上的 endpoint protection 运行状况问题"** ：

- **"/opt/sophos-av/bin/savlog--maxage = 7 |grep-i "计划的扫描。\* 完成 "|tail-1 "** ，返回值

- **"/opt/sophos-av/bin/savlog--maxage = 7 |grep "扫描已完成"** |tail-1 "，返回值

- **"/opt/sophos-av/bin/savdstatus--lastupdate"** 返回 lastupdate，该值应 < = 7 天 

- **"/opt/sophos-av/bin/savdstatus-v"** 等于 **"访问时扫描正在运行"** 

- **"/opt/sophos-av/bin/savconfig Get LiveProtection"** 返回已启用

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

Microsoft 反恶意软件扩展日志位于： **%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware （或 PaaSAntimalware） \1.5.5.x （版本 #） \CommandExecution.log**

### <a name="support"></a>支持

有关更多帮助，请联系 MSDN Azure 上的 Azure 专家[并 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)。 或提供 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。