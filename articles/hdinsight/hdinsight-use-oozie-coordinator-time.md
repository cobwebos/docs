---
title: "在 HDInsight 中使用基于时间的 Hadoop Oozie 协调器 | Microsoft Docs"
description: "在 HDInsight 中使用基于时间的 Hadoop Oozie 协调器（大数据服务）。 了解如何定义 Oozie 工作流和协调器，并提交作业。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00c3a395-d51a-44ff-af2d-1f116c4b1c83
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 709541a88b2b15924b93c630693ad7a3f276e29c
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="use-time-based-oozie-coordinator-with-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>将基于时间的 Oozie 协调器与 HDInsight 中的 Hadoop 配合使用以定义工作流和协调作业
在本文中，学习如何定义工作流和协调器，以及如何基于时间触发协调器作业。 阅读本文前，浏览[将 Oozie 与 HDInsight 配合使用][hdinsight-use-oozie]很有帮助。 除了 Oozie，还可以使用 Azure 数据工厂计划作业。 要了解 Azure 数据工厂，请参阅[将 Pig 和 Hive 用于数据工厂](../data-factory/transform-data.md)。

> [!NOTE]
> 本文需要基于 Windows 的 HDInsight 群集。 有关在基于 Linux 的群集上使用 Oozie 的信息，包括基于时间的作业，请参阅[在基于 Linux 的 HDInsight 上将 Oozie 与 Hadoop 配合使用以定义和运行工作流](hdinsight-use-oozie-linux-mac.md)

## <a name="what-is-oozie"></a>什么是 Oozie
Apache Oozie 是一个管理 Hadoop 作业的工作流/协调系统。 它与 Hadoop 堆栈集成，支持 Apache MapReduce、Apache Pig、Apache Hive 和 Apache Sqoop 的 Hadoop 作业。 它也能用于安排特定于某系统的作业，例如 Java 程序或 shell 脚本。

下图显示将要实施的工作流：

![工作流关系图][img-workflow-diagram]

工作流包含两个操作：

1. Hive 操作运行 HiveQL 脚本以统计 log4j 日志文件中每个日志级类型的次数。 每个 log4j 日志都包含一行字段，其中包含 [LOG LEVEL] 字段，可显示类型和严重性，例如：

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    该 Hive 脚本的输出结果类似于：

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    有关 Hive 的详细信息，请参阅[将 Hive 与 HDInsight 配合使用][hdinsight-use-hive]。
2. Sqoop 操作将 HiveQL 操作输出结果导出到 Azure SQL 数据库中的表。 有关 Sqoop 的详细信息，请参阅[将 Sqoop 与 HDInsight 配合使用][hdinsight-use-sqoop]。

> [!NOTE]
> 有关 HDInsight 群集上支持的 Oozie 版本，请参阅 [HDInsight 提供的群集版本有哪些新功能？][hdinsight-versions]。
>
>

## <a name="prerequisites"></a>先决条件
在开始阅读本教程前，必须具有：

* **配备 Azure PowerShell 的工作站**。

    > [!IMPORTANT]
    > Azure PowerShell 支持使用 Azure Service Manager 管理 HDInsight 资源，但**不建议使用**，而且将于 2017 年 1 月 1 日前删除。 本文档中的步骤使用的是与 Azure Resource Manager 兼容的新 HDInsight cmdlet。
    >
    > 请按照 [安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs) 中的步骤安装最新版本的 Azure PowerShell。 如果脚本需要修改后才能使用与 Azure Resource Manager 兼容的新 cmdlet，请参阅[迁移到适用于 HDInsight 群集的基于 Azure Resource Manager 的开发工具](hdinsight-hadoop-development-using-azure-resource-manager.md)，了解详细信息。

* **一个 HDInsight 群集**。 有关创建 HDInsight 群集的信息，请参阅[创建 HDInsight 群集][hdinsight-provision]或 [HDInsight 入门][hdinsight-get-started]。 需要以下数据才能完成本教程：

    <table border = "1">
    <tr><th>群集属性</th><th>Windows PowerShell 变量名</th><th>值</th><th>说明</th></tr>
    <tr><td>HDInsight 群集名称</td><td>$clusterName</td><td></td><td>要在其中运行本教程的 HDInsight 群集。</td></tr>
    <tr><td>HDInsight 群集用户名</td><td>$clusterUsername</td><td></td><td>HDInsight 群集用户名。 </td></tr>
    <tr><td>HDInsight 群集用户的密码 </td><td>$clusterPassword</td><td></td><td>HDInsight 群集用户的密码。</td></tr>
    <tr><td>Azure 存储帐户名称</td><td>$storageAccountName</td><td></td><td>可用于 HDInsight 群集的 Azure 存储帐户。 在本教程中，使用在群集设置过程中指定的默认存储帐户。</td></tr>
    <tr><td>Azure Blob 容器名称</td><td>$containerName</td><td></td><td>在此示例中，使用用于默认 HDInsight 群集文件系统的 Azure Blob 存储容器。 默认情况下，该容器与 HDInsight 群集同名。</td></tr>
    </table>
