---
title: 为基于 Linux 的 HDInsight 群集配置 OS 修补计划-Azure
description: 了解如何为基于 Linux 的 HDInsight 群集配置 OS 修补计划。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 06111ec35a127cf17fdcc77ff717de7a4bc7299f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076857"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>为基于 Linux 的 HDInsight 群集配置 OS 修补计划 

> [!IMPORTANT]
> Ubuntu 映像可在发布后的三 个月内用于创建新的 Azure HDInsight 群集。 自 2019 年 1 月起，系统不会自动修补正在运行的群集。 客户必须使用脚本操作或其他机制来修补正在运行的群集。 新创建的群集将始终包含最新的可用更新，其中包括最新的安全修补程序。

偶然情况下，必须重启 HDInsight 群集中的虚拟机 (VM)，以便安装重要的安全修补程序。

可以使用本文中介绍的脚本操作将 OS 修补计划修改如下：

1. 安装所有更新、仅安装内核 + 安全更新，或者仅安装内核更新。
2. 立即重启 VM 或计划重启 VM。

> [!NOTE]  
> 本文中介绍的脚本操作将仅适用于 2016 年 8 月 1 日以后创建的基于 Linux 的 HDInsight 群集。 修补程序仅在重启 VM 后生效。
> 脚本操作不会自动应用所有未来更新周期的更新。 每次必须应用新更新以安装更新并重启 VM 时，请运行这些脚本。

## <a name="add-information-to-the-script"></a>将信息添加到脚本

使用脚本需要以下信息：

- install-updates-schedule-reboots 脚本位置： https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh 。
    
   HDInsight 使用此 URI 在群集中的所有 VM 上查找并运行脚本。 此脚本提供安装更新并重启 VM 的选项。
  
- schedule-reboots 脚本位置： https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh 。
    
   HDInsight 使用此 URI 在群集中的所有 VM 上查找并运行脚本。 此脚本可重启 VM。
  
- 脚本所适用的群集节点类型为头节点、辅助角色节点和 zookeeper。 将此脚本应用于群集中的所有节点类型。 如果未将此脚本应用于某个节点类型，则不会更新或重启该节点类型的 VM。

- install-updates-schedule-reboots 脚本接受两个数字参数：

    | 参数 | 定义 |
    | --- | --- |
    | 仅安装内核更新/安装所有更新/仅安装内核 + 安全更新|0、1 或 2。 值为 0 表示仅安装内核更新。 值为 1 表示安装所有更新，为 2 表示仅安装内核 + 安全更新。 如果未提供任何参数，则默认值为 0。 |
    | 不重启/启用计划重启/启用即时重启 |0、1 或 2。 值为 0 表示禁用重启。 值为 1 表示启用计划重启，为 2 表示启用即时重启。 如果未提供任何参数，则默认值为 0。 用户必须更改输入参数 1 才能输入参数 2。 |
   
 - schedule-reboots 脚本接受一个数字参数：

    | 参数 | 定义 |
    | --- | --- |
    | 启用计划重启/启用即时重启 |1 或 2。 值为 1 表示启用计划重启（计划在 12-24 小时内重启）。 值为 2 表示启用即时重启（在 5 分钟内重启） 如果未提供任何参数，则默认值为 1。 |  

> [!NOTE]
> 在将某个脚本应用到现有群集后，必须将其标记为持久性脚本。 否则，通过缩放操作创建的任何新节点都将使用默认修补计划。 如果在群集创建过程中应用该脚本，则其会自动持久化。


## <a name="next-steps"></a>后续步骤

若要了解使用脚本操作的具体步骤，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)中的以下部分:

* [在创建群集期间使用脚本操作](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [将脚本操作应用到正在运行的群集](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
