---
title: 为基于 Linux 的 HDInsight 群集配置 OS 修补计划 - Azure
description: 了解如何为基于 Linux 的 HDInsight 群集配置 OS 修补计划。
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: a73866a8898042b546fa47d9c3d14ab4e58e9a12
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503236"
---
# <a name="os-patching-for-hdinsight"></a>针对 HDInsight 的 OS 修补 

> [!IMPORTANT]
> Ubuntu 映像都可用于创建在三个月发布的新 HDInsight 群集。 自 2019 年 1 月起，系统**不**会自动修补正在运行的群集。 客户必须使用脚本操作或其他机制来修补正在运行的群集。 新创建的群集将始终包含最新的可用更新，其中包括最新的安全修补程序。

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>如何为基于 Linux 的 HDInsight 群集配置 OS 修补计划
需不定期重启 HDInsight 群集中的虚拟机，以便安装重要的安全修补程序。 

使用本文中所述的脚本操作，可以修改 OS 修补计划，如下所示：
1. 安装所有更新或都安装内核 + 安全性仅更新或内核仅都安装更新。
2. 立即重新启动或计划在 VM 上重启。

> [!NOTE]  
> 这些脚本操作将仅适用于 2016 年 8 月 1 日之后创建的基于 Linux 的 HDInsight 群集。 仅在重启 VM 后，修补程序才生效。 这些脚本不会自动应用更新的所有将来的更新周期。 运行每个时间的新更新需要安装更新并重启 VM 才能应用的脚本。

## <a name="how-to-use-the-script"></a>如何使用脚本 

使用此脚本需要以下信息：
1. 安装-更新-计划的重新启动脚本位置： https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh 。
    
   HDInsight 使用此 URI 在群集中的所有虚拟机上查找并运行脚本。 此脚本提供选项来安装更新和重新启动 VM。
  
2. 在计划重启脚本位置： https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh 。
    
   HDInsight 使用此 URI 在群集中的所有虚拟机上查找并运行脚本。 此脚本重新启动 VM。
  
3. 应用该脚本的群集节点类型：头节点、辅助节点、zookeeper。 此脚本必须应用于群集中的所有节点类型。 如果它不应用于节点类型，然后该节点类型的虚拟机不会更新或重新启动。

4. 参数：安装更新的计划重新启动脚本接受两个数字参数：

    | 参数 | 定义 |
    | --- | --- |
    | 内核仅安装更新 / 安装所有更新/都安装内核 + 安全性仅更新 |0 或 1 或 2。 值为 0 时 1 安装所有都更新，和两次安装内核 + 安全性都更新仅安装仅，内核都更新。 如果没有提供任何参数，默认值为 0。 |
    | 不重启/启用计划重启/启用立即重新启动 |0 或 1 或 2。 值为 0 禁用重新启动，1 启用计划重新启动而 2 将启用立即重新启动。 如果没有提供任何参数，默认值为 0。 用户必须输入参数 1 的输入参数 2。 |
   
 5. 参数：计划重新启动脚本接受一个数字参数：

    | 参数 | 定义 |
    | --- | --- |
    | 启用计划重启/启用立即重新启动 |1 或 2。 如果值为 1 启用计划重新启动 （重新启动按计划在接下来的 12-24 小时） 而 2 允许立即重新启动 （在 5 分钟内）。 如果没有提供任何参数，默认值为 1。 |  

> [!NOTE] 
> 必须将脚本标记为持久化时将应用于现有的群集。 否则，通过缩放操作创建的任何新节点都将使用默认修补计划。  如果在群集创建过程中应用该脚本，则其会自动持久化。


## <a name="next-steps"></a>后续步骤

有关使用脚本操作的特定步骤，请参阅以下各节中的[使用脚本操作自定义的基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md):

* [在创建群集期间使用脚本操作](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [将脚本操作应用到正在运行的群集](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
