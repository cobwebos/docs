---
title: "使用 Azure HDInsight 对 YARN 进行故障排除 | Microsoft Docs"
description: "获取有关使用 Apache Hadoop YARN 和 Azure HDInsight 的常见问题的解答。"
keywords: "Azure HDInsight, YARN, 常见问题解答, 故障排除指南, 常见问题"
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
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: fbcb4807aa7f6a3d6227cd630c77714c4d2834b3
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="troubleshoot-yarn-by-using-azure-hdinsight"></a>使用 Azure HDInsight 对 YARN 进行故障排除

了解处理 Apache Ambari 中的 Apache Hadoop YARN 有效负载时的最常见问题及其解决方法。

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>如何在群集上创建新的 YARN 队列？


### <a name="resolution-steps"></a>解决步骤 

在 Ambari 中使用以下步骤可以创建新的 YARN 队列，并在所有队列之间均衡容量分配。 

在此示例中，两个现有队列（**default** 和 **thriftsvr**）的容量都从 50 % 更改为 25%，因此，新队列 (Spark) 具有 50% 的容量。
| 队列 | Capacity | 最大容量 |
| --- | --- | --- | --- |
| default | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |

1. 依次选择“Abari 视图”图标和网格模式。 接下来，选择“YARN 队列管理器”。

    ![选择“Ambari 视图”图标](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
2. 选择 **default** 队列。

    ![选择 default 队列](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
3. 将 **default** 队列的**容量**从 50% 更改为 25%。 将 **thriftsvr** 队列的**容量**更改为 25%。

    ![将 default 和 thriftsvr 队列的容量更改为 25%](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
4. 若要创建新队列，请选择“添加队列”。

    ![选择“添加队列”](media/hdinsight-troubleshoot-yarn/create-queue-4.png)

5. 为新队列命名

    ![为队列 Spark 命名](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  

6. 将**容量**值保留为 50%，并选择“操作”按钮。

    ![选择“操作”按钮](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
7. 选择“保存并刷新队列”。

    ![选择“保存并刷新队列”](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

YARN 计划程序 UI 中会立即显示这些更改。

### <a name="additional-reading"></a>其他阅读材料

- [YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>如何从群集下载 YARN 日志？


### <a name="resolution-steps"></a>解决步骤 

1. 使用安全外壳 (SSH) 客户端连接到 HDInsight 群集。 有关详细信息，请参阅[其他阅读材料](#additional-reading-2)。

2. 若要列出当前正在运行的 YARN 应用程序的所有应用程序 ID，请运行以下命令：

    ```apache
    yarn top
    ```
    ID 会列在 **APPLICATIONID** 列中。 可从 **APPLICATIONID** 列下载日志。

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

3. 若要下载所有应用程序主控的 YARN 容器日志，请使用以下命令：
   
    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    此命令创建名为 amlogs.txt 的日志文件。 

4. 若只要下载最新应用程序主控的 YARN 容器日志，请使用以下命令：

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    此命令创建名为 latestamlogs.txt 的日志文件。 

4. 若要下载前两个应用程序主控的 YARN 容器日志，请使用以下命令：

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
    ```

    此命令创建名为 first2amlogs.txt 的日志文件。 

5. 若要下载所有 YARN 容器日志，请使用以下命令：

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    此命令创建名为 logs.txt 的日志文件。 

6. 若要下载特定容器的 YARN 容器日志，请使用以下命令：

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
    ```

    此命令创建名为 containerlogs.txt 的日志文件。

### <a name="additional-reading-2"></a>其他阅读材料

- [使用 SSH 连接到 HDInsight (Hadoop)](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN 的概念和应用](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/)


### <a name="see-also"></a>另请参阅
[使用 Azure HDInsight 进行故障排除](hdinsight-troubleshoot-guide.md)







