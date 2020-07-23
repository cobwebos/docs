---
title: 教程：Azure Synapse Analytics 入门
description: 本教程将介绍设置和使用 Azure Synapse Analytics 的基本步骤。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: daa8b594b06203c7de9a9b462be469dd71ed2e49
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791854"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Azure Synapse Analytics 入门

本教程提供有关设置和使用 Azure Synapse Analytics 的基本步骤的导览。

## <a name="prepare-a-storage-account"></a>准备存储帐户

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 创建具有以下设置的新存储帐户：

    |选项卡|设置 | 建议的值 | 说明 |
    |---|---|---|---|
    |基础|**存储帐户名称**| 可为其指定任意名称。|在本文档中，我们将其称为 contosolake。|
    |基础|**帐户种类**|必须设置为“StorageV2”。||
    |基础|**位置**|选择任何位置。| 建议 Azure Synapse Analytics 工作区和 Azure Data Lake Storage Gen2 帐户位于同一区域。|
    |高级|**Data Lake Storage Gen2**|**已启用**| Azure Synapse 仅适用于启用了此设置的存储帐户。|
    |||||

1. 创建存储帐户后，在左窗格中选择“访问控制 (IAM)”。 然后，分配以下角色或确保其已经分配：
    1. 为自己分配“所有者”角色。
    1. 为自己分配“存储 Blob 数据所有者”角色。
1. 在左窗格中选择“容器”并创建容器。
1. 可以为此容器指定任何名称。 在本文档中，我们将此容器命名为“users”。
1. 接受默认设置“公共访问级别”，然后选择“创建” 。

在下面的步骤中，你将配置 Azure Synapse 工作区以使用此存储帐户作为“主要”存储帐户，并使用容器来存储工作区数据。 工作区将数据存储在 Apache Spark 表中。 它将 Spark 应用程序日志存储在名为 /synapse/workspacename 的文件夹下。

## <a name="create-a-synapse-workspace"></a>创建 Synapse 工作区

