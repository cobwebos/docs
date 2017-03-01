---
title: "将 MapReduce 和 Curl 与 Hadoop in HDInsight 配合使用 | Microsoft Docs"
description: "了解如何使用 Curl 在 HDInsight 上的 Hadoop 上远程运行 MapReduce 作业。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: bc6daf37-fcdc-467a-a8a8-6fb2f0f773d1
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: ccd1dffda19718a434fc09bb74a536714799740a
ms.openlocfilehash: ae39e06022a8bf53832283173dc97ef8c0e0b9ef


---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-curl"></a>使用 Curl 在 HDInsight 上的 Hadoop 上远程运行 MapReduce 作业
[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

在本文档中，你将学习如何使用 Curl 在 HDInsight 上的 Hadoop 群集中运行 MapReduce 作业。

本文档使用 Curl 演示如何通过使用原始 HTTP 请求来与 HDInsight 交互，以便运行 MapReduce 作业。 若要执行这些操作，需要使用 HDInsight 群集提供的 WebHCat REST API（前称 Templeton）。

> [!NOTE]
> 如果你已熟悉如何使用基于 Linux 的 Hadoop 服务器，但刚接触 HDInsight，请参阅[基于 Linux 的 HDInsight 上的 Hadoop 须知信息](hdinsight-hadoop-linux-information.md)。


## <a name="a-idprereqaprerequisites"></a><a id="prereq"></a>先决条件
若要完成本文中的步骤，你将需要：

* HDInsight 群集上的 Hadoop
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a name="a-idcurlarun-mapreduce-jobs-using-curl"></a><a id="curl"></a>使用 Curl 运行 MapReduce 作业
> [!NOTE]
> 使用 Curl 或者与 WebHCat 进行任何其他形式的 REST 通信时，必须通过提供 HDInsight 群集管理员用户名和密码对请求进行身份验证。 此外，还必须使用群集名称作为用来向服务器发送请求的 URI 的一部分。
> 
> 对本部分中的命令，请将 **USERNAME** 替换为要对群集进行身份验证的用户，并将 **PASSWORD** 替换为用户帐户的密码。 将 **CLUSTERNAME** 替换为群集名称。
> 
> REST API 使用[basic access authentication](http://en.wikipedia.org/wiki/Basic_access_authentication)（基本访问验证）进行保护。 你应该始终通过使用 HTTPS 来发出请求，以确保安全地将凭据发送到服务器。
> 
> 

1. 在命令行中，使用以下命令验证你是否可以连接到 HDInsight 群集：
   
        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
   
    你应会收到类似于下面的响应：
   
        {"status":"ok","version":"v1"}
   
    此命令中使用的参数如下：
   
   * **-u**：指示用来对请求进行身份验证的用户名和密码
   * **-G**：这是 GET 请求
     
     所有请求的 URI 开头都是 **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**。
2. 若要提交 MapReduce 作业，请使用以下命令：
   
        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasbs:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasbs:///example/data/gutenberg/davinci.txt -d arg=wasbs:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar
   
    URI 的末尾 (/mapreduce/jar) 告知 WebHCat 此请求将从 jar 文件中的类启动 MapReduce 作业。 此命令中使用的参数如下：
   
   * **-d**：由于不使用 `-G`，因此请求默认为使用 POST 方法。 `-d` 指定与请求一起发送的数据值。
     
     * **user.name**：正在运行命令的用户
     * **jar**：包含要运行的类的 jar 文件所在位置
     * **class**：包含 MapReduce 逻辑的类
     * **arg**：要传递到 MapReduce 作业的参数；在此示例中是用于输出的输入文本文件和目录
     
     此命令应会返回可用来检查作业状态的作业 ID：
     
       {"id":"job_1415651640909_0026"}
3. 若要检查作业的状态，请使用以下命令。 将 **JOBID** 替换为上一步骤中返回的值。 例如，如果返回值为 `{"id":"job_1415651640909_0026"}`，则 JOBID 将是 `job_1415651640909_0026`。
   
        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
   
    如果作业已完成，则状态将是“SUCCEEDED”。
   
   > [!NOTE]
   > 此 Curl 请求返回具有作业相关信息的 JSON 文档；使用 jq 可以仅检索状态值。
   > 
   > 
4. 在作业的状态更改为“SUCCEEDED”后，可以从 Azure Blob 存储中检索作业的结果。 随查询一起传递的 `statusdir` 参数包含输出文件的位置；在此示例中为 **wasbs:///example/curl**。 此地址会将作业的输出存储在 HDInsight 群集所用的默认存储容器中的 **example/curl** 目录中。

可以使用 [Azure CLI](../xplat-cli-install.md) 列出并下载这些文件。 例如，若要列出 **example/curl** 中的文件，请使用以下命令：

    azure storage blob list <container-name> example/curl

若要下载文件，请使用以下命令：

    azure storage blob download <container-name> <blob-name> <destination-file>

> [!NOTE]
> 必须使用 `-a` 和 `-k` 参数指定包含 Blob 的存储帐户名称，或者设置 **AZURE\_STORAGE\_ACCOUNT** 和 **AZURE\_STORAGE\_ACCESS\_KEY** 环境变量。 有关详细信息，请参阅[如何将数据上传到 HDInsight](hdinsight-upload-data.md)。
> 
> 

## <a name="a-idsummaryasummary"></a><a id="summary"></a>摘要
如本文档中所示，你可以使用原始 HTTP 请求来运行、监视和查看 HDInsight 群集上的 Hive 作业的结果。

有关本文中使用的 REST 接口的详细信息，请参阅 [WebHCat Reference](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)（WebHCat 参考）。

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>后续步骤
有关 HDInsight 中的 MapReduce 作业的一般信息：

* [将 MapReduce 与 HDInsight 上的 Hadoop 配合使用](hdinsight-use-mapreduce.md)

有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [将 Hive 与 Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Pig 与 Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)




<!--HONumber=Jan17_HO3-->


