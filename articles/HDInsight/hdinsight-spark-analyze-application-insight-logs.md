<!-- not suitable for Mooncake -->

<properties
pageTitle="使用 HDInsight 上的 Spark 分析 Application Insights 日志 | Azure"
description="了解如何将 Application Insight 日志导出到 Blob 存储，然后使用 HDInsight 上的 Spark 分析这些日志。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.date="08/15/2016"
	wacn.date=""/>

# 使用 HDInsight 上的 Spark 分析 Application Insights 遥测日志

[Visual Studio Application Insights](/documentation/articles/app-insights-overview/) 是用于监视 Web 应用程序的分析服务。可将 Application Insights 生成的遥测数据导出到 Azure 存储，然后，HDInsight 将在该处分析遥测数据。

本文档介绍如何使用 HDInsight 和 Apache Spark 分析 Application Insights 遥测数据。

## 先决条件

* Azure 订阅。

* 配置为使用 Application Insights 的应用程序。

* 熟悉基于 Linux 的 HDInsight 群集的创建过程。如果不熟悉群集的创建，请参阅 [Create Spark on HDInsight（在 HDInsight 上创建 Spark）](/documentation/articles/hdinsight-apache-spark-jupyter-spark-sql/)了解详细信息。

    > [AZURE.NOTE] 本文档不会逐步讲解如何创建新群集，但会参考其他讲述如何创建可访问遥测数据的群集的文档。

* Web 浏览器。用于通过 Jupyter Notebook 以交互方式运行分析。

开发和测试本文档时使用了以下应用：

* [配置为使用 Application Insights 的 Node.js Web 应用](/documentation/articles/app-insights-nodejs/)，用于生成 Application Insights 遥测数据。

* HDInsight 群集上基于 Linux 的 Spark 版本 3.4，用于分析数据。

## 架构与规划

下图演示了本示例的服务体系结构：

