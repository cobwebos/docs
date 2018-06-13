---
title: 在 HDInsight 中将 Hadoop Hive 与 Curl 配合使用 - Azure | Microsoft Docs
description: 了解如何使用 Curl 向 HDInsight 远程提交 Pig 作业。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6ce18163-63b5-4df6-9bb6-8fcbd4db05d8
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: larryfr
ms.openlocfilehash: f602cf45165625ec252f2e29cb9b0e5ed878f3a8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32170248"
---
# <a name="run-hive-queries-with-hadoop-in-hdinsight-using-rest"></a>使用 REST 在 HDInsight 中通过 Hadoop 运行 Hive 查询

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

了解如何使用 WebHCat REST API 在 Azure HDInsight 群集上通过 Hadoop 运行 Hive 查询。

## <a name="prerequisites"></a>先决条件

* 基于 Linux 的 Hadoop on HDInsight 群集版本 3.4 或更高版本。

  > [!IMPORTANT]
  > Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* 一个 REST 客户端。 本文档使用了 Windows PowerShell 和 [Curl](http://curl.haxx.se/) 示例。

    > [!NOTE]
    > Azure PowerShell 提供了用于使用 Hive on HDInsight 的专用 cmdlet。 有关详细信息，请参阅[将 Hive 与 Azure PowerShell 配合使用](apache-hadoop-use-hive-powershell.md)文档。

本文档还使用 Windows PowerShell 和 [Jq](http://stedolan.github.io/jq/) 来处理从 REST 请求返回的 JSON 数据。

## <a id="curl"></a>运行 Hive 查询

> [!NOTE]
> 使用 cURL 或者与 WebHCat 进行任何其他形式的 REST 通信时，必须提供 HDInsight 群集管理员的用户名和密码以对请求进行身份验证。
>
> REST API 通过 [基本身份验证](http://en.wikipedia.org/wiki/Basic_access_authentication)进行保护。 为了有助于确保将凭据安全地发送到服务器，应始终使用安全 HTTP (HTTPS) 发出请求。

1. 若要设置本文档中的脚本使用的群集登录名，请使用下列命令之一：

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. 若要设置群集名称，请使用下列命令之一：

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. 若要验证是否可以连接到 HDInsight 群集，请使用下列命令之一：

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status)
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    将收到类似于以下文本的响应：

    ```json
    {"status":"ok","version":"v1"}
    ```

    此命令中使用的参数如下：

    * `-u` - 用来对请求进行身份验证的用户名和密码。
    * `-G` - 指示此请求是一个 GET 操作。

   URL 的开头 (`https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`) 对于所有请求都是相同的。 路径 `/status` 指示请求将返回服务器的 WebHCat（也称为 Templeton）状态。 还可以通过使用以下命令请求 Hive 的版本：

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    此请求返回的响应类似于以下文本：

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. 使用以下命令创建名为 **log4jLogs** 的表：

    ```bash
    JOBID=`curl -s -u $LOGIN -d user.name=$LOGIN -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/hive | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="set hive.execution.engine=tez;DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    此请求使用 POST 方法，该方法将数据作为请求的一部分发送到 REST API。 将随请求发送以下数据值：

     * `user.name` - 正在运行命令的用户。
     * `execute` - 要执行的 HiveQL 语句。
     * `statusdir` - 此作业的状态要写入到的目录。

   这些语句将执行以下操作：
   
   * `DROP TABLE` - 如果表已存在，则删除该表。
   * `CREATE EXTERNAL TABLE` - 在 Hive 中创建一个新的“外部”表。 外部表仅在 Hive 中存储表定义。 数据将保留在原始位置。

     > [!NOTE]
     > 如果希望通过外部源更新基础数据，应使用外部表。 例如，使用自动化数据上传过程或其他 MapReduce 操作。
     >
     > 删除外部表**不会**删除数据，只会删除表定义。

   * `ROW FORMAT` - 如何设置数据的格式。 每个日志中的字段都用空格分隔。
   * `STORED AS TEXTFILE LOCATION` - 数据的存储位置（example/data 目录），并且数据存储为文本。
   * `SELECT` - 选择 **t4** 列包含值 **[ERROR]** 的所有行的计数。 此语句返回的值为 **3**，因为有三行包含此值。

     > [!NOTE]
     > 请注意，在与 Curl 配合使用时，将用 `+` 字符替换 HiveQL 语句之间的空格。 如果带引号的值包含空格（例如分隔符），则不应替换为 `+`。

      此命令会返回可用来检查作业状态的作业 ID。

5. 若要检查作业的状态，请使用以下命令：

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    如果作业已完成，状态将是 **SUCCEEDED**。

6. 在作业的状态更改为“SUCCEEDED”后，可以从 Azure Blob 存储中检索作业的结果。 随查询一起传递的 `statusdir` 参数包含输出文件的位置；在本例中，该位置为 `/example/rest`。 此地址将输出存储在群集默认存储中的 `example/curl` 目录。

    可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 列出并下载这些文件。 有关将 Azure CLI 与 Azure 存储配合使用的详细信息，请参阅[将 Azure CLI 2.0 与 Azure 存储配合使用](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs)文档。

## <a id="nextsteps"></a>后续步骤

有关将 Hive 与 HDInsight 配合使用的一般信息：

* [将 Hive 与 Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)

有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [将 Pig 与 Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 上的 Hadoop 配合使用](hdinsight-use-mapreduce.md)

如果将 Tez 与 Hive 配合使用，请参阅以下文档以了解调试信息：

* [在基于 Linux 的 HDInsight 上使用 Ambari Tez 视图](../hdinsight-debug-ambari-tez-view.md)

有关在本文档中使用的 REST API 的详细信息，请参阅 [WebHCat 参考](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)文档。

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


