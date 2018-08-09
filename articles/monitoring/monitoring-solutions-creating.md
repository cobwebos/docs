---
title: 在 Azure 中生成管理解决方案 | Microsoft Docs
description: 管理解决方案包括 Azure 中打包的管理方案，客户可将其添加到 Log Analytics 工作区。  本文提供有关如何创建要在自己的环境中使用或可供客户使用的管理解决方案的详细信息。
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a07a17105b4d84b51689e9636cfacc7a3b5428ad
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39528021"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>在 Azure 中设计和生成管理解决方案（预览）
> [!NOTE]
> 这是在 Azure 中创建管理解决方案（当前处于预览状态）的初步文档。 如下所述的全部架构均会有变动。

[管理解决方案]( monitoring-solutions.md)提供打包的管理方案，客户可将其添加到 Log Analytics 工作区。  本文提供了一个基本的流程，方便用户设计和生成符合最常见要求的管理解决方案。  如果不了解如何生成管理解决方案，则可一开始就使用此流程，并在要求提高以后利用相关概念生成更复杂的解决方案。

## <a name="what-is-a-management-solution"></a>管理解决方案是什么？

管理解决方案包含可协同工作以实现特定管理方案的 Azure 资源。  并且可以作为[资源管理模板](../azure-resource-manager/resource-manager-template-walkthrough.md)来实现。此类模板包含说明，详述了在安装解决方案时如何安装和配置模板中包含的资源。

基本策略是通过在 Azure 环境中生成各个组件来启动管理解决方案。  功能正常以后，即可将其打包到[管理解决方案文件]( monitoring-solutions-solution-file.md)中。 


## <a name="design-your-solution"></a>设计解决方案
管理解决方案的最常见模式如下图所示。  下面介绍了此模式中的不同组件。

![管理解决方案概述](media/monitoring-solutions-creating/solution-overview.png)


### <a name="data-sources"></a>数据源
设计解决方案的第一步是确定需要从 Log Analytics 存储库获取的数据。  可以通过[数据源](../log-analytics/log-analytics-data-sources.md)或[其他解决方案]( monitoring-solutions.md)收集此类数据，否则就必须在解决方案中提供其收集过程。

可以通过多种方式在 Log Analytics 存储库中收集数据源，详见 [Log Analytics 中的数据源](../log-analytics/log-analytics-data-sources.md)。  可以收集 Windows 事件日志中的事件，或者收集由 Windows 和 Linux 客户端的 Syslog 和性能计数器生成的事件。  此外，还可以收集通过 Azure Monitor 收集的 Azure 资源中的数据。  

如果所需数据无法通过任何可用的数据源进行访问，则可通过任何能够调用 REST API 的客户端，使用 [HTTP 数据收集器 API](../log-analytics/log-analytics-data-collector-api.md) 将数据写入 Log Analytics 存储库。  在管理解决方案中自定义数据收集时，最常见的方式是创建 [Azure 自动化中的 runbook](../automation/automation-runbook-types.md)，以便从 Azure 或外部资源收集所需数据，并使用数据收集器 API 将数据写入存储库。  

### <a name="log-searches"></a>日志搜索
[日志搜索](../log-analytics/log-analytics-log-searches.md)用于提取和分析 Log Analytics 存储库中的数据。  视图和警报可以使用日志搜索，用户也可以使用日志搜索对存储库中的数据进行临时分析。  

如果认为某些查询对用户有用，则应定义这些查询，即使视图或警报不使用这些查询。  这些查询会在门户中以“保存的搜索”形式提供给用户，也可将其添加到自定义视图的[“查询列表”可视化效果部件](../log-analytics/log-analytics-view-designer-parts.md#list-of-queries-part)。

### <a name="alerts"></a>警报
[Log Analytics 中的警报](../log-analytics/log-analytics-alerts.md)通过[日志搜索](#log-searches)来搜索存储库中的数据，从而确定问题。  警报会通知用户，或者自动运行某个操作作为响应。 应该为应用程序确定不同的警报条件，并且让相应的警报规则包括在解决方案文件中。

如果有可能通过自动化过程纠正问题，则通常应在 Azure 自动化中创建一个 runbook 来执行该纠正操作。  大多数 Azure 服务可以通过 [cmdlet](/powershell/azure/overview) 进行管理，runbook 也可以利用后者来执行此类功能。

如果解决方案需要外部功能来响应警报，则可使用 [webhook 响应](../log-analytics/log-analytics-alerts-actions.md)，  以便调用外部 Web 服务，发送警报中的信息。

### <a name="views"></a>视图
Log Analytics 中的视图用于可视化 Log Analytics 存储库中的数据。  每个解决方案通常会包含一个视图，其中的[磁贴](../log-analytics/log-analytics-view-designer-tiles.md)会显示在用户的主仪表板上。  该视图可以包含任意数目的[可视化效果部件](../log-analytics/log-analytics-view-designer-parts.md)，为用户提供所收集数据的不同可视化效果。

可以[使用视图设计器创建自定义视图](../log-analytics/log-analytics-view-designer.md)，随后再导出这些视图，将其添加到解决方案文件中。  


## <a name="create-solution-file"></a>创建解决方案文件
配置并测试将要成为解决方案一部分的组件以后，即可[创建解决方案文件]( monitoring-solutions-solution-file.md)。  可在 [Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)中实现解决方案组件。该模板包含一个与文件中的其他资源存在多项关系的[解决方案资源]( monitoring-solutions-solution-file.md#solution-resource)。  


## <a name="test-your-solution"></a>测试解决方案
开发解决方案时，需将其安装在工作区并进行测试。  为此，可以使用任何可用方法来[测试和安装 Resource Manager 模板](../azure-resource-manager/resource-group-template-deploy.md)。

## <a name="publish-your-solution"></a>发布解决方案
完成解决方案并进行测试以后，即可通过以下某种方式将其提供给客户。

- **Azure 快速入门模板**。  [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/)是一套 Resource Manager 模板，由社区通过 GitHub 提供。  可以按[贡献指南](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE)中的说明提供解决方案。
- 
  **Azure 市场**。  可以通过 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/)将解决方案分发和销售给其他开发人员、ISV 和 IT 专业人员。  要了解如何将解决方案发布到 Azure 市场，可参阅[如何在 Azure 市场中发布和管理产品/服务](../marketplace/marketplace-publishers-guide.md)。



## <a name="next-steps"></a>后续步骤
* 了解如何为管理解决方案[创建解决方案文件]( monitoring-solutions-solution-file.md)。
* 了解[创作 Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)的详细信息。
* 在 [Azure 快速启动模板](https://azure.microsoft.com/documentation/templates)中搜索不同 Resource Manager 模板的示例。