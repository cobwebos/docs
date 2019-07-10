---
title: 教程：在 Azure HDInsight 上使用交互式查询执行提取、转换、加载 (ETL) 操作
description: 教程 - 了解如何从原始 CSV 数据集提取数据，在 HDInsight 上使用交互式查询将其转换，然后使用 Apache Sqoop 将已转换的数据加载到 Azure SQL 数据库。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/25/2019
ms.author: hrasheed
ms.custom: hdinsightactive,mvc
ms.openlocfilehash: 403e165d7ebe8365ffa0fd2f5f3779d3b4fab68f
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543632"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>教程：在 Azure HDInsight 中使用交互式查询提取、转换和加载数据

在此教程中，你将获取公开可用的航班数据的原始 CSV 数据文件，将其导入 HDInsight 群集存储，然后在 Azure HDInsight 中使用交互式查询转换数据。 数据转换完毕后，使用 [Apache Sqoop](https://sqoop.apache.org/) 将数据加载到 Azure SQL 数据库。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 下载示例航班数据
> * 将数据上传到 HDInsight 群集
> * 使用交互式查询转换数据
> * 在 Azure SQL 数据库中创建表
> * 使用 Sqoop 将数据导出到 Azure SQL 数据库

## <a name="prerequisites"></a>先决条件

* HDInsight 上的一个交互式查询群集。 请参阅[使用 Azure 门户创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)，并选择“交互式查询”  作为**群集类型**。

* Azure SQL 数据库。 使用 Azure SQL 数据库作为目标数据存储。 如果没有 SQL 数据库，请参阅[在 Azure 门户中创建 Azure SQL 数据库](/azure/sql-database/sql-database-single-database-get-started)。

* SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="download-the-flight-data"></a>下载航班数据

1. 浏览到[美国研究与技术创新管理部门、运输统计局](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)。

2. 在页面上，清除所有字段，然后选择以下值：

   | 名称 | 值 |
   | --- | --- |
   | 筛选年份 |2019 |
   | 筛选期间 |1 月 |
   | 字段 |Year、FlightDate、Reporting_Airline、DOT_ID_Reporting_Airline、Flight_Number_Reporting_Airline、OriginAirportID、Origin、OriginCityName、OriginState、DestAirportID、Dest、DestCityName、DestState、DepDelayMinutes、ArrDelay、ArrDelayMinutes、CarrierDelay、WeatherDelay、NASDelay、SecurityDelay、LateAircraftDelay。 |

3. 选择“下载”  。 你将得到一个具有所选数据字段的 zip 文件。

## <a name="upload-data-to-an-hdinsight-cluster"></a>将数据上传到 HDInsight 群集

可通过多种方式将数据上传到与 HDInsight 群集相关的存储。 本部分使用 `scp` 上传数据。 若要了解上传数据的其他方式，请参阅[将数据上传到 HDInsight](../hdinsight-upload-data.md)。

1. 将 .zip 文件上传到 HDInsight 群集头节点。 编辑以下命令，将 `FILENAME` 替换为 .zip 文件的名称，将 `CLUSTERNAME` 替换为 HDInsight 群集的名称。 然后打开命令提示符，将工作目录设置为文件位置，然后输入命令。

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

2. 上传完成后，使用 SSH 连接到群集。 通过将 `CLUSTERNAME` 替换为 HDInsight 群集的名称来编辑以下命令。 然后输入以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. 建立 SSH 连接后设置环境变量。 将 `FILE_NAME`、`SQL_SERVERNAME`、`SQL_DATABASE`、`SQL_USER` 和 `SQL_PASWORD` 替换为适当的值。 然后输入该命令：

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. 通过输入以下命令解压缩 .zip 文件：

    ```bash
    unzip $FILENAME.zip
    ```

5. 在 HDInsight 存储上创建目录，然后输入以下命令将 .csv 文件复制到该目录：

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>使用 Hive 查询转换数据

可通过多种方式在 HDInsight 群集上运行 Hive 作业。 本部分使用 [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) 运行 Hive 作业。 有关以其他方式运行 Hive 作业的信息，请参阅[在 HDInsight 上使用 Apache Hive](../hadoop/hdinsight-use-hive.md)。

在 Hive 作业运行期间，请将 .csv 文件中的数据导入到名为“Delays”的 Hive 表中  。

1. 在 HDInsight 群集已有的 SSH 提示符中，使用以下命令创建并编辑名为“flightdelays.hql”的新文件  ：

    ```bash
    nano flightdelays.hql
    ```

2. 将以下文本用作此文件的内容：

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. 若要保存该文件，请按“Ctrl + X”，然后按“y”，然后输入   。

4. 若要启动 Hive 并运行 **flightdelays.hql** 文件，请使用以下命令：

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. flightdelays.hql 脚本完成运行后，使用以下命令打开交互式 Beeline 会话  ：

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

6. 收到 `jdbc:hive2://localhost:10001/>` 提示时，使用以下查询从导入的航班延误数据中检索数据：

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    此查询会检索遇到天气延迟的城市的列表以及平均延迟时间，并将其保存到 `/tutorials/flightdelays/output` 中。 稍后，Sqoop 会从该位置读取数据并将其导出到 Azure SQL 数据库。

7. 若要退出 Beeline，请在提示符处输入 `!quit`。

## <a name="create-a-sql-database-table"></a>创建 SQL 数据库表

有多种方法可连接到 SQL 数据库并创建表。 以下步骤从 HDInsight 群集中使用 [FreeTDS](http://www.freetds.org/)。

1. 若要安装 FreeTDS，请使用以下命令从打开的 SSH 连接到群集：

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. 安装完成后，使用以下命令连接到 SQL 数据库服务器。

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    将收到类似于以下文本的输出：

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. 在 `1>` 提示符下，输入以下行：

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    输入 `GO` 语句后，将评估前面的语句。 此语句会创建一个名为“delays”且具有聚集索引的表  。

    使用以下查询验证是否已创建该表：

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    输出与以下文本类似：

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. 在 `1>` 提示符下输入 `exit` 以退出 tsql 实用工具。

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>使用 Apache Sqoop 将数据导出到 SQL 数据库

在前面的部分中，已经在 `/tutorials/flightdelays/output` 复制了转换后的数据。 本部分使用 Sqoop 将数据从 `/tutorials/flightdelays/output` 导出到在 Azure SQL 数据库中创建的表。

1. 通过输入以下命令验证 Sqoop 是否可以查看 SQL 数据库：

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    此命令会返回数据库列表，其中包括此前创建的 `delays` 表所在的数据库。

2. 通过输入以下命令将数据从 `/tutorials/flightdelays/output` 导出到 `delays` 表：

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop 连接到包含 `delays` 表的数据库，并将数据从 `/tutorials/flightdelays/output` 目录导出到 `delays` 表。

3. Sqoop 命令完成后，使用 tsql 实用程序通过输入以下命令连接到数据库：

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    使用以下语句验证数据是否已导出到 delays 表：

    ```sql
    SELECT * FROM delays
    GO
    ```

    会在表中看到一系列数据。 该表包括城市名称和该城市的平均航班延迟时间。 

    键入 `exit` 退出 tsql 实用工具。

## <a name="clean-up-resources"></a>清理资源

完成本教程后，可以删除群集。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。

若要删除群集，请参阅[使用浏览器、PowerShell 或 Azure CLI 删除 HDInsight 群集](../hdinsight-delete-cluster.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你获取了一个原始 CSV 数据文件，将其导入到 HDInsight 群集存储中，然后使用 Azure HDInsight 中的交互式查询转换数据。  继续学习下一篇教程，了解 Apache Hive Warehouse Connector。

> [!div class="nextstepaction"]
>[将 Apache Spark 和 Apache Hive 与 Hive Warehouse Connector 集成](./apache-hive-warehouse-connector.md)
