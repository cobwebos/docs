---
title: "将 Apache Sqoop 与 Hadoop 配合使用 - Azure HDInsight | Microsoft Docs"
description: "了解如何使用 Apache Sqoop 在 Hadoop on HDInsight 与 Azure SQL 数据库之间进行导入和导出。"
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop,sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: larryfr
ms.openlocfilehash: 35dcbb91e6af1480685c9fd5b829c54277c1c605
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>使用 Apache Sqoop 在 Hadoop on HDInsight 与 SQL 数据库之间导入和导出数据

[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

了解如何使用 Apache Sqoop 在 Azure HDInsight 中的 Hadoop 群集与 Azure SQL 数据库或 Microsoft SQL Server 数据库之间进行导入和导出。 本文档中的步骤直接从 Hadoop 群集的头节点使用 `sqoop` 命令。 可以使用 SSH 连接到头节点并运行本文档中的命令。

> [!IMPORTANT]
> 本文档中的步骤仅适用于使用 Linux 的 HDInsight 群集。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

## <a name="install-freetds"></a>安装 FreeTDS

1. 使用 SSH 连接到 HDInsight 群集。 例如，以下命令连接到名为 `mycluster` 的群集的主头节点：

    ```bash
    ssh CLUSTERNAME-ssh.azurehdinsight.net
    ```

    有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

2. 使用以下命令安装 FreeTDS：

    ```bash
    sudo apt --assume-yes install freetds-dev freetds-bin
    ```

    在多个步骤中使用 FreeTDS 连接到 SQL 数据库。

## <a name="create-the-table-in-sql-database"></a>在 SQL 数据库中创建表

> [!IMPORTANT]
> 如果使用的是在[创建群集和 SQL 数据库](hdinsight-use-sqoop.md)中创建的 HDInsight 群集和 SQL 数据库，请跳过本部分中的步骤。 创建数据库和表，作为[创建群集和 SQL 数据库](hdinsight-use-sqoop.md)文档中的一部分步骤。

1. 在 SSH 会话中，使用以下命令连接到 SQL 数据库服务器。

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    将收到类似于以下文本的输出：

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

2. 在 `1>` 提示符下，输入以下查询：

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
    GO
    ```

    输入 `GO` 语句后，将评估前面的语句。 首先会创建 **mobiledata** 表，然后会向其添加聚集索引（根据 SQL 数据库的要求。）

    使用以下查询验证是否已创建该表：

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    将看到类似于以下文本的输出：

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

3. 在 `1>` 提示符下输入 `exit` 以退出 tsql 实用工具。

## <a name="sqoop-export"></a>Sqoop 导出

1. 通过 SSH 连接到群集后，使用以下命令验证 Sqoop 是否可以看到 SQL 数据库：

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    出现提示时，输入 SQL 数据库登录名的密码。

    此命令将返回数据库列表，其中包括之前创建的 **sqooptest** 数据库。

2. 使用以下命令将数据从 **hivesampletable** 导出到 **mobiledata** 表：

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1
    ```

    此命令指示 Sqoop 连接到 **sqooptest** 数据库。 Sqoop 随后将数据从 wasb:///hive/warehouse/hivesampletable 导出到 mobiledata 表。

    > [!IMPORTANT]
    > 如果群集的默认存储是 Azure 存储帐户，请使用 `wasb:///`。 如果是 Azure Data Lake Store，请使用 `adl:///`。

3. 该命令完成后，使用以下命令通过 TSQL 连接到数据库：

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P -p 1433 -D sqooptest
    ```

    连接成功以后，使用以下语句验证数据是否已导出到 **mobiledata** 表：

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata
    GO
    ```

    会在表中看到一系列数据。 键入 `exit` 退出 tsql 实用程序。

## <a name="sqoop-import"></a>Sqoop 导入

1. 使用以下命令将数据从 SQL 数据库中的 mobiledata 表导入 HDInsight 上的 wasb:///tutorials/usesqoop/importeddata 目录：

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    数据中的字段将通过制表符分隔，并且相关行由换行符终止。

2. 完成导入后，可使用以下命令在新目录中列出这些数据：

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>使用 SQL Server

还可以使用 Sqoop 通过 SQL Server 来导入和导出数据，不管是在数据中心进行，还是在托管在 Azure 中的虚拟机上进行。 SQL 数据库和 SQL Server 在使用方面的差异是：

* HDInsight 和 SQL Server 必须位于同一 Azure 虚拟网络上。

    有关示例，请参阅[将 HDInsight 连接到本地网络](./connect-on-premises-network.md)文档。

    有关通过 Azure 虚拟网络使用 HDInsight 的详细信息，请参阅[使用 Azure 虚拟网络扩展 HDInsight](hdinsight-extend-hadoop-virtual-network.md) 文档。 有关 Azure 虚拟网络的详细信息，请参阅[虚拟网络概述](../virtual-network/virtual-networks-overview.md)文档。

* 必须将 SQL Server 配置为允许 SQL 身份验证。 有关详细信息，请参阅[选择身份验证模式](https://msdn.microsoft.com/ms144284.aspx)文档。

* 可能需要将 SQL Server 配置为接受远程连接。 有关详细信息，请参阅[如何解决 SQL Server 数据库引擎的连接问题](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)文档。

* 使用诸如 **SQL Server Management Studio** 或 **tsql** 等实用工具在 SQL Server 中创建 **sqooptest** 数据库。 有关使用 Azure CLI 的步骤仅适用于 Azure SQL 数据库。

    使用以下 Transact-SQL 语句创建 **mobiledata** 表：

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    ```

* 在从 HDInsight 连接到 SQL Server 时，可能需要使用 SQL Server 的 IP 地址。 例如：

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>限制

* 批量导出 - 在基于 Linux 的 HDInsight 上，用于将数据导出到 Microsoft SQL Server 或 Azure SQL 数据库的 Sqoop 连接器目前不支持批量插入。

* 批处理 - 在基于 Linux 的 HDInsight 上，如果在执行插入时使用 `-batch` 开关，Sqoop 将进行多次插入而不是批处理插入操作。

## <a name="next-steps"></a>后续步骤

现在已经学习了如何使用 Sqoop。 若要了解更多信息，请参阅以下文章：

* [将 Oozie 与 HDInsight 配合使用][hdinsight-use-oozie]：在 Oozie 工作流中使用 Sqoop 操作。
* [使用 HDInsight 分析航班延误数据][hdinsight-analyze-flight-data]：使用 Hive 分析航班延误数据，然后使用 Sqoop 将数据导出到 Azure SQL 数据库。
* [将数据上传到 HDInsight][hdinsight-upload-data]：了解将数据上传到 HDInsight/Azure Blob 存储的其他方法。

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
