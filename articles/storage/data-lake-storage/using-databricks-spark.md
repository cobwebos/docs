---
title: 使用 Spark 通过 Azure Databricks 访问 Azure Data Lake Storage Gen2 预览版数据 | Microsoft Docs
description: 了解如何在 Azure Databricks 群集上运行 Spark 查询，以便访问 Azure Data Lake Storage Gen2 存储帐户中的数据。
services: hdinsight,storage
tags: azure-portal
author: dineshm
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 6/27/2018
ms.author: dineshm
ms.openlocfilehash: 4f01352f13b7a831791c7e74520cc08b98afa661
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521449"
---
# <a name="tutorial-access-azure-data-lake-storage-gen2-preview-data-with-azure-databricks-using-spark"></a>教程：使用 Spark 通过 Azure Databricks 访问 Azure Data Lake Storage Gen2 预览版数据

本教程介绍如何在 Azure Databricks 群集上运行 Spark 查询，以便查询支持 Azure Data Lake Storage Gen2 预览版的帐户中的数据。

> [!div class="checklist"]
> * 创建 Databricks 群集
> * 将非结构化数据引入存储帐户中
> * 触发用于处理数据的 Azure Functions
> * 对 Blob 存储中的数据运行分析

## <a name="prerequisites"></a>先决条件

