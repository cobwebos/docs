---
title: 将 Apache Beeline 与 Apache Hive 配合使用 - Azure HDInsight
description: 了解如何使用 Beeline 客户端通过 Hadoop on HDInsight 运行 Hive 查询。 Beeline 是用于通过 JDBC 操作 HiveServer2 的实用工具。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/21/2020
ms.custom: contperfq1
ms.openlocfilehash: f6d8f804fa26383435d191af27289ffd2ecb3e0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88755086"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>将 Apache Beeline 客户端与 Apache Hive 配合使用

了解如何使用 [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) 在 HDInsight 上运行 Apache Hive 查询。

Beeline 是一个 Hive 客户端，包含在 HDInsight 群集的头节点上。 要连接到 HDInsight 群集上安装的 Beeline 客户端，或在本地安装 Beeline，请参阅[连接到或安装 Apache Beeline](connect-install-beeline.md)。 Beeline 使用 JDBC 连接到 HiveServer2，后者是 HDInsight 群集上托管的一项服务。 还可以使用 Beeline 通过 Internet 远程访问 Hive on HDInsight。 以下示例提供用于从 Beeline 连接到 HDInsight 的最常见连接字符串。

## <a name="prerequisites-for-examples"></a>先决条件示例

* HDInsight 上的 Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](./apache-hadoop-linux-tutorial-get-started.md)。

* 请记下群集主存储的 URI 方案。 例如，对于 Azure 存储，此值为 `wasb://`；对于Azure Data Lake Storage Gen2，此值为 `abfs://`；对于 Azure Data Lake Storage Gen1，此值为 `adl://`。 如果为 Azure 存储启用了安全传输，则 URI 将为 `wasbs://`。 有关详细信息，请参阅[安全传输](../../storage/common/storage-require-secure-transfer.md)。

* 选项 1：SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。 本文档中的大多数步骤都假设使用的是从 SSH 会话到群集的 Beeline。

* 选项 2：本地 Beeline 客户端。

## <a name="run-a-hive-query"></a>运行 Hive 查询

此示例的基础是通过 SSH 连接使用 Beeline 客户端。

1. 使用以下代码打开到群集的 SSH 连接。 将 `sshuser` 替换为群集的 SSH 用户，并将 `CLUSTERNAME` 替换为群集的名称。 出现提示时，输入 SSH 用户帐户的密码。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 输入以下命令，通过 Beeline 客户端从打开的 SSH 会话连接到 HiveServer2：

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline 命令以 `!` 字符开头，例如，`!help` 显示帮助。 但是，`!` 对于某些命令可以省略。 例如，`help` 也是有效的。

    有 `!sql`，用于执行 HiveQL 语句。 但是，由于 HiveQL 非常流行，因此可以省略前面的 `!sql`。 以下两个语句等效：

    ```hiveql
    !sql show tables;
    show tables;
    ```

    在新群集上，只会列出一个表：**hivesampletable**。

4. 使用以下命令显示 hivesampletable 的架构：

    ```hiveql
    describe hivesampletable;
    ```

    此命令返回以下信息：

    ```output
    +-----------------------+------------+----------+--+
    |       col_name        | data_type  | comment  |
    +-----------------------+------------+----------+--+
    | clientid              | string     |          |
    | querytime             | string     |          |
    | market                | string     |          |
    | deviceplatform        | string     |          |
    | devicemake            | string     |          |
    | devicemodel           | string     |          |
    | state                 | string     |          |
    | country               | string     |          |
    | querydwelltime        | double     |          |
    | sessionid             | bigint     |          |
    | sessionpagevieworder  | bigint     |          |
    +-----------------------+------------+----------+--+
    ```

    此信息描述表中的列。