1. 打开 [Azure 门户](https://portal.azure.com)，然后在顶部搜索“Synapse”。
1. 在“服务”下的搜索结果中，选择“Azure Synapse Analytics (工作区预览版)” 。
1. 选择“添加”以使用这些设置来创建工作区：

    |选项卡|设置 | 建议的值 | 说明 |
    |---|---|---|---|
    |基础|**工作区名称**|可以随意为其命名。| 在本文档中，我们将使用 myworkspace。|
    |基础|**区域**|匹配存储帐户的区域。|

1. 在“选择 Data Lake Storage Gen 2”下，选择之前创建的帐户和容器。
1. 选择“查看 + 创建” > “创建”。 你的工作区将在几分钟内准备就绪。

## <a name="verify-access-to-the-storage-account"></a>验证对存储帐户的访问权限

Azure Synapse 工作区的托管标识可能已具有对存储帐户的访问权限。 请执行以下步骤，以确保：

1. 打开 [Azure 门户](https://portal.azure.com)和为工作区所选的主存储帐户。
1. 从左窗格中选择“访问控制(标识和访问管理)”。
1. 分配以下角色或确保其已经分配。 我们对工作区标识和工作区使用相同的名称。
    1. 对于存储帐户上的“存储 Blob 数据参与者”角色，请将 myworkspace 指定为工作区标识 。
    1. 将 myworkspace 指定为工作区名称。

1. 选择“保存”。

## <a name="open-synapse-studio"></a>打开 Synapse Studio

创建 Azure Synapse 工作区后，可以通过两种方式打开 Synapse Studio：

* 在 [Azure 门户](https://portal.azure.com)中打开 Synapse 工作区。 在“概述”部分的顶部，选择“启动 Synapse Studio” 。
* 转到 https://web.azuresynapse.net ，然后登录到工作区。

## <a name="create-a-sql-pool"></a>创建 SQL 池

1. 在 Synapse Studio 的左窗格中，选择“管理” > “SQL 池” 。
1. 选择“新建”，然后输入以下设置：

    |设置 | 建议的值 | 
    |---|---|---|
    |**SQL 池名称**| **SQLDB1**|
    |**性能级别**|**DW100C**|
    |||

1. 选择“查看 + 创建” > “创建”。 你的 SQL 池将在几分钟内准备就绪。 你的 SQL 池与另一个名称也为 SQLDB1 的 SQL 池数据库相关联。

只要 SQL 池处于活动状态，就会使用计费资源。 你可以在稍后暂停池以降低成本。

## <a name="create-an-apache-spark-pool"></a>创建 Apache Spark 池

1. 在 Synapse Studio 的左侧窗格中，选择“管理” > “Apache Spark 池” 。
1. 选择“新建”，然后输入以下设置：

    |设置 | 建议的值 | 
    |---|---|---|
    |**Apache Spark 池名称**|**Spark1**
    |**节点大小**| **小型**|
    |**节点数**| 将最小值设置为 3，最大值设置为 3|

1. 选择“查看 + 创建” > “创建”。 你的 Apache Spark 池将在几秒钟内准备就绪。

> [!NOTE]
> 尽管名称类似，但 Apache Spark 池与 SQL 池并不相同。 这只是一些基本元数据，用于告知 Synapse 工作区如何与 Spark 交互。

由于 Spark 池是元数据，因此无法启动或停止。

在 Azure Synapse 中执行 Spark 活动时，请指定要使用的 Spark 池。 该池告知 Azure Synapse 要使用的 Spark 资源数量。 只需支付所用资源的费用。 主动停止使用该池时，资源将自动超时并进行回收。

> [!NOTE]
> Spark 数据库是独立于 Spark 池创建的。 工作区始终具有名为“default”的 Spark 数据库。 可以创建其他 Spark 数据库。

## <a name="the-sql-on-demand-pool"></a>SQL 按需版本池

每个工作区都具有名为“SQL on-demand”的预构建池。 无法删除该池。 使用 SQL 按需版本池即可使用 SQL，无需创建或考虑如何在 Azure Synapse 中管理 SQL 池。

与其他类型的池不同，SQL 按需版本的计费基于运行查询所扫描的数据量，而不是执行查询所使用的资源数。

* SQL 按需版本具有自己的 SQL 按需版本数据库，这些数据库独立于任何 SQL 按需版本池而存在。
* 工作区始终只包含一个名为“SQL 按需版本”的 SQL 按需版本池。

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>将纽约市出租车示例数据加载到 SQLDB1 数据库

1. 在 Synapse Studio 最顶部的蓝色菜单中，选择“?” 图标。
1. 选择“入门” > “入门中心” 。
1. 在标有“查询示例数据”的卡中，选择名为 SQLDB1 的 SQL 池 。
1. 选择“查询数据”。 “正在加载示例数据”通知将短暂出现。 Synapse Studio 的顶部附近的淡蓝色状态栏指示正在将数据加载到 SQLDB1 中。
1. 在状态栏变为绿色后，请将其关闭。

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>浏览 SQL 池中的纽约市出租车数据

1. 在 Synapse Studio 中，转到“数据”中心。
1. 转到“SQLDB1” > “表” 。 你将看到几个已加载的表。
1. 右键单击 dbo.Trip 表，然后选择“新建 SQL 脚本” > “选择前 100 行”  。
1. 等待新的 SQL 脚本创建并运行。
1. 请注意，在 SQL 脚本的顶部，“连接到”自动设置为名为“SQLDB1”的 SQL 池 。
1. 将 SQL 脚本的文本替换为此代码并运行。

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    此查询显示总行程距离和平均行程距离与乘客数之间的关系。
1. 在“SQL 脚本结果”窗口中，将“视图”更改为“图表”，从而以折线图形式查看结果的可视化效果 。

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>将纽约市出租车数据加载到 Spark nyctaxi 数据库

SQLDB1 的表中有可用数据。 将其加载到名为 nyctaxi 的 Spark 数据库。

1. 在 Synapse Studio 中，转到“开发”中心。
1. 选择 + > “笔记本” 。
1. 在笔记本顶部，将“附加到”值设置为 Spark1 。
1. 选择“添加代码”以添加笔记本代码单元，并粘贴以下文本：

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. 转到“数据”中心，右键单击“数据库”，然后选择“刷新”  。 应看到以下数据库：

    - SQLDB1（SQL 池）
    - nyctaxi (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>使用 Spark 和笔记本分析纽约市出租车数据

1. 返回到笔记本。
1. 创建新代码单元并输入以下文本。 然后运行该单元以显示我们加载到 nyctaxi Spark 数据库中的纽约市出租车数据。

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 运行以下代码，执行之前在 SQL 池 SQLDB1 中所做的分析。 此代码将分析结果另存到名为 nyctaxi.passengercountstats 的表，然后可视化结果。

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. 在单元结果中，选择“图表”以查看直观呈现出来的数据。

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>使用 Spark 和笔记本自定义数据可视化效果

使用笔记本可控制图表的呈现方式。 下面的代码显示了一个简单的示例。 它使用常用库 matplotlib 和 seaborn 。 该代码将呈现在之前运行 SQL 查询时所显示的同类型折线图。

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```

## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>将 Spark 表中的数据加载到 SQL 池表

之前我们将数据从 SQL 池表 SQLDB1.dbo.Trip 复制到 Spark 表 nyctaxi.trip 中 。 然后，我们使用 Spark 将数据聚合到了 Spark 表 nyctaxi.passengercountstats。 现在，我们将数据从 nyctaxi.passengercountstats 复制到名为 SQLDB1.dbo.PassengerCountStats 的 SQL 池表中 。

在笔记本中运行以下单元。 它将聚合的 Spark 表复制回 SQL 池表。

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>使用 SQL 按需版本分析 Spark 数据库中的纽约市出租车数据

Spark 数据库中的表会自动显示，且 SQL on-demand 可以查询这些表。

1. 在 Synapse Studio 中，转到“开发”中心，然后新建 SQL 脚本。
1. 将“连接到”设置为“SQL 按需版本” 。
1. 将以下文本粘贴到脚本中，然后运行该脚本。

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > 第一次运行使用 SQL 按需版本的查询时，SQL 按需版本需要大约 10 秒的时间来收集运行查询所需的 SQL 资源。 后续查询的速度要快得多。
  
## <a name="orchestrate-activities-with-pipelines"></a>使用管道安排活动

可以在 Azure Synapse 中安排各种任务。

1. 在 Synapse Studio 中，转到“安排”中心。
1. 选择 + > “管道”以创建新管道 。
1. 转到“开发”中心并找到之前创建的笔记本。
1. 将该笔记本拖动到管道。
1. 在管道中，选择“添加触发器” > “新建/编辑” 。
1. 在“选择触发器”中，选择“新建”，然后在“定期”中，将触发器设置为每 1 小时运行一次  。
1. 选择“确定”。
1. 选择“全部发布”。 管道每小时运行一次。
1. 若要管道立即运行而不等待一个小时，请选择“添加触发器” > “新建/编辑” 。

## <a name="work-with-data-in-a-storage-account"></a>使用存储帐户中的数据

到目前为止，我们介绍了数据驻留在工作区的数据库中的各种情况。 现在，我们将介绍如何使用存储帐户中的文件。 对于这一情况，我们将使用在创建工作区时所指定的工作区和容器的主存储帐户。

* 存储帐户的名称为：contosolake
* 存储帐户中的容器的名称：users

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>在存储帐户中创建 CSV 和 Parquet 文件

在笔记本中运行以下代码。 这会在存储帐户中创建 CSV 文件和 parquet 文件。

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>分析存储帐户中的数据

1. 在 Synapse Studio 中，转到“数据”中心，然后选择“已关联” 。
1. 转到“存储帐户” > “myworkspace (主 - contosolake)” 。
1. 选择“用户(主)”。 应会看到 NYCTaxi 文件夹。 在内部应该会看到名为 PassengerCountStats.csv 和 PassengerCountStats.parquet 的两个文件夹 。
1. 打开 PassengerCountStats.parquet 文件夹。 在其中将看见一个 parquet 文件，其名称类似于 part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet。
1. 右键单击“.parquet”，然后选择“新建笔记本” 。 它会创建一个包含如下所示的单元的笔记本：

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. 运行该单元。
1. 右键单击内部的 parquet 文件，然后选择“新建 SQL 脚本” > “选择前 100 行” 。 它会创建如下所示的 SQL 脚本：

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

     在脚本中，“附加到”字段将设置为“SQL 按需版本” 。

1. 运行该脚本。

## <a name="visualize-data-with-power-bi"></a>使用 Power BI 可视化数据

根据纽约市出租车数据，我们在两个表中创建了聚合数据集：
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

可以将 Power BI 工作区链接到 Azure Synapse 工作区。 这使你可以轻松地将数据导入到 Power BI 工作区中。 可以直接在 Azure Synapse 工作区中编辑 Power BI 报表。

### <a name="create-a-power-bi-workspace"></a>创建 Power BI 工作区

1. 登录到 [powerbi.microsoft.com](https://powerbi.microsoft.com/)。
1. 新建名为 NYCTaxiWorkspace1 的 Power BI 工作区。

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>将 Azure Synapse 工作区链接到新的 Power BI 工作区

1. 在 Synapse Studio 中，转到“管理” > “关联的服务” 。
1. 选择“新建” > “连接到 Power BI”并设置以下字段 ：

    |设置 | 建议的值 | 
    |---|---|
    |**名称**|**NYCTaxiWorkspace1**|
    |**工作区名称**|**NYCTaxiWorkspace1**|

1. 选择“创建”。

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>创建 Power BI 数据集，该数据集使用 Azure Synapse 工作区中的数据

1. 在 Synapse Studio 中，转到“开发” > “Power BI” 。
1. 转到“NYCTaxiWorkspace1” > “Power BI 数据集”，然后选择“新建 Power BI 数据集”  。
1. 将鼠标悬停在 SQLDB1 数据库上，然后选择“下载 .pbids 文件” 。
1. 打开下载的 .pbids 文件。 Power BI Desktop 将打开并自动连接到 Azure Synapse 工作区中的 SQLDB1。
1. 如果看到名为“SQL 服务器数据库”的对话框：
    1. 选择“Microsoft 帐户”。
    1. 选择“登录”并登录到你的帐户。
    1. 选择“连接”。
1. “导航器”对话框打开后，请检查 PassengerCountStats 表，然后选择“加载”  。
1. “连接设置”对话框显示后，请选择“DirectQuery” > “确定”  。
1. 选择左侧的“报表”按钮。
1. 向报表添加“折线图”。
    1. 将“PasssengerCount”列拖到“可视化效果” > “轴”  。
    1. 将“SumTripDistance”和“AvgTripDistance”列拖到“可视化效果” > “值”   。
1. 在“主页”选项卡上，选择“发布” 。
1. 选择“保存”以保存更改。
1. 选择文件名“PassengerAnalysis.pbix”，然后选择“保存” 。
1. 在“选择目标”中选择“NYCTaxiWorkspace1”，然后单击“选择”  。
1. 等待发布完成。

### <a name="configure-authentication-for-your-dataset"></a>为数据集配置身份验证

1. 打开 [powerbi.microsoft.com](https://powerbi.microsoft.com/)然后“登录”。
1. 在左侧的“工作区”下，选择 NYCTaxiWorkspace1 工作区 。
1. 在该工作区中，找到名为“乘客分析”的数据集和名为“乘客分析”的报表 。
1. 将鼠标悬停在 PassengerAnalysis 数据集上，选择省略号 (...) 按钮，然后选择“设置” 。
1. 在“数据源凭据”中，将“身份验证方法”设置为“OAuth2”，然后选择“登录”   。

### <a name="edit-a-report-in-synapse-studio"></a>在 Synapse Studio 中编辑报表

1. 返回到 Synapse Studio，然后选择“关闭并刷新”。
1. 转到“开发”中心。
1. 将鼠标悬停在 Power BI 上，然后选择以刷新“Power BI 报表”节点 。
1. 在 Power BI 下，你应会看到：
    1. 在“NYCTaxiWorkspace1” > “Power BI 数据集”下，名为“PassengerAnalysis”的新数据集  。
    1. 在“NYCTaxiWorkspace1” > “Power BI 报表”下，名为“PassengerAnalysis”的新报表  。
1. 选择 PassengerAnalysis 报表。 该报表随即打开，可以在 Synapse Studio 中直接对其进行编辑。

## <a name="monitor-activities"></a>监视活动

1. 在 Synapse Studio 中，转到“监视”中心。
1. 在此位置，你可以看到工作区中所有活动的历史记录，以及目前处于有效状态的活动。
1. 浏览“管道运行”、“Apache Spark 应用程序”和“SQL 请求”，以查看你已在工作区中执行的操作  。

## <a name="next-steps"></a>后续步骤

了解有关 [Azure Synapse Analytics（工作区预览版）](overview-what-is.md)的详细信息。

