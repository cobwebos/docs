---
title: Azure 中管理解决方案的最佳做法 | Microsoft Docs
description: ''
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: 0bd5e19e00dbae1d0ece27d0498a1f599dba05b7
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>在 Azure 中创建管理解决方案的最佳做法（预览版）
> [!NOTE]
> 这是在 Azure 中创建管理解决方案（当前处于预览状态）的初步文档。 如下所述的全部架构均会有变动。  

本文提供在 Azure 中[创建管理解决方案文件](monitoring-solutions-solution-file.md)的最佳做法。  发现其他最佳做法后，将更新此信息。

## <a name="data-sources"></a>数据源
- 数据源可以[使用 Resource Manager 模板进行配置](../log-analytics/log-analytics-template-workspace-configuration.md)，但它们不应包含在解决方案文件中。  原因是，数据源配置当前是不幂等的，这意味着解决方案可以覆盖用户工作区中的现有配置。<br><br>例如，解决方案可能需要应用程序事件日志中的警告和错误事件。  如果在解决方案中将此项指定为数据源，则当用户已在其工作区中配置此项时，可能会删除信息事件。  如果包含所有事件，则可能会在用户的工作区中收集过多信息事件。

- 如果解决方案需要来自某个标准数据源的数据，则应将此数据源定义为先决条件。  在文档中声明：客户必须自己配置数据源。  
- 向解决方案中的任何视图添加[数据流验证](../log-analytics/log-analytics-view-designer-tiles.md)消息，以针对需要为待收集的所需数据而配置的数据源向用户提供指示。  找不到所需的数据时，此消息会显示在视图的磁贴上。


## <a name="runbooks"></a>Runbook
- 为解决方案中需要按计划运行的每个 runbook 添加[自动化计划](../automation/automation-schedules.md)。
- 在解决方案中包括 [IngestionAPI 模块](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5)，以供将数据写入到 Log Analytics 存储库的 runbook 使用。  将解决方案配置为[引用](monitoring-solutions-solution-file.md#solution-resource)此资源，以便在删除了解决方案后，此资源仍保留。  这就可以让多个解决方案共享此模块。
- 使用[自动化变量](../automation/automation-schedules.md)为解决方案提供用户以后可能要更改的值。  即使解决方案配置为包含该变量，则仍可以更改该变量的值。

## <a name="views"></a>视图
- 所有解决方案都应包含在用户门户中显示的单一视图。  该视图可以包含多个[可视化效果部件](../log-analytics/log-analytics-view-designer-parts.md)以演示不同数据集。
- 向解决方案中的任何视图添加[数据流验证](../log-analytics/log-analytics-view-designer-tiles.md)消息，以针对需要为待收集的所需数据而配置的数据源向用户提供指示。
- 将解决方案配置为[包含](monitoring-solutions-solution-file.md#solution-resource)该视图，因此如果删除解决方案，它也会被删除。

## <a name="alerts"></a>警报
- 在解决方案文件中将收件人列表定义为参数，以便用户在安装解决方案时可以定义它们。
- 将解决方案配置为[引用](monitoring-solutions-solution-file.md#solution-resource)警报规则，以便用户可以更改其配置。  他们可能想要进行更改，例如修改收件人列表、更改警报的阈值或禁用警报规则。 


## <a name="next-steps"></a>后续步骤
* 演练[设计和生成管理解决方案](monitoring-solutions-creating.md)的基本过程。
* 了解如何[创建解决方案文件](monitoring-solutions-solution-file.md)。
* [已保存的搜索和警报](monitoring-solutions-resources-searches-alerts.md)到管理解决方案。
* [将视图](monitoring-solutions-resources-views.md)添加到管理解决方案。
* [将自动化 runbook 和其他资源添加](monitoring-solutions-resources-automation.md)到管理解决方案。

