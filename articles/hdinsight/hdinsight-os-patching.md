---
title: 为 Azure HDInsight 群集配置 OS 修补计划
description: 了解如何为基于 Linux 的 HDInsight 群集配置 OS 修补计划。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: 005bfd4b7e7d062640a79896ccd3d7f76e3d6fb3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447199"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>为基于 Linux 的 HDInsight 群集配置 OS 修补计划

> [!IMPORTANT]
> Ubuntu 映像可在发布后的三 个月内用于创建新的 Azure HDInsight 群集。 不会自动修补正在运行的群集。 客户必须使用脚本操作或其他机制来修补正在运行的群集。 作为最佳做法，你可以在创建群集后运行这些脚本操作并立即应用安全更新。

HDInsight 为你提供支持，允许你在群集上执行常见任务，例如安装 OS 修补程序、安全更新，以及重启节点。 这些任务使用下述两个脚本来完成，这两个脚本可以作为[脚本操作](hdinsight-hadoop-customize-cluster-linux.md)运行，并且可以配置参数：

- `schedule-reboots.sh` - 在群集节点上立即重启或计划重启。
- `install-updates-schedule-reboots.sh` - 安装所有更新、仅安装内核 + 安全更新，或者仅安装内核更新。

> [!NOTE]  
> 脚本操作不会自动应用所有未来更新周期的更新。 每次必须应用新更新以安装更新并重启 VM 时，请运行这些脚本。

## <a name="preparation"></a>准备工作

在部署到生产环境之前，在具有代表性的非生产环境中打补丁。 制定计划，以便在实际打补丁之前对系统进行充分测试。

在与群集的 ssh 会话中，你可能会不时收到消息，其中指出“有安全更新程序可用”。 该消息可能如下所示：

```
89 packages can be updated.
82 updates are security updates.

*** System restart required ***

Welcome to Spark on HDInsight.

```

打补丁是可选的，由你自行决定。

## <a name="restart-nodes"></a>重启节点
  
脚本 [schedule-reboots](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh) 设置将要在群集中的计算机上执行的重启的类型。 提交脚本操作时，请将其设置为应用到所有三个节点类型：头节点、辅助角色节点和 zookeeper。 如果未将此脚本应用于某个节点类型，则不会更新或重启该节点类型的 VM。

`schedule-reboots script` 接受一个数字参数：

| 参数 | 接受的值 | 定义 |
| --- | --- | --- |
| 要执行的重启类型 | 1 或 2 | 值为 1 表示启用计划重启（计划在 12-24 小时内重启）。 值为 2 表示启用即时重启（在 5 分钟内重启） 如果未提供任何参数，则默认值为 1。 |  

## <a name="install-updates-and-restart-nodes"></a>安装更新并重启节点

脚本 [install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) 提供的选项用于安装不同类型的更新并重启 VM。

`install-updates-schedule-reboots` 脚本接受两个数字参数，如下表所述：

| 参数 | 接受的值 | 定义 |
| --- | --- | --- |
| 要安装的更新的类型 | 0、1 或 2 | 值为 0 表示仅安装内核更新。 值为 1 表示安装所有更新，为 2 表示仅安装内核 + 安全更新。 如果未提供任何参数，则默认值为 0。 |
| 要执行的重启类型 | 0、1 或 2 | 值为 0 表示禁用重启。 值为 1 表示启用计划重启，为 2 表示启用即时重启。 如果未提供任何参数，则默认值为 0。 用户必须更改输入参数 1 才能输入参数 2。 |

> [!NOTE]
> 在将某个脚本应用到现有群集后，必须将其标记为持久性脚本。 否则，通过缩放操作创建的任何新节点都将使用默认修补计划。 如果在群集创建过程中应用该脚本，则其会自动持久化。

> [!NOTE]
> “计划重启”选项在 12 到 24 小时内自动滚动重启已修补的群集节点，并考虑到高可用性、更新域和容错域方面的考虑因素。 计划重启不会终止正在运行的工作负载，但可能会在节点不可用期间降低群集容量，从而导致处理时间更长。 

## <a name="next-steps"></a>后续步骤

若要了解使用脚本操作的具体步骤，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)中的以下部分:

- [在创建群集期间使用脚本操作](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [将脚本操作应用到正在运行的群集](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
