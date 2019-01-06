---
title: 教程：使用 Spark 通过 Azure Databricks 访问 Azure Data Lake Storage Gen2 预览版数据 | Microsoft Docs
description: 本教程介绍如何在 Azure Databricks 群集上运行 Spark 查询，以便访问 Azure Data Lake Storage Gen2 存储帐户中的数据。
services: storage
author: dineshmurthy
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: dineshm
ms.openlocfilehash: b0382d31f9d16228ca3447ace9c7d4f171b206f6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548980"
---
# <a name="tutorial-access-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>教程：使用 Spark 通过 Azure Databricks 访问 Data Lake Storage Gen2 预览版数据

本教程介绍如何将 Azure Databricks 群集连接到启用了 Azure Data Lake Storage Gen2（预览版）的 Azure 存储帐户中存储的数据。 建立此连接后，即可在群集本机上针对数据运行查询和分析。

在本教程中，将：

> [!div class="checklist"]
> * 创建 Databricks 群集
> * 将非结构化数据引入存储帐户中
> * 对 Blob 存储中的数据运行分析

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

本教程演示如何使用和查询[美国交通部](https://transtats.bts.gov/DL_SelectFields.asp)提供的航班数据。 

1. 选中“预压缩的文件”复选框，选择所有数据字段。
2. 选择“下载”并将结果保存到计算机。
3. 记下下载内容的文件名和路径；在后面的步骤中需要此信息。

若要完成本教程，需要一个具有分析功能的存储帐户。 我们建议完成有关该主题的[快速入门](data-lake-storage-quickstart-create-account.md)，以一个存储帐户。 创建存储帐户后，导航到该存储帐户以检索配置设置。

1. 在“设置”下，选择“访问密钥”。
2. 选择“密钥 1”旁边的“复制”按钮以复制密钥值。

本教程后面的步骤需要此帐户名称和密钥。 打开一个文本编辑器，在其中存储此帐户名称和密钥，供将来引用。

## <a name="create-a-databricks-cluster"></a>创建 Databricks 群集

下一步是创建 Databricks 群集，以创建数据工作区。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源”。
2. 搜索字段中输入 **Azure Databricks**。
3. 在 Azure Databricks 边栏选项卡上选择“创建”。
4. 将 Databricks 服务命名为 **myFlightDataService**（创建该服务时请务必选中“固定到仪表板”复选框）。
5. 选择“启动工作区”，在新浏览器窗口中打开工作区。
6. 在左侧导航栏中选择“群集”。
7. 选择“创建群集”。
8. 在“群集名称”字段中输入 **myFlightDataCluster**。
9. 在“辅助角色类型”字段中选择“Standard_D8s_v3”。
10. 将“最小辅助角色数”值更改为 **4**。
11. 在页面顶部选择“创建群集”。 （此过程最长需要 5 分钟才能完成。）
12. 完成此过程后，在导航栏的左上角选择“Azure Databricks”。
13. 在页面下半部的“新建”部分下选择“Notebook”。
14. 在“名称”字段中输入选择的名称，然后选择“Python”作为语言。
15. 所有其他字段可以保留默认值。
16. 选择“创建”。
17. 将以下代码粘贴到 **Cmd 1** 单元中。 将示例中括号内显示的占位符替换为自己的值：

    ```scala
    %python%
    configs = {"fs.azure.account.auth.type": "OAuth",
        "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
        "fs.azure.account.oauth2.client.id": "<service-client-id>",
        "fs.azure.account.oauth2.client.secret": "<service-credentials>",
        "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token"}
        
    dbutils.fs.mount(
        source = "abfss://dbricks@<account-name>.dfs.core.windows.net/folder1",
        mount_point = "/mnt/flightdata",
        extra_configs = configs)
    ```
18. 按 **SHIFT + ENTER** 运行代码单元。

## <a name="ingest-data"></a>引入数据

### <a name="copy-source-data-into-the-storage-account"></a>将源数据复制到存储帐户中

下一任务是使用 AzCopy 将数据从 *.csv* 文件复制到 Azure 存储中。 打开命令提示符窗口，输入以下命令。 确保将占位符 `<DOWNLOAD_FILE_PATH>`、`<ACCOUNT_NAME>` 和 `<ACCOUNT_KEY>` 替换为在前面步骤中获取的相应值。

```bash
set ACCOUNT_NAME=<ACCOUNT_NAME>
set ACCOUNT_KEY=<ACCOUNT_KEY>
azcopy cp "<DOWNLOAD_FILE_PATH>" https://<ACCOUNT_NAME>.dfs.core.windows.net/dbricks/folder1/On_Time --recursive 
```

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>使用 Databricks Notebook 将 CSV 转换为 Parquet

在浏览器中重新打开 Databricks，执行以下步骤：

1. 在导航栏的左上角选择“Azure Databricks”。
2. 在页面下半部的“新建”部分下选择“Notebook”。
3. 在“名称”字段中输入 **CSV2Parquet**。
4. 所有其他字段可以保留默认值。
5. 选择“创建”。
6. 将以下代码粘贴到 **Cmd 1** 单元中。 （此代码将在编辑器中自动保存。）

    ```python
    # Use the previously established DBFS mount point to read the data
    # create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/On_Time_On_Time*.csv")
    # read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
    print("Done")
    ```

## <a name="explore-data"></a>浏览数据

返回到 Databricks 工作区，在左侧导航栏中选择“最近”图标。

1. 选择“航班数据分析”Notebook。
2. 按 **Ctrl + Alt + N** 创建新的单元格。

将下面的每个代码块输入到 **Cmd 1** 中，然后按 **Cmd + Enter** 运行 Python 脚本。

若要通过 AzCopy 上传一系列 CSV 文件，请运行以下脚本：

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata/temp/"))
```

若要创建新文件并列出 *parquet/flights* 文件夹中的文件，请运行以下脚本：

```python
dbutils.fs.put("/mnt/flightdata/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/temp/parquet/flights")
```

通过这些代码示例，你已使用启用了 Data Lake Storage Gen2 的存储帐户中存储的数据探讨了 HDFS 的层次结构性质。

## <a name="query-the-data"></a>查询数据

接下来，可以开始查询上传到存储帐户中的数据。 将下面的每个代码块输入到 **Cmd 1** 中，然后按 **Cmd + Enter** 运行 Python 脚本。

### <a name="run-simple-queries"></a>运行简单查询

若要为数据源创建 dataframe，请运行以下脚本：

> [!IMPORTANT]
> 确保将 **<YOUR_CSV_FILE_NAME>** 占位符替换为在本教程开头下载的文件的名称。

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load("/mnt/flightdata/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

#print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

#print the flight database size
print("Number of flights in the database: ", flightDF.count())

#show the first 20 rows (20 is the default)
#to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

#Display to run visualizations
#preferably run this in a separate cmd cell
display(flightDF)
```

若要针对数据运行分析查询，请运行以下脚本：

```python
#Run each of these queries, preferably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

#using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights=out2.count()
print("Jan 2016: ", NumJan2016Flights," Feb 2016: ",NumFeb2016Flights)
Total= NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql("SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'") 
print('Airports in Texas: ', out.show(100))

#find all airlines that fly from Texas
out1 = spark.sql("SELECT distinct(Carrier) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

### <a name="run-complex-queries"></a>运行复杂查询

若要执行下述更复杂的查询，请在 Notebook 中一次运行一个段，然后检查结果。

```python
#find the airline with the most flights

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")

#show the top row without truncation
output.show(1, False)

#show the top 10 airlines
output.show(10, False)

#Determine which is the least on time airline

#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v")
spark.sql("CREATE TEMPORARY VIEW v AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE DepDelay>60 or ArrDelay>60 group by Carrier, UniqueCarrier order by NumFlights desc LIMIT 10")
output = spark.sql("select * from v")
#output = spark.sql("SELECT AirlineName FROM AirlineCodes WHERE AirlineCode in (select Carrier from v)")
#show the top row without truncation
output.show(1, False)

#which airline improved its performance
#find the airline with the most improvement in delays
#create a temporary view to hold the flight delay information aggregated by airline, then select the airline name from the Airlinecodes dataframe
spark.sql("DROP VIEW IF EXISTS v1")
spark.sql("DROP VIEW IF EXISTS v2")
spark.sql("CREATE TEMPORARY VIEW v1 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2016 group by Carrier order by NumFlights desc LIMIT 10")
spark.sql("CREATE TEMPORARY VIEW v2 AS SELECT Carrier, count(*) as NumFlights from FlightTable WHERE (DepDelay>0 or ArrDelay>0) and Year=2017 group by Carrier order by NumFlights desc LIMIT 10")
output = spark.sql("SELECT distinct ac.AirlineName, v1.Carrier, v1.NumFlights, v2.NumFlights from v1 INNER JOIN v2 ON v1.Carrier = v2.Carrier INNER JOIN AirlineCodes ac ON v2.Carrier = ac.AirlineCode WHERE v1.NumFlights > v2.NumFlights")
#show the top row without truncation
output.show(10, False)

#display for visual analysis
display(output)
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要本文中创建的资源，可以删除资源组和所有相关资源。 为此，请选择存储帐户所在的资源组，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

[!div class="nextstepaction"] 
> [使用 Apache Hive on Azure HDInsight 提取、转换和加载数据](data-lake-storage-tutorial-extract-transform-load-hive.md)

