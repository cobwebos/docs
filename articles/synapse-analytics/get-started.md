---
title: 教程：Azure Synapse Analytics 入门
description: 分步骤快速了解 Azure Synapse 中的基本概念
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: 24a34ae6f00eca7154021162184f5e71503da06b
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248322"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Azure Synapse Analytics 入门

本文档将指导你完成设置和使用 Azure Synapse Analytics 所需的所有基本步骤。

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>准备用于 Synapse 工作区的存储帐户

* 打开 [Azure 门户](https://portal.azure.com)
* 使用以下设置新建存储帐户：

    |选项卡|设置 | 建议的值 | 说明 |
    |---|---|---|---|
    |基础|**存储帐户名称**| 可为其指定任意名称。|在本文档中，我们将其称为 `contosolake`。|
    |基础|**帐户种类**|必须设置为 `StorageV2`||
    |基础|**位置**|可以选择任何位置| 建议 Synapse 工作区和 Azure Data Lake Storage (ADLS) Gen2 帐户位于同一区域。|
    |高级|**Data Lake Storage Gen2**|`Enabled`| Azure Synapse 仅适用于启用了此设置的存储帐户。|

1. 创建存储帐户后，从左侧导航栏选择“访问控制 (IAM)”。 然后，分配以下角色或确保其已经分配。 

    a. * 在存储帐户 b 上为自己分配“所有者”角色。 * 在存储帐户上为自己分配“存储 Blob 数据所有者”角色

1. 从左侧导航栏中选择“容器”并创建容器。 可为其指定任意名称。 接受默认的“公共访问级别”。 在本文档中，我们将该容器称为 `users`。 选择“创建”。 

在下面的步骤中，你将配置 Synapse 工作区以使用此存储帐户作为其“主要”存储帐户，并使用容器来存储工作区数据。 工作区将数据存储在此帐户中名为 `/synapse/workspacename` 的文件夹下的 Apache Spark 表和 Spark 应用程序日志中。

## <a name="create-a-synapse-workspace"></a>创建 Synapse 工作区

* 打开 [Azure 门户](https://portal.azure.com)然后在顶部搜索 `Synapse`。
* 在“服务”下的搜索结果中，选择“Azure Synapse Analytics (工作区预览版)” 
* 选择“+ 添加”以使用这些设置来创建工作区

    |选项卡|设置 | 建议的值 | 说明 |
    |---|---|---|---|
    |基础|**工作区名称**|可以随意为其命名。| 在本文档中，我们将使用 `myworkspace`|
    |基础|**区域**|匹配存储帐户的区域|

1. 在“选择 Data Lake Storage Gen 2”下，选择之前创建的帐户和容器。

1. 选择“查看 + 创建”。 选择“创建”。 你的工作区将在几分钟内准备就绪。

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>验证 Synapse 工作区 MSI 是否有权访问存储帐户

验证操作可能已经执行过。 无论如何，你都应该进行验证。

1. 打开 [Azure 门户](https://portal.azure.com)，然后打开为工作区所选的主存储帐户。
1. 在左侧导航栏中，选择“访问控制(IAM)”。 然后，分配以下角色或确保其已经分配。 
    a. 将工作区标识分配到存储帐户上的“存储 Blob 数据参与者”角色。 工作区标识与工作区同名。 在本文档中，工作区名称是 `myworkspace`，因此工作区标识是 `myworkspaced`
1. 选择“保存”。
    
## <a name="launch-synapse-studio"></a>启动 Synapse Studio

创建 Synapse 工作区后，可以通过两种方式打开 Synapse Studio：
* 在 [Azure 门户](https://portal.azure.com)中打开 Synapse 工作区，然后在“概述”部分的顶部，选择“启动 Synapse Studio” 
* 直接转到 https://web.azuresynapse.net ，然后登录到工作区。

## <a name="create-a-sql-pool"></a>创建 SQL 池

1. 在 Synapse Studio 的左侧导航栏中，选择“管理”>“SQL 池”
1. 选择“+新建”，然后输入以下设置：

    |设置 | 建议的值 | 
    |---|---|
    |**SQL 池名称**| `SQLDB1`|
    |**性能级别**|`DW100C`|

1. 选择“查看+创建”，然后选择“创建” 。
1. 你的 SQL 池将在几分钟内准备就绪。 创建 SQL 池时，该池将与另一个名为“SQLDB1”的 SQL 池数据库相关联。

只要 SQL 池处于活动状态，就会使用计费资源。 你可以在稍后暂停池以降低成本。

## <a name="create-an-apache-spark-pool"></a>创建 Apache Spark 池

1. 在 Synapse Studio 的左侧，选择“管理”>“Apache Spark 池”
1. 选择“+新建”，然后输入以下设置：

    |设置 | 建议的值 | 
    |---|---|
    |**Apache Spark 池名称**|`Spark1`
    |**节点大小**| `Small`|
    |**节点数**| 将最小值设置为 3，最大值设置为 3|

1. 选择“查看+创建”，然后选择“创建” 。
1. 你的 Apache Spark 池将在几秒钟内准备就绪。

> [!NOTE]
> 尽管名称类似，但 Apache Spark 池与 SQL 池并不相同。 这只是一些基本元数据，用于通知 Synapse 工作区如何与 Spark 交互。 

由于 Spark 池是元数据，因此无法启动或停止。 

在 Synapse 中执行任何 Spark 活动时，指定要使用的 Spark 池。 该池通知 Synapse 要使用的 Spark 资源数量。 只需支付所用资源的费用。 主动停止使用该池时，资源将自动超时并进行回收。

> [!NOTE]
> Spark 数据库是独立于 Spark 池创建的。 工作区始终具有名为“default”的 Spark 数据库，你可以创建其他 Spark 数据库。

## <a name="the-sql-on-demand-pool"></a>SQL 按需版本池

每个工作区都具有不可删除的预构建池，该池名为“SQL 按需版本”。 使用 SQL 按需版本池即可使用 SQL，无需创建或考虑如何管理 Synapse SQL 池。 与其他类型的池不同，SQL 按需版本的计费基于运行查询所扫描的数据量，而不是执行查询所使用的资源数。

* SQL 按需版本还具有自己的 SQL 按需版本数据库，这些数据库独立于任何 SQL 按需版本池而存在。
* 目前，工作区始终只包含一个名为“SQL 按需版本”的 SQL 按需版本池。

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>将纽约市出租车示例数据加载到 SQLDB1 数据库

1. 在 Synapse Studio 最顶部的蓝色菜单中，选择“?” 图标。
1. 选择“入门”>“入门中心”
1. 在标有“查询示例数据”的卡中，选择名为 `SQLDB1` 的 SQL 池
1. 选择“查询数据”。 你将看到一条通知，告知“正在加载示例数据”，该通知显示后随即消失。
1. Synapse Studio 的顶部附近将显示淡蓝色的通知栏，告知正在将数据加载到 SQLDB1。 等到该通知栏变为绿色，然后将其关闭。

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>浏览 SQL 池中的纽约市出租车数据

1. 在 Synapse Studio 中，导航到“数据”中心
1. 导航到“SQLDB1”>“表”。 你将看到多个已加载的表。
1. 右键单击 dbo.Trip 表，然后选择“新建 SQL 脚本”>“选择前 100 行” 
1. 这将创建并自动运行新的 SQL 脚本。
1. 请注意，在 SQL 脚本的顶部，“连接到”自动设置为名为 `SQLDB1` 的 SQL 池。
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

1. 此查询显示总行程距离和平均行程距离与乘客数之间的关系
1. 在“SQL 脚本结果”窗口中，将“视图”更改为“图表”，从而以折线图形式查看结果的可视化效果 

## <a name="load-the-nyc-taxi-sample-data-into-the-spark-nyctaxi-database"></a>将纽约市出租车示例数据加载到 Spark nyctaxi 数据库

`SQLDB1` 的表中有可用数据。 现在，我们将其加载到名为 `nyctaxi` 的 Spark 数据库。

1. 在 Synapse Studio 中，导航到“开发”中心
1. 依次选择 + 和“Notebook” 
1. 在笔记本顶部，将“附加到”值设置为 `Spark1`
1. 选择“添加代码”以添加笔记本代码单元，并粘贴以下文本：

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. 导航到“数据”中心，右键单击“数据库”，然后选择“刷新”  。
1. 此时你应该看到以下数据库：
    - SQLDB（SQL 池）
    - nyctaxi (Spark)
      
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>使用 Spark 和笔记本分析纽约市出租车数据

1. 返回到笔记本
1. 新建代码单元，输入以下文本，然后运行单元以说明我们加载到 `nyctaxi` Spark 数据库中的纽约市出租车数据。

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 运行以下代码，执行之前在 SQL 池 `SQLDB1` 中所做的分析。 此代码还会将分析结果另存到名为 `nyctaxi.passengercountstats` 的表，然后可视化结果。

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

1. 在单元结果中，选择“图表”以查看直观呈现出来的数据
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>使用 Spark 和笔记本自定义数据可视化效果数据

使用笔记本即可控制图表的呈现方式。 以下代码演示了一个使用常用库 `matplotlib` 和 `seaborn` 的简单示例。 这将呈现在之前运行 SQL 查询时所显示的同类型折线图。

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

之前我们将数据从 SQL 池表 `SQLDB1.dbo.Trip` 复制到了 Spark 表 `nyctaxi.trip`。 然后，我们使用 Spark 将数据聚合到了 Spark 表 `nyctaxi.passengercountstats`。 现在，我们将 `nyctaxi.passengercountstats` 中的数据复制到名为 `SQLDB1.dbo.PassengerCountStats` 的 SQL 池表。 

在笔记本中运行以下单元。 这会将聚合的 Spark 表复制回 SQL 池表。

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>使用 SQL 按需版本分析 Spark 数据库中的纽约市出租车数据 

使用 SQL 按需版本可自动显示和查询 Spark 数据库中的表。

1. 在 Synapse Studio 中，导航到“开发”中心，然后新建 SQL 脚本
1. 将“连接到”设置为“SQL 按需版本”  
1. 将以下文本粘贴到脚本中，然后运行该脚本。

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```
    > [!NOTE]
    > 第一次运行使用 SQL 按需版本的查询时，SQL 按需版本需要大约 10 秒的时间来收集运行查询所需的 SQL 资源。 后续查询将无需进行此操作，并且速度快得多。
  
## <a name="orchestrate-activities-with-pipelines"></a>使用管道安排活动

可以在 Azure Synapse 中安排各种任务。 本节将介绍如何轻而易举就实现这一操作。

1. 在 Synapse Studio 中，导航到“安排”中心。
1. 依次选择 + 和“管道” 。 此时将新建管道。
1. 导航到“开发”中心并找到之前创建的笔记本。
1. 将该笔记本拖动到管道。
1. 在管道中，选择“添加触发器”>“新建/编辑”。
1. 在“选择触发器”中，选择“新建”，然后在“定期”中，将触发器设置为每 1 小时运行一次 。
1. 选择“确定”。
1. 选择“发布所有”，然后管道将会每一小时运行一次。
1. 如果希望管道立即运行而不用等待一个小时，请选择“添加触发器”>“新建/编辑”。

## <a name="working-with-data-in-a-storage-account"></a>使用存储帐户中的数据

到目前为止，我们介绍了数据驻留在工作区的数据库中的各种情况。 现在，我们将介绍如何使用存储帐户中的文件。 对于这一情况，我们将使用在创建工作区时所指定的工作区和容器的主存储帐户。

* 存储帐户的名称：`contosolake`
* 存储帐户中的容器的名称：`users`

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>在存储帐户中创建 CSV 和 Parquet 文件

在笔记本中运行以下代码。 这会在存储帐户中创建 CSV 文件和 parquet 文件

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>分析存储帐户中的数据

1. 在 Synapse Studio 中，导航到“数据”中心
1. 选择“已链接”
1. 导航到“存储帐户”>“我的工作区(主 - contosolake)”
1. 选择“用户(主)”
1. 应看到名为 `NYCTaxi` 的文件夹。 在其中，应看到两个文件夹：`PassengerCountStats.csv` 和 `PassengerCountStats.parquet`。
1. 导航到 `PassengerCountStats.parquet` 文件夹中。
1. 右键单击内部的 parquet 文件，然后选择“新建笔记本”，这将创建笔记本，其中包含如下所示的单元：

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. 运行该单元。
1. 右键单击内部的 parquet 文件，然后选择“新建 SQL 脚本”>“选择前 100 行”，这将创建如下所示的 SQL 脚本：

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
1. 在脚本中，“附加到”字段将设置为“SQL 按需版本” 。
1. 运行该脚本。

## <a name="visualize-data-with-power-bi"></a>使用 Power BI 可视化数据

根据纽约市出租车数据，我们在两个表中创建了聚合数据集：
* `nyctaxi.passengercountstats`
* `SQLDB1.dbo.PassengerCountStats`

可以将 Power BI 工作区链接到 Synapse 工作区。 这使你可以轻松地将数据导入到 Power BI 工作区，然后你可以直接在 Synapse 工作区编辑 Power BI 报表。

### <a name="create-a-power-bi-workspace"></a>创建 Power BI 工作区

1. 登录到 [powerbi.microsoft.com](https://powerbi.microsoft.com/)。
1. 新建名为 `NYCTaxiWorkspace1` 的 Power BI 工作区。

### <a name="link-your-synapse-workspace-to-your-new-power-bi-workspace"></a>将 Synapse 工作区链接到新的 Power BI 工作区

1. 在 Synapse Studio 中，导航到“管理”>“链接服务”。
1. 选择“+新建”，然后选择“连接到 Power BI”并设置以下字段 ：

    |设置 | 建议的值 | 
    |---|---|
    |**名称**|`NYCTaxiWorkspace1`|
    |**工作区名称**|`NYCTaxiWorkspace1`|
        
1. 选择“创建”。

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>创建 Power BI 数据集，该数据集使用 Synapse 工作区中的数据

1. 在 Synapse Studio 中，导航到“开发”>“Power BI”。
1. 导航到“NYCTaxiWorkspace1”>“Power BI 数据集”，然后选择“新建 Power BI 数据集” 。
1. 将鼠标悬停在 `SQLDB1` 数据库上，然后选择“下载 .pbids 文件”。
1. 打开下载的 `.pbids` 文件。 
1. 这将启动 Power BI Desktop 并将其自动连接到 synapse 工作区中的 `SQLDB1`。
1. 如果看到名为“SQL 服务器数据库”的对话框：a. 选择“Microsoft 帐户”。 
    b. 选择“登录”并登录。
    c. 选择“连接”。
1. “导航器”对话框将打开。 该对话框打开时，请检查 PassengerCountStats 表，然后选择“加载” 。
1. 此时将显示“连接设置”对话框。 选择“DirectQuery”，然后选择“确定” 
1. 选择左侧的“报表”按钮。
1. 向报表添加“折线图”。
    a. 将“PasssengerCount”列拖到“可视化效果”>“轴”b 。 将“SumTripDistance”和“AvgTripDistance”列拖到“可视化效果”>“值”  。
1. 在“主页”选项卡上，选择“发布” 。
1. 该操作会询问你是否保存更改。 选择“保存”。
1. 该操作会要求你选择文件名。 选择 `PassengerAnalysis.pbix`，然后选择“保存”。
1. 该操作会要求你依次“选择目标”、`NYCTaxiWorkspace1` 和“选择” 。
1. 等待发布完成。

### <a name="configure-authentication-for-your-dataset"></a>为数据集配置身份验证

1. 打开 [powerbi.microsoft.com](https://powerbi.microsoft.com/)然后“登录”
1. 在左侧的“工作区”下，选择 `NYCTaxiWorkspace1` 工作区。
1. 在该工作区中，你应会看到名为 `Passenger Analysis` 的数据集和名为 `Passenger Analysis` 的报表。
1. 将鼠标悬停在 `PassengerAnalysis` 数据集上，选择具有三个点的图标，然后选择“设置”。
1. 在“数据源凭据”中，将“身份验证方法”设置为“OAuth2”，然后选择“登录”   。

### <a name="edit-a-report-in-synapse-studio"></a>在 Synapse Studio 中编辑报表

1. 返回到 Synapse Studio，然后选择“关闭并刷新” 
1. 导航到“开发”中心 
1. 将鼠标悬停在 Power BI 上，然后单击以刷新“Power BI 报表”节点 。
1. 此时在 Power BI 下，你应会看到：a. * 在“NYCTaxiWorkspace1”>“Power BI 数据集”下，名为“PassengerAnalysis”的新数据集 。
    b. * 在“NYCTaxiWorkspace1”>“Power BI 报表”下，名为“PassengerAnalysis”的新报表 。
1. 选择 PassengerAnalysis 报表。 
1. 该报表将打开，现在可以在 Synapse Studio 中直接编辑该报表。

## <a name="monitor-activities"></a>监视活动

1. 在 Synapse Studio 中，导航到“监视”中心。
1. 在此位置，你可以看到工作区中所有活动的历史记录，以及目前处于有效状态的活动。
1. 浏览“管道运行”、“Apache Spark 应用程序”和“SQL 请求”，然后可以查看你已在工作区中执行的操作  。

## <a name="next-steps"></a>后续步骤

详细了解 [Azure Synapse Analytics(预览版)](overview-what-is.md)

