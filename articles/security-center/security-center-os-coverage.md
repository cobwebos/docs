---
title: Azure 安全中心支持的功能和平台 | Microsoft Docs
description: 本文档列出了 Azure 安全中心支持的功能和平台。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/27/2019
ms.author: monhaber
ms.openlocfilehash: 807bde76bb6bb50490ee599768273a59c49d5e45
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258694"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Azure 安全中心支持的平台和功能

通过使用经典部署模式和资源管理器部署模式创建的虚拟机 (VM) 和计算机支持安全状态监视和建议。

> [!NOTE]
> 了解 Azure 资源的[经典部署模型和 Resource Manager 部署模型](../azure-classic-rm.md)。
>

## <a name="platforms-that-support-the-data-collection-agent"></a>支持数据收集代理的平台 

本部分列出了 Azure 安全中心代理可在其上运行并从中收集数据的平台。

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows 计算机和 VM 支持的平台
支持以下 Windows 操作系统：

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> 与 Windows Defender ATP 集成仅支持 Windows Server 2012 R2 和 Windows Server 2016。

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux 计算机和 VM 支持的平台

支持以下 Linux 操作系统：

> [!NOTE]
> 由于不断更改支持的 Linux 操作系统的列表，如果您愿意，请单击[此处](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems)若要查看支持的版本，最新列表，如果自上次发布本主题后进行了更改。

64 位
* CentOS 6 和 7
* Amazon Linux 2017.09
* Oracle Linux 6 和 7
* Red Hat Enterprise Linux Server 6 和 7
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS、16.04 LTS 和 18.04 LTS
* SUSE Linux Enterprise Server 12

32 位
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS 和 16.04 LTS

## <a name="vms-and-cloud-services"></a>VM 和云服务
此外还支持云服务中运行的 VM。 仅监视云服务 Web 和在生产槽运行的辅助角色。 若要了解有关云服务的详细信息，请参阅[云服务概述](../cloud-services/cloud-services-choose-me.md)。


## <a name="supported-iaas-features"></a>支持的 IaaS 功能

> [!div class="mx-tableFixed"]
> 

|服务器|Windows||Linux||||定价|
|----|----|----|----|----|----|----|----|
|**环境**|**Azure**||**Non-Azure**|**Azure**||**Non-Azure**||
||**虚拟机**|**虚拟机规模集**||**虚拟机**|**虚拟机规模集**|
|VMBA 威胁检测警报|✔|✔|✔|✔（在支持的版本上）|✔（在支持的版本上）|✔|建议 （免费） 威胁检测 （标准）|
|基于网络的威胁检测警报|✔|✔|X|✔|✔|X|标准|
|Windows Defender ATP 集成|✔（在支持的版本上）|✔（在支持的版本上）|✔|X|X|X|标准|
|允许缺失修补程序|✔|✔|✔|✔|✔|✔|免费|
|安全配置|✔|✔|✔|✔|✔|✔|免费|
|Endpoint protection 评估|✔|✔|✔|X|X|X|免费|
|JIT VM 访问|✔|X|X|✔|X|X|标准|
|自适应应用程序控制|✔|X|✔|✔|X|✔|标准|
|FIM|✔|✔|✔|✔|✔|✔|标准|
|磁盘加密评估|✔|✔|X|✔|✔|X|免费|
|第三方部署|✔|X|X|✔|X|X|免费|
|Nsg 评估|✔|✔|X|✔|✔|X|免费|
|无文件威胁检测|✔|✔|✔|X|X|X|标准|
|网络映射|✔|✔|X|✔|✔|X|标准|
|自适应网络控制|✔|✔|X|✔|✔|X|标准|
|法规符合性仪表板和报表|✔|✔|✔|✔|✔|✔|标准|
|建议和 Docker 托管 IaaS 容器上的威胁检测|X|X|X|✔|✔|✔|标准|

### <a name="supported-endpoint-protection-solutions"></a>受支持的终结点保护解决方案

下表提供了一个矩阵：
 - 是否可以使用 Azure 安全中心安装每个解决方案。
 - 安全中心可以发现哪些保护解决方案。 如果发现其中一个终结点保护解决方案，安全中心将建议不要安装。

有关这些保护的每个生成建议时的信息，请参阅[终结点保护评估和建议](security-center-endpoint-protection.md)。

| 终结点保护| 平台 | 安全中心安装 | 安全中心发现 |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| 否，内置到 OS| 是 |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2、2012、2008 R2（请参阅以下备注） | 通过扩展 | 是 |
| Trend Micro – 所有版本 | Windows Server 系列  | 否 | 是 |
| Symantec v12.1.1100+| Windows Server 系列  | 否 | 是 |
| McAfee v10+ | Windows Server 系列  | 否 | 是 |
| Kaspersky| Windows Server 系列  | 否 | 否  |
| Sophos| Windows Server 系列  | 否 | 否  |

> [!NOTE]
> - 在 Windows Server 2008 R2 虚拟机上检测 System Center Endpoint Protection (SCEP) 需要在 PowerShell 3.0（或更高版本）之后安装 SCEP。

## <a name="supported-paas-features"></a>支持的 PaaS 功能


|服务|建议 （免费）|威胁检测 （标准）|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Azure Blob 存储帐户*|✔| ✔|
|应用程序服务|✔| ✔|
|云服务|✔| X|
|VNet|✔| NA|
|子网|✔| NA|
|NIC|✔| NA|
|NSG|✔| NA|
|订阅|✔ **| ✔|
|应用服务|✔| NA|
|Batch|✔| NA|
|Service Fabric|✔| NA|
|自动化帐户|✔| NA|
|负载均衡|✔| NA|
|搜索|✔| NA|
|服务总线|✔| NA|
|流分析|✔| NA|
|事件中心|✔| NA|
|逻辑应用|✔| NA|
|子网|✔| NA|
|Vnet|✔| NA|
|存储帐户|✔| NA|
|Redis|✔| NA|
|SQL|✔| NA|
|Data Lake Analytics|✔| NA|
|存储帐户|✔| NA|
|订阅|✔| NA|
|密钥保管库|✔| NA|




\* 公共预览版目前支持这些功能。

\*\* AAD 建议属性仅适用于标准订阅



## <a name="next-steps"></a>后续步骤

- 了解如何[规划并理解设计注意事项，以便采用 Azure 安全中心](security-center-planning-and-operations-guide.md)。
- 了解关于[安全中心中虚拟机行为分析和故障转储内存分析的详细信息](security-center-alerts-type.md#virtual-machine-behavioral-analysis)。
- 查找[有关使用 Azure 安全中心的常见问题](security-center-faq.md)。
- 查找[关于 Azure 安全性和符合性的博客文章](https://blogs.msdn.com/b/azuresecurity/)。
