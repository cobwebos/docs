---
title: 使用 Power BI 可视化 SQL 数据仓库数据 | Microsoft Azure
description: 使用 Power BI 可视化 SQL 数据仓库数据
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 52581a87caac419a79caab647cc9c5a4ee7453ba
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
ms.locfileid: "31601570"
---
# <a name="visualize-data-with-power-bi"></a>使用 Power BI 可视化数据
本教程说明如何使用 Power BI 连接到 SQL 数据仓库，并创建一些基本的视觉效果。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>先决条件
要逐步完成本教程，需要：

* 已随 AdventureWorksDW 数据库预先加载的 SQL 数据仓库。 若要完成数据仓库预配，请参阅[创建 SQL 数据仓库](create-data-warehouse-portal.md)，并选择加载示例数据。 如果已有数据仓库但没有示例数据，可以[加载 WideWorldImportersDW](load-data-wideworldimportersdw.md)。

## <a name="1-connect-to-your-database"></a>1.连接到数据库
若要打开 Power BI 并连接到 AdventureWorksDW 数据库，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 单击“SQL 数据库”，并选择 AdventureWorks SQL 数据仓库数据库。
   
    ![查找数据库](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png)
3. 单击“在 Power BI 中打开”按钮。
   
    ![Power BI 按钮](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png)
4. 现在，应会看到显示了数据库 Web 地址的 SQL 数据仓库连接页。 单击“下一步”。
   
    ![Power BI 连接](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png)
5. 输入 Azure SQL Server 用户名和密码。
   
    ![Power BI 登录](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png)
6. 要打开数据库，单击左侧边栏选项卡上的 AdventureWorksDW 数据集。
   
    ![在 Power BI 中打开 AdventureWorksDW](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png)

## <a name="2-create-a-report"></a>2.创建报告
现在，可以使用 Power BI 来分析 AdventureWorksDW 示例数据。 为了执行分析，AdventureWorksDW 提供了一个名为 AggregateSales 的视图。 此视图包含用于分析公司销售情况的一些关键指标。

1. 要根据邮政编码创建销售金额图表，请在右侧字段窗格中，单击 AggregateSales 视图将它展开。 单击“PostalCode”和“SalesAmount”列以将其选中。
   
    ![Power BI 选择 AggregateSales](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png)
   
    Power BI 会自动识别地理数据，并将数据放入地图中。
   
    ![Power BI 图表](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png)

2. 此步骤将创建条形图，显示针对每个客户的销售收入金额。 若要创建条形图，请转到展开的 AggregateSales 视图。 单击“SalesAmount”字段。 将“客户收入”字段向左拖放到轴中。
   
    ![Power BI 选择轴](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png)
   
    条形图位于左侧。
   
    ![Power BI 条形图](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png)
3. 此步骤将创建一个折线图，显示每个订单日期的销售金额。 若要创建折线图，请转到展开的 AggregateSales 视图。 单击“SalesAmount”和“OrderDate”。 在“视觉效果”列中单击“折线图”图标，即视觉效果下第二行中的第一个图标。
   
    ![Power BI 选择折线图](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png)
   
    现在已创建了一份报告，其中显示了数据的三种不同视觉效果。
   
    ![Power BI 折线图](media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png)

随时可以通过单击“文件”，并选择“保存”来保存进度。

## <a name="using-direct-connnect"></a>使用直接连接
在 Azure SQL 数据库中，SQL 数据仓库直接连接可以使用逻辑下推，以及 Power BI 的分析功能。 使用直接连接，可在浏览数据时将查询实时发回 Azure SQL 数据仓库。  此项功能与 SQL 数据仓库的缩放性相结合，用户可在数分钟内针对 TB 量级的数据创建动态报表。 此外，“在 Power BI 中打开”按钮的引入可让用户直接将 Power BI 连接到其 SQL 数据仓库，而无需从其他 Azure 部分收集信息。

使用直接连接时，请注意：

* 在连接时指定完全限定的服务器名称。
* 确保数据库的防火墙规则配置为“允许访问 Azure 服务”。
* 每项操作（例如选择列或添加筛选器）都直接查询数据仓库。
* 大约每 15 分钟自动刷新磁贴。
* 问答不适用于直接连接数据集。
* 自动合并架构更改。
* 所有直接连接查询会在 2 分钟后超时。

随着体验的改善，这些限制和说明可能会更改。

## <a name="next-steps"></a>后续步骤
使用示例数据进行练习后，接下来请了解如何[开发](sql-data-warehouse-overview-develop.md)、[加载](design-elt-data-loading.md)或[迁移](sql-data-warehouse-overview-migrate.md)。 也可浏览 [Power BI 网站](http://www.powerbi.com/)。
