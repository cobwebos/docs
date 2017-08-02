---
title: "YARN 故障排除 - Azure HDInsight | Microsoft Docs"
description: "使用 Yarn 常见问题解答，获取有关 Azure HDInsight 平台上 Yarn 的常见问题解答。"
keywords: "Azure HDInsight, Yarn, 常见问题解答, 故障排除指南, 常见问题"
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: 
editor: 
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 576d6bfe9697d2abd6f84824960dad62e9ed3c11
ms.contentlocale: zh-cn
ms.lasthandoff: 07/11/2017


---

# <a name="yarn-troubleshooting"></a>YARN 故障排除

本文介绍处理 Apache Ambari 中的 YARN 有效负载时遇到的最常见问题及其解决方法。

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>如何在群集上创建新的 Yarn 队列

### <a name="issue"></a>问题：

在 HDInsight 群集上使用容量分配创建新的 Yarn 队列。  

### <a name="resolution-steps"></a>解决步骤： 

通过 Ambari 使用以下步骤可以创建新的 Yarn 队列，并在所有队列之间均衡容量分配。 

在此示例中，两个现有队列（default 和 thriftsvr）的容量都从 50 % 更改为 25%，因此，新队列 (Spark) 具有 50% 的容量。
| 队列 | 容量 | 最大 容量 |
| --- | --- | --- | --- |
| default | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |
1. 单击“Abari 视图”图标、网格模式，选择“Yarn 队列管理器”。

    ![单击“Ambari 视图”图标](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
1. 选择 **default** 队列。

    ![选择 default 队列](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
1. 将 **default** 队列的“容量”从 25% 更改为 50%，同样，对于 **thriftsvr** 队列，也更改为 25%。

    ![将 default 和 thriftsvr 队列的容量更改为 25%](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
1. 单击“添加队列”创建新队列。

    ![单击“添加队列”](media/hdinsight-troubleshoot-yarn/create-queue-4.png)
1. 为新队列命名

    ![为队列 Spark 命名](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  
1. 将“容量”值保留为 50%，单击“操作”按钮。

    ![单击“操作”按钮](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
1. 选择“保存并刷新队列”。

    ![选择“保存并刷新队列”](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Yarn 计划程序 UI 中会立即显示这些更改。

### <a name="further-reading"></a>延伸阅读：

- [Yarn 容量计划程序](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>如何从群集下载 Yarn 日志

#### <a name="issue"></a>问题：

需要从 HDInsight 群集下载 Yarn 应用程序主控和其他容器的日志。  

#### <a name="resolution-steps"></a>解决步骤： 

1. 使用安全外壳 (SSH) 客户端连接到 HDInsight 群集（请查看下面的“延伸阅读”部分）。
1. 使用以下命令列出当前正在运行的 Yarn 应用程序的所有应用程序 ID：

```apache
yarn top
```
ID 列在要下载其日志的 `APPLICATIONID` 列中。

```apache
YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                  APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
 application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
 application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
```

使用以下命令下载所有应用程序主控的 Yarn 容器日志：
   
```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
```

这会以文本格式创建名为 `amlogs.txt` 的日志文件。 

使用以下命令只会下载最新应用程序主控的 Yarn 容器日志：

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
```

这会以文本格式创建名为 `latestamlogs.txt` 的日志文件。 

使用以下命令下载前两个应用程序主控的 YARN 容器日志：

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
```

这会以文本格式创建名为 `first2amlogs.txt` 的日志文件。 

使用以下命令下载所有 Yarn 容器日志：

```apache
yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
```

这会以文本格式创建名为 `logs.txt` 的日志文件。 

使用以下命令下载特定容器的 Yarn 容器日志：

```apache
yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
```

这会以文本格式创建名为 `containerlogs.txt` 的日志文件。

#### <a name="further-readings"></a>延伸阅读：

- [使用 SSH 连接到 HDInsight 群集](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

- [Apache Hadoop Yarn 的概念和应用](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/)