本教程演示如何使用和查询[美国交通部](https://transtats.bts.gov/Tables.asp?DB_ID=120&DB_Name=Airline%20On-Time%20Performance%20Data&DB_Short_Name=On-Time)提供的航班数据。 下载至少两年的航班数据（选择所有字段）并将结果保存到计算机。 确保记下下载内容的文件名和路径，在后面的步骤中需要该信息。

> [!NOTE]
> 单击“预压缩的文件”复选框，选择所有数据字段。 下载内容的大小将是数个 GB，但这种数量的数据是进行分析所必需的。

## <a name="create-an-azure-data-lake-storage-gen2-account"></a>创建 Azure Data Lake Storage Gen2 帐户

若要开始本教程，请创建新的 [Azure Data Lake Storage Gen2 帐户](quickstart-create-account.md)并为其提供唯一名称。 然后导航到存储帐户，以便检索配置设置。

> [!IMPORTANT]
> 在预览期间，Azure Functions 仅适用于使用平面命名空间创建的 Azure Data Lake Storage Gen2 帐户。

1. 在“设置”下，单击“访问密钥”。
3. 单击 **key1** 旁边的“复制”按钮，复制密钥值。

本教程后面的步骤需要此帐户名称和密钥。 打开一个文本编辑器，在其中存储此帐户名称和密钥，供将来引用。

## <a name="create-a-databricks-cluster"></a>创建 Databricks 群集

下一步是创建 [Databricks 群集](https://docs.azuredatabricks.net/)，以便创建数据工作区。

1. 创建一个 [Databricks 服务](https://ms.portal.azure.com/#create/Microsoft.Databricks)，将其命名为 **myFlightDataService**（确保在创建此服务时勾选“固定到仪表板”复选框）。
2. 单击“启动工作区”，在新浏览器窗口中打开工作区。
3. 在左侧导航栏中单击“群集”。
4. 单击“创建群集”。
5. 在“群集名称”字段中输入 **myFlightDataCluster**。
6. 在“辅助角色类型”字段中选择“Standard_D8s_v3”。
7. 将“最小辅助角色数”值更改为 *4*。
8. 单击页面顶部的“创建群集”（此过程可能需要长达 5 分钟的时间才能完成）。
9. 当此过程完成时，在导航栏的左上角选择“Azure Databricks”。
10. 在页面下半部的“新建”部分下选择“Notebook”。
11. 在“名称”字段中输入你选择的名称，并选择“Python”作为语言。
12. 所有其他字段可以保留默认值。
13. 选择“创建”。
14. 将以下代码粘贴到 **Cmd 1** 单元格中，将值替换为存储帐户中保留的值。

    ```bash
    spark.conf.set("fs.azure.account.key.<account_name>.dfs.core.windows.net", "<account_key>") 
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
    dbutils.fs.ls("abfs://<file_system>@<account_name>.dfs.core.windows.net/")
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
    ```

## <a name="ingest-data"></a>引入数据

### <a name="copy-source-data-into-the-storage-account"></a>将源数据复制到存储帐户中

下一任务是使用 AzCopy 将数据从 *.csv* 文件复制到 Azure 存储中。 打开命令提示符窗口，输入以下命令。 确保将占位符 `<DOWNLOAD_FILE_PATH>`、`<ACCOUNT_NAME>`、`<ACCOUNT_KEY>` 替换为在前面的步骤中存储的相应值。

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
6. 在 **Cmd 1** 单元格中粘贴以下代码（此代码在编辑器中自动保存）。

    ```python
    #mount Azure Blob Storage as an HDFS file system to your databricks cluster
    #you need to specify a storage account and container to connect to. 
    #use a SAS token or an account key to connect to Blob Storage.  
    accountname = "<insert account name>' 
    accountkey = " <insert account key>'
    fullname = "fs.azure.account.key." +accountname+ ".blob.core.windows.net"
    accountsource = "abfs://dbricks@" +accountname+ ".blob.core.windows.net/folder1"
    #create a dataframe to read data
    flightDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/On_Time_On_Time*.csv")
    #read the all the airline csv files and write the output to parquet format for easy query
    flightDF.write.mode("append").parquet(accountsource + '/parquet/flights')

    #read the flight details parquet file 
    #flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")
    print("Done")
    ```

## <a name="explore-data-using-hadoop-distributed-file-system"></a>使用 Hadoop 分布式文件系统浏览数据

返回到 Databricks 工作区，单击左侧导航栏中的“最新”图标。

1. 单击“航班数据分析”Notebook。
2. 按 **Ctrl + Alt + N** 创建新的单元格。

将下面的每个代码块输入到 **Cmd 1** 中，然后按 **Cmd + Enter** 运行 Python 脚本。

若要通过 AzCopy 上传一系列 CSV 文件，请运行以下脚本：

```python
import os.path
import IPython
from pyspark.sql import SQLContext
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"
dbutils.fs.ls(source + "/temp")
display(dbutils.fs.ls(source + "/temp/"))
```

若要创建新文件并列出 *parquet/flights* 文件夹中的文件，请运行以下脚本：

```python
source = "abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/"

dbutils.fs.help()

dbutils.fs.put(source + "/temp/1.txt", "Hello, World!", True)
dbutils.fs.ls(source + "/temp/parquet/flights")
```
你已经通过这些代码示例使用存储在支持 Azure Data Lake Storage Gen2 的帐户中的数据探讨了 HDFS 的层次结构性质。

## <a name="query-the-data"></a>查询数据

接下来，可以开始查询上传到 Azure Data Lake Storage 中的数据。 将下面的每个代码块输入到 **Cmd 1** 中，然后按 **Cmd + Enter** 运行 Python 脚本。

### <a name="simple-queries"></a>简单查询

若要为数据源创建 dataframe，请运行以下脚本：

> [!IMPORTANT]
> 确保将 **<YOUR_CSV_FILE_NAME>** 占位符替换为在本教程开头下载的文件的名称。

```python
#Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(header='true', inferschema='true').load(accountsource + "/<YOUR_CSV_FILE_NAME>.csv")
acDF.write.parquet(accountsource + '/parquet/airlinecodes')

#read the existing parquet file for the flights database that was created via the Azure Function
flightDF = spark.read.format('parquet').options(header='true', inferschema='true').load(accountsource + "/parquet/flights")

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
#preferrably run this in a separate cmd cell
display(flightDF)
```

若要针对数据运行分析查询，请运行以下脚本：

```python
#Run each of these queries, preferrably in a separate cmd cell for separate analysis
#create a temporary sql view for querying flight information
FlightTable = spark.read.parquet(accountsource + '/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

#create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet(accountsource + '/parquet/airlinecodes')
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
### <a name="complex-queries"></a>复杂查询

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

## <a name="next-steps"></a>后续步骤

* [使用 Apache Hive on Azure HDInsight 提取、转换和加载数据](tutorial-extract-transform-load-hive.md)