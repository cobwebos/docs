---
title: "在 Azure 成本管理中使用成本管理报表 | Microsoft Docs"
description: "本文介绍如何在 Cloudyn 门户中使用各种成本管理报表。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 0a4f6a5940ccffe897a5609b837ddaeea98d87aa
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="use-cost-management-reports"></a>使用成本管理报表

本文介绍如何在 Cloudyn 门户中使用各种成本管理报表。 大多数 Cloudyn 报表都较为直观，具有统一的外观和风格。 有关 Cloudyn 报表的概述，请参阅[了解成本报表](understading-cost-reports.md)。 本文还介绍了大多数报表中使用的各种选项和字段。

## <a name="cost-analysis-reports"></a>成本分析报表

成本分析报表显示来自云提供程序的计费数据。 使用报表可以分组和深入查看计费文件中具体化的各个数据段。 使用报表可以根据不同云供应商的原始计费数据进行精细的成本调查。

成本分析报表不会按标记分组成本。 基于标记的报告只会在使用 Cost Allocation 360 创建成本模型后设置的成本分配报表中提供。

### <a name="actual-cost-analysis"></a>实际成本分析

实际成本分析报表显示主要成本促成因素，包括持续成本和一次性费用。

 使用实际成本分析报表可以：

- 分析和监视指定时间段内花费的实际成本
- 计划阈值警报
- 分析报销和退款成本

#### <a name="to-use-the-actual-cost-analysis-report"></a>使用实际成本分析报表

最起码需要执行以下步骤。 也可以使用其他选项和字段。

1. 选择日期范围。
2. 选择筛选器。

右键单击报表结果可以深入查看更多详细信息。

![实际成本分析报表示例](./media/use-reports/actual-cost-analysis.png)

### <a name="actual-cost-over-time"></a>时段实际成本

时段实际成本报表是一份标准的成本分析报表，可根据定义的时间精度显示成本分布。 该报表显示特定一段时间的支出，以便能够观察趋势并检测异常支出。 此报表显示主要成本促成因素，包括选定时间范围内的持续成本，以及一次性支出的保留实例费用。

使用时段实际成本报表可以：

- 查看一段时间内的成本趋势。
- 发现异常成本。
- 发现与 Amazon Web Services 相关的所有成本问题。

#### <a name="to-use-the-actual-cost-over-time-report"></a>使用时段实际成本报表：

最起码需要执行以下步骤。 也可以使用其他选项和字段。

- 选择日期范围。

例如，可以选择组来查看其在一段时间内的成本。 然后添加筛选器来缩小结果范围。

![时段实际成本报表示例](./media/use-reports/actual-cost-over-time.png)



### <a name="amortized-cost-reports"></a>摊销成本报表

这套摊销成本报表显示线性的非基于用量的服务费用或者一次性的应付成本，并已根据服务整个生存期的不同时间段将成本均匀分摊。

例如，一次性费用可能包括：

- 年度支持费用
- 年度安全组件费用
- 保留实例购买费用
- 某些 Azure Marketplace 项目

在计费文件中，如果服务耗用开始和结束日期或时间戳的值相同，则一次性费用将会特征化。 然后，Cloudyn 可将此类成本识别为可摊销的一次性费用。 存在按需使用成本的其他基于消耗量的服务不可摊销。

以下时段实际成本报表示例图中的演示了摊销成本。 示例中显示成本高峰发生在 8 月 23 日。 与正常的每日成本趋势相比，这似乎是一种异常。 根本原因分析和数据调查已识别出，这笔成本是年度 AWS 服务的 APN 保留费用，并且是该日期发生的一次性采购和帐单费用。 下一部分介绍了此成本的摊销方式。

![显示一次性成本的时段实际成本报表示例](./media/use-reports/actual-amort-example.png)

#### <a name="to-use-the-amortized-cost-over-time-report"></a>使用时段摊销成本报表：

最起码需要执行以下步骤。 也可以使用其他选项和字段。

1. 选择日期范围。
2. 选择服务和提供程序。

沿用前面的示例，如下图所示，一次性成本现已摊销：

![时段摊销成本报表示例](./media/use-reports/amort-cost-over-time.png)

上图显示了一段时间内 APN 保留成本的摊销成本。 此报表显示了一次性费用摊销，以及由于购买年度保留服务而产生的 APN 成本。 APN 成本已按日均匀分摊为保留预付成本的 1/365。

## <a name="cost-allocation-analysis-reports"></a>成本分配分析报表

使用 Cost Allocation 360 创建成本模型后，会提供成本分配分析报表。 Cloudyn 处理成本/计费数据，并将这些数据与云帐户的用量和标记数据进行匹配。 若要匹配数据，Cloudyn 需要能够访问用量数据。 缺少凭据的帐户将标记为未分类资源。

### <a name="cost-analysis-report"></a>成本分析报表

成本分析报表提供选定时间范围内云消耗量和支出的深入信息。 成本分析报表使用成本分配管理器中设置的策略。

Cloudyn 如何计算此报表？

Cloudyn 通过应用帐户关联，确保分配结果保持每个链接帐户的完整性。 关联可确保未使用特定服务的帐户不会被分配此服务的任何成本。 该帐户中的应计成本保留在该帐户中，不会由分配策略计算。 例如，你可能有五个链接帐户。 如果其中只有三个使用存储服务，则只会在三个帐户中的标记之间分配存储服务的成本。

 使用成本分析报表可以：

- 显示特定时间范围内整个部署的聚合视图。
- 根据成本模型中创建的策略，按标记类别查看成本。

#### <a name="to-use-the-cost-analysis-report"></a>使用成本分析报表：

1. 选择日期范围。
2. 根据需要添加标记。
3. 添加组。
4. 选择之前创建的成本模型。

下图以旭日图格式显示一份示例成本分析报表。 圆环显示组。 外环显示服务，内环显示单元。

![成本分析报表示例](./media/use-reports/cost-analysis01.png)



下面以表格视图显示相同的示例信息。

![成本分析报表示例](./media/use-reports/cost-analysis02.png)



### <a name="cost-over-time-report"></a>成本历时报告

时段成本报表显示一段时间内的支出，以便可以观察趋势，并检测部署中的异常。 它实质上是显示定义时间段内的成本分布。 该报表包含主要成本促成因素，包括选定时间范围内的持续成本，以及一次性支出的保留实例费用。 此报表可以使用 Cost Manager 360° 中设置的策略。

使用时段成本报表可以：

- 查看一段时间内的变化，以及在不同的日期（或日期范围）哪些影响因素发生了变化。
- 分析特定实例在一段时间内的成本。
- 了解特定实例的成本为何增长。

#### <a name="to-use-the-cost-over-time-report"></a>使用时段成本报表：

1. 选择日期范围。
2. 根据需要添加标记。
3. 添加组。
4. 选择之前创建的成本模型。
5. 选择实际成本或摊销成本。
6. 选择是否应用分配规则来查看原始计费数据视图或 Cloudyn 重新计算的成本视图。

下面是一个报表示例。

![时段成本报表](./media/use-reports/cost-over-time.png)



## <a name="next-steps"></a>后续步骤

- 如果尚未完成有关成本管理的第一本教程，请阅读[查看使用情况和成本](tutorial-review-usage.md)。
