---
title: 将 MapReduce 和 Curl 与 HDInsight 中的 Apache Hadoop 配合使用 - Azure
description: 了解如何使用 Curl 通过 HDInsight 上的 Apache Hadoop 远程运行 MapReduce 作业。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: abc3cc8c526e37e18f1e67b109a9a8e15ff8c989
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302706"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>使用 REST 通过 HDInsight 上的 Apache Hadoop 运行 MapReduce 作业

了解如何使用 Apache Hive WebHCat REST API 在 HDInsight 群集上的 Apache Hadoop 上运行 MapReduce 作业。 本文档使用 Curl 演示如何通过使用原始 HTTP 请求来与 HDInsight 交互，以便运行 MapReduce 作业。

> [!NOTE]  
> 如果已熟悉如何使用基于 Linux 的 Hadoop 服务器，但刚接触 HDInsight，请参阅 [HDInsight 上的基于 Linux 的 Apache Hadoop 须知信息](../hdinsight-hadoop-linux-information.md)文档。

## <a name="prerequisites"></a>先决条件

* HDInsight 中的 Apache Hadoop 群集。 请参阅[使用 Azure 门户创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)。

以下两个因素中的任一个：
  * Windows PowerShell 或
  * 用[Jq](https://stedolan.github.io/jq/) [弯曲](https://curl.haxx.se/)

## <a name="run-a-mapreduce-job"></a>运行 MapReduce 作业

> [!NOTE]  
> 使用 Curl 或者与 WebHCat 进行任何其他形式的 REST 通信时，必须通过提供 HDInsight 群集管理员用户名和密码对请求进行身份验证。 必须使用群集名称作为用来向服务器发送请求的 URI 的一部分。
>
> REST API 使用[basic access authentication](https://en.wikipedia.org/wiki/Basic_access_authentication)（基本访问验证）进行保护。 你应该始终通过使用 HTTPS 来发出请求，以确保安全地将凭据发送到服务器。

### <a name="curl"></a>Curl

1. 为方便使用，请设置以下变量。 此示例基于 Windows 环境，根据环境需要进行修订。

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. 在命令行中，使用以下命令验证你是否可以连接到 HDInsight 群集。

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    此命令中使用的参数如下：

   * **-u**：指示用来对请求进行身份验证的用户名和密码
   * **-G**：指示此操作是 GET 请求。

   URI `https://CLUSTERNAME.azurehdinsight.net/templeton/v1`的开头对于所有请求都是相同的。

    将收到类似于以下 JSON 的响应：

    ```output
    {"version":"v1","status":"ok"}
    ```

1. 若要提交 MapReduce 作业，请使用以下命令。 根据需要修改**jq**的路径。

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    URI 的末尾 (/mapreduce/jar) 告知 WebHCat，此请求从 jar 文件中的类启动 MapReduce 作业。 此命令中使用的参数如下：

   * **-d**： `-G` 未使用，因此该请求默认为 POST 方法。 `-d` 指定与请求一起发送的数据值。
     * **user.name**：正在运行命令的用户
     * **jar**：包含要运行的类的 jar 文件所在位置
     * **class**：包含 MapReduce 逻辑的类
     * **arg**：要传递到 MapReduce 作业的参数。 在此示例中是用于输出的输入文本文件和目录

    此命令应会返回可用来检查作业状态的作业 ID：

       job_1415651640909_0026

1. 若要检查作业的状态，请使用以下命令。 将 `JOBID` 的值替换为上一步中返回的**实际**值。 根据需要修改**jq**的位置。

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. 为方便使用，请设置以下变量。 将 `CLUSTERNAME` 替换为实际的群集名称。 执行命令，并在出现提示时输入群集登录密码。

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. 使用以下命令验证是否可以连接到 HDInsight 群集：

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    将收到类似于以下 JSON 的响应：

    ```output
    {"version":"v1","status":"ok"}
    ```

1. 若要提交 MapReduce 作业，请使用以下命令：

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    URI 的末尾 (/mapreduce/jar) 告知 WebHCat，此请求从 jar 文件中的类启动 MapReduce 作业。 此命令中使用的参数如下：

    * **user.name**：正在运行命令的用户
    * **jar**：包含要运行的类的 jar 文件所在位置
    * **class**：包含 MapReduce 逻辑的类
    * **arg**：要传递到 MapReduce 作业的参数。 在此示例中是用于输出的输入文本文件和目录

   此命令应会返回可用来检查作业状态的作业 ID：

       job_1415651640909_0026

1. 若要检查作业的状态，请使用以下命令：

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

### <a name="both-methods"></a>两种方法

1. 如果作业已完成，返回的状态将是 `SUCCEEDED`。

1. 在作业的状态更改为 `SUCCEEDED` 后，可以从 Azure Blob 存储中检索作业的结果。 随查询一起传递的 `statusdir` 参数包含输出文件的位置。 在本示例中，位置为 `/example/curl`。 此地址在群集默认存储的 `/example/curl` 中存储作业的输出。

可以使用 [Azure CLI](/cli/azure/install-azure-cli) 列出并下载这些文件。 有关使用 Azure CLI 来处理 Azure Blob 存储的详细信息，请参阅[快速入门：使用 Azure CLI 创建、下载和列出 blob](../../storage/blobs/storage-quickstart-blobs-cli.md)。

## <a name="next-steps"></a>后续步骤

有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [将 MapReduce 与 HDInsight 上的 Apache Hadoop 配合使用](hdinsight-use-mapreduce.md)
* [将 Apache Hive 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)

有关本文中使用的 REST 接口的详细信息，请参阅 [WebHCat Reference](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)（WebHCat 参考）。
