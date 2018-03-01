---
title: "在 Azure Log Analytics 中管理数据保留成本 | Microsoft Docs"
description: "了解如何更改 Azure 门户中 Log Analytics 工作区的定价计划和数据保留策略。"
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 5143abdde715424a41a53bb661db342acf817e0c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="manage-cost-of-data-retention-with-your-log-analytics-workspace"></a>使用 Log Analytics 工作区管理数据保留成本
注册 Log Analytics 时，对连接的资源生成的数据可存储在工作区中的时长限制因选择的计划而异。  本文重点介绍可能影响不同时期数据保留成本的注意事项以及该限制的配置方式。   

因为 Log Analytics 可能占用大量来自本地、云和混合环境的数据，所以存储该数据一段时间的成本取决于以下因素：

* 系统数量、基础结构组件、云资源等（从中收集数据）
* 源创建的数据类型（例如，消息队列、日志、事件、安全相关数据或性能指标）
* 这些源生成的数据量 
* 启用的管理解决方案数、数据源和收集频率

> [!NOTE]
> 请参考每个解决方案的文档，这些文档提供了数据收集量的预测值。   

如果是“免费”计划，数据保留期限制为七天。  对于“独立”或“付费”层，收集的数据在最后 31 天可用。  

使用“免费”计划时，如果发现一直超出允许的量，可以将工作区更改为付费计划，从而收集超出该限制的数据。 

> [!NOTE]
> 如果为该付费层选择更长的保留期，会收取费用。 可随时更改计划类型，关于定价的详细信息，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/log-analytics/)。 

## <a name="change-the-data-retention-period"></a>更改数据保留期 

1. 登录到 [Azure 门户](http://portal.azure.com)。 
2. 单击“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。
3. 在 Log Analytics 订阅窗格中，选择要从列表中修改的工作区。
4. 在工作区页面上，从左侧窗格单击“保留”。
5. 在工作区保留窗格上，移动滑块以增加或减少天数，然后单击“保存”。  如果位于“免费”层，则不能修改数据保留期，需要升级到付费层才能控制这一项设置。<br><br> ![更改工作区数据保留设置](media/log-analytics-manage-cost/manage-cost-change-retention.png)

## <a name="next-steps"></a>后续步骤  

要确定收集的数据量、发送源和发送的各种不同数据类型，从而帮助管理使用量和成本，请参阅[在 Log Analytics 中分析数据使用情况](log-analytics-usage.md)