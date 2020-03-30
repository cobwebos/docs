---
title: Azure 安全中心中支持的功能 |微软文档
description: 本文档提供 Azure 安全中心支持的服务列表。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 9d3fa1e0b62ea6f4762c3df6ac7da310d5703807
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245234"
---
# <a name="feature-coverage-for-machines"></a>机器功能覆盖率

下表显示了可用于虚拟机和服务器的 Azure 安全中心功能。

## <a name="supported-features-for-virtual-machines-and-servers"></a>虚拟机和服务器受支持的功能<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Windows 计算机](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**功能**|**Azure 虚拟机**|**Azure 虚拟机规模集**|**非 Azure 计算机**|**定价**
|[微软后卫ATP集成](security-center-wdatp.md)|✔</br>（在支持的版本上）|✔</br>（在支持的版本上）|✔|Standard|
|[虚拟机行为分析（和安全警报）](threat-protection.md)|✔|✔|✔|建议（免费） </br></br> 安全警报（标准）|
|[无文件安全警报](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[基于网络的安全警报](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[实时 VM 访问](security-center-just-in-time.md)|✔|-|-|Standard|
|[本机漏洞评估](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[文件完整性监控](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[自适应应用程序控制](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[网络映射](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[自适应网络硬化](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|自适应网络控制|✔|✔|-|Standard|
|[法规遵从性仪表板&报告](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Docker 托管的 IaaS 容器上的建议和威胁保护|-|-|-|Standard|
|缺少操作系统修补程序评估|✔|✔|✔|免费|
|安全配置错误评估|✔|✔|✔|免费|
|[端点保护评估](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|免费|
|磁盘加密评估|✔|✔|-|免费|
|第三方漏洞评估|✔|-|-|免费|
|[网络安全评估](security-center-network-recommendations.md)|✔|✔|-|免费|


### <a name="linux-machines"></a>[Linux 计算机](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**功能**|**Azure 虚拟机**|**Azure 虚拟机规模集**|**非 Azure 计算机**|**定价**
|[微软后卫ATP集成](security-center-wdatp.md)|-|-|-|Standard|
|[虚拟机行为分析（和安全警报）](security-center-alerts-iaas.md)|✔</br>（在支持的版本上）|✔</br>（在支持的版本上）|✔|建议（免费） </br></br> 安全警报（标准）|
|[无文件安全警报](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[基于网络的安全警报](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[实时 VM 访问](security-center-just-in-time.md)|✔|-|-|Standard|
|[本机漏洞评估](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[文件完整性监控](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[自适应应用程序控制](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[网络映射](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[自适应网络硬化](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|自适应网络控制|✔|✔|-|Standard|
|[法规遵从性仪表板&报告](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Docker 托管的 IaaS 容器上的建议和威胁保护|✔|✔|✔|Standard|
|缺少操作系统修补程序评估|✔|✔|✔|免费|
|安全配置错误评估|✔|✔|✔|免费|
|[端点保护评估](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|免费|
|磁盘加密评估|✔|✔|-|免费|
|第三方漏洞评估|✔|-|-|免费|
|[网络安全评估](security-center-network-recommendations.md)|✔|✔|-|免费|

--- 


> [!TIP]
>要试用仅在标准定价层上可用的功能，免费套餐用户可以注册 30 天试用版。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。


## <a name="supported-endpoint-protection-solutions"></a>支持的端点保护解决方案<a name="endpoint-supported"></a>

下表提供了一个矩阵：

 - 是否可以使用 Azure 安全中心安装每个解决方案。
 - 安全中心可以发现哪些保护解决方案。 如果发现此列表中的终结点保护解决方案，安全中心不会建议安装。

有关何时为每个保护生成建议的信息，请参阅[端点保护评估和建议](security-center-endpoint-protection.md)。

| 终结点保护| 平台 | 安全中心安装 | 安全中心发现 |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| 否，内置到 OS| 是 |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2、2012、2008 R2（请参阅以下备注） | 通过扩展 | 是 |
| 趋势科技 [ 所有版本] | Windows Server 系列  | 否 | 是 |
| Symantec v12.1.1100+| Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Linux 服务器系列  | 否 | 是的**\*** |
| 索福斯 V9+| Linux 服务器系列  | 否 | 是的**\***  |

 **\*** 覆盖状态和支持数据当前仅在与受保护订阅关联的日志分析工作区中可用。 它不反映在 Azure 安全中心门户中。

> [!NOTE]
> - 在 Windows Server 2008 R2 虚拟机上检测 System Center Endpoint Protection (SCEP) 需要在 PowerShell 3.0（或更高版本）之后安装 SCEP。
> - 深度安全代理支持检测趋势微保护。  不支持 OfficeScan 代理。


## <a name="next-steps"></a>后续步骤

- 了解[安全中心如何收集数据和日志分析代理](security-center-enable-data-collection.md)。
- 了解[安全中心如何管理和保护数据](security-center-data-security.md)。
- 了解如何[规划和了解采用 Azure 安全中心的设计注意事项](security-center-planning-and-operations-guide.md)。
- 查看[支持安全中心的平台](security-center-os-coverage.md)。
- 详细了解[Azure 安全中心中 Windows 和 Linux 计算机的威胁保护](threat-protection.md#windows-machines)。
- 查找[有关 Azure 安全中心的常见问题](faq-general.md)。