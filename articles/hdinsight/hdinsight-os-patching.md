---
title: 为 Azure HDInsight 群集配置 OS 修补计划
description: 了解如何为基于 Linux 的 HDInsight 群集配置 OS 修补计划。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: f8e694f658d6e9de04c92001214ecd5c32ff7753
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206854"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>为基于 Linux 的 HDInsight 群集配置 OS 修补计划

> [!IMPORTANT]
> Ubuntu 映像可用于在发布三个月内创建新的 Azure HDInsight 群集。 从2019年1月起，运行群集不会自动修补。 客户必须使用脚本操作或其他机制来修补正在运行的群集。 新创建的群集将始终包含最新的可用更新，其中包括最新的安全修补程序。

HDInsight 支持你在群集上执行常见任务，例如安装 OS 修补程序、安全更新和重启节点。 使用以下两个可作为[脚本操作](hdinsight-hadoop-customize-cluster-linux.md)运行的脚本，并使用参数配置这些任务：

- `schedule-reboots.sh`-立即重新启动，或在群集节点上计划重启。
- `install-updates-schedule-reboots.sh` 安装所有更新、仅限内核 + 安全更新或仅限内核更新。

> [!NOTE]  
> 脚本操作不会自动对所有将来的更新周期应用更新。 每次必须应用新更新以安装更新，然后重启 VM 时运行脚本。

## <a name="preparation"></a>准备

在部署到生产环境之前，在有代表性的非生产环境中进行修补。 制定计划，在实际修补之前充分测试系统。

从与群集的 ssh 会话的时间上来说，你可能会收到一条消息，指出升级可用。 消息可能如下所示：

```
New release '18.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade it
```

修补是可选的，可自行决定。

## <a name="restart-nodes"></a>重新启动节点
  
脚本[计划-重新启动](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)，设置将在群集中的计算机上执行的重新启动类型。 提交脚本操作时，将其设置为适用于所有三种节点类型：头节点、辅助角色节点和 zookeeper。 如果脚本未应用于节点类型，则不会更新或重新启动该节点类型的 Vm。

`schedule-reboots script` 接受一个数字参数：

| 参数 | 接受的值 | Definition |
| --- | --- | --- |
| 要执行的重新启动类型 | 1或2 | 如果值为1，则启用计划重新启动（以12-24 小时为单位）。 如果值为2，则允许立即重新启动（5分钟）。 如果未提供任何参数，则默认值为1。 |  

## <a name="install-updates-and-restart-nodes"></a>安装更新并重启节点

脚本[install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh)提供了安装不同类型的更新和重新启动 VM 的选项。

`install-updates-schedule-reboots` 脚本接受两个数值参数，如下表中所述：

| 参数 | 接受的值 | Definition |
| --- | --- | --- |
| 要安装的更新的类型 | 0、1或2 | 值0仅安装内核更新。 如果值为1，则安装所有更新，而2仅安装内核 + 安全更新。 如果未提供任何参数，则默认值为0。 |
| 要执行的重新启动类型 | 0、1或2 | 值0将禁用 restart。 如果值为1，则启用计划重新启动，而2则启用立即重启。 如果未提供任何参数，则默认值为0。 用户必须将输入参数1更改为输入参数2。 |

> [!NOTE]
> 将脚本应用到现有群集后，必须将该脚本标记为持久化。 否则，通过缩放操作创建的任何新节点都将使用默认修补计划。 如果在群集创建过程中应用该脚本，则该脚本将自动持久保存。

## <a name="next-steps"></a>后续步骤

有关使用脚本操作的特定步骤，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)中的以下部分：

- [在创建群集期间使用脚本操作](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [将脚本操作应用到正在运行的群集](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
