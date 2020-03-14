---
title: Azure 数据资源管理器数据可视化
description: 了解可用于直观显示 Azure 数据资源管理器数据的不同方式
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 7b1c7825beb769b610d661cb9644fc3f3919d548
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139056"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Azure 数据资源管理器中的数据可视化 

Azure 数据资源管理器是一种快速且高度可缩放的数据浏览服务，用于为大量数据生成复杂的分析解决方案。 Azure 数据资源管理器与各种可视化工具集成，因此可以直观显示数据，并在组织中共享结果。 此数据可以转换为可操作的见解，以影响你的业务。

数据可视化和报告是数据分析过程中的一个关键步骤。 Azure 数据资源管理器支持很多 BI 服务，因此可以使用最适合你的方案和预算的 BI 服务。

## <a name="kusto-query-language-visualizations"></a>Kusto 查询语言可视化

Kusto 查询语言[`render operator`](/azure/kusto/query/renderoperator)提供各种可视化效果，如表、饼图和条形图，以描述查询结果。 查询可视化效果在异常检测和预测、机器学习等方面非常有用。

## <a name="power-bi"></a>Power BI

Azure 数据资源管理器提供使用各种方法连接到[Power BI](https://powerbi.microsoft.com)的功能： 

  * [内置本机 Power BI 连接器](/azure/data-explorer/power-bi-connector)

  * [从 Azure 数据资源管理器到 Power BI 的查询导入](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL 查询](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure 数据资源管理器提供使用[内置本机 Excel 连接器](excel-connector.md)连接到[Microsoft Excel](https://products.office.com/excel)的功能，或将查询从 Azure 数据资源管理器[导入](excel-blank-query.md)到 Excel 中。

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com)提供了一个 azure 数据资源管理器插件，可用于将 azure 数据资源管理器中的数据可视化。 [将 Azure 数据资源管理器设置为 Grafana 的数据源，然后将数据可视化](/azure/data-explorer/grafana)。 

## <a name="kibana"></a>Kibana

Azure 数据资源管理器提供使用 K2Bridge （一个开源连接器）连接到[Kibana （"发现" 页）](https://www.elastic.co/guide/en/kibana/6.8/discover.html)的功能。 [将 Azure 数据资源管理器设置为 Kibana 的数据源，然后将数据可视化](/azure/data-explorer/k2bridge)。

## <a name="odbc-connector"></a>ODBC 连接器

Azure 数据资源管理器提供[开放式数据库连接（ODBC）连接器](connect-odbc.md)，因此支持 ODBC 的任何应用程序都可以连接到 Azure 数据资源管理器。

## <a name="tableau"></a>Tableau

Azure 数据资源管理器提供使用[ODBC 连接器](/azure/data-explorer/connect-odbc)连接到[Tableau](https://www.tableau.com) ，然后[在 Tableau 中对数据进行可视化](tableau.md)的功能。

## <a name="qlik"></a>Qlik

Azure 数据资源管理器提供使用[ODBC 连接器](/azure/data-explorer/connect-odbc)连接到[qlik sense](https://www.qlik.com)的功能，然后创建 qlik sense 感知仪表板并使数据可视化。 使用以下视频，可以了解如何使用 Qlik sense 可视化 Azure 数据资源管理器数据。 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure 数据资源管理器提供使用 JDBC 连接器连接到[Sisense](https://www.sisense.com)的功能。 [将 Azure 数据资源管理器设置为 Sisense 的数据源，然后将数据可视化](/azure/data-explorer/sisense)。

## <a name="redash"></a>Redash

你可以使用[Redash](https://redash.io/)来生成仪表板和可视化数据。 [将 Azure 数据资源管理器设置为 Redash 的数据源，然后将数据可视化](/azure/data-explorer/redash)。