5. 输入以下语句，以使用 HDInsight 群集随附的示例数据来创建名为 **log4jLogs** 的表：（基于 URI 方案根据需要进行修改。）

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    这些语句执行以下操作：

    |语句 |说明 |
    |---|---|
    |DROP TABLE|如果该表存在，则将其删除。|
    |CREATE EXTERNAL TABLE|在 Hive 中创建一个**外部**表。 外部表只会在 Hive 中存储表定义。 数据保留在原始位置。|
    |ROW FORMAT|如何设置数据的格式。 在此情况下，每个日志中的字段以空格分隔。|
    |STORED AS TEXTFILE LOCATION|数据存储的位置和文件格式。|
    |SELECT|选择 **t4** 列包含值 **[ERROR]** 的所有行的计数。 此查询返回值 **3**，因为有三行包含此值。|
    |INPUT__FILE__NAME LIKE '%.log'|Hive 会尝试对目录中的所有文件应用架构。 在此示例中，目录包含与架构不匹配的文件。 为防止结果中包含垃圾数据，此语句指示 Hive 应当仅返回以 .log 结尾的文件中的数据。|

   > [!NOTE]  
   > 如果希望通过外部源更新基础数据，应使用外部表。 例如，自动化数据上传进程或 MapReduce 操作。
   >
   > 删除外部表**不会**删除数据，只会删除表定义。

    此命令的输出类似于以下文本：

    ```output
    INFO  : Tez session hasn't been created yet. Opening session
    INFO  :

    INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

    INFO  : Map 1: -/-      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
    INFO  : Map 1: 1/1      Reducer 2: 1/1
    +----------+--------+--+
    |   sev    | count  |
    +----------+--------+--+
    | [ERROR]  | 3      |
    +----------+--------+--+
    1 row selected (47.351 seconds)
    ```

6. 退出 Beeline：

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>运行 HiveQL 文件

本示例是上一示例的延续部分。 使用以下步骤创建文件，并使用 Beeline 运行该文件。

1. 使用以下命令创建一个名为 **query.hql** 的文件：

    ```bash
    nano query.hql
    ```

1. 将以下文本用作文件的内容。 此查询创建名为 **errorLogs** 的新“内部”表：

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    这些语句执行以下操作：

    |语句 |说明 |
    |---|---|
    |CREATE TABLE IF NOT EXISTS|如果该表尚不存在，则创建它。 由于未使用 **EXTERNAL** 关键字，因此此语句会创建内部表。 内部表存储在 Hive 数据仓库中，由 Hive 全权管理。|
    |STORED AS ORC|以优化的行纵栏式 (ORC) 格式存储数据。 ORC 格式是高度优化且有效的 Hive 数据存储格式。|
    |INSERT OVERWRITE ...SELECT|从包含 **[ERROR]** 的 **log4jLogs** 表中选择行，然后将数据插入 **errorLogs** 表中。|

    > [!NOTE]  
    > 与外部表不同，删除内部表会同时删除基础数据。

1. 如果要保存文件，请使用 Ctrl+X，并输入 Y，最后按 Enter   。

1. 使用以下命令通过 Beeline 运行该文件：

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i` 参数启动 Beeline，并运行 `query.hql` 文件中的语句。 查询完成后，会出现 `jdbc:hive2://headnodehost:10001/>` 提示符。 还可以使用 `-f` 参数运行文件，该参数在查询完成后会退出 Beeline。

1. 若要验证是否已创建 **errorLogs** 表，请使用以下语句从 **errorLogs** 返回所有行：

    ```hiveql
    SELECT * from errorLogs;
    ```

    应返回三行数据，所有行都包含 t4 列中的 **[ERROR]** ：

    ```output
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    3 rows selected (0.813 seconds)
    ```

## <a name="next-steps"></a>后续步骤

* 有关 HDInsight 中的 Hive 的更多常规信息，请参阅[将 Apache Hive 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)

* 若要深入了解使用 Hadoop on HDInsight 的其他方法，请参阅[将 MapReduce 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-mapreduce.md)
