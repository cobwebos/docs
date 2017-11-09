---
title: "Azure 安全中心支持的平台 | Microsoft Docs"
description: "本文档提供 Azure 安全中心支持的 Windows 和 Linux 操作系统列表。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: terrylan
ms.openlocfilehash: c80e78b39b3fba4dcd9523e1e679822758822805
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="supported-platforms-in-azure-security-center"></a>Azure 安全中心支持的平台
使用经典部署模式和 Resource Manager 部署模式创建的虚拟机 (VM) 支持安全状态监视和建议。

> [!NOTE]
> 了解 Azure 资源的[经典部署模型和 Resource Manager 部署模型](../azure-classic-rm.md)。
>
>

## <a name="supported-platforms-for-windows-vms"></a>适用于 Windows VM 的支持的平台
支持的 Windows 操作系统：

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


## <a name="supported-platforms-for-linux-vms"></a>适用于 Linux VM 的支持的平台
支持的 Linux 操作系统：

* Ubuntu 版本 12.04、14.04、16.04、16.10
* Debian 版本 7、8
* CentOS 版本 6.\*、7.*
* Red Hat Enterprise Linux (RHEL) 版本 6.\*、7.*
* SUSE Linux Enterprise Server (SLES) 版本 11 SP4+、12.*
* Oracle Linux 版本 6.\*、7.*

> [!NOTE]
> Linux 操作系统暂不支持虚拟机行为分析。
>
>

## <a name="vms-and-cloud-services"></a>VM 和云服务
此外还支持云服务中运行的 VM。 仅监视云服务 web 和在生产槽运行的辅助角色。 若要了解有关云服务的详细信息，请参阅[云服务概述](../cloud-services/cloud-services-choose-me.md)。

## <a name="next-steps"></a>后续步骤

- [Azure 安全中心规划和操作指南](security-center-planning-and-operations-guide.md) - 了解如何规划和了解设计注意事项，以适应 Azure 安全中心
- [Azure 安全中心按类型划分的安全警报](https://docs.microsoft.com/en-us/azure/security-center/security-center-alerts-type.md#virtual-machine-behavioral-analysis) - 了解关于安全中心中虚拟机行为分析和故障转储内存分析的详细信息
- [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题
- [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章
