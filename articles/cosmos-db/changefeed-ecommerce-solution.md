---
title: 使用 Azure Cosmos DB 更改源将实时数据分析可视化 | Microsoft Docs
description: 本文介绍零售公司如何使用更改源来了解用户模式，以及执行实时数据分析和可视化。
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 08/12/2018
ms.author: sngun
ms.openlocfilehash: 21f63ebcca4b766f8df8c7b2390c4f44ff4dc7b1
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2018
ms.locfileid: "40099659"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>使用 Azure Cosmos DB 更改源将实时数据分析可视化

Azure Cosmos DB 更改源是在 Azure Cosmos DB 容器中创建或修改记录时，获取连续增量记录源的一种机制。 更改源支持的工作原理是侦听容器中发生的任何更改。 然后，它会按照所更改文档的修改顺序输出这些文档的排序列表。 若要详细了解更改源，请参阅[使用更改源](change-feed.md)一文。 

本文介绍零售公司如何使用更改源来了解用户模式，以及执行实时数据分析和可视化。 我们将分析各种事件，例如，用户查看某个商品、将商品添加到购物车，或购买商品。 发生其中一个事件时，将创建一个新记录，而更改源将会记录该记录。 然后，更改源触发一系列步骤，从而生成用于分析公司绩效和活动的指标的可视化效果。 可以可视化的示例指标包括收入、唯一的站点访客、最受欢迎的商品，以及查看的商品、添加到购物车的商品与购买的商品的平均价格。 这些示例指标可帮助电子商务公司评估其站点热门度、制定其广告和定价策略，并做出投资哪些存货的决策。

如有兴趣，请在开始之前观看有关该解决方案的以下视频：

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>解决方案组件
下图显示了该解决方案涉及的数据流和组件：

