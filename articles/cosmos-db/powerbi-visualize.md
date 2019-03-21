---
title: Azure Cosmos DB 连接器的 Power BI 教程
description: 使用此 Power BI 教程来导入 JSON、创建见解深入的报表以及使用 Azure Cosmos DB 和 Power BI 连接器来可视化数据。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: sngun
ms.openlocfilehash: 2c58b982e596c95aa47442c1897410fe9ab6b99a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137668"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>使用 Power BI 连接器可视化 Azure Cosmos DB 数据

[Power BI](https://powerbi.microsoft.com/) 是一种在线服务，你可以使用它创建和共享仪表板和报表。 Power BI Desktop 是一种报表创作工具，可以从各种数据源检索数据。 Azure Cosmos DB 是可以与 Power BI Desktop 一起使用的一种数据源。 可以使用 Power BI 的 Azure Cosmos DB 连接器将 Power BI Desktop 连接到 Azure Cosmos DB 帐户。  将 Azure Cosmos DB 数据导入到 Power BI 之后，可以对其进行转换，创建报表，以及将报表发布到 Power BI。   

本文介绍了将 Azure Cosmos DB 帐户连接到 Power BI Desktop 所需执行的步骤。 在连接后，可以导航到某个集合，提取数据，将 JSON 数据转换为表格格式，以及将报表发布到 Power BI。

> [!NOTE]
> Azure Cosmos DB 的 Power BI 连接器连接到 Power BI Desktop。 在 Power BI Desktop 中创建的报表可以发布到 PowerBI.com。 无法从 PowerBI.com 执行 Azure Cosmos DB 数据的直接提取。 

> [!NOTE]
> 目前，仅 Azure Cosmos DB SQL API 和 Gremlin API 帐户支持使用 Power BI 连接器连接 Azure Cosmos DB。

## <a name="prerequisites"></a>必备组件
在按照此 Power BI 教程中的说明操作之前，请确保已拥有对以下资源的访问权限：

* [下载最新版本的 Power BI Desktop](https://powerbi.microsoft.com/desktop)。

* 从 GitHub 下载[示例火山数据](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json)。

* [创建一个 Azure Cosmos DB 数据库帐户](https://azure.microsoft.com/documentation/articles/create-account/)并使用 [Azure Cosmos DB 数据迁移工具](import-data.md)导入火山数据。 导入数据时，请考虑数据迁移工具中源和目标的以下设置：

   * **源参数** 

       * **导入源：** JSON 文件

   * **目标参数** 

      * **连接字符串：**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **分区键：**/Country 

      * **集合吞吐量：** 1000 

要在 PowerBI.com 中共享报表，必须在 PowerBI.com 中拥有帐户。  若要了解有关 Power BI 和 Power BI Pro 的详细信息，请参阅 [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing)。

## <a name="lets-get-started"></a>让我们开始吧
在本教程中，假设你是一位研究世界各地火山的地理学家。 火山数据存储在一个 Azure Cosmos DB 帐户中，JSON 文档格式如下所示：

    {
        "Volcano Name": "Rainier",
           "Country": "United States",
          "Region": "US-Washington",
          "Location": {
            "type": "Point",
            "coordinates": [
              -121.758,
              46.87
            ]
          },
          "Elevation": 4392,
          "Type": "Stratovolcano",
          "Status": "Dendrochronology",
          "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
    }

你将从该 Azure Cosmos DB 帐户中检索火山数据并在交互式 Power BI 报表中将数据可视化。

1. 运行 Power BI Desktop。

2. 可以“获取数据”、查看“最近使用的源”或直接从“欢迎”屏幕“打开其他报表”。 单击右上角的“X”以关闭屏幕。 将显示 Power BI Desktop 的“报表”视图。
   
   ![Power BI Desktop 报表视图 - Power BI 连接器](./media/powerbi-visualize/power_bi_connector_pbireportview.png)

3. 选择“主页”功能区，并单击“获取数据”。  应出现“获取数据”窗口。

4. 单击“Azure”，选择“Azure Cosmos DB (Beta 版本)”，并单击“连接”。 

    ![Power BI Desktop 获取数据 - Power BI 连接器](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   

5. 在“预览连接器”页中，单击“继续”。 此时会显示“Azure Cosmos DB”窗口。

6. 如下所示指定想要从其中检索数据的 Azure Cosmos DB 帐户终结点 URL，并单击“确定”。 要使用自己的帐户，可以从 Azure 门户的“密钥”边栏选项卡的 URI 框中取得 URL。 可选：你可以提供数据库名称、集合名称，也可以使用导航器来选择数据库和集合以标识数据来源。
   
7. 如果是首次连接到此终结点，则会提示输入帐户密钥。 若使用自己的帐户，请从 Azure 门户的“只读密钥”边栏选项卡中的“主密钥”框中取得密钥。 输入相应的密钥，然后单击“连接”。
   
   我们建议在生成报表时使用只读密钥。 这可防止主密钥不必要地暴露于潜在的安全风险中。 可以从 Azure 门户的**密钥**边栏选项卡获取只读密钥。 
    
8. 帐户成功连接后，将出现“导航器”窗格。 “导航器”会在帐户下显示数据库的列表。

9. 单击并展开作为报表数据来源的数据库，并选择 **volcanodb**（你的数据库名称可能不同）。   

10. 现在，选择包含要检索的数据的集合，选择 **volcano1**（你的集合名称可能不同）。
    
    预览窗格显示“记录”项的列表。  文档在 Power BI 中表示为一种“记录”类型。 同样，文档内部的嵌套 JSON 块也是“记录”。
    
    ![Azure Cosmos DB Power BI 连接器的 Power BI 教程 - 导航器窗口](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. 单击“编辑”在新窗口中启动查询编辑器，以转换数据。

## <a name="flattening-and-transforming-json-documents"></a>平展和转换 JSON 文档
1. 切换到“Power BI 查询编辑器”窗口，其中“文档”列位于中心窗格。
   ![Power BI Desktop 查询编辑器](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. 单击“文档”列标头右侧的扩展器。  将显示带有字段列表的上下文菜单。  选择报表所需的字段，例如，火山名称、国家/地区、区域、位置、海拔、类型、状态和已知的上次喷发。 取消选中“使用原始列名作为前缀”框，然后单击“确定”。
   
    ![Azure Cosmos DB Power BI 连接器的 Power BI 教程 - 扩展文档](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. 中央窗格显示其中包含所选字段的结果预览。
   
    ![Azure Cosmos DB Power BI 连接器的 Power BI 教程 - 平铺结果](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. 在我们的示例中，“位置”属性在文档中为 GeoJSON 块。  正如你所看到的那样，“位置”在 Power BI Desktop 中表示为一种“记录”类型。  
5. 单击 Document.Location 列标题右侧的扩展器。  此时会显示包含类型和坐标字段的上下文菜单。  让我们选择坐标字段，确保未选中“使用原始列名作为前缀”，然后单击“确定”。
   
    ![Azure Cosmos DB Power BI 连接器的 Power BI 教程 - 位置记录](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. 中心窗格现在显示“列表”类型的坐标列。  如教程的开头所示，本教程中的 GeoJSON 数据是“点”类型，纬度值和经度值记录在坐标数组中。
   
    coordinates[0] 元素表示经度，coordinates[1] 表示纬度。
    ![Azure Cosmos DB Power BI 连接器的 Power BI 教程 - 坐标列表](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. 若要平展坐标数组，请创建一个名为 LatLong 的**自定义列**。  选择“添加列”功能区并单击“自定义列”。  此时将显示“自定义列”窗口。
8. 为新列提供一个名称，例如经纬度。
9. 接下来，为新列指定自定义公式。  对于我们的示例，我们将如下所示，使用以下公式连接逗号分隔的纬度值和经度值：`Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`。 单击“确定”。
   
    在数据分析表达式 (DAX) 包括 DAX 函数的详细信息，请访问[Power BI Desktop 中的 DAX 基础知识](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics)。
   
    ![Azure Cosmos DB Power BI 连接器的 Power BI 教程 - 添加自定义列](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. 现在，中央窗格将显示填充了值的新 LatLong 列。
    
    ![Azure Cosmos DB Power BI 连接器的 Power BI 教程 - 自定义经纬度列](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    如果新的列中出现“错误”，请确保“查询设置”中应用的步骤与下图匹配：
    
    ![应用的步骤应该为“源”、“导航”、“展开的文档”、“展开的文档位置”、“添加自定义”](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    如果步骤不同，请删除额外的步骤并再次尝试添加自定义列。 

11. 单击“关闭并应用”以保存数据模型。
    
    ![Azure Cosmos DB Power BI 连接器的 Power BI 教程 - 关闭和应用](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>生成报表
可以在 Power BI Desktop 报表视图中开始创建报表以可视化数据。  可以通过将字段拖放到“报表”画布中来创建报表。

![Power BI Desktop 报表视图 - Power BI 连接器](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

在报表视图中，应找到：

1. 在“字段”窗格中，可以看到包含可用于报表的字段的数据模型的列表。
2. “可视化”窗格。 一个报表可以包含单个或多个可视化效果。  从“可视化”窗格中选取所需视觉对象类型。
3. 在“报表”画布中，可以为报表生成视觉效果。
4. “报表”页。 可以在 Power BI Desktop 中添加多个报表页。

下面将演示创建简单交互地图视图报表的基本步骤。

1. 对于我们的示例，我们创建显示每座火山的位置的地图视图。  在“可视化”窗格中，单击如上屏幕截图中突出显示的“地图”视觉对象类型。  应该会看到绘制在“报表”画布上的地图视觉对象类型。  “可视化”窗格也应该显示一组与地图视觉对象类型相关的属性。
2. 现在，从“字段”窗格将经纬度字段拖放到“可视化”窗格中的“位置”属性。
3. 接下来，将“火山名称”字段拖放到“图例”属性。  
4. 然后，将“海拔”字段拖放到“大小”属性。  
5. 现在应会看到显示一组表示每座火山位置的气泡的地图视觉对象，气泡的大小与火山的海拔相关联。
6. 此时已创建了基本报表。  可以通过添加更多可视化效果来进一步自定义该报表。  在本例中，我们添加了火山类型切片器以使报表具有交互性。  
   
7. 在“文件”菜单中，单击“保存”并将该文件保存为 PowerBITutorial.pbix。

## <a name="publish-and-share-your-report"></a>发布和共享报表
要共享报表，必须在 PowerBI.com 中拥有帐户。

1. 在 Power BI Desktop 中，单击“主页”功能区。
2. 单击“发布” 。  系统会提示输入 PowerBI.com 帐户的用户名和密码。
3. 一旦验证凭据后，就可将报表发布到所选目的地。
4. 单击“打开 Power BI 中的‘PowerBITutorial.pbix’”，查看并共享 Power BI.com 上的报表。
   
    ![成功发布到 Power BI！ 在 Power BI 中打开教程](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>在 PowerBI.com 中创建仪表板
现在，已创建一份可在 PowerBI.com 上共享的报表

从 Power BI Desktop 发布报表到 PowerBI.com 时，会在 PowerBI.com 租户中生成一个“报表”和“数据集”。 例如，将一个名为 **PowerBITutorial** 的报表发布到 PowerBI.com 后，PowerBITutorial 会出现在 PowerBI.com 的“报表”和“数据集”部分。

   ![PowerBI.com 中新“报表”和“数据集”的屏幕截图](./media/powerbi-visualize/powerbi-reports-datasets.png)

若要创建可共享的仪表板，请单击 PowerBI 报表上的“固定活动页”按钮。

   ![PowerBI.com 中新“报表”和“数据集”的屏幕截图](./media/powerbi-visualize/power-bi-pin-live-tile.png)

然后，根据 [Pin a tile from a report](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report)（从报表固定磁贴）中的说明创建新仪表板。 

创建仪表板之前也可以临时修改报表。 但是，建议使用 Power BI Desktop 执行修改并将报表重新发布到 PowerBI.com。

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>后续步骤
* 有关 Power BI 的详细信息，请参阅 [Power BI 入门](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)。
* 若要了解有关 Azure Cosmos DB 的详细信息，请参阅 [Azure Cosmos DB 文档登陆页](https://azure.microsoft.com/documentation/services/cosmos-db/)。

