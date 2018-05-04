---
title: 在 HDInsight 中将 Hadoop Pig 与 REST 配合使用 — Azure | Microsoft Docs
description: 了解如何使用 REST 在 Azure HDInsight 中的 Hadoop 群集上运行 Pig Latin 作业。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: ed5e10d1-4f47-459c-a0d6-7ff967b468c4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 4883794261116abf4925e7e4e9a8df14626c7a71
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-rest"></a>使用 REST 通过 HDInsight 上的 Hadoop 运行 Pig 作业

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

了解如何通过向 Azure HDInsight 群集发出 REST 请求运行 Pig Latin 作业。 Curl 用于演示如何使用 WebHCat REST API 与 HDInsight 交互。

> [!NOTE]
> 如果已熟悉如何使用基于 Linux 的 Hadoop 服务器，但刚接触 HDInsight，请参阅[基于 Linux 的 HDInsight 提示](../hdinsight-hadoop-linux-information.md)。

## <a id="prereq"></a>先决条件

* Azure HDInsight（HDInsight 上的 Hadoop）群集（基于 Linux 或 Windows）

  > [!IMPORTANT]
  > Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>通过使用 Curl 运行 Pig 作业

> [!NOTE]
> REST API 通过[基本访问身份验证](http://en.wikipedia.org/wiki/Basic_access_authentication)进行保护。 始终使用安全 HTTP (HTTPS) 发出请求，以确保安全地将凭据发送到服务器。
>
> 使用本部分中的命令时，请将 `USERNAME` 替换为要向群集进行身份验证的用户，并将 `PASSWORD` 替换为用户帐户的密码。 将 `CLUSTERNAME` 替换为群集的名称。
>


1. 在命令行中，使用以下命令验证你是否可以连接到 HDInsight 群集。

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    应收到以下 JSON 响应：

        {"status":"ok","version":"v1"}

    此命令中使用的参数如下：

    * **-u**：用于对请求进行身份验证的用户名和密码
    * **-G**：指示此请求是 GET 请求

     URL 的开头 (**https://CLUSTERNAME.azurehdinsight.net/templeton/v1**) 对于所有请求都是相同的。 路径 **/status** 指示请求是要返回服务器的 WebHCat（也称为 Templeton）状态。

2. 使用以下代码将 Pig Latin 作业提交到群集：

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    此命令中使用的参数如下：

    * **-d**：由于未使用 `-G`，因此该请求默认为使用 POST 方法。 `-d` 指定与请求一起发送的数据值。

    * **user.name**：正在运行命令的用户
    * **execute**：要执行的 Pig Latin 语句
    * **statusdir**：此作业的状态要写入到的目录

    > [!NOTE]
    > 请注意，在与 Curl 配合使用时，将使用 `+` 字符替换 Pig Latin 语句中的空格。

    此命令应会返回可用来检查作业状态的作业 ID，例如：

        {"id":"job_1415651640909_0026"}

3. 若要检查作业的状态，请使用以下命令

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     将 `JOBID` 替换为上一步骤返回的值。 例如，如果返回值为 `{"id":"job_1415651640909_0026"}`，则 `JOBID` 将是 `job_1415651640909_0026`。

    如果作业已完成，状态将是 **SUCCEEDED**。

    > [!NOTE]
    > 此 Curl 请求返回具有作业相关信息的 JavaScript 对象表示法 (JSON) 文档；使用 jq 可以仅检索状态值。

## <a id="results"></a>查看结果

当作业的状态更改为“成功”后，可检索作业结果。 随查询一起传递的 `statusdir` 参数包含输出文件的位置；在本例中，该位置为 `/example/pigcurl`。

HDInsight 可以使用 Azure 存储或 Azure Data Lake Store 作为默认数据存储。 有多种方法可访问数据，具体取决于使用哪种存储。 有关详细信息，请参阅[基于 Linux 的 HDInsight 信息](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store)文档的存储部分。

## <a id="summary"></a>摘要

如本文档中所示，可以使用原始 HTTP 请求来运行、监视和查看 HDInsight 群集上的 Pig 作业的结果。

有关本文中使用的 REST 接口的详细信息，请参阅 [WebHCat 参考](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)。

## <a id="nextsteps"></a>后续步骤

有关 HDInsight 上的 Pig 的一般信息：

* [将 Pig 与 Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)

有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [将 Hive 与 Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)
* [将 MapReduce 与 HDInsight 上的 Hadoop 配合使用](hdinsight-use-mapreduce.md)