![项目视觉对象](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **数据生成：** 数据模拟器用于生成零售数据，这些数据表示各种事件，例如，用户查看某个商品、将商品添加到购物车，或购买商品。 可以使用数据生成器来生成大量的示例数据。 生成的示例数据包含采用以下格式的文档：
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB：** 生成的数据存储在 Azure Cosmos DB 集合中的。  

3. **更改源：** 更改源将侦听 Azure Cosmos DB 集合发生的更改。 每次将新文档添加到集合时（即，发生了用户查看商品、将商品添加到购物车或购买商品等事件时），更改源将触发一个 [Azure 函数](../azure-functions/functions-overview.md)。  

4. **Azure 函数：** Azure 函数处理新数据，并将其发送到 [Azure 事件中心](../event-hubs/event-hubs-about.md)。  

5. **事件中心：** Azure 事件中心存储这些事件，并将其发送到 [Azure 流分析](../stream-analytics/stream-analytics-introduction.md)以执行进一步的分析。  

6. **Azure 流分析：** Azure 流分析定义查询来处理事件和执行实时数据分析。 然后，将此数据发送到 [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop)。  

7. **Power BI：** Power BI 用于可视化 Azure 流分析发送的数据。 可以构建一个仪表板来实时了解指标的变化。  

## <a name="prerequisites"></a>先决条件

* Microsoft .NET Framework 4.7.1 或更高版本

* Microsoft .NET Core 2.1（或更高版本）

* 包含通用 Windows 平台开发、.NET 桌面开发以及 ASP.NET 和 Web 开发工作负荷的 Visual Studio

* Microsoft Azure 订阅

* Microsoft Power BI 帐户

* 从 GitHub 下载 [Azure Cosmos DB 更改源实验室](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample)。 

## <a name="create-azure-resources"></a>创建 Azure 资源 

创建 Azure 资源 - 解决方案所需的 Azure Cosmos DB、存储帐户、事件中心和流分析。 我们将通过 Azure 资源管理器模板部署这些资源。 使用以下步骤部署这些资源： 

1. 将 Windows PowerShell 执行策略设置为“不受限制”。 为此，请**以管理员身份打开 Windows PowerShell** 并运行以下命令：

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. 在上一步骤下载的 GitHub 存储库中，导航到“Azure 资源管理器”文件夹，并打开名为 **parameters.json** 的文件。  

3. 为 **parameters.json** 文件中指定的 cosmosdbaccount_name、eventhubnamespace_name 和 storageaccount_name 参数提供值。 稍后需要对每个资源使用这些名称。  

4. 在 **Windows PowerShell** 中，导航到“Azure 资源管理器”文件夹并运行以下命令：

   ```powershell
   .\deploy.ps1
   ```
5. 出现提示时，请输入 Azure **订阅 ID**，并输入 **changefeedlab** 作为资源组名称，输入 **run1** 作为部署名称。 资源开始部署后，最多可能需要 10 分钟才能完成部署。

## <a name="create-a-database-and-the-collection"></a>创建数据库和集合

现在，请创建一个集合用于保存电子商务站点事件。 当某个用户查看商品、将商品添加到购物车或购买商品时，该集合将会收到一条记录，其中包含操作（“已查看”、“已添加”或“已购买”）、涉及的商品名称、涉及的商品价格和涉及的用户购物车的 ID 编号。

1. 转到 [Azure 门户](http://portal.azure.com/)并找到模板部署创建的 **Azure Cosmos DB 帐户**。  

2. 在“数据资源管理器”窗格中，选择“新建集合”并在表单中填写以下详细信息：  

   * 对于“数据库 ID”字段，请选择“新建”，然后输入 **changefeedlabdatabase**。 将“预配数据库吞吐量”框保留未选中状态。  
   * 对于“集合 ID”字段，请输入 **changefeedlabcollection**。  
   * 对于“存储容量”，请选择“无限制”。  
   * 对于“分区键”字段，请输入 **/Item**。 此值区分大小写，因此请务必正确输入。  
   * 对于“吞吐量”字段，请输入 **10000**。  
   * 单击“确定”按钮。  

3. 接下来，创建名为 **leases** 的另一个集合用于处理更改源。 租约 (lease) 集合协调处理跨多个辅助角色的更改源。 单独集合用于存储租用，一个分区一个租用。  

4.  返回“数据资源管理器”窗格，选择“新建集合”并在表单中填写以下详细信息：

   * 对于“数据库 ID”字段，请选择“使用现有项”，然后输入 **changefeedlabdatabase**。  
   * 对于“集合 ID”字段，请输入 **leases**。  
   * 对于“存储容量”，请选择“固定”。  
   * 将“吞吐量”字段保留设置为默认值。  
   * 单击“确定”按钮。

## <a name="get-the-connection-string-and-keys"></a>获取连接字符串和密钥

### <a name="get-the-azure-cosmos-db-connection-string"></a>获取 Azure Cosmos DB 连接字符串

1. 转到 [Azure 门户](http://portal.azure.com/)并找到模板部署创建的 **Azure Cosmos DB 帐户**。  

2. 导航到“密钥”窗格，将“主连接字符串”复制到记事本，或复制到可在整个实验室中访问的另一个文档。 应将其标记为“Cosmos DB 连接字符串”。 稍后需要将此字符串复制到代码中，因此请将其记下，并记住其存储位置。

### <a name="get-the-storage-account-key-and-connection-string"></a>获取存储帐户密钥和连接字符串

Azure 存储帐户允许用户存储数据。 在此实验室中，我们将通过一个存储帐户来存储 Azure 函数使用的数据。 对集合进行任何修改时，会触发该 Azure 函数。

1. 返回到资源组，并打开前面创建的存储帐户  

2. 在左侧菜单中选择“访问密钥”。  

3. 将“密钥 1”下的值复制到记事本，或复制到可在整个实验室中访问的另一个文档。 应将“密钥”标记为“存储密钥”，并将“连接字符串”标记为“存储连接字符串”。 稍后需将这些字符串复制到代码中，因此请将其记下，并记住其存储位置。  

### <a name="get-the-event-hub-namespace-connection-string"></a>获取事件中心命名空间连接字符串

Azure 事件中心接收事件数据，并存储、处理和转发这些数据。 在此实验室中，每当发生新事件时（即，用户查看了商品、将商品添加到了购物车，或用户购买了商品），Azure 事件中心会收到一个文档，然后将该文档转发到 Azure 流分析。

1. 返回到资源组，打开前面在实验室中创建并命名的**事件中心命名空间**。  

2. 在左侧菜单中选择“共享访问策略”。  

3. 选择“RootManageSharedAccessKey”。 将“连接字符串 - 主密钥”复制到记事本，或复制到可在整个实验室中访问的另一个文档。 应将其标记为“事件中心命名空间”连接字符串。 稍后需要将此字符串复制到代码中，因此请将其记下，并记住其存储位置。

## <a name="set-up-azure-function-to-read-the-change-feed"></a>将 Azure 函数设置为读取更改源

创建新文档，或者在 Cosmos DB 集合中修改当前文档时，更改源会自动将修改后的文档添加到其集合更改历史记录中。 现在，我们生成并运行一个用于处理更改源的 Azure 函数。 在创建的集合中创建或修改文档时，更改源会触发该 Azure 函数。 然后，该 Azure 函数会将修改后的文档发送到事件中心。

1. 返回到在设备上克隆的存储库。  

2. 右键单击名为“ChangeFeedLabSolution.sln”的文件，并选择“使用 Visual Studio 打开”。  

3. 在 Visual Studio 中导航到“local.settings.json”。 然后，使用前面记下的值来填写空白位置。  

4. 导航到“ChangeFeedProcessor.cs”。 在 **Run** 函数的参数中执行以下操作：  

   * 将文本 **YOUR COLLECTION NAME HERE** 替换为集合的名称。 如果已遵照前面的说明，则集合的名称是 changefeedlabcollection。  
   * 将文本 **YOUR LEASES COLLECTION NAME HERE** 替换为租约集合的名称。 如果已遵照前面的说明，则租约集合的名称是 **leases**。  
   * 在 Visual Studio 的顶部，确保绿色箭头左侧的“启动项目”框中显示了“ChangeFeedFunction”。  
   * 在页面顶部选择“启动”以运行程序  
   * 如果控制台应用显示“作业宿主已启动”，即表示该函数正在运行。

## <a name="insert-data-into-azure-cosmos-db"></a>将数据插入 Azure Cosmos DB 

若要查看更改源如何处理电子商务站点上的新操作，必须模拟数据来表示用户正在查看产品目录中的商品、将这些商品添加到购物车，以及购买这些商品。 此数据是任意性的，其目的是再现电子商务站点上的数据的大致形式。

1. 导航回到文件资源管理器中的存储库，并右键单击“ChangeFeedFunction.sln”，在新的 Visual Studio 窗口中再次将其打开。  

2. 导航到“App.config”文件。在 <appSettings> 块中，添加前面检索到的 Azure Cosmos DB 帐户的 URI 和唯一“主密钥”。  

3. 添加**集合**和**数据库**的名称。 （除非你已选择不同的名称，否则这些名称应是 **changefeedlabcollection** 和 **changefeedlabdatabase**。）

   ![更新连接字符串](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. 保存对所有已编辑文件的更改。  

5. 在 Visual Studio 的顶部，确保绿色箭头左侧的“启动项目”框中显示了“DataGenerator”。 然后，在页面顶部选择“启动”以运行程序。  
 
6. 等待程序运行。 出现星星表示数据正在传入！ 让程序保持运行 - 必须收集大量的数据。  

7. 如果依次导航到 [Azure 门户](http://portal.azure.com/)、资源组中的 Cosmos DB 帐户、“数据资源管理器”，则会看到 **changefeedlabcollection** 中导入的随机化数据。
 
   ![门户中生成的数据](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>设置流分析作业

Azure 流分析是实时处理流数据的完全托管式云服务。 在此实验室中，我们将使用流分析来处理事件中心发来的新事件（即，查看了商品、将商品添加到了购物车，或购买了商品）、将这些事件合并到实时数据分析，然后将其发送到 Power BI 进行可视化。

1. 在 [Azure 门户](http://portal.azure.com/)中，依次导航到资源组和“streamjob1”（前面在实验室中创建的流分析作业）。  

2. 按如下所示选择“输入”。  

   ![创建输入](./media/changefeed-ecommerce-solution/create-input.png)

3. 选择“+ 添加流输入”。 然后，从下拉菜单中选择“事件中心”。  

4. 在“新建输入”表单中填写以下详细信息：

   * 在“输入别名”字段中，输入 **input**。  
   * 选择“从订阅选择事件中心”选项。  
   * 将“订阅”字段设置为自己的订阅。  
   * 在“事件中心命名空间”字段中，输入前面在实验室中创建的事件中心命名空间的名称。  
   * 在“事件中心名称”字段中，选择“使用现有项”选项，然后从下拉菜单中选择“event-hub1”。  
   * 将“事件中心策略名称”字段保留设置为默认值。  
   * 将“事件序列化格式”保留为“JSON”。  
   * 将“编码”字段保留设置为“UTF-8”。  
   * 将“事件压缩类型”字段保留设置为“无”。  
   * 单击“保存”按钮  。

5. 导航回到流分析作业页，并选择“输出”。  

6. 选择“+ 添加”。 然后，从下拉菜单中选择“Power BI”。  

7. 若要创建新的 Power BI 输出来可视化平均价格，请执行以下操作：

   * 在“输出别名”字段中，输入 **averagePriceOutput**。  
   * 将“组工作区”字段保留设置为“授权连接以加载工作区”。  
   * 在“数据集名称”字段中，输入 **averagePrice**。  
   * 在“表名称”字段中，输入 **averagePrice**。  
   * 单击“授权”按钮，然后遵照说明授权连接到 Power BI。  
   * 单击“保存”按钮  。  

8. 返回到“streamjob1”并单击“编辑查询”。

   ![编辑查询](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. 将以下查询粘贴到查询窗口中。 **AVERAGE PRICE** 查询计算用户查看的、添加到购物车的以及购买的所有商品的平均价格。 此指标可帮助电子商务公司确定商品的售价，以及要投资购买哪些存货。 例如，如果查看的商品的平均价格比购买的商品的平均价格要高得多，则公司可以选择将更廉价的商品添加到库存中。

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. 然后单击左上角的“保存”。  

11. 现在，请返回到“streamjob1”并单击页面顶部的“启动”按钮。 Azure 流分析可能需要几分钟才能启动，但最终其状态会从“正在启动”更改为“正在运行”。

## <a name="connect-to-power-bi"></a>连接到 Power BI

Power BI 是一套商业分析工具，可以分析数据和分享见解。 在这个典范性的服务中，我们能够策略性地将分析的数据可视化。

1. 登录到 Power BI，并通过打开页面左侧的菜单导航到“我的工作区”。  

2. 选择右上角的“+ 创建”，然后选择“仪表板”以创建仪表板。  

3. 选择右上角的“+ 添加磁贴”。  

4. 选择“自定义流数据”，然后单击“下一步”按钮。  
 
5. 从“你的数据集”中选择“averagePrice”，然后单击“下一步”。  

6. 在“可视化效果类型”字段中，从下拉菜单中选择“簇状条形图”。 在“轴”下面添加操作。 跳过“图例”，不要添加任何内容。 在名为“值”的下一个部分下面，添加“avg”。选择“下一步”，为图表指定标题，然后选择“应用”。 仪表板上应会出现一个新图表！  

7. 现在，若要可视化更多的指标，可以返回到“streamjob1”，并使用以下字段额外创建三个输出。

   a. **输出别名：** incomingRevenueOutput；数据集名称：incomingRevenue；表名称：incomingRevenue  
   b. **输出别名：** top5Output；数据集名称：top5；表名称：top5  
   c. **输出别名：** uniqueVisitorCountOutput；数据集名称：uniqueVisitorCount；表名称：uniqueVisitorCount

   然后单击“编辑查询”，并将以下查询粘贴到所编写的查询的**上面**。

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   TOP 5 查询计算按购买次数排名的前 5 个商品。 此指标可以帮助电子商务公司评估哪些商品最受欢迎，并可能影响公司的广告、定价和库存决策。

   REVENUE 查询计算收入：将每分钟购买的所有商品的价格累加。 此指标可以帮助电子商务公司评估其财务绩效，同时了解一天中的哪些时间贡献了最大的收入。 这可能会影响整个公司战略，特别是市场营销。

   UNIQUE VISITORS 查询每隔 5 秒通过检测唯一的购物车 ID，来计算站点上的唯一访客数目。 此指标可帮助电子商务公司评估其站点活动，并制定吸引客户的战略。

8. 现在，也可以添加这些数据集的磁贴。

   * 对于 Top 5 查询，最好是绘制簇状柱形图，其中以商品为轴，以计数为值。  
   * 对于 Revenue 查询，最好是绘制折线图，其中以时间为轴，以价格总和为值。 显示的时间窗口应尽可能大，以便尽量提供最多的信息。  
   * 对于 Unique Visitors 查询，最好是绘制卡片可视化效果，其中以唯一访客数为值。

   包含这些图表的仪表板如下所示：

   ![可视化效果](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>可选：在电子商务站点中进行可视化

现在，我们知道可以如何使用新的数据分析工具来连接实际的电子商务站点。 若要构建电子商务站点，请使用 Azure Cosmos DB 数据库来存储产品类别（女用、男用、不分性别）列表、产品目录和最受欢迎商品的列表。

1. 依次导航到 [Azure 门户](http://portal.azure.com/)、**Cosmos DB 帐户**、“数据资源管理器”。  

   在“changefeedlabdatabase  -  产品和类别”下添加具有“固定”存储容量的两个集合。

   在“changefeedlabdatabase”下添加名为 **topItems**、具有“无限制”存储容量的另一个集合。 写入 **/Item** 作为分区键。

2. 单击“topItems”集合，然后在“规模和设置”下，将“生存时间”设置为“30 秒”，使 topItems 每隔 30 秒更新一次。

   ![生存时间](./media/changefeed-ecommerce-solution/time-to-live.png)

3. 若要在 **topItems** 集合中填充最经常购买的商品，请导航回到“streamjob1”，并添加新的**输出**。 选择“Cosmos DB”。

4. 按下图所示填写必填字段。

   ![Cosmos 输出](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. 如果在实验室的前一部分中添加了可选的 TOP 5 查询，请转到第 5a 部分。 否则，请转到第 5b 部分。

   5a. 在“streamjob1”中选择“编辑查询”，并将以下查询粘贴到 Azure 流分析查询编辑器中 TOP 5 查询的下面、剩余查询的上面。

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. 在“streamjob1”中选择“编辑查询”，并将以下查询粘贴到 Azure 流分析查询编辑器中其他所有查询的上面。

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. 在“解决方案资源管理器”中打开“EcommerceWebApp.sln”，并导航到“Web.config”文件。  

7. 在 `<appSettings>` 块中，将前面保存的“URI”和“主密钥”分别添加到带有“在此处提供 URI”和“在此处提供主密钥”字样的字段中。 然后，根据指示添加**数据库名称**和**集合名称**。 （除非你已选择不同的名称，否则这些名称应是 **changefeedlabdatabase** 和 **changefeedlabcollection**。）

   根据指示填写**产品集合名称**、**类别集合名称**和**排名靠前的商品集合名称**。 （除非你已选择不同的名称，否则这些名称应是 **products、categories 和 topItems**。）  

8. 导航到 **EcommerceWebApp.sln** 中的 **Checkout 文件夹**并将其打开。 然后打开该文件夹中的 **Web.config** 文件。  

9. 在 `<appSettings>` 块中，根据指示添加前面保存的“URI”和“主密钥”。 然后，根据指示添加**数据库名称**和**集合名称**。 （除非你已选择不同的名称，否则这些名称应是 **changefeedlabdatabase** 和 **changefeedlabcollection**。）  

10. 在页面顶部按“启动”以运行程序。  

11. 现在，可以任意体验该电子商务站点。 查看商品、将商品添加到购物车、更改购物车中的商品数量或购买商品时，这些事件将通过 Cosmos DB 更改源依次传递到事件中心、ASA 和 Power BI。 我们建议继续运行数据生成器以生成大量的 Web 流量数据，并在电子商务站点上提供一组实际的“热门产品”。

## <a name="delete-the-resources"></a>删除资源

若要删除在此实验室中创建的资源，请在 [Azure 门户](http://portal.azure.com/)中导航到资源组，从页面顶部的菜单中选择“删除资源组”，然后遵照提供的说明操作。

## <a name="next-steps"></a>后续步骤 
  
* 若要详细了解更改源，请参阅[使用 Azure Cosmos DB 中的更改源支持](change-feed.md)。 
* 面向使用 Azure Cosmos DB 的医疗保健组织的[更改源通知解决方案](change-feed-hl7-fhir-logic-apps.md)。
