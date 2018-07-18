---
title: Azure 安全中心支持的平台 | Microsoft Docs
description: 本文档提供 Azure 安全中心支持的 Windows 和 Linux 操作系统列表。
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: terrylan
ms.openlocfilehash: 1bb0b61fe8a74edd35bb096fd295aedc67f6efb3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335884"
---
# <a name="supported-platforms-in-azure-security-center"></a>Azure 安全中心支持的平台
使用经典部署模式和资源管理器部署模式创建的虚拟机 (VM) 和计算机支持安全状态监视和建议。

> [!NOTE]
> 了解 Azure 资源的[经典部署模型和 Resource Manager 部署模型](../azure-classic-rm.md)。
>
>

## <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows 计算机和 VM 支持的平台
支持的 Windows 操作系统：

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


## <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux 计算机和 VM 支持的平台
支持的 Linux 操作系统：

* Ubuntu 版本 12.04 LTS、14.04 LTS、16.04 LTS
* Debian 版本 6、7、8、9
* CentOS 版本 5、6、7
* Red Hat Enterprise Linux (RHEL) 版本 5、6、7
* SUSE Linux Enterprise Server (SLES) 版本 11、12
* Oracle Linux 版本 5、6、7
* Amazon Linux 2012.09 - 2017
* 仅 x86_64 平台（64 位）支持 Openssl 1.1.0

> [!NOTE]
> Linux 操作系统暂不支持虚拟机行为分析。
>
>

## <a name="vms-and-cloud-services"></a>VM 和云服务
此外还支持云服务中运行的 VM。 仅监视云服务 web 和在生产槽运行的辅助角色。 若要了解有关云服务的详细信息，请参阅[云服务概述](../cloud-services/cloud-services-choose-me.md)。

## <a name="next-steps"></a>后续步骤

- [Azure 安全中心规划和操作指南](security-center-planning-and-operations-guide.md) - 了解如何规划和了解设计注意事项，以适应 Azure 安全中心
- [Azure 安全中心按类型划分的安全警报](security-center-alerts-type.md#virtual-machine-behavioral-analysis) - 了解关于安全中心中虚拟机行为分析和故障转储内存分析的详细信息
- [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题
- [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章