![演示数据从 Application Insights 流向 Blob 存储，然后使用 HDInsight 上的 Spark 进行处理的示意图](./media/hdinsight-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### Azure 存储空间

HDInsight 群集可以直接从 Azure 存储帐户访问块 Blob，Application Insights 可配置为将遥测信息连续导出到 Azure 存储中的 Blob。但是，必须符合某些要求：

* __位置__：存储帐户必须与 HDInsight 位于同一区域中。这样可以在访问数据时减少延迟，避免在区域之间移动数据时发生出口流量费用。

* __Blob 类型__：HDInsight 仅支持块 Blob。Application Insights 默认为使用块 Blob，因此，默认情况下可配合 HDInsight 一起使用。

* __访问权限__：如果为 Application Insights 连续导出和 HDInsight 的默认存储使用相同的存储帐户，HDInsight 将对 Application Insights 遥测数据拥有完全访问权限。这意味着可以从 HDInsight 群集中删除遥测数据。

    与之相反，建议为 HDInsight 和 Application Insights 遥测使用不同的存储帐户，并[使用共享访问签名 (SAS) 来限制对 HDInsight 上数据的访问](/documentation/articles/hdinsight-storage-sharedaccesssignature-permissions/)。使用 SAS 可以授予对 HDInsight 遥测数据的只读访问权限。

### 数据架构

Application Insights 为导出到 Blob 的遥测数据格式提供[导出数据模型](/documentation/articles/app-insights-export-data-model/)信息。本文档中的步骤使用 Spark SQL 来处理数据。Spark SQL 可以自动生成 Application Insights 记录的 JSON 数据结构的架构，因此不需要在执行分析时手动定义架构。

## 导出遥测数据

根据 [Configure Continuous Export（配置连续导出）](/documentation/articles/app-insights-export-telemetry/)中的步骤配置 Application Insights，将遥测信息导出到 Azure 存储 Blob。

## 配置 HDInsight 以访问数据

根据 [Use Shared Access Signatures (SAS) to restrict access to the data from HDInsight（使用共享访问签名 (SAS) 来限制对 HDInsight 上数据的访问）](/documentation/articles/hdinsight-storage-sharedaccesssignature-permissions/)中的信息，为保存所导出遥测数据的 Blob 容器创建 SAS。SAS 应提供对数据的只读访问权限。

“共享访问签名”文档介绍了如何将 SAS 存储添加到现有的基于 Linux 的 HDInsight 群集。此外，还介绍了如何在创建新 HDInsight 群集时添加 SAS 存储。

## 使用 Python 分析数据 (PySpark)

1. 在 [Azure 门户](https://portal.azure.cn)中，选择 HDInsight 群集上的 Spark。在“快速链接”部分中，选择“群集仪表板”，然后从“群集仪表板”边栏选项卡中选择“Jupyter 笔记本”。

    ![群集仪表板](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)

2. 在 Jupyter 页面右上角选择“新建”，然后选择“PySpark”。此时将打开新的浏览器选项卡，其中包含基于 Python 的 Jupyter 笔记本。

3. 在页面上的第一个字段（称为__单元格__）中输入以下命令：

        sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')

    这样，Spark 便以递归方式访问输入数据的目录结构。Application Insights 遥测数据将记录到类似于下面的目录结构中：

        /{telemetry type}/YYYY-MM-DD/{##}/

4. 使用 __SHIFT + ENTER__ 运行代码。在单元格左侧，方括号之间出现“\*”即表示正在执行此单元格中的代码。完成后，“\*”将变成数字，单元格下面将显示类似于下面的输出：

        Creating SparkContext as 'sc'

        ID	YARN Application ID	Kind	State	Spark UI	Driver log	Current session?
        3	application_1468969497124_0001	pyspark	idle	Link	Link	✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. 在第一个单元格的下面创建了一个新单元格。在新单元格中输入以下命令。将 __CONTAINER__ 和 __STORAGEACCOUNT__ 替换为配置 Application Insights 连续导出时使用的 Azure 存储帐户名和 Blob 容器名称。

        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.chinacloudapi.cn/
    
    使用 __SHIFT + ENTER__ 执行此单元格中的命令。将显示类似于下面的输出：

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.chinacloudapi.cn/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    
    返回的 wasb 路径是 Application Insights 遥测数据的位置。将单元格中的 `hdfs dfs -ls` 行更改为使用返回的 wasb 路径，然后再次使用 __SHIFT + ENTER__ 执行单元格中的命令。这一次，结果应显示包含遥测数据的目录。

    > [AZURE.NOTE] 本部分中的余下步骤使用了 `wasb://appinsights@contosostore.blob.core.chinacloudapi.cn/contosoappinsights_{ID}/Requests` 目录。除非遥测数据用于 Web 应用，否则此目录可能并不存在。如果使用不包含请求目录的遥测数据，请选择其他目录，并将余下的步骤调整为使用该目录及其中存储的数据的架构。

6. 在下一个单元格中输入以下命令。将 __WASB\_PATH__ 替换为前一步骤中的路径。

        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)

    这会从通过连续导出过程导出的 JSON 文件创建新的数据框架。使用 __SHIFT + ENTER__ 运行此单元格中的命令。

7. 在下一个单元格中输入并运行以下命令，查看 Spark 为 JSON 文件创建的架构：

        jsonData.printSchema()
    
    每种类型的遥测数据的架构都不相同。下面是为 Web 请求生成的架构（数据存储在 `Requests` 子目录中）：

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. 使用以下命令将数据框架注册为临时表，然后针对数据运行查询：

        jsonData.registerTempTable("requests")
        sqlContext.sql("select context.location.city from requests where context.location.city is not null")

    此查询将返回 context.location.city 不为 null 的前 20 条记录的 city 信息。

    > [AZURE.NOTE] 上下文结构显示在 Application Insights 记录的所有遥测数据中；但是，日志中可能未填充 city 元素。使用架构识别可以查询的、可能包含日志数据的其他元素。

    此查询将返回类似于下面的信息：

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## 使用 Scala 分析数据

1. 在 [Azure 门户](https://portal.azure.cn)中，选择 HDInsight 群集上的 Spark。在“快速链接”部分中，选择“群集仪表板”，然后从“群集仪表板”边栏选项卡中选择“Jupyter 笔记本”。

    ![群集仪表板](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)

2. 在 Jupyter 页面右上角选择“新建”，然后选择“Scala”。此时将打开新的浏览器选项卡，其中包含基于 Scala 的 Jupyter 笔记本。

3. 在页面上的第一个字段（称为__单元格__）中输入以下命令：

        sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")

    这样，Spark 便以递归方式访问输入数据的目录结构。Application Insights 遥测数据将记录到类似于下面的目录结构中：

        /{telemetry type}/YYYY-MM-DD/{##}/

4. 使用 __SHIFT + ENTER__ 运行代码。在单元格左侧，方括号之间出现“\*”即表示正在执行此单元格中的代码。完成后，“\*”将变成数字，单元格下面将显示类似于下面的输出：

        Creating SparkContext as 'sc'

        ID	YARN Application ID	Kind	State	Spark UI	Driver log	Current session?
        3	application_1468969497124_0001	spark	idle	Link	Link	✔

        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...

5. 在第一个单元格的下面创建了一个新单元格。在新单元格中输入以下命令。将 __CONTAINER__ 和 __STORAGEACCOUNT__ 替换为配置 Application Insights 连续导出时使用的 Azure 存储帐户名和 Blob 容器名称。

        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.chinacloudapi.cn/
    
    使用 __SHIFT + ENTER__ 执行此单元格中的命令。将显示类似于下面的输出：

        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.chinacloudapi.cn/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    
    返回的 wasb 路径是 Application Insights 遥测数据的位置。将单元格中的 `hdfs dfs -ls` 行更改为使用返回的 wasb 路径，然后再次使用 __SHIFT + ENTER__ 执行单元格中的命令。这一次，结果应显示包含遥测数据的目录。

    > [AZURE.NOTE] 本部分中的余下步骤使用了 `wasb://appinsights@contosostore.blob.core.chinacloudapi.cn/contosoappinsights_{ID}/Requests` 目录。除非遥测数据用于 Web 应用，否则此目录可能并不存在。如果使用不包含请求目录的遥测数据，请选择其他目录，并将余下的步骤调整为使用该目录及其中存储的数据的架构。

6. 在下一个单元格中输入以下命令。将 __WASB\_PATH__ 替换为前一步骤中的路径。

        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)

    这会从通过连续导出过程导出的 JSON 文件创建新的数据框架。使用 __SHIFT + ENTER__ 运行此单元格中的命令。

7. 在下一个单元格中输入并运行以下命令，查看 Spark 为 JSON 文件创建的架构：

        jsonData.printSchema
    
    每种类型的遥测数据的架构都不相同。下面是为 Web 请求生成的架构（数据存储在 `Requests` 子目录中）：

        root
        |-- context: struct (nullable = true)
        |    |-- application: struct (nullable = true)
        |    |    |-- version: string (nullable = true)
        |    |-- custom: struct (nullable = true)
        |    |    |-- dimensions: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |    |-- metrics: array (nullable = true)
        |    |    |    |-- element: string (containsNull = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- eventTime: string (nullable = true)
        |    |    |-- isSynthetic: boolean (nullable = true)
        |    |    |-- samplingRate: double (nullable = true)
        |    |    |-- syntheticSource: string (nullable = true)
        |    |-- device: struct (nullable = true)
        |    |    |-- browser: string (nullable = true)
        |    |    |-- browserVersion: string (nullable = true)
        |    |    |-- deviceModel: string (nullable = true)
        |    |    |-- deviceName: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- osVersion: string (nullable = true)
        |    |    |-- type: string (nullable = true)
        |    |-- location: struct (nullable = true)
        |    |    |-- city: string (nullable = true)
        |    |    |-- clientip: string (nullable = true)
        |    |    |-- continent: string (nullable = true)
        |    |    |-- country: string (nullable = true)
        |    |    |-- province: string (nullable = true)
        |    |-- operation: struct (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |-- session: struct (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- isFirst: boolean (nullable = true)
        |    |-- user: struct (nullable = true)
        |    |    |-- anonId: string (nullable = true)
        |    |    |-- isAuthenticated: boolean (nullable = true)
        |-- internal: struct (nullable = true)
        |    |-- data: struct (nullable = true)
        |    |    |-- documentVersion: string (nullable = true)
        |    |    |-- id: string (nullable = true)
        |-- request: array (nullable = true)
        |    |-- element: struct (containsNull = true)
        |    |    |-- count: long (nullable = true)
        |    |    |-- durationMetric: struct (nullable = true)
        |    |    |    |-- count: double (nullable = true)
        |    |    |    |-- max: double (nullable = true)
        |    |    |    |-- min: double (nullable = true)
        |    |    |    |-- sampledValue: double (nullable = true)
        |    |    |    |-- stdDev: double (nullable = true)
        |    |    |    |-- value: double (nullable = true)
        |    |    |-- id: string (nullable = true)
        |    |    |-- name: string (nullable = true)
        |    |    |-- responseCode: long (nullable = true)
        |    |    |-- success: boolean (nullable = true)
        |    |    |-- url: string (nullable = true)
        |    |    |-- urlData: struct (nullable = true)
        |    |    |    |-- base: string (nullable = true)
        |    |    |    |-- hashTag: string (nullable = true)
        |    |    |    |-- host: string (nullable = true)
        |    |    |    |-- protocol: string (nullable = true)

8. 使用以下命令将数据框架注册为临时表，然后针对数据运行查询：

        jsonData.registerTempTable("requests")
        var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()

    此查询将返回 context.location.city 不为 null 的前 20 条记录的 city 信息。

    > [AZURE.NOTE] 上下文结构显示在 Application Insights 记录的所有遥测数据中；但是，日志中可能未填充 city 元素。使用架构识别可以查询的、可能包含日志数据的其他元素。

    此查询将返回类似于下面的信息：

        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## 后续步骤

有关使用 Spark 处理 Azure 中数据和服务的更多示例，请参阅以下文档：

* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](/documentation/articles/hdinsight-apache-spark-use-bi-tools/)

* [Spark 和机器学习：使用 HDInsight 中的 Spark 对使用 HVAC 数据生成温度进行分析](/documentation/articles/hdinsight-apache-spark-ipython-notebook-machine-learning/)

* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](/documentation/articles/hdinsight-apache-spark-machine-learning-mllib-ipython/)

* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](/documentation/articles/hdinsight-apache-spark-eventhub-streaming/)

* [使用 HDInsight 中的 Spark 分析网站日志](/documentation/articles/hdinsight-apache-spark-custom-library-website-log-analysis/)

有关创建和运行 Spark 应用程序的信息，请参阅以下文档：

* [使用 Scala 创建独立的应用程序](/documentation/articles/hdinsight-apache-spark-create-standalone-application/)

* [使用 Livy 在 Spark 群集中远程运行作业](/documentation/articles/hdinsight-apache-spark-livy-rest-interface/)

<!---HONumber=Mooncake_0912_2016-->