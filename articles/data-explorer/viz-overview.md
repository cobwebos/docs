---
title: Azure 数据资源管理器数据可视化
description: 了解如何实现 Azure 数据资源管理器数据的可视化效果的不同方式
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 85c37b6d626fc9942f5df956e738431d2727d282
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481829"
---
# <a name="data-visualization-with-azure-data-explorer"></a>使用 Azure 数据资源管理器进行数据可视化 

Azure 数据资源管理器是用于生成复杂的分析解决方案有大量的数据的日志和遥测数据快速且高度可缩放的数据探索服务。 Azure 数据资源管理器与各种可视化效果工具，以便您可以可视化你的数据和在组织内共享结果。 可以将此数据转换为可操作见解以对你的业务产生的影响。

数据可视化和报告数据的分析过程中的关键步骤。 Azure 数据资源管理器支持许多 BI 服务，因此可以使用最适合您的方案和预算。

* Azure 资源管理器中的数据可视化效果：使用 Kusto 查询语言[ `render operator` ](/azure/kusto/query/renderoperator)提供了各种可视化效果类型，以描述查询结果。 查询可视化效果是有帮助的异常情况检测和预测、 机器学习和的详细信息。

* [Power BI](https://powerbi.microsoft.com)：Azure 数据资源管理器提供了连接到 Power BI 的功能使用的各种方法： 

  * [内置的本机 Power BI 连接器](/azure/data-explorer/power-bi-connector)

  * [查询从 Azure 数据资源管理器导入到 Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL 查询](/azure/data-explorer/power-bi-sql-query)。

* [Microsoft Excel](https://products.office.com/excel):Azure 数据资源管理器提供的功能以连接到 Excel 中使用内置的本机 Excel 连接器，或将查询从 Azure 数据资源管理器导入 Excel。

* [Grafana](https://grafana.com):Grafana 提供了一个 Azure 数据资源管理器插件，可用于直观显示 Azure 数据资源管理器中的数据。 您[作为数据源为 Grafana，设置 Azure 数据资源管理器，然后将数据可视化](/azure/data-explorer/grafana)

* [Sisense](https://www.sisense.com):Azure 数据资源管理器提供了连接到 Sisense 的能力使用 JDBC 连接器。 您[作为数据源为 Sisense，设置 Azure 数据资源管理器，然后将数据可视化](/azure/data-explorer/sisense)。

* [Tableau](https://www.tableau.com):Azure 数据资源管理器提供的功能以连接到使用 Tableau [ODBC 连接器并在 Tableau 中的将数据可视化](/azure/data-explorer/connect-odbc)。

* [Qlik](https://www.qlik.com):Azure 数据资源管理器提供的功能连接到 Qlik [ODBC 连接器](/azure/data-explorer/connect-odbc)。