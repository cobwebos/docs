---
title: 重新启动 Azure HDInsight 群集的 Vm
description: 了解如何重新启动 HDInsight 群集的无响应 Vm。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: c0f0bd9eb423b3de6a602647dff93fd9fce6e13e
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077008"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>重新启动 HDInsight 群集的 Vm

HDInsight 群集包含 Vm 组作为群集节点。 对于长时间运行的群集，这些节点可能会因各种原因而无法响应。 本文介绍如何在 HDInsight 群集中重新启动无响应的 Vm。

## <a name="when-to-reboot"></a>何时重新启动

> [!WARNING]  
> 重新启动群集中的 Vm 会使节点停机，并在节点上重新启动服务。 

节点重新启动时，群集可能会变得不正常，作业可能会减慢或失败。 如果你正在尝试重新启动活动头节点，则所有正在运行的作业都将终止，并且你无法将作业提交到群集，直到服务重新启动并再次运行。 只应考虑在必要时重新启动 Vm。 下面是有关何时考虑重启 Vm 的一些指导。

- 你无法通过 SSH 连接到该节点，但它会响应 ping。
- 辅助角色节点在 Ambari UI 中没有检测信号的情况下关闭。
- 节点上的临时磁盘已满。
- VM 上的进程表中有很多条目，进程已完成，但以 "终止状态" 列出。

> [!WARNING]  
> 重新启动**HBase**和**Kafka** clustes 的 vm 时应更加小心，因为这可能会导致数据丢失。

## <a name="use-powershell-to-reboot-vms"></a>使用 PowerShell 重新启动 Vm

使用节点重新启动操作需要执行两个步骤：列出节点和重新启动节点。

1. 列出节点。 可以通过[AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost)获取群集节点列表。 

  ```
  Get-AzHDInsightHost -ClusterName myclustername
  ```

2. 重新启动主机。 获取想要重新启动的节点名称后，使用[AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)重启节点。

  ```
  Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
  ```

## <a name="use-rest-api-to-reboot-vms"></a>使用 REST API 重新启动 Vm

可以使用 API 文档中的 "**试用 it** " 功能将请求发送到 HDInsight。 使用节点重新启动操作需要执行两个步骤：列出节点和重新启动节点。

1. 列出节点。 可以从 REST API 或 Ambari 中获取群集节点列表。 有关详细信息，请参阅[HDInsight 列表主机 REST API 操作](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts)。

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. 重新启动主机。 获取想要重新启动的节点的名称后，请使用 "重新启动节点 REST API 重启节点。节点名称遵循以下模式： **"NodeType （w)/hn/zk/gw）" + "x" + "群集名称的前6个字符"**。 可以在[HDInsight Restart 主机 REST API 操作](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts)中找到更多详细信息。

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

* [重新启动-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [HDInsight 虚拟机 REST API](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [HDInsight REST API](https://docs.microsoft.com/rest/api/hdinsight/)
