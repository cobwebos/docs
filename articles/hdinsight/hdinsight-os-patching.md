---
title: 配置 OS 修补计划对于基于 Linux 的 HDInsight 群集-Azure
description: 了解如何为基于 Linux 的 HDInsight 群集配置 OS 修补计划。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: efe74618b269000749f7ba6c24d35903e540dcfb
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657052"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>配置 OS 修补计划的基于 Linux 的 HDInsight 群集 

> [!IMPORTANT]
> Ubuntu 映像都可用于创建在三个月发布的新 Azure HDInsight 群集。 截至年 1 月 2019年正在运行的群集不自动修补。 客户必须使用脚本操作或其他机制来修补正在运行的群集。 新创建的群集将始终包含最新的可用更新，其中包括最新的安全修补程序。

有时，必须在 HDInsight 群集中安装重要的安全修补程序来重新启动虚拟机 (Vm)。

通过使用本文中所述的脚本操作，可以修改 OS 修补计划，如下所示：

1. 安装所有更新，或者仅内核 + 安全更新或内核更新。
2. 立即重新启动，或计划在 VM 上的重启。

> [!NOTE]  
> 本文中所述的脚本操作将仅使用 2016 年 8 月 1 日之后创建的基于 Linux 的 HDInsight 群集。 修补程序在重新启动 Vm 后才会生效。
> 脚本操作不会自动应用更新为所有将来的更新周期。 每次必须应用新的更新以安装更新，然后重新启动 VM 运行的脚本。

## <a name="add-information-to-the-script"></a>将信息添加到脚本

使用脚本需要以下信息：

- 安装-更新-计划的重新启动脚本位置： https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh 。
    
   HDInsight 使用此 URI 来查找并在群集中的所有 Vm 上运行该脚本。 此脚本提供选项来安装更新和重启 VM。
  
- 在计划重启脚本位置： https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh 。
    
   HDInsight 使用此 URI 来查找并在群集中的所有 Vm 上运行该脚本。 此脚本重新启动 VM。
  
- 该脚本应用到群集节点类型是头节点、 辅助角色节点和 zookeeper。 将脚本应用到群集中的所有节点类型。 如果脚本未应用于节点类型，不会更新或重新启动该节点类型的 Vm。

- 安装更新的计划重新启动脚本接受两个数字参数：

    | 参数 | 定义 |
    | --- | --- |
    | 内核仅安装更新 / 安装所有更新/都安装内核 + 安全性仅更新|0、 1 或 2。 值为 0 将仅安装内核更新。 如果值为 1 将安装所有更新，以及两次都安装仅内核 + 安全更新。 如果没有提供任何参数，默认值为 0。 |
    | 不重启/启用计划重启/启用立即重新启动 |0、 1 或 2。 值为 0 会禁用重新启动。 值为 1 启用计划的重新启动，并 2 将启用立即重新启动。 如果没有提供任何参数，默认值为 0。 用户必须更改输入的参数 1 的输入参数 2。 |
   
 - 计划重新启动脚本接受一个数字参数：

    | 参数 | 定义 |
    | --- | --- |
    | 启用计划重启/启用立即重新启动 |1 或 2。 值为 1 将启用计划重新启动 （12-24 小时内已计划）。 值为 2 将启用立即重新启动 （以 5 分钟为单位）。 如果未不指定任何参数，默认值为 1。 |  

> [!NOTE]
> 必须将脚本标记为保存后将其应用于现有的群集。 否则，通过缩放操作创建的任何新节点都将使用默认修补计划。 如果将脚本应用作为群集创建过程的一部分，它具有自动持久化。


## <a name="next-steps"></a>后续步骤

有关使用脚本操作的特定步骤，请参阅以下各节中的[使用脚本操作自定义的基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md):

* [在创建群集期间使用脚本操作](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [将脚本操作应用到正在运行的群集](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
