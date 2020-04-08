---
title: 缩放群集大小 - Azure HDInsight
description: 弹性扩展 Apache Hadoop 群集，以匹配 Azure HDInsight 中的工作负荷
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 0fc067f0be4ac5d2b8fa7db9ad7999efe06625a0
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804497"
---
# <a name="scale-azure-hdinsight-clusters"></a>缩放 Azure HDInsight 群集

HDInsight 提供了弹性，提供了扩展和缩小群集中辅助节点数量的选项。 这种弹性允许您在下班后或周末收缩群集。 并在高峰业务需求期间扩展它。

在定期批处理之前向上扩展群集，以便群集具有足够的资源。 处理完成后，使用量下降，将 HDInsight 群集缩减为更少的辅助节点。

您可以使用下面概述的方法之一手动缩放群集。 您还可以使用[自动缩放](hdinsight-autoscale-clusters.md)选项来自动向上和向下扩展以响应某些指标。

> [!NOTE]  
> 只支持使用 HDInsight 3.1.3 或更高版本的群集。 如果不确定群集的版本，可以查看“属性”页。

## <a name="utilities-to-scale-clusters"></a>用来缩放群集的实用程序

Microsoft 提供以下实用程序来缩放群集：

|实用工具 | 说明|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[`Set-AzHDInsightClusterSize`](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[`Set-AzureRmHDInsightClusterSize`](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) `-ClusterName CLUSTERNAME -TargetInstanceCount NEWSIZE`|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) | [`az hdinsight resize`](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) `--resource-group RESOURCEGROUP --name CLUSTERNAME --workernode-count NEWSIZE`|
|[Azure 经典 CLI](hdinsight-administer-use-command-line.md)|`azure hdinsight cluster resize CLUSTERNAME NEWSIZE` |
|[Azure 门户](https://portal.azure.com)|打开 HDInsight 群集的窗格，在左侧菜单中选择“群集大小”，然后在“群集大小”窗格中键入工作节点数并选择“保存”。****|  

![Azure 门户缩放群集选项](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

使用以下任一方法可在几分钟之内扩展或缩放 HDInsight 群集。

> [!IMPORTANT]  
> * Azure 经典 CLI 已弃用，应仅与经典部署模型一起使用。 对于所有其他部署，请使用 Azure [CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。
> * PowerShell AzureRM 模块已弃用。  请尽可能使用 [Az 模块](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0)。

## <a name="impact-of-scaling-operations"></a>缩放操作的影响

当您**将节点添加到**正在运行的 HDInsight 群集（向上扩展）时，作业不会受到影响。 在运行缩放过程时，可以安全提交新作业。 如果缩放操作失败，故障将使群集处于功能状态。

如果**删除**节点（缩小），则挂起或正在运行的作业将在缩放操作完成后失败。 此失败是由于缩放过程中某些服务重新启动。 在手动缩放操作期间，群集可能会卡在安全模式。

对于 HDInsight 支持的每种类型的群集，更改数据节点数的影响有所不同：

* Apache Hadoop

    您可以无缝地增加正在运行的 Hadoop 群集中的辅助节点数，而不会影响任何作业。 还可以在操作进行中提交新作业。 缩放操作中的故障得到正常处理。 群集始终处于功能状态。

    当 Hadoop 群集以较少的数据节点进行缩减时，某些服务将重新启动。 此行为会导致所有正在运行和挂起的作业在缩放操作完成时失败。 但是，可以在操作完成后重新提交这些作业。

* Apache HBase

    您可以在 HBase 群集运行时无缝添加或删除节点。 在完成缩放操作后的几分钟内，区域服务器就能自动平衡。 但是，您可以手动平衡区域服务器。 登录到群集头节点并运行以下命令：

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    有关使用 HBase shell 的详细信息，请参阅 [HDInsight 中的 Apache HBase 示例入门](hbase/apache-hbase-tutorial-get-started-linux.md)。

* Apache Storm

    您可以在 Storm 运行时无缝添加或删除数据节点。 但是，在成功完成缩放操作后，您需要重新平衡拓扑。

    可以使用两种方法来完成重新平衡操作：

  * Storm Web UI
  * 命令行界面 (CLI) 工具

    有关详细信息，请参阅[阿帕奇风暴文档](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。

    HDInsight 群集上提供了 Storm Web UI：

    ![HDInsight Storm 规模重新平衡](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    以下是用于重新平衡 Storm 拓扑的示例 CLI 命令：

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>如何安全地纵向缩减群集

### <a name="scale-down-a-cluster-with-running-jobs"></a>通过运行的作业纵向缩减群集

为了避免运行的作业在纵向缩减操作过程中失败，可以尝试三项操作：

1. 等待作业完成之后再纵向缩减群集。
1. 手动结束作业。
1. 在缩放操作完成后重新提交这些作业。

要查看挂起和正在运行的作业的列表，可以使用 YARN**资源管理器 UI，** 按照以下步骤操作：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择群集。  有关说明，请参阅[列出和显示群集](./hdinsight-administer-use-portal-linux.md#showClusters)。 群集会在新的门户页中打开。
2. 从主视图导航到**群集仪表板** > **Ambari 主页**。 输入群集凭据。
3. 在 Ambari UI 的左侧菜单中的服务列表内选择“YARN”。****  
4. 从 YARN 页面中，选择 **"快速链接"** 并将鼠标悬停在活动头节点上，然后选择**资源管理器 UI**。

    ![阿帕奇·安巴里快速链接资源管理器 UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

您可以直接使用`https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`访问资源管理器 UI。

可以看到作业的列表及其当前状态。 在屏幕截图中，当前有一个作业正在运行：

![资源管理器 UI 应用程序](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

若要手动终止正在运行的应用程序，请通过 SSH shell 执行以下命令：

```bash
yarn application -kill <application_id>
```

例如：

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>停滞在安全模式下

缩小群集时，HDInsight 使用 Apache Ambari 管理接口首先停用额外的辅助节点。 节点将其 HDFS 块复制到其他联机辅助节点。 然后，HDInsight 安全地纵向缩减群集。 HDFS 在缩放操作期间进入安全模式。 HDFS 应该在缩放完成后出来。 但在某些情况下，HDFS 会在缩放操作期间停滞在安全模式下，因为文件块复制数量不足。

默认情况下，HDFS 配置的`dfs.replication`设置为 1，用于控制每个文件块可用的副本数。 文件块的每个副本存储在群集的不同节点上。

当预期的块副本数不可用时，HDFS 进入安全模式，Ambari 生成警报。 HDFS 可能进入缩放操作的安全模式。 如果未检测到复制所需的节点数，群集可能会卡在安全模式下。

### <a name="example-errors-when-safe-mode-is-turned-on"></a>启用安全模式时的错误示例

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

可以查看 `/var/log/hadoop/hdfs/` 文件夹中的名称节点日志，以了解缩放群集时群集进入安全模式的大致时间。 日志文件命名为 `Hadoop-hdfs-namenode-<active-headnode-name>.*`。

根本原因是 Hive 在运行查询时依赖于 HDFS 中的临时文件。 当 HDFS 进入安全模式时，Hive 无法运行查询，因为它无法写入 HDFS。 HDFS 中的临时文件位于装载到单个辅助节点 VM 的本地驱动器中。 这些文件在三个副本的其他辅助节点之间复制，最小。

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>如何防止 HDInsight 停滞在安全模式下

可通过多种方法防止 HDInsight 保留在安全模式：

* 在缩减 HDInsight 之前停止所有 Hive 作业。 或者，计划好缩减进程，以避免与运行中的 Hive 作业冲突。
* 执行缩减操作之前，在 HDFS 中手动清理 Hive 的 scratch `tmp` 目录文件。
* 只将 HDInsight 缩减为三个工作节点（最少数量）。 避免将工作节点数减少至一个。
* 根据需要运行命令来退出安全模式。

以下部分将介绍这些选项。

#### <a name="stop-all-hive-jobs"></a>停止所有 Hive 作业

在缩减至一个工作节点之前停止所有 Hive 作业。 如果已计划工作负荷，请在完成 Hive 工作后执行缩减。

在缩放之前停止 Hive 作业有助于将临时文件夹中的 scratch 文件（如果有）的数目减至最少。

#### <a name="manually-clean-up-hives-scratch-files"></a>手动清理 Hive 的 scratch 文件

如果 Hive 遗留了临时文件，可以在缩减之前手动清理这些文件，以避免进入安全模式。

1. 通过查看 `hive.exec.scratchdir` 配置属性，了解用于 Hive 临时文件的具体位置。 此参数在 `/etc/hive/conf/hive-site.xml` 中设置：

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. 停止 Hive 服务，并确保所有查询和作业都已完成。

1. 列出在上面找到的 scratch 目录 `hdfs://mycluster/tmp/hive/` 的内容，看其是否包含任何文件：

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

1. 如果知道 Hive 已处理这些文件，则可以删除这些文件。 通过在 Yarn 资源管理器 UI 页中查找，确保 Hive 没有任何查询运行。

    用于从 HDFS 中删除文件的示例命令行：

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>缩减 HDInsight 时保持三个或更多个工作器节点

如果群集在向下扩展到少于三个辅助节点时经常陷入安全模式，则至少保留三个辅助节点。

拥有三个辅助节点比仅缩减到一个辅助节点的成本更高。 但是，此操作将防止群集陷入安全模式。

### <a name="scale-hdinsight-down-to-one-worker-node"></a>将 HDInsight 缩放到一个辅助节点

即使群集缩小到一个节点，辅助节点 0 仍将存活。 辅助节点 0 永远不会停用。

#### <a name="run-the-command-to-leave-safe-mode"></a>运行命令来退出安全模式。

最后一种做法是执行退出安全模式的命令。 如果 HDFS 由于 Hive 文件复制不足而进入安全模式，请执行以下命令以离开安全模式：

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>纵向缩减 Apache HBase 群集

在完成缩放操作后的几分钟内，区域服务器会自动进行均衡。 若要手动均衡区域服务器，请完成以下步骤：

1. 使用 SSH 连接到 HDInsight 群集。 有关详细信息，请参阅将[SSH 与 HDInsight 一起使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

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
