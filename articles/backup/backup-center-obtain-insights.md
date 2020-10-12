---
title: 使用备份中心获取见解
description: 了解如何分析历史趋势，并通过备份中心获取有关备份的更深入见解。
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 5964f285089feea721a0b452efed884e905b89cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994148"
---
# <a name="obtain-insights-using-backup-center"></a>使用备份中心获取见解

为了分析历史趋势并深入了解你的备份，备份中心为 [备份报表](configure-reports.md)提供了一个接口，该接口使用 [Azure Monitor 日志](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) 和 [Azure 工作簿](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview)。 备份报表提供以下功能：

- 分配和预测需使用的云存储空间。

- 审核备份和还原。

- 确定不同粒度级别的关键趋势。

- 了解备份的成本优化机会。

## <a name="supported-scenarios"></a>支持的方案

- 备份报表当前不可用于 Azure Database for PostgreSQL 服务器备份。

- 有关支持的和不支持的方案的详细列表，请参阅 [支持矩阵](backup-center-support-matrix.md) 。

## <a name="get-started"></a>入门

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>将保管库配置为将数据发送到 Log Analytics 工作区

[了解如何为保管库配置 "大规模诊断设置"](https://docs.microsoft.com/azure/backup/configure-reports#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>在备份中心门户中查看备份报表

选择 "备份中心" 中的 " **备份报表** " 菜单项将打开报表。 选择一个或多个 Log Analytics 工作区来查看和分析备份中的关键信息。

![备份中心的备份报表](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

下面是可用的视图：

1. **摘要** -使用此选项卡可大致了解备份空间。 [了解详细信息](https://docs.microsoft.com/azure/backup/configure-reports#summary)

1. **备份项** -使用此选项卡查看在备份项级别使用的云存储的信息和趋势。 [了解详细信息](https://docs.microsoft.com/azure/backup/configure-reports#backup-items)

1. **使用情况** -使用此选项卡查看备份的按键帐单参数。 [了解详细信息](https://docs.microsoft.com/azure/backup/configure-reports#usage)

1. **作业** -使用此选项卡可以查看作业的长时间运行趋势，例如每天失败的作业数，以及失败的原因的主要原因。 [了解详细信息](https://docs.microsoft.com/azure/backup/configure-reports#jobs)

1. **策略** -使用此选项卡查看所有活动策略的相关信息，例如，关联的项目数和在给定策略中备份的项目所消耗的云存储总量。 [了解详细信息](https://docs.microsoft.com/azure/backup/configure-reports#policies)

1. **优化** -使用此选项卡可查看备份的潜在成本优化机会。 [了解详细信息](https://docs.microsoft.com/azure/backup/configure-reports#optimize)

## <a name="next-steps"></a>后续步骤

- [监视和操作备份](backup-center-monitor-operate.md)
- [控制备份场地](backup-center-govern-environment.md)
- [使用备份中心执行操作](backup-center-actions.md)
