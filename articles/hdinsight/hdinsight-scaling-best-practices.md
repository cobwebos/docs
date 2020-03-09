---
title: 缩放群集大小 - Azure HDInsight
description: 缩放 Apache Hadoop 群集弹性，以匹配 Azure HDInsight 中的工作负荷
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: a88682cd2217850e336afc2f9ef5af84c0d8cb82
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389509"
---
# <a name="scale-azure-hdinsight-clusters"></a>缩放 Azure HDInsight 群集

HDInsight 提供弹性，可让你选择纵向扩展和纵向缩减群集中的工作节点数。 使用此弹性，你可以在数小时或周末缩减群集，并在高峰业务需求期间将其展开。

如果有定期批处理，HDInsight 群集可以在该操作之前的几分钟内扩展，以便群集具有足够的内存和 CPU 能力。  稍后，在完成处理后，使用再次出现故障时，可以将 HDInsight 群集缩小到更少的辅助角色节点。

你可以使用下述方法之一手动缩放群集，或使用[自动缩放](hdinsight-autoscale-clusters.md)选项使系统在响应 CPU、内存和其他指标时自动增加和减少。

> [!NOTE]  
> 只支持使用 HDInsight 3.1.3 或更高版本的群集。 如果不确定群集的版本，可以查看“属性”页。

## <a name="utilities-to-scale-clusters"></a>用于缩放群集的实用工具

Microsoft 提供了以下实用程序来缩放群集：

