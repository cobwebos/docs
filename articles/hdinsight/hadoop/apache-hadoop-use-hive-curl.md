---
title: 在 HDInsight 中将 Apache Hadoop Hive 与 Curl 配合使用 - Azure
description: 了解如何使用卷将 Apache Pig 作业远程提交到 Azure HDInsight。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 3bb09f1958685a3474b49d2d194e89fe81a80076
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690494"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>使用 REST 在 HDInsight 中通过 Apache Hadoop 运行 Apache Hive 查询

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

了解如何使用 WebHCat REST API 通过 Apache Hadoop on Azure HDInsight 群集运行 Apache Hive 查询。

## <a name="prerequisites"></a>必备组件

* HDInsight 中的 Apache Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](./apache-hadoop-linux-tutorial-get-started.md)。

* 一个 REST 客户端。 本文档在[WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest)上使用 Windows PowerShell 并在[Bash](https://docs.microsoft.com/windows/wsl/install-win10)上[弯曲](https://curl.haxx.se/)。

* 如果使用 Bash，还需要 jq，它是一个命令行 JSON 处理器。  请参阅 [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)。

## <a name="base-uri-for-rest-api"></a>Rest API 的基本 URI

HDInsight 上 REST API 的基本统一资源标识符（URI）是 `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`的，其中 `CLUSTERNAME` 是群集的名称。  Uri 中的群集名称**区分大小写**。  尽管 URI 的完全限定的域名（FQDN）部分（`CLUSTERNAME.azurehdinsight.net`）中的群集名称不区分大小写，但 URI 中的其他事件区分大小写。

## <a name="authentication"></a>身份验证

使用 cURL 或者与 WebHCat 进行任何其他形式的 REST 通信时，必须提供 HDInsight 群集管理员的用户名和密码以对请求进行身份验证。 REST API 通过 [基本身份验证](https://en.wikipedia.org/wiki/Basic_access_authentication)进行保护。 为了有助于确保将凭据安全地发送到服务器，应始终使用安全 HTTP (HTTPS) 发出请求。

### <a name="setup-preserve-credentials"></a>设置（保留凭据）

保留您的凭据以避免为每个示例重新输入凭据。  群集名称将保留在单独的步骤中。

**答： Bash**  
通过使用实际密码替换 `PASSWORD` 来编辑下面的脚本。  然后输入命令。

```bash
export password='PASSWORD'
```  

**B.** 执行以下代码，然后在弹出窗口中输入你的凭据：

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>识别大小写正确的群集名称

群集名称的实际大小写格式可能出乎预期，具体取决于群集的创建方式。  此处的步骤将显示实际的大小写，并将其存储在一个变量中以供以后使用。

编辑以下脚本，将 `CLUSTERNAME` 替换为群集名称。 然后输入命令。 （FQDN 的群集名称不区分大小写。）

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a name="run-a-hive-query"></a>运行 Hive 查询

1. 若要验证是否可以连接到 HDInsight 群集，请使用下列命令之一：

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
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

1. URL 的开头 (`https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`) 对于所有请求都是相同的。 路径 `/status` 指示请求将返回服务器的 WebHCat（也称为 Templeton）状态。 还可以通过使用以下命令请求 Hive 的版本：

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    此请求返回的响应类似于以下文本：

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. 使用以下命令创建名为 **log4jLogs** 的表：

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
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

   * `DROP TABLE`-如果表已存在，则将其删除。
   * `CREATE EXTERNAL TABLE` - 在 Hive 中创建一个新的“外部”表。 外部表仅在 Hive 中存储表定义。 数据将保留在原始位置。

     > [!NOTE]  
     > 如果希望通过外部源更新基础数据，应使用外部表。 例如，使用自动化数据上传过程或其他 MapReduce 操作。
     >
     > 删除外部表**不会**删除数据，只会删除表定义。

   * `ROW FORMAT` - 如何设置数据的格式。 每个日志中的字段都用空格分隔。
   * `STORED AS TEXTFILE LOCATION`-数据的存储位置（example/data 目录），并且存储为文本。
   * `SELECT` - 选择 **t4** 列包含值 **[ERROR]** 的所有行的计数。 此语句返回的值为 **3**，因为有三行包含此值。

     > [!NOTE]  
     > 请注意，在与 Curl 配合使用时，将用 `+` 字符替换 HiveQL 语句之间的空格。 如果带引号的值包含空格（例如分隔符），则不应替换为 `+`。

      此命令会返回可用来检查作业状态的作业 ID。

1. 若要检查作业的状态，请使用以下命令：

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
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

1. 在作业的状态更改为“SUCCEEDED”后，可以从 Azure Blob 存储中检索作业的结果。 随查询一起传递的 `statusdir` 参数包含输出文件的位置；在本例中，该位置为 `/example/rest`。 此地址将输出存储在群集默认存储中的 `example/curl` 目录。

    可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 列出并下载这些文件。 有关将 Azure CLI 与 Azure 存储配合使用的详细信息，请参阅[将 Azure CLI 与 Azure 存储配合使用](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs)文档。

## <a name="next-steps"></a>后续步骤

有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [将 Apache Hive 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)
* [将 MapReduce 与 HDInsight 上的 Apache Hadoop 配合使用](hdinsight-use-mapreduce.md)

有关在本文档中使用的 REST API 的详细信息，请参阅 [WebHCat 参考](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)文档。