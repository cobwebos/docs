---
title: Azure 数据资源管理器数据可视化
description: 了解如何实现 Azure 数据资源管理器数据的可视化效果的不同方式
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: d1c73d8eb65ed5d67d5250b4a3bca3b80450001e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536725"
---
# <a name="data-visualization-with-azure-data-explorer"></a>使用 Azure 数据资源管理器进行数据可视化 

Azure 数据资源管理器是用于生成复杂的分析解决方案有大量的数据的日志和遥测数据快速且高度可缩放的数据探索服务。 Azure 数据资源管理器与各种可视化效果工具，以便您可以可视化你的数据和在组织内共享结果。 可以将此数据转换为可操作见解以对你的业务产生的影响。

数据可视化和报告数据的分析过程中的关键步骤。 Azure 数据资源管理器支持许多 BI 服务，因此可以使用最适合您的方案和预算。

## <a name="kusto-query-language-visualizations"></a>Kusto 查询语言的可视化效果

Kusto 查询语言[ `render operator` ](/azure/kusto/query/renderoperator)提供了各种可视化效果，如表、 饼图和条形图以描述查询结果。 查询可视化效果是有帮助的异常情况检测和预测、 机器学习和的详细信息。

## <a name="power-bi"></a>Power BI

Azure 数据资源管理器提供了连接到的功能[Power BI](https://powerbi.microsoft.com)使用各种方法： 

  * [内置的本机 Power BI 连接器](/azure/data-explorer/power-bi-connector)

  * [查询从 Azure 数据资源管理器导入到 Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL 查询](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure 数据资源管理器提供了连接到的功能[Microsoft Excel](https://products.office.com/excel)使用内置的本机 Excel 连接器，或将查询从 Azure 数据资源管理器导入 Excel。

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com)提供了一个 Azure 数据资源管理器插件，可用于直观显示 Azure 数据资源管理器中的数据。 您[作为数据源为 Grafana，设置 Azure 数据资源管理器，然后将数据可视化](/azure/data-explorer/grafana)。 

## <a name="odbc-connector"></a>ODBC 连接器

Azure 数据资源管理器提供了[开放式数据库连接 (ODBC) 连接器](connect-odbc.md)以便支持 ODBC 的任何应用程序可以连接到 Azure 数据资源管理器。

## <a name="tableau"></a>Tableau

Azure 数据资源管理器提供了连接到的功能[Tableau](https://www.tableau.com)使用[ODBC 连接器](/azure/data-explorer/connect-odbc)，然后[可视化的 Tableau 数据](tableau.md)。

## <a name="qlik"></a>Qlik

Azure 数据资源管理器提供了连接到的功能[Qlik](https://www.qlik.com)使用[ODBC 连接器](/azure/data-explorer/connect-odbc)创建 Qlik Sense 仪表板并直观显示数据。 使用下面的视频，您可以了解如何使用直观显示 Azure 数据资源管理器数据 Qlik。 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure 数据资源管理器提供了连接到的功能[Sisense](https://www.sisense.com)使用 JDBC 连接器。 您[作为数据源为 Sisense，设置 Azure 数据资源管理器，然后将数据可视化](/azure/data-explorer/sisense)。