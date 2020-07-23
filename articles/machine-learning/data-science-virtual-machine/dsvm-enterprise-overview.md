---
title: 团队分析和 AI 环境
titleSuffix: Azure Data Science Virtual Machine
description: 将数据科学 VM 部署到企业团队环境的模式。
keywords: 深度学习, AI, 数据科学工具, 数据科学虚拟机, 地理空间分析, 团队数据科学过程
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 03fdbf6979db3249d1322a3025b48de81b953ae1
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856216"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>基于数据科学虚拟机的团队分析和 AI 环境 
[Data Science Virtual Machine](overview.md) (DSVM) 在 Azure 平台上提供了一个丰富的环境，其中包含为人工智能 (AI) 和数据分析预建的软件。

传统上，DSVM 已被用作单独的分析桌面。 个人数据科学家可通过这一共享的预建分析环境提高生产效率。 当大型分析团队为其数据科学家和 AI 开发人员规划环境时，其中一个反复出现的主题是用于开发和实验的共享分析基础结构。 该基础结构的托管方式符合企业 IT 策略，这些策略还有助于促进数据科学和分析团队之间的协作和一致性。

共享基础结构使 IT 部门能够更好地利用该分析环境。 某些组织将基于团队的数据科学/分析基础结构称为“分析沙盒”  。 它使数据科学家能够访问各种数据资产，以便快速了解数据。 此沙盒环境还帮助数据科学家运行实验、验证假设并构建预测模型，而不影响生产环境。

由于 DSVM 在 Azure 基础结构级别运行，因此 IT 管理员可以轻松配置 DSVM，使其在遵守企业 IT 策略的情况下运行。 DSVM 在实现各种共享体系结构方面提供了充分的灵活性，同时还以受控方式提供对公司数据资产的访问。

本节讨论一些可以用来将 DSVM 部署为基于团队的数据科学基础结构的模式和指南。 由于这些模式的构建基块来自 Azure 基础结构即服务 (IaaS)，因此它们适用于任何 Azure VM。 此系列的文章侧重于将这些标准 Azure 基础结构功能应用到 DSVM。

企业团队分析环境的关键构建基块包括：

* [DSVM 的自动缩放池](dsvm-pools.md)
* [对池中任意 DSVM 工作区的通用标识和访问](dsvm-common-identity.md)
* [对数据源的安全访问](dsvm-secure-access-keys.md)


这一系列针对前面的每个主题提供指导和方针。 它不包括在大型企业配置中部署 DSVM 的所有注意事项和需求。 以下是在企业中实现 DSVM 实例时可以使用的其他一些 Azure 资源：

* [网络安全](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [监视](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)和[管理](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [日志记录和审核](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [策略设置和执行](../../governance/policy/overview.md)
* [反恶意软件](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [加密](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-overview)
* [数据发现和管理](https://docs.microsoft.com/azure/data-catalog/)

最后，[Azure 体系结构中心](https://docs.microsoft.com/azure/architecture/)为构建和管理基于云的分析基础结构提供了详细的端到端体系结构和模型。