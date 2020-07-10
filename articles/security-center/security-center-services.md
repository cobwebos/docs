---
title: Azure 安全中心提供的支持功能 |Microsoft Docs
description: 此文档提供了 Azure 安全中心支持的服务的列表。
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
ms.date: 05/27/2020
ms.author: memildin
ms.openlocfilehash: 060db99cf0163876b68eeee2cff8ef2d03e2b5b9
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187007"
---
# <a name="feature-coverage-for-machines"></a>适用于计算机的功能覆盖范围

下表显示了可用于虚拟机和服务器的 Azure 安全中心功能。

## <a name="supported-features-for-virtual-machines-and-servers"></a>虚拟机和服务器支持的功能 <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Windows 计算机](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**功能**|**Azure 虚拟机**|**Azure 虚拟机规模集**|**非 Azure 虚拟机**|**价格**
|[Microsoft Defender ATP 集成](security-center-wdatp.md)|✔</br>（在支持的版本上）|✔</br>（在支持的版本上）|✔|标准|
|[虚拟机行为分析（和安全警报）](threat-protection.md)|✔|✔|✔|建议（免费） </br></br> 安全警报（标准）|
|[无文件安全警报](alerts-reference.md#alerts-windows)|✔|✔|✔|标准|
|[基于网络的安全警报](threat-protection.md#network-layer)|✔|✔|-|标准|
|[实时 VM 访问](security-center-just-in-time.md)|✔|-|-|标准|
|[本机漏洞评估](built-in-vulnerability-assessment.md)|✔|-|-|标准|
|[文件完整性监视](security-center-file-integrity-monitoring.md)|✔|✔|✔|标准|
|[自适应应用程序控制](security-center-adaptive-application.md)|✔|-|✔|标准|
|[网络映射](security-center-network-recommendations.md#network-map)|✔|✔|-|标准|
|[自适应网络强化](security-center-adaptive-network-hardening.md)|✔|-|-|标准|
|[合规性仪表板和报表](security-center-compliance-dashboard.md)|✔|✔|✔|标准|
|针对 Docker 托管的 IaaS 容器的建议和威胁防护|-|-|-|标准|
|缺少 OS 修补程序评估|✔|✔|✔|Free|
|安全配置错误评估|✔|✔|✔|Free|
|[终结点保护评估](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Free|
|磁盘加密评估|✔|✔|-|免费|
|第三方漏洞评估|✔|-|-|免费|
|[网络安全评估](security-center-network-recommendations.md)|✔|✔|-|免费|


### <a name="linux-machines"></a>[Linux 计算机](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**功能**|**Azure 虚拟机**|**Azure 虚拟机规模集**|**非 Azure 虚拟机**|**价格**
|[Microsoft Defender ATP 集成](security-center-wdatp.md)|-|-|-|标准|
|[虚拟机行为分析（和安全警报）](security-center-alerts-iaas.md)|✔</br>（在支持的版本上）|✔</br>（在支持的版本上）|✔|建议（免费） </br></br> 安全警报（标准）|
|[无文件安全警报](alerts-reference.md#alerts-windows)|-|-|-|标准|
|[基于网络的安全警报](threat-protection.md#network-layer)|✔|✔|-|标准|
|[实时 VM 访问](security-center-just-in-time.md)|✔|-|-|标准|
|[本机漏洞评估](built-in-vulnerability-assessment.md)|✔|-|-|标准|
|[文件完整性监视](security-center-file-integrity-monitoring.md)|✔|✔|✔|标准|
|[自适应应用程序控制](security-center-adaptive-application.md)|✔|-|✔|标准|
|[网络映射](security-center-network-recommendations.md#network-map)|✔|✔|-|标准|
|[自适应网络强化](security-center-adaptive-network-hardening.md)|✔|-|-|标准|
|自适应网络控制|✔|✔|-|标准|
|[合规性仪表板和报表](security-center-compliance-dashboard.md)|✔|✔|✔|标准|
|针对 Docker 托管的 IaaS 容器的建议和威胁防护|✔|✔|✔|标准|
|缺少 OS 修补程序评估|✔|✔|✔|Free|
|安全配置错误评估|✔|✔|✔|Free|
|[终结点保护评估](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|免费|
|磁盘加密评估|✔|✔|-|免费|
|第三方漏洞评估|✔|-|-|免费|
|[网络安全评估](security-center-network-recommendations.md)|✔|✔|-|免费|

--- 


> [!TIP]
>若要试用仅在标准定价层上可用的功能，免费层用户可以注册 30 天试用版。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。


## <a name="supported-endpoint-protection-solutions"></a>支持的终结点保护解决方案 <a name="endpoint-supported"></a>

下表提供了一个矩阵：

 - 是否可以使用 Azure 安全中心安装每个解决方案。
 - 安全中心可以发现哪些保护解决方案。 如果你发现此列表中有终结点保护解决方案，安全中心会建议你不要安装该解决方案。

若要了解何时会针对其中的每种保护生成建议，请参阅[终结点保护评估和建议](security-center-endpoint-protection.md)。

| 终结点保护| 平台 | 安全中心安装 | 安全中心发现 |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| 否，内置到 OS| 是 |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2、2012、2008 R2（请参阅以下备注） | 通过扩展 | 是 |
| 趋势微–深度安全 | Windows Server 系列  | 否 | 是 |
| Symantec v12.1.1100+| Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Linux 服务器系列  | 否 | 是 **\*** |
| Sophos V9+| Linux 服务器系列  | 否 | 是  **\***  |

 **\*** 覆盖状态和支持数据目前仅在与受保护订阅关联的 Log Analytics 工作区中提供。 这些数据不会在 Azure 安全中心门户中反映。

> [!NOTE]
> Windows Server 2008 R2 虚拟机上的 System Center Endpoint Protection (SCEP) 检测要求在 PowerShell (3.0 版或更高版本的) 后安装 SCEP。


## <a name="next-steps"></a>后续步骤

- 了解[安全中心如何收集数据以及 Log Analytics 代理](security-center-enable-data-collection.md)。
- 了解[安全中心如何管理和保护数据](security-center-data-security.md)。
- 查看[支持安全中心的平台](security-center-os-coverage.md)。