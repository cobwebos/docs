---
title: 教程 - 在 Azure HDInsight 中使用 Apache HBase
description: 按照本 Apache HBase 教程开始在 HDInsight 上使用 Hadoop。 从 HBase shell 创建表，并使用 Hive 查询这些表。
keywords: hbasecommand，hbase 示例
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: tutorial
ms.date: 06/25/2019
ms.author: hrasheed
ms.openlocfilehash: e43d2d64535085a9b22d2febc761fc7026498ba8
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077138"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>教程：在 Azure HDInsight 中使用 Apache HBase

本教程演示如何在 Azure HDInsight 中创建 Apache HBase 群集、创建 HBase 表以及使用 Apache Hive 查询表。  有关 HBase 的一般信息，请参阅 [HDInsight HBase 概述](./apache-hbase-overview.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 Apache HBase 群集
> * 创建 HBase 表和插入数据
> * 使用 Apache Hive 查询 Apache HBase
> * 通过 Curl 使用 HBase REST API
> * 检查群集状态

## <a name="prerequisites"></a>先决条件

* SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

* Bash。 本文中的示例使用 Windows 10 上的 Bash shell 执行 curl 命令。 有关安装步骤，请参阅[适用于 Linux 的 Windows 子系统 (Windows 10) 安装指南](https://docs.microsoft.com/windows/wsl/install-win10)。  其他 [Unix shell](https://www.gnu.org/software/bash/) 也将适用。  Curl 示例经过一些细微的修改，可在 Windows 命令提示符下运行。  或者，可以使用 Windows PowerShell cmdlet [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod)。

## <a name="create-apache-hbase-cluster"></a>创建 Apache HBase 群集

以下过程使用 Azure 资源管理器模板创建 HBase 群集，以及相关的默认 Azure 存储帐户。 若要了解该过程与其他群集创建方法中使用的参数，请参阅 [在 HDInsight 中创建基于 Linux 的 Hadoop 群集](../hdinsight-hadoop-provision-linux-clusters.md)。

1. 选择下面的图像即可在 Azure 门户中打开该模板。 该模板位于 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/)中。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. 在“自定义部署”边栏选项卡中输入以下值： 

    |属性 |说明 |
    |---|---|
    |Subscription|选择用于创建群集的 Azure 订阅。|
    |Resource group|创建 Azure 资源管理组，或使用现有的组。|
    |位置|指定资源组的位置。 |
    |ClusterName|输入 HBase 群集的名称。|
    |群集登录名和密码|默认登录名为“admin”  。|
    |SSH 用户名和密码|默认用户名为“sshuser”  。|

    其他参数是可选的。  

    每个群集都有一个 Azure 存储帐户依赖项。 删除群集后，数据将保留在存储帐户中。 群集的默认存储帐户名为群集名称后接“store”。 该名称已在模板 variables 节中硬编码。

3. 选择“我同意上述条款和条件”，然后选择“购买”   。 创建群集大约需要 20 分钟时间。

在删除 HBase 群集后，可以通过使用相同的默认 Blob 容器创建另一个 HBase 群集。 新群集将选取已在原始群集中创建的 HBase 表。 为了避免不一致，建议在删除群集之前先禁用 HBase 表。

## <a name="create-tables-and-insert-data"></a>创建表和插入数据

可以使用 SSH 连接到 HBase 群集，并使用 [Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) 来创建 HBase 表以及插入和查询数据。

对于大多数人而言，数据以表格形式显示：

![HDInsight Apache HBase 表格数据](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

在 HBase（[Cloud BigTable](https://cloud.google.com/bigtable/) 的一种实现）中，相同的数据看起来类似于：

![HDInsight Apache HBase BigTable 数据](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**使用 HBase shell**

1. 使用 `ssh` 命令连接到 HBase 群集。 通过将 `CLUSTERNAME` 替换为群集的名称来编辑以下命令，然后输入该命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 使用 `hbase shell` 命令来启动 HBase 交互式 shell。 在 SSH 连接中输入以下命令。

    ```bash
    hbase shell
    ```

1. 使用 `create` 命令，创建包含双列系列的 HBase 表。 表名和列名区分大小写。 输入以下命令：

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. 使用 `list` 命令，列出 HBase 中的所有表。 输入以下命令：

    ```hbase
    list
    ```

1. 使用 `put` 命令，将指定列中的值插入特定表中的指定行。 输入以下命令：

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. 使用 `scan` 命令扫描并返回 `Contacts` 表数据。 输入以下命令：

    ```hbase
    scan 'Contacts'
    ```

    ![HDInsight Apache Hadoop HBase shell](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. 使用 `get` 命令提取某个行的内容。 输入以下命令：

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    看到的结果与使用 `scan` 命令的结果类似，因为只有一个行。

    有关 HBase 表架构的详细信息，请参阅 [Apache HBase 架构设计简介](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf)。 有关 HBase 命令的详细信息，请参阅 [Apache HBase 参考指南](https://hbase.apache.org/book.html#quickstart)。

1. 使用 `exit` 命令来停止 HBase 交互式 shell。 输入以下命令：

    ```hbaseshell
    exit
    ```

**在联系人 HBase 表中批量加载数据**

HBase 提供了多种方法用于将数据载入表中。  有关详细信息，请参阅 [批量加载](https://hbase.apache.org/book.html#arch.bulk.load)。

示例数据文件可在公共 Blob 容器 `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt` 中找到。  该数据文件的内容为：

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

可以选择创建一个文本文件并将该文件上传到自己的存储帐户。 有关说明，请参阅[在 HDInsight 中为 Apache Hadoop 作业上传数据](../hdinsight-upload-data.md)。

此过程会使用你在上一过程中创建的 `Contacts` HBase 表。

1. 从打开的 SSH 连接，运行以下命令将数据文件转换为 StoreFiles，并将其存储在由 `Dimporttsv.bulk.output` 指定的相对路径下。

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. 运行以下命令将数据从 `/example/data/storeDataFileOutput` 上传到 HBase 表：

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. 可以打开 HBase shell，并使用 `scan` 命令列出该表的内容。

## <a name="use-apache-hive-to-query-apache-hbase"></a>使用 Apache Hive 查询 Apache HBase

可以使用 [Apache Hive](https://hive.apache.org/) 查询 HBase 表中的数据。 本部分将创建要映射到 HBase 表的 Hive 表，并使用该表来查询 HBase 表中的数据。

1. 从打开的 SSH 连接，使用以下命令启动 Beeline：

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    有关 Beeline 的详细信息，请参阅[通过 Beeline 将 Hive 与 HDInsight 中的 Hadoop 配合使用](../hadoop/apache-hadoop-use-hive-beeline.md)。

1. 运行以下 [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) 脚本，创建映射到 HBase 表的 Hive 表。 确保在运行以下语句前，已使用 HBase shell 创建了本文中前面引用的示例表。

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. 运行以下 HiveQL 脚本，查询 HBase 表中的数据：

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. 若要退出 Beeline，请使用 `!exit`。

1. 若要退出 SSH 连接，请使用 `exit`。

## <a name="use-hbase-rest-apis-using-curl"></a>通过 Curl 使用 HBase REST API

REST API 通过 [基本身份验证](https://en.wikipedia.org/wiki/Basic_access_authentication)进行保护。 始终应该使用安全 HTTP (HTTPS) 来发出请求，确保安全地将凭据发送到服务器。

1. 为便于使用，请启动环境变量。 将 `MYPASSWORD` 替换为群集登录密码，以编辑下面的命令。 将 `MYCLUSTERNAME` 替换为 HBase 群集的名称。 然后，输入相应的命令。

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. 使用以下命令列出现有的 HBase 表：

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. 使用以下命令创建包含两个列系列的新 HBase 表：

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    架构以 JSON 格式提供。
1. 使用以下命令插入一些数据：

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    必须使用 base64 来为 -d 参数中指定的值编码。 在示例中：

   * MTAwMA==：1000
   * UGVyc29uYWw6TmFtZQ==：Personal:Name
   * Sm9obiBEb2xl：John Dole

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) 允许插入多个（批处理）值。

1. 使用以下命令获取行：

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

有关 HBase Rest 的详细信息，请参阅 [Apache HBase 参考指南](https://hbase.apache.org/book.html#_rest)。

> [!NOTE]  
> Thrift 不受 HDInsight 中的 HBase 支持。
>
> 使用 Curl 或者与 WebHCat 进行任何其他形式的 REST 通信时，必须提供 HDInsight 群集管理员用户名和密码对请求进行身份验证。 此外，还必须使用群集名称作为用来向服务器发送请求的统一资源标识符 (URI) 的一部分：
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    应会收到类似于以下响应的响应：
>   
>        {"status":"ok","version":"v1"}

## <a name="check-cluster-status"></a>检查群集状态

HDInsight 中的 HBase 随附了一个 Web UI 用于监视群集。 使用该 Web UI 可以请求有关区域的统计或信息。

**访问 HBase Master UI**

1. 登录到 Ambari Web UI（网址是 `https://CLUSTERNAME.azurehdinsight.net`，其中，`CLUSTERNAME` 是 HBase 群集的名称）。

1. 从左侧菜单中选择“HBase”  。

1. 选择页面顶部的“快速链接”  ，指向活动的 Zookeeper 节点链接，然后选择“HBase Master UI”  。  该 UI 会在另一个浏览器标签页中打开：

   ![HDInsight Apache HBase HMaster UI](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   HBase Master UI 包含以下部分：

   - 区域服务器
   - 备份主机
   - 表
   - 任务
   - 软件属性

## <a name="clean-up-resources"></a>清理资源

为了避免不一致，建议在删除群集之前先禁用 HBase 表。 可以使用 HBase 命令 `disable 'Contacts'`。 如果不打算继续使用此应用程序，请使用以下步骤删除创建的 HBase 群集：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 在顶部的“搜索”框中，键入 **HDInsight**。 
1. 选择“服务”下的“HDInsight 群集”   。
1. 在显示的 HDInsight 群集列表中，单击为本教程创建的群集旁边的“...”。 
1. 单击“删除”  。 单击 **“是”** 。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建 Apache HBase 群集、如何创建表以及如何从 HBase shell 查看这些表中的数据。 此外，学习了如何对 HBase 表中的数据使用 Hive 查询，以及如何使用 HBase C# REST API 创建 HBase 表并从该表中检索数据。 若要了解更多信息，请参阅以下文章：

> [!div class="nextstepaction"]
> [HDInsight HBase 概述](./apache-hbase-overview.md)