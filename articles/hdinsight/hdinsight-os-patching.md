---
title: 为 Azure HDInsight 群集配置 OS 修补计划
description: 了解如何为基于 Linux 的 HDInsight 群集配置 OS 修补计划。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: d0a490fd3b23c96923af10db3c1f9ee9ea0dfad5
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044883"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>为基于 Linux 的 HDInsight 群集配置 OS 修补计划 

> [!IMPORTANT]
> Ubuntu 映像可用于在发布三个月内创建新的 Azure HDInsight 群集。 从2019年1月起，运行群集不会自动修补。 客户必须使用脚本操作或其他机制来修补正在运行的群集。 新创建的群集将始终包含最新的可用更新，其中包括最新的安全修补程序。

偶尔，你必须重启 HDInsight 群集中的虚拟机（Vm），才能安装重要的安全修补程序。

使用本文中所述的脚本操作，可以修改 OS 修补计划，如下所示：

1. 安装所有更新，或仅安装内核 + 安全更新或内核更新。
2. 立即重新启动，或在 VM 上计划重启。

> [!NOTE]  
> 本文中所述的脚本操作仅适用于2016年8月1日之后创建的基于 Linux 的 HDInsight 群集。 仅在重新启动 Vm 后，修补程序才有效。
> 脚本操作不会自动对所有将来的更新周期应用更新。 每次必须应用新更新以安装更新，然后重启 VM 时运行脚本。

## <a name="add-information-to-the-script"></a>将信息添加到脚本

使用脚本需要以下信息：

- 安装-更新-计划-重新启动脚本位置： https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh 。
    
   HDInsight 使用此 URI 在群集中的所有 Vm 上查找并运行脚本。 此脚本提供用于安装更新并重新启动 VM 的选项。
  
- 计划-重新启动脚本位置： https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh 。
    
   HDInsight 使用此 URI 在群集中的所有 Vm 上查找并运行脚本。 此脚本将重新启动 VM。
  
- 应用脚本的群集节点类型为头节点、workernode 和 zookeeper。 将脚本应用到群集中的所有节点类型。 如果脚本未应用于节点类型，则不会更新或重新启动该节点类型的 Vm。

- 安装-更新-计划-重启脚本接受两个数值参数：

    | 参数 | 定义 |
    | --- | --- |
    | 仅安装或安装所有更新/仅安装内核 + 安全更新（& a）|0、1或2。 值0仅安装内核更新。 如果值为1，则安装所有更新，而2仅安装内核 + 安全更新。 如果未提供任何参数，则默认值为0。 |
    | 无重新启动/启用计划重新启动/启用立即重新启动 |0、1或2。 值0将禁用 restart。 如果值为1，则启用计划重新启动，而2则启用立即重启。 如果未提供任何参数，则默认值为0。 用户必须将输入参数1更改为输入参数2。 |
   
 - 计划重启脚本接受一个数字参数：

    | 参数 | 定义 |
    | --- | --- |
    | 启用计划重启/启用立即重新启动 |1或2。 如果值为1，则启用计划重新启动（以12-24 小时为单位）。 如果值为2，则允许立即重新启动（5分钟）。 如果未提供任何参数，则默认值为1。 |  

> [!NOTE]
> 将脚本应用到现有群集后，必须将该脚本标记为持久化。 否则，通过缩放操作创建的任何新节点都将使用默认修补计划。 如果在群集创建过程中应用该脚本，则该脚本将自动持久保存。


## <a name="next-steps"></a>后续步骤

有关使用脚本操作的特定步骤，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)中的以下部分：

* [在创建群集期间使用脚本操作](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [将脚本操作应用到正在运行的群集](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
