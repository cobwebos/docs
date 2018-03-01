---
title: "监视 Azure Cosmos DB 请求和存储 | Microsoft Docs"
description: "了解如何监视 Azure Cosmos DB 帐户的性能指标（如请求和服务器错误）以及使用情况指标（如存储消耗）。"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 4c6a2e6f-6e78-48e3-8dc6-f4498b235a9e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: mimig
ms.openlocfilehash: 0e9a47e6a1a5c7a44c5553742d6c5c81f8ca7286
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-azure-cosmos-db"></a>监视 Azure Cosmos DB
可以在 [Azure 门户](https://portal.azure.com/)中监视 Azure Cosmos DB 帐户。 对于每个 Azure Cosmos DB 帐户，一整套指标可用于监视吞吐量、存储、可用性、延迟和一致性。

可在“帐户”页、新的“指标”页或 Azure Monitor.中查看指标。

## <a name="view-performance-metrics-on-the-metrics-page"></a>在“指标”页上查看性能指标
1. 在 [Azure 门户](https://portal.azure.com/)中，单击“所有服务”，滚动到“数据库”，单击“Azure Cosmos DB”，然后单击要查看其性能指标的 Azure Cosmos DB 帐户的名称。
2. 新页加载时，在资源菜单的“监视”下，单击“指标”。
3. “指标”页打开时，从“集合”下拉列表中选择要查看的集合。

   Azure 门户显示了一套可用的集合指标。 请注意，吞吐量、存储、可用性、延迟和一致性指标在单独的选项卡上提供。 若要获取有关所提供指标的更多详细信息，请单击每个指标窗格右上方的双箭头。

   ![显示指标套件的“监视”可重用功能区的屏幕截图](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>使用 Azure Monitor 查看性能指标
1. 在 [Azure 门户](https://portal.azure.com/)中，单击左栏中的“监视”。
2. 在资源菜单中，单击“指标”。
3. 在“监视 - 指标”窗口的“资源组”下拉菜单中，选择与想要监视的 Azure Cosmos DB 帐户关联的资源组。 
4. 在“资源”下拉菜单中，选择要监视的数据库帐户。
5. 在“可用指标”列表中，选择要显示的指标。 使用 Ctrl 按钮进行多选。 

## <a name="view-performance-metrics-on-the-account-page"></a>在“帐户”页上查看性能指标
1. 在 [Azure 门户](https://portal.azure.com/)中，单击“所有服务”，滚动到“数据库”，单击“Azure Cosmos DB”，然后单击要查看其性能指标的 Azure Cosmos DB 帐户的名称。
2. 默认情况下，“监视”可重用功能区显示以下磁贴：
   
   * 当天的请求总数。
   * 使用的存储量。
   
   ![“监视”可重用功能区的屏幕截图，其中显示请求数和存储使用情况](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. 单击“请求”磁贴右上角的双箭头将打开详细的“指标”页。
4. “指标”页显示有关请求总数的详细信息。 

## <a name="set-up-alerts-in-the-portal"></a>在门户中设置警报
1. 在 [Azure 门户](https://portal.azure.com/)中，依次单击“所有服务”、“Azure Cosmos DB”，并单击要设置性能指标警报的 Azure Cosmos DB 帐户的名称。
2. 在资源菜单中，单击“警报规则”打开“警报规则”页。  
   ![所选的警报规则部件的屏幕截图](./media/monitor-accounts/madocdb10.5.png)
3. 在“警报规则”页中，单击“添加警报”。  
   ![“添加警报”按钮突出显示的“警报规则”页的屏幕截图](./media/monitor-accounts/madocdb11.png)
4. 在“添加警报规则”页中，指定：
   
   * 正在设置的警报规则的名称。
   * 新的警报规则的说明。
   * 警报规则指标。
   * 确定何时激活警报的条件、阈值和时间段。 例如，在过去的 15 分钟服务器错误计数大于 5。
   * 当警报被触发时，服务管理员和协同管理员是否将通过电子邮件得到通知。
   * 警报通知的其他电子邮件地址。  
     ![“添加警报规则”页的屏幕截图](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>以编程方式监视 Azure Cosmos DB
门户中提供的帐户级指标（如帐户存储使用率和总请求数）不可通过 SQL API 获得。 但是，可以使用 SQL API 在集合级别检索使用情况数据。 若要检索集合级别的数据，请执行以下操作：

* 若要使用 REST API，请[对集合执行 GET](https://msdn.microsoft.com/library/mt489073.aspx)。 集合的配额和使用情况信息将返回到响应中的 x-ms-resource-quota 和 x-ms-resource-usage 标头中。
* 要使用 .NET SDK，请使用 [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) 方法，它将返回 [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx)，其中包含大量使用情况属性，例如 **CollectionSizeUsage**、**DatabaseUsage**、**DocumentUsage** 等。

若要访问其他指标，请使用 [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)。 可以通过调用以下命令来检索可用的指标定义：

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

用于检索各个指标的查询使用以下格式：

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

有关详细信息，请参阅 [Retrieving Resource Metrics via the Azure Insights API](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/)（通过 Azure Monitor REST API 检索资源指标）。 请注意，已将“Azure Insights”重命名为“Azure Monitor”。  此博客条目引用的是旧名称。

## <a name="next-steps"></a>后续步骤
若要深入了解 Azure Cosmos DB 容量规划，请参阅 [Azure Cosmos DB Capacity Planner 计算器](https://www.documentdb.com/capacityplanner)。

