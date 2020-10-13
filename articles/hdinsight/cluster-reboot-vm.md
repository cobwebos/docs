---
title: 重新启动 Azure HDInsight 群集的 Vm
description: 了解如何重新启动 Azure HDInsight 群集的无响应 Vm。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: 2177e74bd627e80ea1afbcacaf85baf4e030834c
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91928973"
---
# <a name="reboot-vms-for-hdinsight-clusters"></a>重新启动 HDInsight 群集的 Vm

Azure HDInsight 群集包含虚拟机组 (Vm) 作为群集节点。 对于长时间运行的群集，这些节点可能会因各种原因而无法响应。 本文介绍如何在 HDInsight 群集中重新启动无响应的 VM。

## <a name="when-to-reboot"></a>何时重新启动

> [!WARNING]
> 当你重新启动群集中的 Vm 时，该节点不可用，且节点上的服务必须重新启动。

节点重新启动时，群集可能会变得不正常，作业可能会减慢或失败。 如果正在尝试重新启动活动头节点，将停止所有正在运行的作业。 在服务启动并再次运行之前，无法将作业提交到群集。 出于这些原因，只应在必要时重新启动 Vm。 请考虑在以下情况时重新启动 Vm：

- 不能使用 SSH 进入节点，但会响应 ping。
- 辅助角色节点在 Ambari UI 中没有检测信号的情况下关闭。
- 节点上的临时磁盘已满。
- VM 上的进程表中有很多条目，进程已完成，但以 "终止状态" 列出。

> [!NOTE]
> **HBase**和**Kafka**群集不支持重启 vm，因为重新启动可能会导致数据丢失。

## <a name="use-powershell-to-reboot-vms"></a>使用 PowerShell 重新启动 VM

使用节点重新启动操作需要执行两个步骤：列出节点和重新启动节点。

1. 列出节点。 可以在 [AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost)中获取群集节点列表。

      ```
      Get-AzHDInsightHost -ClusterName myclustername
      ```

1. 重新启动主机。 获取想要重新启动的节点的名称后，请使用 [AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)重启节点。

      ```
      Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
      ```

## <a name="use-a-rest-api-to-reboot-vms"></a>使用 REST API 重新启动 Vm

可以使用 API 文档中的“试一试”功能将请求发送到 HDInsight 。 使用节点重新启动操作需要执行两个步骤：列出节点和重新启动节点。

1. 列出节点。 可以在 REST API 或 Ambari 中获取群集节点列表。 有关详细信息，请参阅 [HDInsight 列出主机 REST API 操作](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts)。

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

1. 重新启动主机。 获取想要重新启动的节点的名称后，通过使用 REST API 重新启动节点来重新启动节点。 节点名称后跟群集名称的*w) /hn/zk/gw) *  +  *x*  +  *前六个字符*的节点名称 (模式。 有关详细信息，请参阅 [HDInsight restart 主机 REST API 操作](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts)。

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

要重新启动的节点的实际名称是在请求正文的 JSON 数组中指定的。

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>后续步骤

* [Restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [HDInsight 虚拟机 REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [HDInsight REST API](https://docs.microsoft.com/rest/api/hdinsight/)
