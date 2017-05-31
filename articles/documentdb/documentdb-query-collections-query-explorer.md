---
title: "Azure Cosmos DB 门户工具：查询资源管理器 | Microsoft Docs"
description: "了解 Azure Cosmos DB 查询资源管理器，它是 Azure 门户中的 SQL 查询编辑器，用于编写 SQL 查询，并针对 Azure Cosmos DB 集合运行这些查询。"
keywords: "编写 SQL 查询, SQL 查询编辑器"
services: cosmosdb
author: kirillg
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: ac378240-b11f-4522-ae9f-09da3a6f9c16
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: kirillg
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 77389c6d0779a08b8d717076fc5678d3d7fb3b0c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="write-edit-and-run-sql-queries-for-azure-cosmos-db-using-query-explorer-in-the-azure-portal"></a>在 Azure 门户中使用查询资源管理器为 Azure Cosmos DB 编写、编辑和运行 SQL 查询
本文概述了 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 查询资源管理器，该资源管理器是一个 Azure 门户工具，用于针对 [DocumentDB](documentdb-create-collection.md) 集合编写、编辑和运行 SQL 查询。

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航栏中，单击 ![Azure Cosmos DB 图标](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png)“Azure Cosmos DB”。 

    如果未显示 Azure Cosmos DB，单击底部的“更多服务”，然后单击 ![Azure Cosmos DB 图标](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png)“Azure Cosmos DB”。
2. 在资源菜单中，单击“查询资源管理器”。 
   
    ![Azure 门户的屏幕截图，其中突出显示了查询资源管理器](./media/documentdb-query-collections-query-explorer/queryexplorercommand.png)
3. 在“查询资源管理器”边栏选项卡中，从下拉列表中选择要查询的**数据库**和**集合**，然后键入要运行的查询。 
   
    “数据库”和“集合”下拉列表会根据启动查询资源管理器的上下文进行预填充。 
   
    提供了 `SELECT TOP 100 * FROM c` 的默认查询。  可以接受默认查询，也可以使用 [SQL 查询速查表](documentdb-sql-query-cheat-sheet.md)或 [SQL 查询和 SQL 语法](documentdb-sql-query.md)文章中所述的 SQL 查询语言构造自己的查询。
   
    单击“运行查询”查看结果。
   
    ![在查询资源管理器（一个 SQL 查询编辑器）中编写 SQL 查询的屏幕截图](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)
4. “结果”边栏选项卡将显示查询的输出。 
   
    ![在查询资源管理器中编写 SQL 查询的结果的屏幕截图](./media/documentdb-query-collections-query-explorer/queryresults1.png)

## <a name="work-with-results"></a>处理结果
默认情况下，查询资源管理器会返回 100 条一组的结果。  如果查询生成了 100 多条结果，只需使用“下一页”和“上一页”命令即可浏览结果集。

![查询资源管理器分页支持的屏幕截图](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

对于成功的查询，“信息”窗格将包含度量值，如请求费用、查询进行的往返数、当前显示的结果集，以及是否有更多结果（如上文所述，可通过“下一页”命令进行访问）。

![查询资源管理器查询信息的屏幕截图](./media/documentdb-query-collections-query-explorer/queryinformation.png)

## <a name="use-multiple-queries"></a>使用多个查询
如果正在使用多个查询，并且想要在它们之间快速切换，则可以在“查询资源管理器”边栏选项卡的查询文本框中输入所有查询，然后突出显示想要运行的查询，再单击“运行查询”以查看结果。

![在查询资源管理器（一个 SQL 查询编辑器）中编写多个 SQL 查询并突出显示和运行单个查询的屏幕截图](./media/documentdb-query-collections-query-explorer/queryexplorerhighlightandrun.png)

## <a name="add-queries-from-a-file-into-the-sql-query-editor"></a>将查询从文件添加到 SQL 查询编辑器
可以使用“加载文件”命令加载现有文件的内容。

![显示如何使用“加载文件”将 SQL 查询从文件加载到查询资源管理器的屏幕截图](./media/documentdb-query-collections-query-explorer/loadqueryfile.png)

## <a name="troubleshoot"></a>故障排除
如果查询完成但有错误，查询资源管理器将显示可以帮助进行故障排除工作的错误列表。

![查询资源管理器查询错误的屏幕截图](./media/documentdb-query-collections-query-explorer/queryerror.png)

## <a name="run-documentdb-api-sql-queries-outside-the-portal"></a>运行门户外部的 DocumentDB API SQL 查询
Azure 门户中的查询资源管理器只是一种对 Cosmos DB 运行 SQL 查询的方式。 还可以使用 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 或[客户端 SDK](documentdb-sdk-dotnet.md) 运行 SQL 查询。 有关使用这些其他方法的详细信息，请参阅[执行 SQL 查询](documentdb-sql-query.md#ExecutingSqlQueries)

## <a name="next-steps"></a>后续步骤
有关查询资源管理器中支持的 DocumentDB API SQL 语法的详细信息，请参阅 [SQL 查询和 SQL 语法](documentdb-sql-query.md)一文或打印 [SQL 查询速查表](documentdb-sql-query-cheat-sheet.md)。
还可以尝试使用[查询板块](https://www.documentdb.com/sql/demo)，在其中可以使用示例数据集联机测试查询。


