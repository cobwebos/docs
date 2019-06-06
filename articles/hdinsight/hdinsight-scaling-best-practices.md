---
title: 缩放群集大小 - Azure HDInsight
description: 缩放弹性以匹配你的工作负荷的 Azure HDInsight 群集。
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: eb68421c4f62d94eedf266a0c34a0e276eacc4a6
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479281"
---
# <a name="scale-hdinsight-clusters"></a>缩放 HDInsight 群集

HDInsight 提供弹性，可让你选择扩展和缩减群集中的工作节点数。 此灵活性，可以收缩群集几个小时后或在周末，并将其展开业务高峰。

如果有定期批量处理，HDInsight 群集可以扩展该操作前的几分钟中，以便你的群集具有足够的内存和 CPU 能力。  在完成处理并且用量再次下降后，可将 HDInsight 群集缩减为更少的工作节点。

可以手动使用一种方法，如下所述的群集也可以使用[自动缩放](hdinsight-autoscale-clusters.md)选项以便让系统自动向上和向下缩放以响应 CPU、 内存和其他指标。

## <a name="utilities-to-scale-clusters"></a>用来缩放群集的实用程序

Microsoft 提供以下实用程序来缩放群集：

|实用程序 | 描述|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<群集名称> -TargetInstanceCount \<NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<群集名称> -TargetInstanceCount \<NewSize>|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resource-group \<资源组> --name \<群集名称> --target-instance-count \<NewSize>|
|[Azure CLI](hdinsight-administer-use-command-line.md)|azure hdinsight cluster resize \<clusterName> \<目标实例计数> |
|[Azure 门户](https://portal.azure.com)|打开 HDInsight 群集的窗格，在左侧菜单中选择“群集大小”，然后在“群集大小”窗格中键入工作节点数并选择“保存”。 |  

![缩放群集](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

使用以下任一方法可在几分钟之内扩展或缩放 HDInsight 群集。

> [!IMPORTANT]  
> * Aure 经典 CLI 已弃用，只能与经典部署模型配合使用。 进行所有其他的部署时，请使用 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。  
> * PowerShell AzureRM 模块已弃用。  请尽可能使用 [Az 模块](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0)。

## <a name="impact-of-scaling-operations"></a>缩放操作的影响

当您**添加**到正在运行 HDInsight 群集 （纵向扩展），任何挂起或正在运行的作业的节点不会受到影响。 在运行缩放过程时，可以安全提交新作业。 如果缩放操作出于任何原因失败，将处理失败，将你的群集保留在正常运行状态。

如果您**删除**缩放操作完成时，节点 （向下缩放），任何挂起或正在运行的作业将失败。 此失败是由于某些在缩放过程中重新启动服务。 此外，还有群集可以手动缩放操作期间获取停滞在安全模式下的风险。

## <a name="how-to-safely-scale-down-a-cluster"></a>如何安全地缩减群集

### <a name="scale-down-a-cluster-with-running-jobs"></a>减少具有正在运行的作业的群集

若要避免必须正在运行的作业在缩放减少操作过程中失败，可以尝试以下三项任务：

1. 等待作业完成后缩减群集。
1. 手动结束作业。
1. 缩放操作结束后，重新提交这些作业。

若要查看挂起的列表，以及正在运行的作业，可以使用 YARN**资源管理器 UI**，执行以下步骤：

1. 从[Azure 门户](https://portal.azure.com/)，选择你的群集。  有关说明，请参阅[列出和显示群集](./hdinsight-administer-use-portal-linux.md#showClusters)。 在群集中的新的门户页打开。
2. 在主视图中，导航到“群集仪表板”   >   “Ambari 主页”。 输入群集凭据。
3. 在 Ambari UI 的左侧菜单中的服务列表内选择“YARN”。   
4. 在“YARN”页中选择“快速链接”，将鼠标悬停在活动头节点上，然后选择“ResourceManager UI”。  

    ![ResourceManager UI](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

可以使用 `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster` 直接访问 ResourceManager UI。

可以看到作业的列表及其当前状态。 在屏幕截图中，没有一个作业正在运行：

![ResourceManager UI 应用程序](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

若要手动终止正在运行的应用程序，请通过 SSH shell 执行以下命令：

```bash
yarn application -kill <application_id>
```

例如：

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>停滞在安全模式

时减少群集时，HDInsight 使用 Apache Ambari 管理接口来首先解除授权额外的工作节点，以将其 HDFS 块复制到其他联机工作节点。 然后，HDInsight 安全地进行群集缩减。 HDFS 进入安全模式下，在缩放操作期间，并应在完成缩放后会退出。 在某些情况下，但是，HDFS 获取停滞在安全模式下缩放操作期间由于文件块未得到充分复制。

默认情况下，HDFS 配置有`dfs.replication`设置为 3，控件的每个文件块的副本数量可用。 文件块的每个副本存储在群集的不同节点上。

当 HDFS 检测到预期的块副本数不可用时，HDFS 进入安全模式和 Ambari 将生成警报。 如果 HDFS 进入安全模式对于缩放操作，但因为复制不检测所需的数量的节点，然后就不能退出安全模式下，群集可能会停滞在安全模式下。

### <a name="example-errors-when-safe-mode-is-turned-on"></a>启用安全模式时的错误示例

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

可以查看 `/var/log/hadoop/hdfs/` 文件夹中的名称节点日志，以了解缩放群集时群集进入安全模式的大致时间。 日志文件命名为 `Hadoop-hdfs-namenode-hn0-clustername.*`。

上述错误的根本原因是 Hive 在运行查询时依赖于 HDFS 中的临时文件。 当 HDFS 进入安全模式时，Hive 无法运行查询，因为它无法写入 HDFS。 HDFS 中的临时文件位于已装入到各个工作节点 VM 的本地驱动器上，并且在其他工作节点之间至少复制成三个副本。

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>如何防止 HDInsight 停滞在安全模式

可通过多种方法防止 HDInsight 保留在安全模式：

* 在缩减 HDInsight 之前停止所有 Hive 作业。 或者，计划好缩减进程，以避免与运行中的 Hive 作业冲突。
* 执行缩减操作之前，在 HDFS 中手动清理 Hive 的 scratch `tmp` 目录文件。
* 只将 HDInsight 缩减为三个工作节点（最少数量）。 避免将工作节点数减少至一个。
* 根据需要运行命令来退出安全模式。

以下部分将介绍这些选项。

#### <a name="stop-all-hive-jobs"></a>停止所有 Hive 作业

在缩减至一个工作节点之前停止所有 Hive 作业。 如果已计划工作负荷，请在完成 Hive 工作后执行缩减。

在缩放之前停止的 Hive 作业，可帮助最大程度减少将临时文件夹中的 scratch 文件数 （如果有）。

#### <a name="manually-clean-up-hives-scratch-files"></a>手动清理 Hive 的 scratch 文件

如果 Hive 遗留了临时文件，可以在缩减之前手动清理这些文件，以避免进入安全模式。

1. 检查哪些位置正由 Hive 临时文件查看`hive.exec.scratchdir`配置属性。 此参数设置内`/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. 停止 Hive 服务，并确保所有查询和作业都已完成。
2. 列出 scratch 目录，上面找到的内容`hdfs://mycluster/tmp/hive/`以查看它是否包含任何文件：

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

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>缩放到三个或多个辅助角色节点的 HDInsight

如果你的群集时缩减少于 3 个辅助角色节点，需要经常获取停滞在安全模式和上一步骤不起作用，则可以避免进入安全模式完全通过保留至少三个辅助角色节点群集。

保留三个工作节点向下缩放到只有一个辅助节点比更高但停滞在安全模式将阻止你的群集。

#### <a name="run-the-command-to-leave-safe-mode"></a>运行命令来退出安全模式。

最后一个选项是执行保留安全模式的命令。 如果您知道，HDFS 进入安全模式的原因是 Hive 文件未得到充分复制是因为，你可以执行以下命令来退出安全模式：


```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>缩减的 Apache HBase 群集

区域服务器会自动完成缩放操作后的几分钟内进行平衡。 若要手动均衡区域服务器，请完成以下步骤：

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
* [缩放群集](hdinsight-administer-use-portal-linux.md#scale-clusters)
