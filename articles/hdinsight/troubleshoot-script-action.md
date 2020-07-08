---
title: 排查 Azure HDInsight 中的脚本操作问题
description: Azure HDInsight 中脚本操作的常规故障排除步骤。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: e8585779a263f4ff5dbdd998bbf065c6a4e1acdf
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86079252"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>排查 Azure HDInsight 中的脚本操作问题

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="viewing-logs"></a>查看日志

可以使用 Apache Ambari web UI 查看脚本操作记录的信息。 如果在创建群集期间脚本失败，可在默认的群集存储帐户中查看相关日志。 本部分提供有关如何使用这两个选项检索日志的信息。

### <a name="apache-ambari-web-ui"></a>Apache Ambari web UI

1. 在 Web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是群集的名称。

1. 从页面顶部栏中选择“操作”条目 **** 。 此时会显示通过 Ambari 在群集上执行的当前操作和以前操作的列表。

    ![选中了“操作”的 Ambari Web UI 栏](./media/troubleshoot-script-action/hdi-apache-ambari-nav.png)

1. 查找“操作”**** 列中包含 **run\_customscriptaction** 的条目。 这些条目是在运行脚本操作时创建的。

    ![“Apache Ambari 脚本操作”操作](./media/troubleshoot-script-action/ambari-script-action.png)

    若要查看 **STDOUT** 和 **STDERR** 输出，请选择 **run\customscriptaction** 条目，并通过链接向下钻取。 此输出是在脚本运行时生成的，可能包含有用的信息。

### <a name="default-storage-account"></a>默认存储器帐户

如果因脚本错误导致群集创建失败，则日志会保存在群集存储帐户中。

* 存储日志位于 `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`。

    ![脚本操作日志](./media/troubleshoot-script-action/script-action-logs-in-storage.png)

    在此目录下，日志分别针对**头节点**、**工作器节点**和 **Zookeeper 节点**进行组织。 请看以下示例：

    * **头节点**：`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **工作节点**：`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Zookeeper 节点**：`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* 相应主机的所有 **stdout** 和 **stderr** 将上传到存储帐户。 每个脚本操作各有一个 **output-\*.txt** 和 **errors-\*.txt**。 **output-*.txt** 文件包含有关在主机上运行的脚本的 URI 信息。 以下文本是此信息的示例：

    ```output
    'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'
    ```

* 有可能重复创建了同名的脚本操作群集。 在这种情况下，可以根据 **DATE** 文件夹名称来区分相关的日志。 例如，在不同日期创建的群集 **mycluster** 的文件夹结构类似于以下日志条目：

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* 如果在同一天创建同名的脚本操作群集，可以使用唯一的前缀来标识相关日志。

* 如果在临近晚上 12:00（午夜）时创建群集，则日志可能跨越两天。 在这种情况下，会看到同一群集有两个不同的日期文件夹。

* 将日志上传到默认容器可能需要 5 分钟，特别是对于大型群集。 因此，如果想要访问日志，则不应在脚本操作失败时立即删除群集。

## <a name="ambari-watchdog"></a>Ambari 监视器

不要在基于 Linux 的 HDInsight 群集上更改 Ambari 监视程序 (hdinsightwatchdog) 的密码。 密码更改会破坏在 HDInsight 群集上运行新脚本操作的能力。

## <a name="cant-import-name-blobservice"></a>无法导入名称 BlobService

__症状__。 脚本操作失败。 在 Ambari 中查看该操作时，显示类似于以下错误的文本：

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__原因__。 如果升级 HDInsight 群集中随附的 Python Azure 存储客户端，则会发生此错误。 HDInsight 需要 Azure 存储客户端 0.20.0。

__解决方法__。 若要解决此错误，请使用 `ssh` 手动连接到每个群集节点。 运行以下命令重新安装正确的存储客户端版本：

```bash
sudo pip install azure-storage==0.20.0
```

有关使用 SSH 连接到群集的信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>历史记录未显示创建群集期间使用的脚本

如果群集是在 2016 年 3 月 15 日之前创建的，则脚本操作历史记录中可能不显示任何条目。 调整群集大小后，脚本会出现在脚本操作历史记录中。

有两种例外情况：

* 群集是在 2015 年 9 月 1 日之前创建的。 这是脚本操作的推出时间。 在此日期之前创建的任何群集都不可能是使用脚本操作创建的。

* 创建群集期间使用了多个脚本操作。 或者，将相同的名称和相同的 URI 用于多个脚本，但将不同的参数用于多个脚本。 在这种情况下，将收到以下错误：

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
