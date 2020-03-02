---
title: Azure 安全中心提供的支持功能 |Microsoft Docs
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
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208779"
---
# <a name="feature-coverage-for-machines"></a>计算机的功能覆盖率

下表显示了可用于虚拟机和服务器的 Azure 安全中心功能。

## 支持的虚拟机和服务器功能<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Windows 计算机](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**功能**|**Azure 虚拟机**|**Azure 虚拟机规模集**|**非 Azure 计算机**|**定价**
|[Microsoft Defender ATP 集成](security-center-wdatp.md)|✔</br>（支持的版本）|✔</br>（支持的版本）|✔|Standard|
|[虚拟机行为分析（和安全警报）](threat-protection.md)|✔|✔|✔|建议（免费） </br></br> 安全警报（标准）|
|[Fileless 安全警报](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[基于网络的安全警报](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[实时 VM 访问](security-center-just-in-time.md)|✔|-|-|Standard|
|[本机漏洞评估](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[文件完整性监视](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[自适应应用程序控制](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[网络映射](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[自适应网络强化](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|自适应网络控制|✔|✔|-|Standard|
|[合规性仪表板 & 报表](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Docker 托管的 IaaS 容器上的建议和威胁防护|-|-|-|Standard|
|缺少操作系统修补程序评估|✔|✔|✔|免费|
|安全错误配置评估|✔|✔|✔|免费|
|[Endpoint protection 评估](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|免费|
|磁盘加密评估|✔|✔|-|免费|
|第三方漏洞评估|✔|-|-|免费|
|[网络安全评估](security-center-network-recommendations.md)|✔|✔|-|免费|


### <a name="linux-machines"></a>[Linux 计算机](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**功能**|**Azure 虚拟机**|**Azure 虚拟机规模集**|**非 Azure 计算机**|**定价**
|[Microsoft Defender ATP 集成](security-center-wdatp.md)|-|-|-|Standard|
|[虚拟机行为分析（和安全警报）](security-center-alerts-iaas.md)|✔</br>（支持的版本）|✔</br>（支持的版本）|✔|建议（免费） </br></br> 安全警报（标准）|
|[Fileless 安全警报](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[基于网络的安全警报](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[实时 VM 访问](security-center-just-in-time.md)|✔|-|-|Standard|
|[本机漏洞评估](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[文件完整性监视](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[自适应应用程序控制](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[网络映射](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[自适应网络强化](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|自适应网络控制|✔|✔|-|Standard|
|[合规性仪表板 & 报表](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Docker 托管的 IaaS 容器上的建议和威胁防护|✔|✔|✔|Standard|
|缺少操作系统修补程序评估|✔|✔|✔|免费|
|安全错误配置评估|✔|✔|✔|免费|
|[Endpoint protection 评估](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|免费|
|磁盘加密评估|✔|✔|-|免费|
|第三方漏洞评估|✔|-|-|免费|
|[网络安全评估](security-center-network-recommendations.md)|✔|✔|-|免费|

--- 


> [!TIP]
>若要试验仅适用于标准定价层的功能，免费级别用户可以在30天内注册。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。


## 支持的 endpoint protection 解决方案<a name="endpoint-supported"></a>

下表提供了一个矩阵：

 - 是否可以使用 Azure 安全中心安装每个解决方案。
 - 安全中心可以发现哪些保护解决方案。 如果发现此列表中的 endpoint protection 解决方案，则安全中心不建议安装一个。

有关为每个保护生成建议的时间的信息，请参阅[Endpoint Protection 评估和建议](security-center-endpoint-protection.md)。

| 终结点保护| 平台 | 安全中心安装 | 安全中心发现 |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| 否，内置到 OS| 是 |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2、2012、2008 R2（请参阅以下备注） | 通过扩展 | 是 |
| 趋势微–所有版本 * | Windows Server 系列  | 否 | 是 |
| Symantec v12.1.1100+| Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Linux 服务器系列  | 否 | 是 **\*** |
| Sophos V9.x +| Linux 服务器系列  | 否 | 是 **\***  |

 **\*** 覆盖率状态和支持数据当前仅在与受保护订阅关联的 Log Analytics 工作区中可用。 它不会在 Azure 安全中心门户中反映出来。

> [!NOTE]
> - 在 Windows Server 2008 R2 虚拟机上检测 System Center Endpoint Protection (SCEP) 需要在 PowerShell 3.0（或更高版本）之后安装 SCEP。
> - 深度安全代理支持检测趋势微保护。  不支持 OfficeScan 代理。


## <a name="next-steps"></a>后续步骤

- 了解[安全中心如何收集数据和 Log Analytics 代理](security-center-enable-data-collection.md)。
- 了解[安全中心如何管理和保护数据](security-center-data-security.md)。
- 了解如何[规划并理解设计注意事项，以便采用 Azure 安全中心](security-center-planning-and-operations-guide.md)。
- 查看[支持安全中心的平台](security-center-os-coverage.md)。
- 详细了解[Azure 安全中心中的 Windows 和 Linux 计算机的威胁保护](threat-protection.md#windows-machines)。
- 查找[有关 Azure 安全中心](faq-general.md)的常见问题。