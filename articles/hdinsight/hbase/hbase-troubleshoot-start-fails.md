---
title: Apache HBase Master 无法在 Azure HDInsight 中启动
description: Apache HBase Master (HMaster) 无法在 Azure HDInsight 中启动
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "75887200"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) 无法在 Azure HDInsight 中启动

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="scenario-atomic-renaming-failure"></a>方案：原子重命名失败

### <a name="issue"></a>问题

启动过程中识别到意外的文件。

### <a name="cause"></a>原因

在启动过程中，HMaster 会执行许多初始化步骤，包括将数据从暂存文件夹 (.tmp) 移到数据文件夹。 HMaster 还会查看预写日志 (WAL) 文件夹中是否存在任何无响应的区域服务器。

HMaster 会对 WAL 文件夹执行一个基本的 list 命令。 在任何时候，如果 HMaster 发现其中任何一个文件夹中存在意外的文件，则会引发异常并且不会启动。

### <a name="resolution"></a>解决方法

检查调用堆栈并尝试确定哪个文件夹可能导致问题（例如，是 WAL 文件夹还是 .tmp 文件夹）。 然后，通过 Cloud Explorer 或 HDFS 命令尝试找到问题文件。 通常这是一个 `*-renamePending.json` 文件。 （`*-renamePending.json` 文件是用于在 WASB 驱动程序中实现原子重命名操作的日记文件。 由于此实现中的 bug，在发生进程崩溃之类的问题后，这些文件可能会保留。）可以通过 Cloud Explorer 或 HDFS 命令强制删除此文件。

有时，此位置还可能存在名称类似于 `$$$.$$$` 的临时文件。 必须使用 HDFS `ls` 命令查看此文件，而不能在 Cloud Explorer 中查看。 若要删除此文件，请使用 HDFS 命令 `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`。

运行这些命令后，HMaster 应会立即启动。

---

## <a name="scenario-no-server-address-listed"></a>方案：未列出服务器地址

### <a name="issue"></a>问题

可能会看到一条消息，指示 `hbase: meta` 表未联机。 运行 `hbck` 可能会报告 `hbase: meta table replicaId 0 is not found on any region.` 在 HMaster 日志中，可能会看到以下消息：`No server address listed in hbase: meta for region hbase: backup <region name>`。  

### <a name="cause"></a>原因

重启 HBase 后 HMaster 无法初始化。

### <a name="resolution"></a>解决方法

1. 在 HBase shell 中输入以下命令（根据情况更改实际值）：

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. 删除 `hbase: namespace` 条目。 此条目可能是扫描 `hbase: namespace` 表时报告的相同错误。

1. 从 Ambari UI 重启活动 HMaster，使 HBase 恢复运行状态。

1. 在 HBase shell 中，运行以下命令使所有脱机表联机：

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>场景：java.io.IOException:超时

### <a name="issue"></a>问题

HMaster 超时并出现如下所示的严重异常：`java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`。

### <a name="cause"></a>原因

如果有大量表和区域且在重启 HMaster 服务时未刷新，则会遇到此问题。 超时是 HMaster 中的一个已知缺陷。 一般群集启动任务可能会花费很长时间。 如果命名空间表尚未分配，HMaster 将会关闭。 如果存在大量未刷新的数据，而五分钟超时值不足以完成启动任务，则启动任务会长时间运行。

### <a name="resolution"></a>解决方法

1. 在 Apache Ambari UI 中，转到“HBase” > “配置”   。 在自定义 `hbase-site.xml` 文件中添加以下设置：

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. 重启所需的服务（HMaster，可能还有其他 HBase 服务）。

---

## <a name="scenario-frequent-region-server-restarts"></a>方案：区域服务器频繁重启

### <a name="issue"></a>问题

节点定期重新启动。 在区域服务器日志中，可能会看到如下所示的条目：

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>原因

`regionserver` JVM GC 长时间暂停。 暂停会导致 `regionserver` 无响应，并且无法在 40 秒 zk 会话超时期限内向 HMaster 发送检测信号。 HMaster 将认为 `regionserver` 已死机，因此会中止 `regionserver` 并重启。

### <a name="resolution"></a>解决方法

更改 Zookeeper 会话超时，同时，不仅需要更改 `hbase-site` 设置 `zookeeper.session.timeout`，而且还要更改 Zookeeper `zoo.cfg` 设置 `maxSessionTimeout`。

1. 访问 Ambari UI，转到“HBase”->“配置”->“设置”，在“超时”部分，更改 Zookeeper 会话超时值。 

1. 访问 Ambari UI，转到“Zookeeper”->“配置”->“自定义”-> `zoo.cfg`，并添加/更改以下设置。  确保该值与 HBase `zookeeper.session.timeout` 相同。

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. 重启所需的服务。

---

## <a name="scenario-log-splitting-failure"></a>方案：日志拆分失败

### <a name="issue"></a>问题

HMasters 无法在 HBase 群集上启动。

### <a name="cause"></a>原因

错误地配置了辅助存储帐户的 HDFS 和 HBase 设置。

### <a name="resolution"></a>解决方法

设置 hbase.rootdir: wasb://@.blob.core.windows.net/hbase，并在 Ambari 中重启服务。

---

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