* **Azure SQL 数据库**。 必须为 SQL 数据库服务器配置防火墙规则以允许从工作站进行访问。 有关创建 Azure SQL 数据库和配置防火墙的说明，请参阅使用 [Azure SQL 数据库入门][sqldatabase-get-started]。 本文提供了用于创建本教程所需的 Azure SQL 数据库表的 Windows PowerShell 脚本。

    <table border = "1">
    <tr><th>SQL 数据库属性</th><th>Windows PowerShell 变量名</th><th>值</th><th>说明</th></tr>
    <tr><td>SQL 数据库服务器名称</td><td>$sqlDatabaseServer</td><td></td><td>Sqoop 要将数据导出到其中的 SQL 数据库服务器。 </td></tr>
    <tr><td>SQL 数据库登录名</td><td>$sqlDatabaseLogin</td><td></td><td>SQL 数据库登录名。</td></tr>
    <tr><td>SQL 数据库登录密码</td><td>$sqlDatabaseLoginPassword</td><td></td><td>SQL 数据库登录密码。</td></tr>
    <tr><td>SQL 数据库名</td><td>$sqlDatabaseName</td><td></td><td>Sqoop 要将数据导出到其中的 Azure SQL 数据库。 </td></tr>
    </table>

  > [!NOTE]
  > 默认情况下，可以从 Azure HDInsight 这样的 Azure 服务连接 Azure SQL 数据库。 如果禁用了此防火墙设置，则必须从 Azure 门户启用它。 有关创建 SQL 数据库和配置防火墙规则的说明，请参阅[创建和配置 SQL 数据库][sqldatabase-get-started]。

> [!NOTE]
> 将值填充到表中。 这会有助于学习本教程。

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>定义 Oozie 工作流及相关 HiveQL 脚本
Oozie 工作流定义是用 hPDL（一种 XML 过程定义语言）编写的。 默认的工作流文件名为 *workflow.xml*。  将在本地保存该工作流文件，并会在本教程后面使用 Azure PowerShell 将它部署到 HDInsight 群集。

该工作流中的 Hive 操作调用 HiveQL 脚本文件。 此脚本文件包含三个 HiveQL 语句：

1. **DROP TABLE 语句**删除 log4j Hive 表（如果存在）。
2. **CREATE TABLE 语句**创建一个 log4j Hive 外部表，该表指向 log4j 日志文件的位置；
3. **log4j 日志文件的位置**。 字段分隔符为“,”。 默认分行符为“\n”。 假如需要多次运行 Oozie 工作流，可使用 Hive 外部表避免从原始位置删除数据文件。
4. **INSERT OVERWRITE 语句**从 log4j Hive 表统计每个日志级类型的次数，并将输出结果保存到 Azure Blob 存储位置。

> [!NOTE]
> 有一个已知的 Hive 路径问题。 在提交 Oozie 作业时会遇到这个问题。 可在 TechNet Wiki 上找到解决此问题的说明：[HDInsight Hive 错误：无法重命名][technetwiki-hive-error]。

**将 HiveQL 脚本文件定义为由工作流调用**

1. 创建一个内容如下的文本文件：

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    该脚本中使用了三个变量：

   * ${hiveTableName}
   * ${hiveDataFolder}
   * ${hiveOutputFolder}

     工作流定义文件（本教程中的 workflow.xml）在运行时会将三个值传递到这个 HiveQL 脚本。
2. 使用 ANSI(ASCII) 编码将文件另存为 **C:\Tutorials\UseOozie\useooziewf.hql**。 （如果文本编辑器不提供此选项，则使用记事本。）在本教程的后面，此脚本文件会被部署到 HDInsight 群集。

**定义工作流**

