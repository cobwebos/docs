---
title: Apache HBase Master 无法在 Azure HDInsight 中启动
description: Apache HBase Master (HMaster) 无法在 Azure HDInsight 中启动
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/06/2019
ms.openlocfilehash: 8368ebfca4cdd72c5c455a04e29b6c0cb44938ea
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935401"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Apache HBase Master (HMaster) 无法在 Azure HDInsight 中启动

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="scenario-atomic-renaming-failure"></a>方案:原子重命名失败

### <a name="issue"></a>问题

启动过程中发现意外文件。

### <a name="cause"></a>原因

在启动过程中, HMaster 执行大量的初始化步骤, 包括将数据从暂存 (.tmp) 文件夹移动到 data 文件夹。 HMaster 还会查看 Wal (预写日志) 文件夹, 以查看是否存在任何死区服务器。 在所有这些情况下, 它都会对`list`这些文件夹执行基本命令。 如果在任何时候都看到其中任何一个文件夹中出现意外的文件, 则会引发异常, 因此不会启动。

### <a name="resolution"></a>解决

在这种情况下, 请检查 "调用堆栈" 以查看哪个文件夹可能导致问题 (例如, 它是 Wal 文件夹还是 .tmp 文件夹)。 然后通过 Cloud Explorer 或通过 hdfs 命令查找问题文件。 问题文件通常是一个`*-renamePending.json`文件 (用于在 WASB 驱动程序中实现原子重命名操作的日志文件)。 由于此实现中的 bug, 在发生进程崩溃的情况下, 可以保留此类文件。 通过 Cloud Explorer 强制删除此文件。 此外, 此位置中还可能存在性质 $ 的临时文件。 此文件无法通过 cloud explorer 查看, 只能通过 hdfs `ls`命令查看。 可以使用 hdfs 命令`hdfs dfs -rm //\$\$\$.\$\$\$`删除此文件。

删除问题文件后, HMaster 应立即启动。

---

## <a name="scenario-no-server-address-listed"></a>方案:未列出任何服务器地址

### <a name="issue"></a>问题

HMaster 日志显示一条错误消息, 该消息类似于 "用于区域 xxx 的 hbase: meta 中未列出的服务器地址"。

### <a name="cause"></a>原因

重新启动 HBase 后, HMaster 无法初始化。

### <a name="resolution"></a>解决

1. 在 HBase shell 上执行以下命令 (根据情况更改实际值):

    ```
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>' 
    ```

1. 删除 hbase: namespace 的条目，因为扫描 hbase: namespace 表时可能会报告相同的错误。

1. 从 Ambari UI 重启主动 HMaster 以使 HBase 恢复运行状态。

1. 在 HBase shell 中运行以下命令来使所有脱机表联机：

    ```
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>方案: IOException:超时

### <a name="issue"></a>问题

HMaster 超时, 如所示`java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`。

### <a name="cause"></a>原因

超时是 HMaster 的已知缺陷。 一般群集启动任务可能会花费很长时间。 如果尚未分配命名空间表, HMaster 将关闭。 长时间启动任务发生在大量未刷新数据存在并且超时为五分钟的情况下。

### <a name="resolution"></a>解决

1. 访问 Ambari UI, 中转到 HBase > 配置, 在 "自`hbase-site.xml`定义" 中添加以下设置:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. 重启所需的服务（主要是 HMaster，可能还有其他 HBase 服务）。

---

## <a name="scenario-frequent-regionserver-restarts"></a>方案:频繁的 regionserver 重启

### <a name="issue"></a>问题

节点定期重启。 在 regionserver 日志中, 你可能会看到类似于以下内容的条目:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>原因

长 regionserver JVM GC 暂停。 暂停会导致 regionserver 无响应, 并且无法在 zk 会话超时 .40s 内向 HMaster 发送检测信号。 HMaster 会相信 regionserver 是死的, 将中止 regionserver, 然后重新启动。

### <a name="resolution"></a>解决

更改 zookeeper 会话超时, 而不仅是 hbase 站点设置`zookeeper.session.timeout` , 还需要更改 zookeeper 的 zoo 设置。 `maxSessionTimeout`

1. 访问 Ambari UI, 转到**HBase-> 配置-> 设置**, 在 "超时" 部分中, 更改 Zookeeper 会话超时值。

1. 访问 Ambari UI, 转到**Zookeeper-> 配置-> 自定义**zoo, 添加/更改以下设置。 请确保该值与 hbase `zookeeper.session.timeout`相同。

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. 重启所需的服务。

---

## <a name="scenario-log-splitting-failure"></a>方案:日志拆分失败

### <a name="issue"></a>问题

HMasters 无法在 HBase 群集上启动。

### <a name="cause"></a>原因

为辅助存储帐户配置了错误的 HDFS 和 HBase 设置。

### <a name="resolution"></a>解决

设置 rootdir: wasb://@.blob.core.windows.net/hbase并在 Ambari 上重新启动服务。

---

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
