---
title: 基于数据科学虚拟机的团队环境简介 - Azure | Microsoft Docs
description: 将数据科学 VM 部署到企业团队环境的模式。
keywords: 深度学习, AI, 数据科学工具, 数据科学虚拟机, 地理空间分析, 团队数据科学过程
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 8486b0be1fb5e1385da3c7ad55f6410a1059df93
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309242"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>基于数据科学虚拟机的团队分析和 AI 环境 
[数据科学虚拟机](overview.md) (DSVM) 通过人工智能 (AI) 和数据分析的预建软件在 Azure 平台上提供丰富的环境。 

传统上，DSVM 已被用作单独的分析桌面。 个人数据科学家通过他们预建的分析环境的共享概念获得生产力。 当大型分析团队为其数据科学家和 AI 开发人员规划分析环境时，其中一个反复出现的主题是用于开发和实验的共享分析基础结构。 该基础结构的托管方式符合企业 IT 策略，这些策略还有助于促进数据科学/分析团队之间的协作和一致性。 

共享基础结构还使 IT 部门能够更好地利用分析环境。 某些组织将基于团队的数据科学/分析基础结构称为“分析沙盒”。 它使数据科学家能够访问各种数据资产，以快速了解数据、运行实验、验证假设，并在不影响生产环境的情况下构建预测模型。 

由于 DSVM 在 Azure 基础结构级别运行，因此 IT 管理员可以轻松配置 DSVM，使其在遵守企业 IT 策略的情况下运行。 DSVM 提供了完全灵活性，可以实现以受控方式访问公司数据资产的各种共享体系结构。 

本节讨论一些可以用来将 DSVM 部署为基于团队的数据科学基础结构的模式和指南。 这些模式的构建基块来自 Azure 基础结构即服务 (IaaS)，因此它们适用于任何 Azure VM。 此系列文章关注将这些标准 Azure 基础结构功能应用到数据科学 VM。 

以下是企业团队分析环境的一些关键构建基块：

* [数据科学虚拟机的自动缩放池](dsvm-pools.md)
* [对池中任意 DSVM 工作区的通用标识和访问](dsvm-common-identity.md)
* [对数据源的安全访问](dsvm-secure-access-keys.md)


此系列文章针对之前的每个项目提供指南和指针。 它不包括在大型企业配置中部署 DSVM 的所有注意事项和需求。 以下是在企业中实现 DSVM 实例时可以使用的其他 Azure 文档： 

* [网络安全](https://docs.microsoft.com/azure/security/azure-network-security)
* [监视](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)和[管理](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [日志记录和审核](https://docs.microsoft.com/azure/security/azure-log-audit)
* [基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [策略设置和执行](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)
* [反恶意软件](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [加密](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [数据发现和管理](https://docs.microsoft.com/azure/data-catalog/)

[Azure 体系结构中心](https://docs.microsoft.com/en-us/azure/architecture/)为构建和管理基于云的分析基础结构提供了详细的端到端体系结构和模式。 