1. 创建一个内容如下的文本文件：

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>

        <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.job.queue.name</name>
                        <value>${queueName}</value>
                    </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
                <param>hiveOutputFolder=${hiveOutputFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>

        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.compress.map.output</name>
                        <value>true</value>
                    </property>
                </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveOutputFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\001"</arg>
            </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>

        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>

        <end name="end"/>
    </workflow-app>
    ```

    该工作流中定义了两个操作。 start-to 操作是 *RunHiveScript*。 如果该操作运行*正常OK*，则下一个操作是 *RunSqoopExport*。

    RunHiveScript 有几个变量。 在从工作站使用 Azure PowerShell 提交 Oozie 作业时，会传递值。

    工作流变量

    <table border = "1">
    <tr><th>工作流变量</th><th>说明</th></tr>
    <tr><td>${jobTracker}</td><td>指定 Hadoop 作业跟踪器的 URL。 在 HDInsight 群集版本 2.0 和 3.0 上使用 <strong>jobtrackerhost:9010</strong>。</td></tr>
    <tr><td>${nameNode}</td><td>指定 Hadoop 名称节点的 URL。 请使用默认的文件系统 wasb:// 地址，例如 <i>wasb://&lt;containerName&gt;@&lt;storageAccountName&gt;.blob.core.windows.net</i>。</td></tr>
    <tr><td>${queueName}</td><td>指定要将作业提交到的队列名称。 使用“默认”<strong></strong>。</td></tr>
    </table>

    Hive 操作变量

    <table border = "1">
    <tr><th>Hive 操作变量</th><th>说明</th></tr>
    <tr><td>${hiveDataFolder}</td><td>Hive Create Table 命令的源目录。</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>INSERT OVERWRITE 语句的输出文件夹。</td></tr>
    <tr><td>${hiveTableName}</td><td>引用 log4j 数据文件的 Hive 表的名称。</td></tr>
    </table>

    Sqoop 操作变量

    <table border = "1">
    <tr><th>Sqoop 操作变量</th><th>说明</th></tr>
    <tr><td>${sqlDatabaseConnectionString}</td><td>SQL 数据库连接字符串。</td></tr>
    <tr><td>${sqlDatabaseTableName}</td><td>数据将要导出到的 Azure SQL 数据库表。</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>Hive INSERT OVERWRITE 语句的输出文件夹。 这是用于 Sqoop 导出 (export-dir) 的同一个文件夹。</td></tr>
    </table>

    有关 Oozie 工作流和使用工作流操作的详细信息，请参阅 [Apache Oozie 4.0 文档][apache-oozie-400]（对于 HDInsight 群集 3.0 版）或 [Apache Oozie 3.3.2 文档][apache-oozie-332]（对于 HDInsight 群集 2.1 版）。

1. 使用 ANSI (ASCII) 编码将文件另存为 **C:\Tutorials\UseOozie\workflow.xml**。 （如果文本编辑器不提供此选项，则使用记事本。）

**定义协调器**

1. 创建一个内容如下的文本文件：

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
            <workflow>
                <app-path>${wfPath}</app-path>
            </workflow>
        </action>
    </coordinator-app>
    ```

    该定义文件中使用了五个变量：

   | 变量 | 说明 |
   | --- | --- |
   | ${coordFrequency} |作业暂停时间。 频率总是用分钟来表示的。 |
   | ${coordStart} |作业开始时间。 |
   | ${coordEnd} |作业结束时间。 |
   | ${coordTimezone} |Oozie 在没有夏时制的固定时区（通常用 UTC 表示）处理协调器作业。 此时区被称为“Oozie 处理时区”。 |
   | ${wfPath} |workflow.xml 的路径。  如果该工作流文件名不是默认文件名 (workflow.xml)，则必须指定该名称。 |
2. 使用 ANSI (ASCII) 编码将文件另存为 **C:\Tutorials\UseOozie\coordinator.xml**。 （如果文本编辑器不提供此选项，则使用记事本。）

## <a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>部署 Oozie 项目并准备教程
将运行 Azure PowerShell 脚本来执行以下操作：

* 将 HiveQL 脚本 (useoozie.hql) 复制到 Azure Blob 存储 (wasb:///tutorials/useoozie/useoozie.hql)。
* 将 workflow.xml 复制到 wasb:///tutorials/useoozie/workflow.xml。
* 将 coordinator.xml 复制到 wasb:///tutorials/useoozie/coordinator.xml。
* 将数据文件 (/example/data/sample.log) 复制到 wasb:///tutorials/useoozie/data/sample.log。
* 创建用于存储 Sqoop 导出数据的 Azure SQL 数据库表。 表的名称为 *log4jLogCount*。

**了解 HDInsight 存储**

HDInsight 将 Azure Blob 存储用于数据存储。 在 Azure Blob 存储中，wasb:// 是 Microsoft 的 Hadoop 分布式文件系统 (HDFS) 的实现。 有关详细信息，请参阅将 [Azure Blob 存储与 HDInsight 配合使用][hdinsight-storage]。

设置 HDInsight 群集时，请将 Azure Blob 存储帐户和该帐户上的特定容器指定为默认文件系统，就像在 HDFS 中一样。 除了此存储帐户外，在设置过程中，还可以从同一 Azure 订阅或不同 Azure 订阅添加其他存储帐户。 有关添加其他存储帐户的说明，请参阅[设置 HDInsight 群集][hdinsight-provision]。 为了简化本教程中使用的 Azure PowerShell 脚本，所有文件都存储在默认文件系统容器（位于 */tutorials/useoozie*）中。 默认情况下，此容器与 HDInsight 群集同名。
语法为：

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [!NOTE]
> HDInsight 群集 3.0 版只支持 *wasb://* 语法。 较早的 *asv://* 语法在 HDInsight 2.1 和 1.6 群集中受支持，但在 HDInsight 3.0 群集中不受支持。
>
> wasb:// 路径是虚拟路径。 有关详细信息，请参阅将 [Azure Blob 存储与 HDInsight 配合使用][hdinsight-storage]。

存储在默认文件系统容器中的文件可以使用以下任一 URI 从 HDInsight 进行访问（以 workflow.xml 为例）：

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

如果要从存储帐户直接访问该文件，则请注意，该文件的 Blob 名称是：

    tutorials/useoozie/workflow.xml

**了解 Hive 内部表和外部表**

以下是需要了解的有关 Hive 内部表和外部表的一些信息：

* CREATE TABLE 命令创建内部表，也称为托管表。 数据文件必须位于默认容器中。
* CREATE TABLE 命令将数据文件移动到默认容器中的 /hive/warehouse/<TableName> 文件夹。
* CREATE EXTERNAL TABLE 命令创建外部表。 数据文件可以位于默认容器以外的位置。
* CREATE EXTERNAL TABLE 命令不移动数据文件。
* CREATE EXTERNAL TABLE 命令不允许 LOCATION 子句中指定的文件夹下有任何子文件夹。 这是本教程生成 sample.log 文件的副本的原因。

有关详细信息，请参阅 [HDInsight：Hive 内部和外部表简介][cindygross-hive-tables]。

**准备教程**

1. 打开 Windows PowerShell ISE（在 Windows 8“开始”屏幕上，键入 **PowerShell_ISE**，并单击“Windows PowerShell ISE”。 有关详细信息，请参阅[在 Windows 8 和 Windows 上启动 Windows PowerShell][powershell-start]）。
2. 在底部窗格中，运行以下命令以连接到 Azure 订阅：

    ```powershell
    Add-AzureAccount
    ```

    系统会提示输入 Azure 帐户凭据。 添加订阅连接的这种方法超时，需要在 12 小时后重新运行该 cmdlet。

   > [!NOTE]
   > 如果有多个 Azure 订阅，而默认订阅不是想使用的，则请使用 <strong>Select-AzureSubscription</strong> cmdlet 来选择订阅。

3. 将以下脚本复制到脚本窗格，并设置前六个变量：

    ```powershell
    # WASB variables
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<BlobStorageContainerName>"

    # SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    # Oozie files for the tutorial
    $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
    $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
    $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here
    ```

    有关这些变量的详细说明，请参阅本教程中的[先决条件](#prerequisites)部分。

4. 在脚本窗格中将以下内容追加到脚本：

    ```powershell
    # Create a storage context object
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    function uploadOozieFiles()
    {
        Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
        Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
        Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
        Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
    }

    function prepareHiveDataFile()
    {
        Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
        Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
    }

    function prepareSQLDatabase()
    {
        # SQL query string for creating log4jLogsCount table
        $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [Level] [nvarchar](10) NOT NULL,
                [Total] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
            (
            [Level] ASC
            )
            )"

        #Create the log4jLogsCount table
        Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $cmdCreateLog4jCountTable
        $cmd.executenonquery()

        $conn.close()
    }

    # upload workflow.xml, coordinator.xml, and ooziewf.hql
    uploadOozieFiles;

    # make a copy of example/data/sample.log to example/data/log4j/sample.log
    prepareHiveDataFile;

    # create log4jlogsCount table on SQL database
    prepareSQLDatabase;
    ```

5. 单击“运行脚本” 或按 **F5** 以运行该脚本。 输出结果会类似于：

    ![教程准备的输出结果][img-preparation-output]

## <a name="run-the-oozie-project"></a>运行 Oozie 项目
Azure PowerShell 目前不提供任何用于定义 Oozie 作业的 cmdlet。 可以使用 **Invoke-RestMethod** cmdlet 来调用 Oozie Web 服务。 Oozie Web 服务 API 是 HTTP REST JSON API。 有关 Oozie Web 服务 API 的详细信息，请参阅 [Apache Oozie 4.0 文档][apache-oozie-400]（对于 HDInsight 群集 3.0 版）或 [Apache Oozie 3.3.2 文档][apache-oozie-332]（对于 HDInsight 群集 2.1 版）。

**提交 Oozie 作业**

1. 打开 Windows PowerShell ISE（在 Windows 8“开始”屏幕上，键入 **PowerShell_ISE**，并单击“Windows PowerShell ISE”。 有关详细信息，请参阅[在 Windows 8 和 Windows 上启动 Windows PowerShell][powershell-start]）。
2. 将以下脚本复制到脚本窗格，并设置前 14 个变量（不过，请跳过 **$storageUri**）。

    ```powershell
    #HDInsight cluster variables
    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterUserPassword>"

    #Azure Blob storage (WASB) variables
    $storageAccountName = "<StorageAccountName>"
    $storageContainerName = "<BlobContainerName>"
    $storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"

    #Azure SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"

    #Oozie WF/coordinator variables
    $coordStart = "2014-03-21T13:45Z"
    $coordEnd = "2014-03-21T13:45Z"
    $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
    $coordTimezone = "UTC"    #UTC/GMT

    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10

    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

    #Sqoop action variables
    $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
    $sqlDatabaseTableName = "log4jLogsCount"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
    ```

    有关这些变量的详细说明，请参阅本教程中的[先决条件](#prerequisites)部分。

    $coordstart 和 $coordend 是工作流的开始和结束时间。 若要了解 UTC/GMT 时间，请在 bing.com 上搜索“utc 时间”。$coordFrequency 是指要运行工作流的频率（以分钟为单位）。
3. 将以下内容追加到脚本。 这部分定义 Oozie 负载：

    ```powershell
    #OoziePayload used for Oozie web service submission
    $OoziePayload =  @"
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
            <name>nameNode</name>
            <value>$storageUrI</value>
        </property>

        <property>
            <name>jobTracker</name>
            <value>jobtrackerhost:9010</value>
        </property>

        <property>
            <name>queueName</name>
            <value>default</value>
        </property>

        <property>
            <name>oozie.use.system.libpath</name>
            <value>true</value>
        </property>

        <property>
            <name>oozie.coord.application.path</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>wfPath</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>coordStart</name>
            <value>$coordStart</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>$coordEnd</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>$coordFrequency</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>$coordTimezone</value>
        </property>

        <property>
            <name>hiveScript</name>
            <value>$hiveScript</value>
        </property>

        <property>
            <name>hiveTableName</name>
            <value>$hiveTableName</value>
        </property>

        <property>
            <name>hiveDataFolder</name>
            <value>$hiveDataFolder</value>
        </property>

        <property>
            <name>hiveOutputFolder</name>
            <value>$hiveOutputFolder</value>
        </property>

        <property>
            <name>sqlDatabaseConnectionString</name>
            <value>&quot;$sqlDatabaseConnectionString&quot;</value>
        </property>

        <property>
            <name>sqlDatabaseTableName</name>
            <value>$SQLDatabaseTableName</value>
        </property>

        <property>
            <name>user.name</name>
            <value>admin</value>
        </property>

    </configuration>
    "@
    ```

   > [!NOTE]
   > 与工作流提交有效负载文件相比，主要区别是变量 **oozie.coord.application.path**。 在提交工作流作业时，使用的是 **oozie.wf.application.path**。

4. 将以下内容追加到脚本。 这部分检查 Oozie Web 服务状态：

    ```powershell
    function checkOozieServerStatus()
    {
        Write-Host "Checking Oozie server status..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieServerSatus = $jsonResponse[0].("systemMode")
        Write-Host "Oozie server status is $oozieServerSatus..."

        if($oozieServerSatus -notmatch "NORMAL")
        {
            Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
            exit 1
        }
    }
    ```

5. 将以下内容追加到脚本。 这部分创建一项 Oozie 作业：

    ```powershell
    function createOozieJob()
    {
        # create Oozie job
        Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
        Write-Host "`n--------`n$OoziePayload`n--------"
        $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieJobId = $jsonResponse[0].("id")
        Write-Host "Oozie job id is $oozieJobId..."

        return $oozieJobId
    }
    ```

   > [!NOTE]
   > 在提交工作流作业时，必须在创建作业后进行另一次 Web 服务调用以启动该作业。 在这种情况下，该协调器作业会按时间触发。 该作业会自动启动。

6. 将以下内容追加到脚本。 这部分检查 Oozie 作业状态：

    ```powershell
    function checkOozieJobStatus($oozieJobId)
    {
        # get job status
        Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

        Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
        $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")

        while($JobStatus -notmatch "SUCCEEDED|KILLED")
        {
            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")
        }

        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
        if($JobStatus -notmatch "SUCCEEDED")
        {
            Write-Host "Check logs at http://headnode0:9014/cluster for detais."
            exit -1
        }
    }
    ```

7. （可选）将以下内容追加到脚本。

    ```powershell
    function listOozieJobs()
    {
        Write-Host "Listing Oozie jobs..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

        write-host "Job ID                                   App Name        Status      Started                         Ended"
        write-host "----------------------------------------------------------------------------------------------------------------------------------"
        foreach($job in $response.workflows)
        {
            Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
        }
    }

    function ShowOozieJobLog($oozieJobId)
    {
        Write-Host "Showing Oozie job info..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
        write-host $response
    }

    function killOozieJob($oozieJobId)
    {
        Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
        $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
        $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
    }
    ```

8. 将以下内容追加到脚本：

    ```powershell
    checkOozieServerStatus
    # listOozieJobs
    $oozieJobId = createOozieJob($oozieJobId)
    checkOozieJobStatus($oozieJobId)
    # ShowOozieJobLog($oozieJobId)
    # killOozieJob($oozieJobId)
    ```

    如果要运行这些附加的功能，请删除这些 # 号。
9. 如果 HDinsight 群集是 2.1 版的，请将“https://$clusterName.azurehdinsight.net:443/oozie/v2/”替换为“https://$clusterName.azurehdinsight.net:443/oozie/v1/”。 HDInsight 群集版本 2.1 不支持 Web 服务的版本 2。
10. 单击“运行脚本” 或按 **F5** 以运行该脚本。 输出结果会类似于：

     ![教程运行工作流输出][img-runworkflow-output]
11. 连接到 SQL 数据库以查看导出的数据。

**检查作业错误日志**

若要解决工作流的疑难问题，可从群集头节点中的 C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log 位置找到 Oozie 日志文件。 有关 RDP 的信息，请参阅[使用 Azure 门户管理 HDInsight 群集][hdinsight-admin-portal]。

**重新运行教程**

若要重新运行该工作流，必须执行以下任务：

* 删除 Hive 脚本输出文件。
* 删除 log4jLogsCount 表中的数据。

这是可以使用的一个示例 Windows PowerShell 脚本：

```powershell
$storageAccountName = "<AzureStorageAccountName>"
$containerName = "<ContainerName>"

#SQL database variables
$sqlDatabaseServer = "<SQLDatabaseServerName>"
$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
$sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
$sqlDatabaseName = "<SQLDatabaseName>"
$sqlDatabaseTableName = "log4jLogsCount"

Write-host "Delete the Hive script output file ..." -ForegroundColor Green
$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
$conn.open()
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.connection = $conn
$cmd.commandtext = "delete from $sqlDatabaseTableName"
$cmd.executenonquery()

$conn.close()
```

## <a name="next-steps"></a>后续步骤
在本教程中，已经学习了如何定义 Oozie 工作流、Oozie 协调器，以及如何使用 Azure PowerShell 运行 Oozie 协调器作业。 若要了解更多信息，请参阅下列文章：

* [HDInsight 入门][hdinsight-get-started]
* [将 Azure Blob 存储与 HDInsight 配合使用][hdinsight-storage]
* [使用 Azure PowerShell 管理 HDInsight][hdinsight-admin-powershell]
* [将数据上传到 HDInsight][hdinsight-upload-data]
* [将 Sqoop 与 HDInsight 配合使用][hdinsight-use-sqoop]
* [将 Hive 与 HDInsight 配合使用][hdinsight-use-hive]
* [将 Pig 与 HDInsight 配合使用][hdinsight-use-pig]
* [为 HDInsight 开发 Java MapReduce 程序][hdinsight-develop-java-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

