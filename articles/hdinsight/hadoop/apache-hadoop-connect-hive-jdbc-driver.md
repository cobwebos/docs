---
title: 通过 JDBC 驱动程序查询 Apache Hive - Azure HDInsight
description: 从 Java 应用程序使用 JDBC 驱动程序将 Apache Hive 查询提交到 HDInsight 上的 Hadoop。 以编程方式从 SQuirrel SQL 客户端进行连接。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: hrasheed
ms.openlocfilehash: dc088d3a6baa4198a1d5cb566b968c631ae5f2c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58114708"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>在 HDInsight 中通过 JDBC 驱动程序查询 Apache Hive

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

了解如何从 Java 应用程序使用 JDBC 驱动程序将 Apache Hive 查询提交到 Azure HDInsight 中的 Apache Hadoop。 本文档中的信息演示如何以编程方式从 SQuirreL SQL 客户端进行连接。

有关 Hive JDBC 接口的详细信息，请参阅 [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface)。

## <a name="prerequisites"></a>必备组件

* HDInsight Hadoop 群集。 要创建此群集，请参阅 [Azure HDInsight 入门](apache-hadoop-linux-tutorial-get-started.md)。
* [Java 开发人员工具包 (JDK) 版本 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) 或更高版本。
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/)。 SQuirreL 是 JDBC 客户端应用程序。


## <a name="jdbc-connection-string"></a>JDBC 连接字符串

JDBC 通过端口 443 连接到 Azure 上的 HDInsight 群集，并使用 SSL 保护通信安全。 公用网关（群集位于其后）会将通信重定向到 HiveServer2 在其上进行实际侦听的端口。 以下连接字符串显示要用于 HDInsight 的格式：

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

将 `CLUSTERNAME` 替换为 HDInsight 群集的名称。

## <a name="authentication"></a>Authentication

建立连接时，必须使用 HDInsight 群集管理员名称和密码向群集网关进行身份验证。 从 JDBC 客户端（例如 SQuirreL SQL）进行连接时，必须在客户端设置中输入管理员名称和密码。

从 Java 应用程序建立连接时，必须使用该名称和密码。 例如，以下 Java 代码使用连接字符串、管理员名称和密码打开新连接：

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>使用 SQuirreL SQL 客户端进行连接

SQuirreL SQL 是一种 JDBC 客户端，可用于通过 HDInsight 群集远程运行 Hive 查询。 下列步骤假定已安装了 SQuirreL SQL。

1. 创建一个目录来包含要从群集复制的某些文件。

2. 在下面的脚本中，将 `sshuser` 替换为群集的 SSH 用户帐户名。  将 `CLUSTERNAME` 替换为 HDInsight 群集名称。  从命令行中，输入以下命令来从 HDInsight 群集复制文件：

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/hadoop-common.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/lib/log4j-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/lib/slf4j-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/commons-codec*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/commons-logging-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/hive-*-1.2*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/httpclient-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/httpcore-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/libfb*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/libthrift-*.jar .
    ```

3. 启动 SQuirreL SQL 应用程序。 在窗口左侧中，选择“驱动程序”。

    ![窗口左侧的“驱动程序”选项卡](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. 从“驱动程序”对话框顶部的图标中，选择 **+** 图标来创建驱动程序。

    ![驱动程序图标](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. 在“添加驱动程序”对话框中，添加以下信息：

    * **名称**：Hive
    * **示例 URL**：`jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **额外类路径**：使用“添加”按钮添加此前下载的所有 jar 文件
    * **类名**：org.apache.hive.jdbc.HiveDriver

   ![添加驱动程序对话框](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   选择“确定”保存这些设置。

6. 在 SQuirreL SQL 窗口左侧，选择“别名”。 然后选择 **+** 图标来创建连接别名。

    ![添加新的别名](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. 将以下值用于“添加别名”对话框。

    * **名称**：Hive on HDInsight

    * **驱动程序**：使用下拉列表选择**Hive**驱动程序

    * **URL：**`jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        将 **CLUSTERNAME** 替换为 HDInsight 群集名。

    * **用户名**：HDInsight 群集的群集登录帐户名。 默认为 `admin`。

    * **密码**：群集登录帐户的密码。

   ![添加别名对话框](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > 使用“测试”按钮验证连接是否有效。 出现“连接到: Hive on HDInsight”对话框时，选择“连接”进行测试。 如果测试成功，会看到“连接成功”对话框。 如果发生错误，请参阅[故障排除](#troubleshooting)。

    若要保存连接别名，请使用“添加别名”对话框底部的“确定”按钮。

8. 在 SQuirreL SQL 顶部的“连接到”下拉列表中，选择“Hive on HDInsight”。 出现提示时，选择“连接”。

    ![连接对话框](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. 连接后，在 SQL 查询对话框中输入以下查询，然后选择“运行”图标（一个正在跑步的人）。 结果区域会显示查询的结果。

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![sql 查询对话框，其中包括结果](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>从示例 Java 应用程序进行连接

[https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc) 上提供了使用 Java 客户端查询 Hive on HDInsight 的示例。 按照存储库中的说明生成并运行该示例。

## <a name="troubleshooting"></a>故障排除

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>尝试打开 SQL 连接时发生意外错误

**症状**：连接到 HDInsight 群集版本 3.3 或更高版本时，可能会遇到意外错误。 此错误的堆栈跟踪的开头为以下行：

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**原因**：此错误由 SQuirreL 随附的较旧版本 commons-codec.jar 文件引起。

**解决方法**：若要解决此错误，请使用以下步骤：

1. 退出 SQuirreL，并转到系统上安装 SQuirreL 的目录。 在 SquirreL 目录的 `lib` 目录下，将现有的 commons-codec.jar 替换为从 HDInsight 群集下载的文件。

2. 重新启动 SQuirreL。 连接到 HDInsight 上的 Hive 时，应不再会出现该错误。

## <a name="next-steps"></a>后续步骤

现在，已了解如何将 JDBC 与 Hive 配合使用，请使用以下链接来学习 Azure HDInsight 的其他用法。

* [在 Azure HDInsight 中使用 Microsoft Power BI 直观显示 Apache Hive 数据](apache-hadoop-connect-hive-power-bi.md)。
* [在 Azure HDInsight 中使用 Power BI 直观显示交互式查询 Hive 数据](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [在 Azure HDInsight 中使用 Apache Zeppelin 运行 Apache Hive 查询](./../hdinsight-connect-hive-zeppelin.md)。
* [使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 HDInsight](apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [使用 Power Query 将 Excel 连接到 Apache Hadoop](apache-hadoop-connect-excel-power-query.md)。
* [使用针对 Visual Studio 的 Data Lake 工具连接到 Azure HDInsight 并运行 Apache Hive 查询](apache-hadoop-visual-studio-tools-get-started.md)。
* [使用用于 Visual Studio Code 的 Azure HDInsight 工具](../hdinsight-for-vscode.md)。
* [将数据上传到 HDInsight](../hdinsight-upload-data.md)
* [将 Apache Hive 和 HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Apache Pig 和 HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 作业与 HDInsight 配合使用](hdinsight-use-mapreduce.md)
