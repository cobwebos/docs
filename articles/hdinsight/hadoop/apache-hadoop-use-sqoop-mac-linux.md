---
title: 将 Apache Sqoop 与 Apache Hadoop 配合使用 - Azure HDInsight
description: 了解如何使用 Apache Sqoop 在 Apache Hadoop on HDInsight 与 Azure SQL 数据库之间进行导入和导出。
keywords: hadoop sqoop,sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c839aeae77d7e75fb30d82c410c331d21f5868ae
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406044"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>使用 Apache Sqoop 在 Apache Hadoop on HDInsight 与 SQL 数据库之间导入和导出数据

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

了解如何使用 Apache Sqoop 在 Azure HDInsight 中的 Apache Hadoop 群集与 Azure SQL 数据库或 Microsoft SQL Server 数据库之间进行导入和导出。 本文档中的步骤直接从 Hadoop 群集的头节点使用 `sqoop` 命令。 可以使用 SSH 连接到头节点并运行本文档中的命令。 本文是[在 HDInsight 中将 Apache Sqoop 与 Hadoop 配合使用](./hdinsight-use-sqoop.md)的续篇。

## <a name="prerequisites"></a>系统必备

* 从[在 HDInsight 中将 Apache Sqoop 与 Hadoop 配合使用](./hdinsight-use-sqoop.md)中完成[设置测试环境](./hdinsight-use-sqoop.md#create-cluster-and-sql-database)。

* 用于查询 Azure SQL 数据库的客户端。 考虑使用 [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) 或 [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)。

* SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="sqoop-export"></a>Sqoop 导出

从 Hive 到 SQL Server。

1. 使用 SSH 连接到 HDInsight 群集。 将 `CLUSTERNAME` 替换为群集的名称，然后输入以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 将 `MYSQLSERVER` 替换为 SQL Server 的名称。 若要验证 Sqoop 是否可以查看 SQL 数据库，请在打开的 SSH 连接中输入以下命令。 出现提示时，请输入与 SQL Server 登录名对应的密码。 此命令会返回数据库列表。

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. 将 `MYSQLSERVER` 替换为 SQL Server 的名称，并将 `MYDATABASE` 替换为 SQL 数据库的名称。 若要将数据从 Hive `hivesampletable` 表导出到 SQL 数据库中的 `mobiledata` 表，请在打开的 SSH 连接中输入以下命令。 出现提示时，输入与 SQL Server 登录名对应的密码

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. 若要验证数据是否已导出，请从 SQL 客户端使用以下查询，以查看导出的数据：

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Sqoop 导入

从 SQL Server 到 Azure 存储。

1. 将 `MYSQLSERVER` 替换为 SQL Server 的名称，并将 `MYDATABASE` 替换为 SQL 数据库的名称。 在打开的 SSH 连接中输入以下命令，以将数据从 SQL 数据库中的 `mobiledata` 表导入到 HDInsight 上的 `wasb:///tutorials/usesqoop/importeddata` 目录。 出现提示时，请输入与 SQL Server 登录名对应的密码。 数据中的字段将通过制表符分隔，并且相关行由换行符终止。

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. 在导入完成后，请在打开的 SSH 连接中输入以下命令，以列出新目录中的数据：

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>限制

* 批量导出 - 在基于 Linux 的 HDInsight 上，用于将数据导出到 Microsoft SQL Server 或 Azure SQL 数据库的 Sqoop 连接器不支持批量插入。

* 批处理 - 在基于 Linux 的 HDInsight 上，如果在执行插入时使用 `-batch` 开关，Sqoop 将进行多次插入而不是批处理插入操作。

## <a name="important-considerations"></a>重要注意事项

* HDInsight 和 SQL Server 必须位于同一 Azure 虚拟网络上。

    有关示例，请参阅[将 HDInsight 连接到本地网络](./../connect-on-premises-network.md)文档。

    有关通过 Azure 虚拟网络使用 HDInsight 的详细信息，请参阅[使用 Azure 虚拟网络扩展 HDInsight](../hdinsight-plan-virtual-network-deployment.md) 文档。 有关 Azure 虚拟网络的详细信息，请参阅[虚拟网络概述](../../virtual-network/virtual-networks-overview.md)文档。

* 必须将 SQL Server 配置为允许 SQL 身份验证。 有关详细信息，请参阅[选择身份验证模式](https://msdn.microsoft.com/ms144284.aspx)文档。

* 可能需要将 SQL Server 配置为接受远程连接。 有关详细信息，请参阅[如何解决 SQL Server 数据库引擎的连接问题](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)文档。

## <a name="next-steps"></a>后续步骤

现在已经学习了如何使用 Sqoop。 若要了解更多信息，请参阅以下文章：

* [将 Apache Oozie 和 HDInsight 配合使用](../hdinsight-use-oozie-linux-mac.md)：在 Oozie 工作流中使用 Sqoop 操作。
* [使用 HDInsight 分析航班延误数据](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)：使用交互式查询分析航班延误数据, 然后使用 Sqoop 将数据导出到 Azure SQL 数据库。
* [将数据上传到 HDInsight](../hdinsight-upload-data.md)：了解将数据上传到 HDInsight/Azure Blob 存储的其他方法。
