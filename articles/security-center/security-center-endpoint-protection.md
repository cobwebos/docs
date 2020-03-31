---
title: Azure 安全中心的端点保护建议
description: 如何发现端点保护解决方案并将其标识为正常解决方案。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208536"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Azure 安全中心的端点保护评估和建议

Azure 安全中心提供[受支持的](security-center-services.md#endpoint-supported)终结点保护解决方案版本的运行状况评估。 本文介绍了导致安全中心生成以下两项建议的方案：

* **在虚拟机上安装端点保护解决方案**
* **解决计算机上的终结点保护运行状况问题**

## <a name="windows-defender"></a>Windows Defender

* 安全中心建议您在[运行获取 MpComputer状态](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)时 **"在虚拟机上安装端点保护解决方案"，** 结果为 **"启用 AMService：False"**

* 安全中心建议您在[运行 Get-MpComputerStatus 时](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)**"解决计算机上的端点保护运行状况问题"，** 并发生以下任一情况：

  * 以下任一属性都是错误的：

    **已启用 AM 服务**

    **启用反间谍软件**

    **支持实时保护**

    **行为监视器启用**

    **Ioav 保护启用**

    **启用访问保护**

  * 如果以下一个或两个属性为 7 或更多。

    **反间谍软件签名**

    **防病毒签名**

## <a name="microsoft-system-center-endpoint-protection"></a>微软系统中心端点保护

* 安全中心建议您在导入**SCEPMpModule（"$env：程序文件_微软安全客户端_MpProvider_mpProvider.psd1"）** 和运行具有**AMService 启用的** **Get-MProt 计算机状态**结果时 **"在虚拟机上安装端点保护解决方案"**

* 当**Get-Mprot 计算机状态**运行且发生以下任一情况时，安全中心建议您 **"解决计算机上的端点保护运行状况问题"：**

    * 以下属性中至少有一个为 false：

            **AMServiceEnabled**

            **AntispywareEnabled**
    
            **RealTimeProtectionEnabled**
    
            **BehaviorMonitorEnabled**
    
            **IoavProtectionEnabled**
    
            **OnAccessProtectionEnabled**
          
    * 如果以下签名更新中的一个或两个大于或等于 7。 

            **AntispywareSignatureAge**
    
            **AntivirusSignatureAge**

## <a name="trend-micro"></a>趋势科技

* 当未满足以下任何检查时，安全中心建议您 **"在虚拟机上安装端点保护解决方案"：**
    * **HKLM：_软件\趋势微\深度安全代理**存在
    * **HKLM：_软件\趋势微\深度安全代理\安装文件夹**存在
    * **dsa_query.cmd**文件位于安装文件夹中
    * 使用组件.AM.模式运行**dsa_query.cmd**结果 **：打开 - 检测到趋势微深度安全代理**

## <a name="symantec-endpoint-protection"></a>赛门铁克端点保护
当未满足以下任何检查时，安全中心建议您 **"在虚拟机上安装端点保护解决方案"：**

* **HKLM：[软件]赛门铁克_赛门铁克端点保护\当前版本_PRODUCTNAME = "赛门铁克端点保护"**

* **HKLM：[软件]赛门铁克_赛门铁克端点保护\当前版本\公共-opstate_AS运行状态 = 1**

或

* **HKLM：[软件]哇6432Node_赛门铁克_赛门铁克端点保护\当前版本_PRODUCTNAME = "赛门铁克端点保护"**

* **HKLM：[软件]哇6432Node_赛门铁克_赛门铁克端点保护\当前版本\公共-opstate_AS运行状态 = 1**

当未满足以下任何检查时，安全中心建议您 **"解决机器上的端点保护运行状况问题"：**

* 检查赛门铁克版本>= 12：注册表位置 **：HKLM：\软件\赛门铁克_赛门铁克端点保护\当前版本" -值"PRODUCTVERSION"**

* 检查实时保护状态 **：HKLM：\软件\哇6432Node_赛门铁克_赛门铁克端点保护_AV_存储_文件系统_实时扫描\OnOff = 1**

* 检查签名更新状态 **：HKLM_软件\赛门铁克_赛门铁克端点保护\当前版本\公共-opstate_最新病毒Defsdate<= 7 天**

* 检查完全扫描状态 **：HKLM：[软件]赛门铁克_赛门铁克端点保护\当前版本\公共-opstate_上次成功扫描日期时间<= 7 天**

* 查找赛门铁克 12 的签名版本签名版本路径：**注册表路径* "当前版本_共享 Defs" -值"SRTSP"** 

* 赛门铁克 14 的签名版本的路径：**注册表路径* "当前版本_共享 Defs_SDSDefs" - 值"SRTSP"**

注册表路径：

* **"HKLM：[软件]赛门铁克\赛门铁克端点保护" = $Path;**
* **"HKLM：\软件_哇6432Node_赛门铁克_赛门铁克端点保护" = $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>针对 Windows 的迈克菲端点保护

当未满足以下任何检查时，安全中心建议您 **"在虚拟机上安装端点保护解决方案"：**

* **HKLM：_软件\McAfee\端点\AV_产品版本**存在

* **HKLM：[软件]MCAfee_AV解决方案\MCSHIELDGLOBAL_全球_全球启用 = 1**

当未满足以下任何检查时，安全中心建议您 **"解决机器上的端点保护运行状况问题"：**

* 迈克菲版本 **：HKLM：_软件_McAfee_endpoint_AV_产品版本>= 10**

* 查找签名版本 **：HKLM：\软件\McAfee_AVSolution_DS_DS -值"dwContent主要版本"**

* 查找签名日期 **：HKLM：\软件\McAfee_AVSolution_DS_值"scontent创建日期">= 7 天**

* 查找扫描日期 **：HKLM：\软件\McAfee_Endpoint_AV_ODS - 值"最后全扫描运行时间">= 7 天**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>用于 Linux 威胁防御的迈克菲端点安全性 

当未满足以下任何检查时，安全中心建议您 **"在虚拟机上安装端点保护解决方案"：**

- 文件 **/选择/isec/ens/威胁预防/箱/isecav**出口 

- **"/选择/isec/ens/威胁预防/bin/isecav -版本"** 输出是：**迈克菲名称 = 用于 Linux 威胁防御的迈克菲端点安全，以及 >的 McAfee 版本 = 10**

当未满足以下任何检查时，安全中心建议您 **"解决机器上的端点保护运行状况问题"：**

- **"/选择/isec/ens/威胁预防/bin/isecav --列表任务"** 返回**快速扫描、完整扫描**和两个扫描<= 7 天

- **"/选择/isec/ens/威胁预防/宾/isecav --列表任务"** 返回**DAT 和引擎更新时间**，它们<= 7 天

- **"/选择/isec/ens/威胁预防/宾/伊塞卡夫 -getoasconfig - 摘要"** 返回**访问扫描**状态

## <a name="sophos-antivirus-for-linux"></a>Linux 的 Sophos 防病毒 

当未满足以下任何检查时，安全中心建议您 **"在虚拟机上安装端点保护解决方案"：**

- 文件 **/选择/sophos-av/bin/savdstatus**退出或搜索自定义位置 **"读取链接 $（即 savscan）"**

- **"/选择/sophos-av/bin/savdstatus -版本"** 返回 Sophos 名称 = **Sophos 防病毒和 Sophos 版本>= 9**

当未满足以下任何检查时，安全中心建议您 **"解决机器上的端点保护运行状况问题"：**

- **"/选择/索福斯-av/bin/slog --maxage_7 |grep-i"预定扫描"。\*已完成" |尾部 -1"**，返回值

- **"/选择/索福斯-av/bin/slog --maxage_7 |grep "扫描完成"** |尾部 -1"，返回值

- **"/选择/sophos-av/bin/savdstatus -- 上次更新"** 返回上次更新，应<= 7 天 

- **"/选择/索福斯-av/bin/savdstatus -v"** 等于 **"正在运行访问扫描"** 

- **"/选择/sophos-av/bin/savconfig 获取实时保护"** 返回启用

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>疑难解答

微软反恶意软件扩展日志可在： **%系统驱动器%\WindowsAzure_Logs_Plugins_Microsoft.Azure.Security.IaaSAnti恶意软件（或 PaaSAnti恶意软件）\1.5.5.x（版本*）\命令执行.log**

### <a name="support"></a>支持

如需更多帮助，请与[MSDN Azure 和堆栈溢出论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家联系。 或提交 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择"获取支持"。 有关使用 Azure 支持的信息，请阅读[Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。