|实用工具 | 说明|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<群集名称 >-TargetInstanceCount \<NewSize >|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<群集名称 >-TargetInstanceCount \<NewSize >|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --资源组 \<资源组 >--Name \<群集名称 >--Workernode \<NewSize >|
|[Azure 经典 CLI](hdinsight-administer-use-command-line.md)|azure hdinsight 群集大小 \<clusterName > \<目标实例计数 > |
|[Azure 门户](https://portal.azure.com)|打开 HDInsight 群集窗格，在左侧菜单中选择 "**群集大小**"，然后在 "群集大小" 窗格中，键入辅助角色节点的数量，然后选择 "保存"。|  

![Azure 门户缩放群集选项](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

使用以下任一方法可在几分钟之内扩展或缩放 HDInsight 群集。

> [!IMPORTANT]  
> * Azure 经典 CLI 已弃用，只应与经典部署模型配合使用。 对于所有其他部署，使用[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。  
> * PowerShell AzureRM 模块已弃用。  请尽可能使用[Az 模块](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0)。

## <a name="impact-of-scaling-operations"></a>缩放操作的影响

将节点**添加**到正在运行的 HDInsight 群集（向上缩放）时，任何挂起或正在运行的作业都不会受到影响。 在运行缩放过程时，可以安全提交新作业。 如果缩放操作由于任何原因而失败，则会处理失败，使群集处于正常工作状态。

如果**删除**节点（向下缩放），则在缩放操作完成时，任何挂起或正在运行的作业都将失败。 此失败的原因是在缩放过程中某些服务重新启动。 此外，在执行手动缩放操作期间，你的群集可能会停滞在安全模式。

对于 HDInsight 支持的每种类型的群集，更改数据节点数的影响有所不同：

* Apache Hadoop

    可以顺利地增加正在运行的 Hadoop 群集中的辅助节点数，而不会影响任何挂起或运行中的作业。 还可以在操作进行中提交新作业。 系统会正常处理失败的缩放操作，让群集始终保持正常运行状态。

    减少数据节点数目以缩减 Hadoop 群集时，系统会重新启动群集中的某些服务。 此行为会导致所有正在运行和挂起的作业在缩放操作完成时失败。 但是，可以在操作完成后重新提交这些作业。

* Apache HBase

    可以顺利地在 HBase 群集运行时对其添加或删除节点。 在完成缩放操作后的几分钟内，区域服务器就能自动平衡。 不过，也可以手动平衡区域服务器，方法是登录到群集的头节点，并在命令提示符窗口中运行以下命令：

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    有关使用 HBase shell 的详细信息，请参阅 [HDInsight 中的 Apache HBase 示例入门](hbase/apache-hbase-tutorial-get-started-linux.md)。

* Apache Storm

    可以顺利地在 Storm 群集运行时对其添加或删除数据节点。 但是，在缩放操作成功完成后，需要重新平衡拓扑。

    可以使用两种方法来完成重新平衡操作：

  * Storm Web UI
  * 命令行界面 (CLI) 工具

    有关详细信息，请参阅 [Apache Storm 文档](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。

    HDInsight 群集上提供了 Storm Web UI：

    ![HDInsight Storm 规模重新平衡](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    以下是用于重新平衡 Storm 拓扑的示例 CLI 命令：

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>如何安全地缩减群集

### <a name="scale-down-a-cluster-with-running-jobs"></a>缩小包含正在运行的作业的群集

若要避免在向下缩放操作期间运行的作业失败，可以尝试执行以下三项操作：

1. 请等待作业完成，然后再缩减群集。
1. 手动结束作业。
1. 缩放操作结束后重新提交作业。

若要查看挂起和正在运行的作业的列表，可以使用 YARN**资源管理器 UI**，操作步骤如下：

1. 在[Azure 门户](https://portal.azure.com/)中，选择群集。  有关说明，请参阅[列出和显示群集](./hdinsight-administer-use-portal-linux.md#showClusters)。 群集在新的门户页中打开。
2. 在主视图中，导航到 "**群集仪表板**" > **Ambari home**"。 输入群集凭据。
3. 在 Ambari UI 中，在左侧菜单上的 "服务" 列表中选择 " **YARN** "。  
4. 从 "YARN" 页上，选择 "**快速链接**" 并将鼠标悬停在活动头节点上，然后选择 " **ResourceManager UI**"。

    ![Apache Ambari 快速链接 ResourceManager UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

可以使用 `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` 直接访问 ResourceManager UI。

可以看到作业的列表及其当前状态。 在屏幕截图中，有一个当前正在运行的作业：

![ResourceManager UI 应用程序](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

若要手动终止正在运行的应用程序，请通过 SSH shell 执行以下命令：

```bash
yarn application -kill <application_id>
```

例如：

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>在安全模式下停滞

当你向下缩放群集时，HDInsight 使用 Apache Ambari 管理界面首先解除额外的辅助角色节点，将其 HDFS 块复制到其他联机工作节点。 之后，HDInsight 会安全地降低群集的规模。 在缩放操作期间，HDFS 进入安全模式，并且应在缩放完成后发出。 但是，在某些情况下，由于文件块在复制下，HDFS 在缩放操作期间会停滞在安全模式下。

默认情况下，HDFS 配置了 `dfs.replication` 设置为1，这会控制每个文件块的可用副本数。 文件块的每个副本都存储在群集的其他节点上。

当 HDFS 检测到所需的块副本数不可用时，HDFS 将进入安全模式，并且 Ambari 会生成警报。 如果 HDFS 为缩放操作进入安全模式，但随后无法退出安全模式，因为没有检测到所需的节点数进行复制，则群集可能会停滞在安全模式下。

### <a name="example-errors-when-safe-mode-is-turned-on"></a>启用安全模式时的错误示例

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

可以查看 `/var/log/hadoop/hdfs/` 文件夹中的名称节点日志，以了解缩放群集时群集进入安全模式的大致时间。 日志文件命名为 `Hadoop-hdfs-namenode-<active-headnode-name>.*`。

上述错误的根本原因是 Hive 在运行查询时依赖于 HDFS 中的临时文件。 当 HDFS 进入安全模式时，Hive 无法运行查询，因为它无法写入 HDFS。 HDFS 中的临时文件位于已装入到各个工作节点 VM 的本地驱动器上，并且在其他工作节点之间至少复制成三个副本。

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>如何防止 HDInsight 在安全模式下停滞

可通过多种方法防止 HDInsight 保留在安全模式：

* 在缩减 HDInsight 之前停止所有 Hive 作业。 或者，计划好纵向缩减进程，以避免与运行中的 Hive 作业冲突。
* 执行缩减操作之前，在 HDFS 中手动清理 Hive 的 scratch `tmp` 目录文件。
* 只将 HDInsight 纵向缩减为三个工作节点（最少数量）。 避免将工作节点数减少至一个。
* 根据需要运行命令来退出安全模式。

以下部分将介绍这些选项。

#### <a name="stop-all-hive-jobs"></a>停止所有 Hive 作业

在缩减至一个工作节点之前停止所有 Hive 作业。 如果已计划工作负荷，请在完成 Hive 工作后执行缩减。

在缩放之前停止 Hive 作业有助于最大程度地减少 tmp 文件夹（如果有）中的暂存文件数。

#### <a name="manually-clean-up-hives-scratch-files"></a>手动清理 Hive 的 scratch 文件

如果 Hive 遗留了临时文件，可以在缩减之前手动清理这些文件，以避免进入安全模式。

1. 通过查看 `hive.exec.scratchdir` 配置属性来检查是否正在将哪个位置用于 Hive 临时文件。 在 `/etc/hive/conf/hive-site.xml`中设置此参数：

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. 停止 Hive 服务，并确保所有查询和作业都已完成。
2. 列出上面找到的暂存目录的内容，`hdfs://mycluster/tmp/hive/` 查看其是否包含任何文件：

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    下面是存在文件时的示例输出：

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. 如果知道 Hive 已处理这些文件，则可以删除这些文件。 查看 Yarn ResourceManager UI 页，确保 Hive 中没有任何正在运行的查询。

    用于从 HDFS 中删除文件的示例命令行：

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>将 HDInsight 缩放为三个或更多个工作节点

如果群集在向下缩放到三个以上的辅助角色节点时频繁停滞在安全模式下，而前面的步骤不起作用，则可以通过至少保留三个工作节点来避免群集进入安全模式。

与仅向下扩展到一个工作节点相比，保留三个工作节点的成本更高，但它会阻止群集进入安全模式。

### <a name="scale-hdinsight-down-to-one-worker-node"></a>将 HDInsight 缩小到一个工作节点

即使群集缩小到1个节点，工作节点0仍将保留。 工作节点0不能解除授权。

#### <a name="run-the-command-to-leave-safe-mode"></a>运行命令来退出安全模式。

最后一种方法是执行 "退出安全模式" 命令。 如果你知道 HDFS 进入安全模式的原因是由于 Hive 文件在复制下，则可以执行以下命令来退出安全模式：

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>缩小 Apache HBase 群集

在完成缩放操作后的几分钟内，区域服务器会自动进行平衡。 若要手动平衡区域服务器，请完成以下步骤：

1. 使用 SSH 连接到 HDInsight 群集。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

2. 启动 HBase shell：

    ```bash
    hbase shell
    ```

3. 使用以下命令手动均衡区域服务器：

    ```bash
    balancer
    ```

## <a name="next-steps"></a>后续步骤

* [自动缩放 Azure HDInsight 群集](hdinsight-autoscale-clusters.md)
* [Azure HDInsight 简介](hadoop/apache-hadoop-introduction.md)
