---
title: 备份中心概述
description: 本文概述了 Azure 的备份中心。
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: e7577f302b9a137f5f3c257dc35d0fdf339f69a4
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994575"
---
# <a name="overview-of-backup-center"></a>备份中心概述

备份中心在 Azure 中提供了一种 **统一的管理体验** ，使企业能够大规模控制、监视、操作和分析备份。 因此，它与 Azure 的本机管理体验一致。

备份中心的一些主要优点包括：

* **用于管理备份的单个窗格** ：备份中心设计为可在大型分布式 Azure 环境中正常运行。 你可以使用备份中心来有效地管理跨多个工作负荷类型、保管库、订阅、区域和 [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) 租户的备份。
* 以**数据中心为中心的管理**–备份中心提供视图和筛选器，这些视图和筛选器位于你备份 (例如，vm 和数据库) 的数据源中。 这允许资源所有者或备份管理员监视和操作项的备份，而无需专注于将项备份到的保管库。 此设计的一项重要功能是能够按特定于数据源的属性（如数据源订阅、数据源资源组和数据源标记）筛选视图。 例如，如果你的组织遵循将不同的标记分配给属于不同部门的 Vm 的做法，则可以使用备份中心基于要备份的底层 Vm 的标记筛选备份信息，而无需将精力集中在保管库的标记上。
* **连接体验** –备份中心提供对现有 Azure 服务的本机集成，可大规模管理。 例如，备份中心使用 [Azure 策略](https://docs.microsoft.com/azure/governance/policy/overview) 体验来帮助你管理备份。 它还利用 [Azure 工作簿](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview) 和 [Azure Monitor 日志](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) 来帮助你查看有关备份的详细报表。 因此，您无需学习任何新的原则即可使用备份中心提供的各种功能。

## <a name="supported-scenarios"></a>支持的方案

* 目前，Azure VM 备份和 Azure Database for PostgreSQL 服务器备份支持备份中心。
* 有关支持的和不支持的方案的详细列表，请参阅 [支持矩阵](backup-center-support-matrix.md) 。

## <a name="get-started"></a>入门

若要开始使用备份中心，请在 Azure 门户中搜索 " **备份中心** "，并导航到 " **备份中心 (预览") ** 仪表板。

![备份中心搜索](./media/backup-center-overview/backup-center-search.png)

你看到的第一个屏幕是 **概述**。 它包含两个磁贴– **作业** 和 **备份实例**。

![备份中心磁贴](./media/backup-center-overview/backup-center-overview-widgets.png)

在 " **作业** " 磁贴中，可以获得在过去24小时内通过备份空间触发的所有备份和还原相关作业的汇总视图。 你可以查看有关已完成、失败和正在进行中的作业数的信息。 选择此磁贴中的任何数字可查看有关特定数据源类型、操作类型和状态的详细信息。

在 " **备份实例** " 磁贴中，可以获得备份空间内所有备份实例的汇总视图。 例如，与仍配置为要进行保护的实例数相比，可以看到处于软删除状态的备份实例数。 选择此磁贴中的任何数字可查看有关特定数据源类型和保护状态的备份实例的详细信息。

请按照以下步骤来了解备份中心提供的不同功能，以及如何使用这些功能来有效地管理备份空间。

## <a name="next-steps"></a>后续步骤

* [监视和操作备份](backup-center-monitor-operate.md)
* [控制备份场地](backup-center-govern-environment.md)
* [获取有关备份的见解](backup-center-obtain-insights.md)
* [使用备份中心执行操作](backup-center-actions.md)
