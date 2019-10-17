---
title: 在 Azure HDInsight 中运行 Apache 基本查询，并提供 Apache Phoenix
description: 了解如何使用 Apache Zeppelin 通过 Phoenix 运行 Apache 基本查询。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 28eeb446e55213f1ffa0a638878f6432fd15a05a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392236"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>使用 Apache Zeppelin 在 Azure HDInsight 中的 Apache HBase 上运行 Apache Phoenix 查询

Apache Phoenix 是基于 HBase 构建的开源的大规模并行关系数据库层。 Phoenix 允许通过 HBase 使用类似于 SQL 的查询。 Phoenix 使用下的 JDBC 驱动程序来创建、删除、更改 SQL 表、索引、视图和序列。  你还可以使用 Phoenix 分别和批量更新行。 Phoenix 使用 NOSQL 本机编译而不是使用 MapReduce 来编译查询，从而能够在 HBase 顶层创建低延迟的应用程序。

Apache Zeppelin 是一种基于 web 的开源笔记本，使你能够使用交互式数据分析和语言（如 SQL 和 Scala）创建数据驱动的协作文档。 它帮助数据开发人员 & 数据科学家开发、组织、执行和共享数据操作的代码。 它允许你在不引用命令行或需要群集详细信息的情况下对结果进行可视化。

HDInsight 用户可以使用 Apache Zeppelin 来查询 Phoenix 表。 Apache Zeppelin 与 HDInsight 群集集成，无其他步骤可用于使用该群集。 只需使用 JDBC 解释器创建 Zeppelin 笔记本，开始编写 Phoenix SQL 查询

## <a name="prerequisites"></a>必备组件

HDInsight 上的 Apache HBase 群集。 请参阅[Apache HBase 入门](./apache-hbase-tutorial-get-started-linux.md)。

## <a name="create-an-apache-zeppelin-note"></a>创建 Apache Zeppelin 笔记

1. 请将以下 URL 中的 `CLUSTERNAME` 替换为你的群集的名称：`https://CLUSTERNAME.azurehdinsight.net/zeppelin`。 然后在 Web 浏览器中输入该 URL。 输入群集登录用户名和密码。

1. 从 "Zeppelin" 页中，选择 "**新建注释**"。

    ![HDInsight 交互式查询 zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. 在“创建新笔记”对话框中，键入或选择以下值：

    - 备注名称：输入便笺的名称。
    - 默认解释器：从下拉列表中选择 " **jdbc** "。

    然后选择 "**创建便笺**"。

1. 确保笔记本标头显示连接状态。 其右上角有一个绿色圆点表示。

    ![Zeppelin 笔记本状态](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Zeppelin 笔记本状态")

1. 创建一个 HBase 表。 输入以下命令，然后按**Shift + enter**：

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    第一行中的 **% jdbc （phoenix）** 语句告诉笔记本使用 phoenix jdbc 解释器。

1. 查看已创建的表。

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. 在表中插入值。

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. 查询表。

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. 删除记录。

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. 删除表。

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>后续步骤

- [Apache Phoenix 现在支持 Azure HDInsight 中的 Zeppelin](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [Apache Phoenix 语法](https://phoenix.apache.org/language/index.html)
