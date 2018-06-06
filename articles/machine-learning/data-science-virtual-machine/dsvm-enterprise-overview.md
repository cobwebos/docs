---
title: 基于数据科学虚拟机的团队环境简介 - Azure | Microsoft Docs
description: 将数据科学 VM 部署为企业团队环境的模式。
keywords: 深度学习, AI, 数据科学工具, 数据科学虚拟机, 地理空间分析, 团队数据科学过程
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 6a755ef4d933046377a6a25be76655b44f4bf508
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34361363"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>基于数据科学虚拟机的团队分析和 AI 环境 
[数据科学虚拟机](overview.md) (DSVM) 通过 AI 和数据分析的预建软件在 Azure 云上提供丰富的环境。 DSVM 历来被用作个人分析桌面，并且个人数据科学家通过其预建分析环境的共享概念提高了生产率。 当大型分析团队为其数据科学家和 AI 开发人员规划分析环境时，其中一个反复出现的主题就是打造共享分析开发和试验基础结构，该基础结构根据跨整个数据科学/分析团队促进协作和一致的企业 IT 策略进行管理。 共享基础结构还使 IT 部门能够更好地利用分析环境。 基于团队的数据科学/分析基础结构还被某些组织称为“分析沙盒”，它帮助数据科学家在有权访问多种数据资产但不影响生产环境的情况下，以安全的方式快速理解数据、运行试验、验证假设、构建预测模型。 

由于 DSVM 在 Azure 基础结构级别运作，因此 IT 管理员可以轻松配置 DSVM，使其遵照企业的 IT 策略运作，还可以充分灵活地实现各种共享体系结构，并以受控方式访问公司数据资产。 

本节讨论一些可以用来将 DSVM 部署为基于团队的数据科学基础结构的模式和指南。  直接从 Azure IaaS（服务架构）利用这些模式的构建基块，因此这些构建基块适用于任意 Azure VM。 此系列文章重点关注将这些标准 Azure 基础结构功能应用到数据科学 VM。 

以下是企业团队分析环境的一些关键构建基块：

* [数据科学虚拟机的自动缩放池](dsvm-pools.md)
* [对池中任意 DSVM 工作区的通用标识和访问](dsvm-common-identity.md)
* [对数据源的安全访问](dsvm-secure-access-keys.md)


此系列文章提供了上述每个方面的指南和指针。 显然，在大型企业配置中部署 DSVM 时，还有一些其他注意事项和需求，这些内容尚未直接在本系列文章中介绍。 以下是针对一般 Azure 文档的一些其他注意事项和指针，方便你在企业中的 DSVM 实例上实现它时轻松参考。 

* [网络安全](https://docs.microsoft.com/azure/security/azure-network-security)
* [监视](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)和[管理](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [日志记录和审核](https://docs.microsoft.com/azure/security/azure-log-audit)
* [基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [策略设置和执行](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [反恶意软件](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [加密](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [数据发现和管理](https://docs.microsoft.com/azure/data-catalog/)

[Azure 体系结构中心](https://docs.microsoft.com/en-us/azure/architecture/)也是很好的资源，它为构建和管理基于云的分析基础结构提供了详细的端到端体系结构和模式。 
