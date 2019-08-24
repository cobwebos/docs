---
title: 基于数据科学虚拟机的团队环境简介 - Azure | Microsoft Docs
description: 将数据科学 VM 部署到企业团队环境的模式。
keywords: 深度学习, AI, 数据科学工具, 数据科学虚拟机, 地理空间分析, 团队数据科学过程
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: vijetaj
ms.openlocfilehash: 5f34498fbdacf7fc6e62788913c795ab70ceef23
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991614"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>基于数据科学虚拟机的团队分析和 AI 环境 
[Data Science Virtual Machine](overview.md) (DSVM) 在 Azure 平台上提供丰富的环境, 其中包含预先构建的用于人工智能 (AI) 的软件和数据分析。

传统上，DSVM 已被用作单独的分析桌面。 单个数据科学家利用此共享的预建分析环境提高工作效率。 随着大型分析团队为其数据科学家和 AI 开发人员规划环境, 其中一个重复的主题是用于开发和试验的共享分析基础结构。 此基础结构以企业 IT 策略为基础进行管理, 这些策略还有助于跨数据科学和分析团队进行协作和一致性。

使用共享基础结构可以更好地利用分析环境。 某些组织将*分析沙箱*称为基于团队的数据科学/分析基础结构。 它使数据科学家能够访问不同的数据资产, 以快速理解数据。 此沙盒环境还可帮助数据科学家运行试验, 验证假设并构建预测模型, 而不会影响生产环境。

由于 DSVM 在 Azure 基础结构级别运行，因此 IT 管理员可以轻松配置 DSVM，使其在遵守企业 IT 策略的情况下运行。 DSVM 提供了实现各种共享体系结构的完全灵活性, 同时还提供了以受控方式访问公司数据资产的权限。

本节讨论一些可以用来将 DSVM 部署为基于团队的数据科学基础结构的模式和指南。 由于这些模式的构建基块来自 Azure 基础结构即服务 (IaaS), 因此它们适用于任何 Azure Vm。 本系列文章重点介绍如何将这些标准 Azure 基础结构功能应用到 DSVM。

企业团队分析环境的重要构建基块包括:

* [Dsvm 的自动缩放池](dsvm-pools.md)
* [对池中任意 DSVM 工作区的通用标识和访问](dsvm-common-identity.md)
* [对数据源的安全访问](dsvm-secure-access-keys.md)


此系列提供上述每个主题的指导和指针。 它不包含在大型企业配置中部署 Dsvm 的所有注意事项和要求。 下面是在企业中实现 DSVM 实例时可以使用的其他一些 Azure 资源:

* [网络安全](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [监视](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)和[管理](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [日志记录和审核](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [策略设置和执行](../../governance/policy/overview.md)
* [反恶意软件](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [加密](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [数据发现和管理](https://docs.microsoft.com/azure/data-catalog/)

最后, [Azure 体系结构中心](https://docs.microsoft.com/azure/architecture/)提供了一种详细的端到端体系结构和模型, 用于构建和管理基于云的分析基础结构。
