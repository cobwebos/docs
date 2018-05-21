---
title: 在 HDInsight 中将 Hadoop Sqoop 与 Curl 配合使用 - Azure | Microsoft Docs
description: 了解如何使用 Curl 向 HDInsight 远程提交 Sqoop 作业。
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: a83b87f1ed052c6d21d337eb37bc560efbf118ba
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>使用 Curl 在 HDInsight 中的 Hadoop 上运行 Sqoop 作业
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

学习如何使用 Curl 在 HDInsight 中的 Hadoop 群集上运行 Sqoop 作业。

本文档使用 Curl 演示如何使用原始 HTTP 请求来与 HDInsight 交互，以便运行、监视和检索 Sqoop 作业的结果。 若要执行这些操作，需要使用 HDInsight 群集提供的 WebHCat REST API（前称 Templeton）。

## <a name="prerequisites"></a>先决条件
要完成本文中的步骤，需要：

* 完成[在 HDInsight 中将 Sqoop 与 Hadoop 配合使用](hdinsight-use-sqoop.md#create-cluster-and-sql-database)，配置具有 HDInsight 群集和 Azure SQL 数据库的环境。
* [Curl](http://curl.haxx.se/). Curl 是一种将数据传入或传出 HDInsight 群集的工具。
* [jq](http://stedolan.github.io/jq/). jq 实用工具用于处理从 REST 请求返回的 JSON 数据。

## <a name="submit-sqoop-jobs-by-using-curl"></a>使用 Curl 提交 Sqoop 作业
> [!NOTE]
> 使用 Curl 或者与 WebHCat 进行任何其他形式的 REST 通信时，必须提供 HDInsight 群集管理员用户名和密码对请求进行身份验证。 此外，还必须使用群集名称作为用来向服务器发送请求的统一资源标识符 (URI) 的一部分。
> 
> 对本部分中的所有命令，请将 **USERNAME**替换为在群集上进行身份验证的用户，并将 **PASSWORD** 替换为用户帐户的密码。 将 **CLUSTERNAME** 替换为群集名称。
> 
> REST API 通过 [基本身份验证](http://en.wikipedia.org/wiki/Basic_access_authentication)进行保护。 始终应该使用安全 HTTP (HTTPS) 来发出请求，以确保安全地将凭据发送到服务器。
> 
> 

1. 在命令行中，使用以下命令验证你是否可以连接到 HDInsight 群集。

    ```bash   
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    应会收到类似于下面的响应：

    ```json   
    {"status":"ok","version":"v1"}
    ```
   
    此命令中使用的参数如下：
   
   * **-u** - 用来对请求进行身份验证的用户名和密码。
   * **-G** - 指出这是 GET 请求。
     
     URL 的开头 (**https://CLUSTERNAME.azurehdinsight.net/templeton/v1**) 对于所有请求都是相同的。 路径 **/status** 指示请求将返回服务器的 WebHCat（也称为 Templeton）状态。 
2. 使用以下命令 sqoop 作业：

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    此命令中使用的参数如下：

    * **-d** - 由于未使用 `-G`，请求将按默认使用 POST 方法。 `-d` 指定与请求一起发送的数据值。

        * **user.name** - 正在运行命令的用户。

        * **command** - 要执行的 Sqoop 命令。

        * **statusdir** - 此作业的状态要写入到的目录。

    此命令应会返回可用于检查作业状态的作业 ID。

        ```json
        {"id":"job_1415651640909_0026"}
        ```

3. 若要检查作业的状态，请使用以下命令。 将“JOBID”替换为上一步骤返回的值。 例如，如果返回值为 `{"id":"job_1415651640909_0026"}`，则 **JOBID** 将是 `job_1415651640909_0026`。

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    如果作业已完成，状态将是 **SUCCEEDED**。
   
   > [!NOTE]
   > 此 Curl 请求返回具有作业相关信息的 JavaScript 对象表示法 (JSON) 文档；使用 jq 可以仅检索状态值。
   > 
   > 
4. 在作业的状态更改为“SUCCEEDED”后，可以从 Azure Blob 存储中检索作业的结果。 随查询一起传递的 `statusdir` 参数包含输出文件的位置；在本例中，位置为 wasb:///example/data/sqoop/curl。 此地址会将作业的输出存储在 HDInsight 群集所用的默认存储容器的 example/data/sqoop/curl 目录中。
   
    可使用 Azure 门户访问 stderr 和 stdout blob。  还可使用 Microsoft SQL Server Management Studio 检查上传到 log4jlogs 表的数据。

## <a name="limitations"></a>限制
* 批量导出 - 在基于 Linux 的 HDInsight 上，用于将数据导出到 Microsoft SQL Server 或 Azure SQL 数据库的 Sqoop 连接器目前不支持批量插入。
* 批处理 - 在基于 Linux 的 HDInsight 上，如果执行插入时使用 `-batch` 开关，Sqoop 将执行多次插入而不是批处理插入操作。

## <a name="summary"></a>摘要
如本文档中所示，可以使用原始 HTTP 请求来运行、监视和查看 HDInsight 群集上的 Sqoop 作业的结果。

有关本文中使用的 REST 接口的详细信息，请参阅 <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Sqoop REST API 指南</a>。

## <a name="next-steps"></a>后续步骤
有关将 Hive 与 HDInsight 配合使用的一般信息：

* [将 Sqoop 与 HDInsight 上的 Hadoop 配合使用](hdinsight-use-sqoop.md)

有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [将 Hive 与 Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Pig 与 Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 上的 Hadoop 配合使用](hdinsight-use-mapreduce.md)

涉及 curl 的其他 HDInsight 文章:
 
* [使用 Azure REST API 创建 Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [使用 REST 在 HDInsight 中通过 Hadoop 运行 Hive 查询](apache-hadoop-use-hive-curl.md)
* [使用 REST 在 HDInsight 上通过 Hadoop 运行 MapReduce 作业](apache-hadoop-use-mapreduce-curl.md)
* [使用 cURL 配合 HDInsight 上的 Hadoop 运行 Pig 作业](apache-hadoop-use-pig-curl.md)



