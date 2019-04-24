---
title: 自动缩放 Azure HDInsight 群集（预览）
description: 使用 HDInsight 自动缩放功能来自动缩放群集
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
origin.date: 03/19/2019
ms.date: 04/29/2019
ms.author: hrasheed
ms.openlocfilehash: 11828b3b056519d0ebe3233f078c6b3f6fc2ea1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60253477"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>自动缩放 Azure HDInsight 群集（预览）

>[!Important]
>HDInsight 自动缩放功能目前以预览版提供。 若要为订阅启用自动缩放，请向 hdiautoscalepm@microsoft.com 发送电子邮件。

Azure HDInsight 的群集自动缩放功能可根据预定义范围内的负载自动增加和减少群集中的工作节点数。 创建新 HDInsight 群集期间，可以设置最小和最大工作节点数。 自动缩放功能随后监视分析负载的资源需求，并相应地增加或减少工作节点数。 此功能不会产生额外的费用。

## <a name="getting-started"></a>入门

### <a name="create-a-cluster-with-the-azure-portal"></a>通过 Azure 门户创建群集

> [!Note]
> 目前仅 Azure HDInsight Hive、MapReduce 和 Spark 群集版本3.6 支持自动缩放功能。

若要启用自动缩放功能，请在正常群集创建过程中执行以下操作：

1. 选择“自定义(大小、设置、应用)”而非“快速创建”。
2. 在“自定义”第 5 步（群集大小）中，选中“工作节点自动缩放”复选框。
3. 为以下属性输入所需的值：  

    * 初始工作节点数。  
    * 最小工作节点数。  
    * 最大工作节点数。  

![启用工作节点自动缩放选项](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

工作节点的初始数量必须介于最小值和最大值之间（含最大值和最小值）。 此值定义创建群集时的群集初始大小。 最小工作节点数必须大于零。

为每个节点类型选择 VM 类型后，将能够看到整个群集的估计成本范围。 随后可调整这些设置，使其符合你的预算。

你的订阅具有针对每个区域的容量配额。 头节点核心总数加最大工作节点数不能超过容量配额。 但是，此配额是软性限制；始终可创建支持票证来轻松地增加此配额。

> [!Note]  
> 如果超出总核心配额限制，将收到一条错误消息，指出“the maximum node exceeded the available cores in this region, please choose another region or contact the support to increase the quota”（最大节点数超出此区域中的可用核心数，请选择其他区域或联系客户支持以增加配额）。

有关使用 Azure 门户创建 HDInsight 群集的详细信息，请参阅[使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-portal.md)。  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>使用资源管理器模板创建群集

若要使用 Azure 资源管理器模板创建 HDInsight 群集，请向具有属性 `minInstanceCount` 和 `maxInstanceCount` 的 `computeProfile` > `workernode` 部分添加 `autoscale` 节点，如以下 json 代码段所示。

```json
{                            
    "name": "workernode",
    "targetInstanceCount": 4,
    "autoscale": {
        "capacity": {
            "minInstanceCount": 2,
            "maxInstanceCount": 10
        }        
    },
    "hardwareProfile": {
        "vmSize": "Standard_D13_V2"
    },
    "osProfile": {
        "linuxOperatingSystemProfile": {
            "username": "[parameters('sshUserName')]",
            "password": "[parameters('sshPassword')]"
        }
    },
    "virtualNetworkProfile": null,
    "scriptActions": []
}
```

若要深入了解如何使用资源管理器模板创建群集，请参阅[在 HDInsight 中使用资源管理器模板创建 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)。  

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>为正在运行的群集启用和禁用自动缩放

你仅可以启用或禁用自动缩放的新 HDInsight 群集。

## <a name="monitoring"></a>监视

可查看群集指标中包含的群集增加和减少历史记录。 还可以列出过去一天、过去一周或更长时间内的所有缩放操作。

## <a name="how-it-works"></a>工作原理

### <a name="metrics-monitoring"></a>指标监视

自动缩放会持续监视群集并收集以下指标：

1. **总待处理 CPU**：开始执行所有待处理容器所需的核心总数。
2. **总待处理内存**：开始执行所有待处理容器所需的总内存（以 MB 为单位）。
3. **总可用 CPU**：活动工作节点上所有未使用核心的总和。
4. **总可用内存**：活动工作节点上未使用内存的总和（以 MB 为单位）。
5. **每个节点的已使用内存**：工作节点上的负载。 使用了 10 GB 内存的工作节点的负载被认为比使用了 2 GB 内存的工作节点的负载更大。
6. **每个节点的应用程序主机数**：在工作节点上运行的应用程序主机 (AM) 容器的数量。 托管两个 AM 容器的工作节点被认为比托管零个 AM 容器的工作节点更重要。

每 60 秒检查一次上述指标。 自动缩放将根据这些指标做出纵向扩展和纵向缩减决策。

### <a name="cluster-scale-up"></a>群集纵向扩展

检测到以下情况时，自动缩放将发出纵向扩展请求：

* 挂起的 CPU 总数大于总可用 CPU 超过 3 分钟。
* 挂起的内存总数大于可用内存总量超过 3 分钟。

我们将计算得出需要一定数量新的工作节点来满足当前 CPU 和内存需求，然后通过添加相同数量的新工作节点来发出纵向扩展请求。

### <a name="cluster-scale-down"></a>群集纵向缩减

检测到以下情况时，自动缩放将发出纵向缩减请求：

* 总待处理 CPU 小于总可用 CPU 的时间超过 10 分钟。
* 总待处理内存小于总可用内存的时间超过 10 分钟。

根据每个节点的当前 CPU 和内存要求 AM 容器数，自动缩放将发出一个请求以删除一定数量的节点，指定哪些节点会删除的潜在候选项。向下的缩放会触发的节点停用和节点都已完全解除授权后，将删除它们。

## <a name="next-steps"></a>后续步骤

* 阅读[缩放最佳做法](hdinsight-scaling-best-practices.md)，了解手动缩放群集的最佳做法
