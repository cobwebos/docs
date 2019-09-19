---
title: Apache HBase Master 无法在 Azure HDInsight 中启动
description: Apache HBase Master （HMaster）无法在 Azure HDInsight 中启动
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: d994fe1501dedf6a8ea2c3366f6559c7abac0892
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091609"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master （HMaster）无法在 Azure HDInsight 中启动

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="scenario-atomic-renaming-failure"></a>场景：原子重命名失败

### <a name="issue"></a>问题

启动过程中发现意外文件。

### <a name="cause"></a>原因

在启动过程中，HMaster 执行大量的初始化步骤，包括将数据从暂存（.tmp）文件夹移动到 data 文件夹。 HMaster 还查看预写日志（WAL）文件夹，查看是否有任何无响应的区域服务器。

HMaster 对 WAL 文件夹执行基本列表命令。 在任何时候，如果 HMaster 发现其中任何一个文件夹中存在意外的文件，则会引发异常并且不会启动。

### <a name="resolution"></a>分辨率

检查调用堆栈，并尝试确定哪个文件夹可能导致问题（例如，它可能是 WAL 文件夹或 .tmp 文件夹）。 然后，通过 Cloud Explorer 或 HDFS 命令尝试找到问题文件。 通常，这是一个`*-renamePending.json`文件。 （该`*-renamePending.json`文件是用于在 WASB 驱动程序中实现原子重命名操作的日志文件。 由于此实现中的 bug，在发生进程崩溃之类的问题后，这些文件可能会保留。）可以通过 Cloud Explorer 或 HDFS 命令强制删除此文件。

有时，可能还有一个名为的临时文件，如`$$$.$$$`此位置。 必须使用 HDFS `ls` 命令查看此文件，而不能在 Cloud Explorer 中查看。 若要删除此文件，请使用 HDFS 命令 `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`。

运行这些命令后，HMaster 应会立即启动。

---

## <a name="scenario-no-server-address-listed"></a>场景：未列出任何服务器地址

### <a name="issue"></a>问题

你可能会看到一条消息，指示`hbase: meta`该表未处于联机状态。 运行`hbck`可能会`hbase: meta table replicaId 0 is not found on any region.`报告在 HMaster 日志中，你可能会看到以下消息`No server address listed in hbase: meta for region hbase: backup <region name>`：。  

### <a name="cause"></a>原因

重新启动 HBase 后，HMaster 无法初始化。

### <a name="resolution"></a>分辨率

1. 在 HBase shell 中输入以下命令（根据情况更改实际值）：

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. `hbase: namespace`删除条目。 此条目可能与扫描`hbase: namespace`表时报告的错误相同。

1. 从 Ambari UI 重启主动 HMaster 以使 HBase 恢复运行状态。

1. 在 HBase shell 中，运行以下命令使所有脱机表联机：

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>方案： IOException：超时

### <a name="issue"></a>问题

HMaster 超时，类似于： `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`。

### <a name="cause"></a>原因

如果有大量表和区域且在重启 HMaster 服务时未刷新，则会遇到此问题。 超时是 HMaster 的已知缺陷。 一般群集启动任务可能会花费很长时间。 如果尚未分配命名空间表，HMaster 将关闭。 长时间启动任务发生在大量未刷新数据存在并且超时为五分钟的情况下。

### <a name="resolution"></a>分辨率

1. 从 Apache Ambari UI 中转到**HBase** > **配置。** 在自定义`hbase-site.xml`文件中添加以下设置：

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. 重启所需的服务（HMaster，可能还有其他 HBase 服务）。

---

## <a name="scenario-frequent-region-server-restarts"></a>场景：频繁区域服务器重启

### <a name="issue"></a>问题

节点定期重启。 从区域服务器日志中，你可能会看到类似于以下内容的条目：

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>原因

长`regionserver` JVM GC 暂停。 暂停将导致`regionserver`无响应，并且无法在 zk 会话超时 .40s 中向 HMaster 发送检测信号。 HMaster 将认为`regionserver`是死信，将`regionserver`中止并重新启动。

### <a name="resolution"></a>分辨率

更改 Zookeeper `hbase-site`会话超时，不仅要更改设置`zookeeper.session.timeout` ，还需要更改 Zookeeper `zoo.cfg`设置`maxSessionTimeout` 。

1. 访问 Ambari UI，转到**HBase-> 配置-> 设置**，在 "超时" 部分中，更改 Zookeeper 会话超时值。

1. 访问 Ambari UI，转到**Zookeeper-> 配置-> 自定义** `zoo.cfg`，添加/更改以下设置。 请确保该值与 HBase `zookeeper.session.timeout`相同。

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. 重启所需的服务。

---

## <a name="scenario-log-splitting-failure"></a>场景：日志拆分失败

### <a name="issue"></a>问题

HMasters 无法在 HBase 群集上启动。

### <a name="cause"></a>原因

为辅助存储帐户配置了错误的 HDFS 和 HBase 设置。

### <a name="resolution"></a>分辨率

设置 rootdir： wasb://@.blob.core.windows.net/hbase并在 Ambari 上重新启动服务。

---

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
