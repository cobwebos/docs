---
title: 缩放群集大小 - Azure HDInsight | Microsoft Docs
description: 根据工作负荷缩放 HDInsight 群集。
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: ashish
ms.openlocfilehash: 8b76d7d0441a5c1c25ad17b73083ec0e4feef1fe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="scale-hdinsight-clusters"></a>缩放 HDInsight 群集

HDInsight 提供弹性，可让你选择扩展和缩减群集中的工作节点数。 这样，便可以在若干小时后或者在周末收缩群集，或者在业务高峰期扩展群集。

例如，如果要每隔一天或每隔一个月执行某种批处理一次，则可以在该计划事件之前的几分钟扩展 HDInsight 群集，以便有足够的内存和 CPU 计算能力。 可以使用 PowerShell cmdlet [`Set–AzureRmHDInsightClusterSize`](hdinsight-administer-use-powershell.md#scale-clusters) 自动缩放。  在完成处理并且用量再次下降后，可将 HDInsight 群集缩减为更少的工作节点。

* 通过 [PowerShell](hdinsight-administer-use-powershell.md) 缩放群集：

    ```powershell
    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    ```
    
* 通过 [Azure CLI](hdinsight-administer-use-command-line.md) 缩放群集：

    ```
    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
    ```
    
* 若要通过 [Azure 门户](https://portal.azure.com)缩放群集，请打开 HDInsight 群集的窗格，在左侧菜单中选择“缩放群集”，然后在“缩放群集”窗格中键入工作节点数并选择“保存”。

    ![缩放群集](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

使用以下任一方法可在几分钟之内扩展或缩放 HDInsight 群集。

## <a name="scaling-impacts-on-running-jobs"></a>缩放对运行的作业的影响

将节点**添加**到正在运行的 HDInsight 群集时，任何挂起的或正在运行的作业不受影响。 此外，在运行缩放过程时，可以安全提交新作业。 如果缩放操作出于任何原因而失败，系统会正常处理失败，让群集保持正常运行状态。

但是，如果通过**删除**节点来缩减群集，则当缩放操作完成时，任何挂起的或正在运行的作业将会失败。 发生这种失败的原因是在此过程中某些服务重启。

若要解决此问题，可以等到作业完成后再缩减群集、手动终止作业，或者在缩放操作结束后重新提交作业。

若要查看挂起的和正在运行的作业列表，可以遵循以下步骤使用 YARN ResourceManager UI：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，依次选择“浏览”、“HDInsight 群集”和自己的群集。
3. 在 HDInsight 群集窗格中，选择顶部菜单中的“仪表板”打开 Ambari UI。 输入群集登录凭据。
4. 在左侧菜单中的服务列表内单击“YARN”。 在“YARN”页上选择“快速链接”，将鼠标悬停在活动头节点上，然后单击“ResourceManager UI”。

    ![ResourceManager UI](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

可以使用 `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` 直接访问 ResourceManager UI。

可以看到作业的列表及其当前状态。 在屏幕截图中，当前有一个作业正在运行：

![ResourceManager UI 应用程序](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

若要手动终止正在运行的应用程序，请通过 SSH shell 执行以下命令：

```bash
yarn application -kill <application_id>
```

例如：

```bash
yarn application -kill "application_1499348398273_0003"
```

## <a name="rebalancing-an-hbase-cluster"></a>重新均衡 HBase 群集

在完成缩放操作后的几分钟内，区域服务器会自动进行均衡。 若要手动均衡区域服务器，请使用以下步骤：

1. 使用 SSH 连接到 HDInsight 群集。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

2. 启动 HBase shell：

        hbase shell

3. 使用以下命令手动均衡区域服务器：

        balancer

## <a name="scale-down-implications"></a>缩减的影响

如前所述，在完成缩减操作时，任何挂起的或正在运行的作业都会终止。 但是，缩减操作还可能造成其他一些影响。

## <a name="hdinsight-name-node-stays-in-safe-mode-after-scaling-down"></a>执行缩减操作后，HDInsight 名称节点会停留安全模式

![缩放群集](./media/hdinsight-scaling-best-practices/scale-cluster.png)

如果将群集缩减到最少量的（一个）工作节点（如上图所示），则在由于修补而重新启动工作节点时，HDFS 可能会停滞在安全模式，或者在执行缩放操作后立即发生这种情况。

发生此问题的主要原因是 Hive 使用一些 `scratchdir` 文件，并且默认预期每个块有三个副本，但是，如果缩减到最少量的（一个）工作节点，则只会有一个副本。 因此，`scratchdir` 中的文件复制数量不足。 在完成缩放操作后重启服务时，这可能导致 HDFS 保留在安全模式。

在尝试缩减时，HDInsight 首先依赖于 Ambari 管理接口解除额外的多余工作节点，以将其 HDFS 块复制到其他联机工作节点，然后安全缩减群集。 HDFS 在维护时段进入安全模式，在完成缩放后应会退出此模式。 此时，HDFS 可能会停滞在安全模式。

HDFS 是使用 `dfs.replication`设置 3 配置的。 因此，每当联机工作节点数少于三个时，scratch 文件块的复制数据就会不足，因为预期会为每个文件块提供三个副本。

可以执行某个命令来使 HDFS 脱离安全模式。 例如，如果知道启用安全模式的唯一原因是临时文件复制数量不足，则可以安全地退出安全模式。 这是因为，复制数量不足的文件是 Hive 临时 scratch 文件。

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

退出安全模式后，可以手动删除临时文件，或等待 Hive 最终自动清除这些文件。

### <a name="example-errors-when-safe-mode-is-turned-on"></a>启用安全模式时的错误示例

* H070 无法打开 Hive 会话。 org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Cannot create directory** /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. 名称节点处于安全模式。 报告的块数为 75，需要额外的 12 个块才能达到总块数 87 的阈值 0.9900。 实时数据节点数 10 已达到最小数字 0。 达到阈值后，安全模式会自动禁用。

* H100 Unable to submit statement show databases: org.apache.thrift.transport.TTransportException: org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: **Connection refused**

* H020 无法连接到 hn0-hdisrv.servername.bx.internal.cloudapp.net:10001: org.apache.thrift.transport.TTransportException: 无法以 http 连接到 http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/。 org.apache.http.conn.HttpHostConnectException: 连接到 hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] 失败: 拒绝连接: org.apache.thrift.transport.TTransportException: 无法以 http 连接到 http://hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/。 org.apache.http.conn.HttpHostConnectException: Connect to hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] failed: **Connection refused**

* From the Hive logs: WARN [main]: server.HiveServer2 (HiveServer2.java:startHiveServer2(442)) – Error starting HiveServer2 on attempt 21, will retry in 60 seconds java.lang.RuntimeException: Error applying authorization policy on hive configuration: org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Cannot create directory** /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374. 名称节点处于安全模式。
    报告的块数为 0，需要额外的 9 个块才能达到总块数 9 的阈值 0.9900。
    实时数据节点数 10 已达到最小数字 0。 **达到阈值后，安全模式会自动禁用**。
    at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324)

可以查看 `/var/log/hadoop/hdfs/` 文件夹中的名称节点日志，以了解缩放群集时群集进入安全模式的大致时间。 日志文件命名为 `Hadoop-hdfs-namenode-hn0-clustername.*`。

上述错误的根本原因是 Hive 在运行查询时依赖于 HDFS 中的临时文件。 当 HDFS 进入安全模式时，Hive 无法运行查询，因为它无法写入 HDFS。 HDFS 中的临时文件位于已装入到各个工作节点 VM 的本地驱动器上，并且在其他工作节点之间至少复制成三个副本。

Hive 中的 `hive.exec.scratchdir` 参数在 `/etc/hive/conf/hive-site.xml` 中配置：

```xml
<property>
    <name>hive.exec.scratchdir</name>
    <value>hdfs://mycluster/tmp/hive</value>
</property>
```

### <a name="view-the-health-and-state-of-your-hdfs-file-system"></a>查看 HDFS 文件系统的运行状况和状态

可以查看每个名称节点中的状态报告，以确定节点是否处于安全模式。 若要查看报告，请通过 SSH 连接到每个头节点并运行以下命令：

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode get
```

![安全模式关闭](./media/hdinsight-scaling-best-practices/safe-mode-off.png)

> [!NOTE]
> 必须使用 `-D` 开关，因为 HDInsight 中的默认文件系统为 Azure 存储或 Azure Data Lake Store。 `-D` 指定针对本地 HDFS 文件系统执行命令。

接下来，可以查看显示 HDFS 状态详细信息的报告：

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -report
```

对于已根据预期程度复制了所有块的正常群集，此命令会生成以下信息：

![安全模式关闭](./media/hdinsight-scaling-best-practices/report.png)

HDFS 支持使用 `fsck` 命令来检查各个文件的不一致性，例如文件缺少块，或块的复制数量不足。 针对 `scratchdir`（临时 scratch 磁盘）文件运行 `fsck` 命令：

```
hdfs fsck -D 'fs.default.name=hdfs://mycluster/' /tmp/hive/hive
```

在未出现块复制数量不足的正常 HDFS 文件系统上执行此命令时，可以看到如下所示的输出：

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:07:01 UTC 2017
..Status: HEALTHY
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
 Minimally replicated blocks:   2 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          4
 Number of racks:               1
FSCK ended at Thu Jul 06 20:07:01 UTC 2017 in 3 milliseconds


The filesystem under path '/tmp/hive/hive' is HEALTHY
```

相反，在出现块复制数量不足的 HDFS 文件系统上执行 `fsck` 命令时，将看到如下所示的输出：

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:13:58 UTC 2017
.
/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info:  Under replicated BP-1867508080-10.0.0.21-1499348422953:blk_1073741826_1002. Target Replicas is 3 but found 1 live replica(s), 0 decommissioned replica(s) and 0 decommissioning replica(s).
.
/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: CORRUPT blockpool BP-1867508080-10.0.0.21-1499348422953 block blk_1073741825

/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: MISSING 1 blocks of total size 26 B.Status: CORRUPT
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
  ********************************
  UNDER MIN REPL'D BLOCKS:      1 (50.0 %)
  dfs.namenode.replication.min: 1
  CORRUPT FILES:        1
  MISSING BLOCKS:       1
  MISSING SIZE:         26 B
  CORRUPT BLOCKS:       1
  ********************************
 Minimally replicated blocks:   1 (50.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       1 (50.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     0.5
 Corrupt blocks:                1
 Missing replicas:              2 (33.333332 %)
 Number of data-nodes:          1
 Number of racks:               1
FSCK ended at Thu Jul 06 20:13:58 UTC 2017 in 28 milliseconds


The filesystem under path '/tmp/hive/hive' is CORRUPT
```

也可以在 Ambari UI 中（选择左侧的“HDFS”服务）或使用 `https://<HDInsightClusterName>.azurehdinsight.net/#/main/services/HDFS/summary` 查看 HDFS 状态。

![Ambari HDFS 状态](./media/hdinsight-scaling-best-practices/ambari-hdfs.png)

活动或待机 NameNodes 上还可能出现一种或多种严重错误。 若要查看 NameNode 块的运行状况，请选择警报旁边的“NameNode”链接。

![NameNode 块的运行状况](./media/hdinsight-scaling-best-practices/ambari-hdfs-crit.png)

若到清理 scratch 文件以消除块复制错误，请通过 SSH 连接到每个头节点并运行以下命令：

```
hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
```

> [!NOTE]
> 如果某些作业仍在运行，此命令可能会中断 Hive。

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode-due-to-under-replicated-blocks"></a>如何防止块复制数量不足导致 HDInsight 停滞在安全模式

可通过多种方法防止 HDInsight 保留在安全模式：

* 在缩减 HDInsight 之前停止所有 Hive 作业。 或者，计划好缩减进程，以避免与运行中的 Hive 作业冲突。
* 执行缩减操作之前，在 HDFS 中手动清理 Hive 的 scratch `tmp` 目录文件。
* 只将 HDInsight 缩减为三个工作节点（最少数量）。 避免将工作节点数减少至一个。
* 根据需要运行命令来退出安全模式。

以下部分将介绍这些选项。

#### <a name="stop-all-hive-jobs"></a>停止所有 Hive 作业

在缩减至一个工作节点之前停止所有 Hive 作业。 如果已计划工作负荷，请在完成 Hive 工作后执行缩减。

这有助于将临时文件夹中的 scratch 文件数减至最少。

#### <a name="manually-clean-up-hives-scratch-files"></a>手动清理 Hive 的 scratch 文件

如果 Hive 遗留了临时文件，可以在缩减之前手动清理这些文件，以避免进入安全模式。

1. 停止 Hive 服务，并确保所有查询和作业都已完成。

2. 列出 `hdfs://mycluster/tmp/hive/` 目录的内容，以确定它是否包含任何文件：

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```
    
    下面是存在文件时的示例输出：

    ```
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. 如果知道 Hive 已处理这些文件，则可以删除这些文件。 查看 Yarn ResourceManager UI 页，确保 Hive 中没有任何正在运行的查询。

    用于从 HDFS 中删除文件的示例命令行：

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```
    
#### <a name="scale--hdinsight-to-three-worker-nodes"></a>将 HDInsight 缩减为三个工作节点

如果持续停滞在安全模式，并且上述步骤不起作用，则只需缩减为三个工作节点就能避免该问题。 这种方法可能不是最佳的，因为与缩减为一个节点相比，它的成本更高。 但是，如果只有一个工作节点，则 HDFS 无法保证为群集提供三个数据副本。

#### <a name="run-the-command-to-leave-safe-mode"></a>运行命令来退出安全模式。

最后一种做法是观察 HDFS 在哪种极少见的情况下进入安全模式，然后执行退出安全模式的命令。 确定 HDFS 进入安全模式的原因是 Hive 文件复制数量不足后，请执行以下命令退出安全模式：

* Linux 上的 HDInsight：

    ```bash
    hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
    ```
    
* Windows 上的 HDInsight：

    ```bash
    hdfs dfsadmin -fs hdfs://headnodehost:9000 -safemode leave
    ```
    
## <a name="next-steps"></a>后续步骤

* [Azure HDInsight 简介](hadoop/apache-hadoop-introduction.md)
* [缩放群集](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [使用 Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)
