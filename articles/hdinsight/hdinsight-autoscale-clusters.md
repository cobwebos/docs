---
title: 自动缩放 Azure HDInsight 群集（预览）
description: 使用 HDInsight 自动缩放功能来自动缩放群集
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: hrasheed
ms.openlocfilehash: 6ec981164de0ff61b0e83d54255d046a1418ed96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66000105"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>自动缩放 Azure HDInsight 群集（预览）

> [!Important]
> 自动缩放功能仅适用于 5 月 8 日 2019年之后创建的 Spark、 Hive 和 MapReduce 群集。 

Azure HDInsight 的群集自动缩放功能可以自动增加和减少群集中的工作器节点数。 目前无法缩放群集中其他类型的节点。  创建新 HDInsight 群集期间，可以设置最小和最大工作节点数。 自动缩放功能随后监视分析负载的资源需求，并增加或减少工作器节点数。 此功能不会产生额外的费用。

## <a name="cluster-compatibility"></a>群集兼容性

下表描述了与自动缩放功能兼容的群集类型和版本。

| Version | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| 不包含 ESP 的 HDInsight 3.6 | 是 | 是 | 否 | 否 | 否 | 否 | 否 |
| 不包含 ESP 的 HDInsight 4.0 | 是 | 是 | 否 | 否 | 否 | 否 | 否 |
| 包含 ESP 的 HDInsight 3.6 | 是 | 是 | 否 | 否 | 否 | 否 | 否 |
| 包含 ESP 的 HDInsight 3.6 | 是 | 是 | 否 | 否 | 否 | 否 | 否 |

## <a name="how-it-works"></a>工作原理

可为 HDInsight 群集选择基于负载的缩放或基于计划的缩放。 基于负载的缩放会在设置的范围内更改群集中的节点数，以确保获得最佳的 CPU 利用率并尽量降低运行成本。

基于计划的缩放根据特定时间生效的条件更改群集中的节点数。 这些条件会将群集缩放到所需的节点数。

### <a name="metrics-monitoring"></a>指标监视

自动缩放会持续监视群集并收集以下指标：

* **总待处理 CPU**：开始执行所有待处理容器所需的核心总数。
* **总待处理内存**：开始执行所有待处理容器所需的总内存（以 MB 为单位）。
* **总可用 CPU**：活动工作节点上所有未使用核心的总和。
* **总可用内存**：活动工作节点上未使用内存的总和（以 MB 为单位）。
* **每个节点的已使用内存**：工作节点上的负载。 使用了 10 GB 内存的工作节点的负载被认为比使用了 2 GB 内存的工作节点的负载更大。
* **每个节点的应用程序主机数**：在工作节点上运行的应用程序主机 (AM) 容器的数量。 托管两个 AM 容器的工作节点被认为比托管零个 AM 容器的工作节点更重要。

每 60 秒检查一次上述指标。 自动缩放根据这些指标做出纵向扩展和纵向缩减决策。

### <a name="load-based-cluster-scale-up"></a>基于负载的群集纵向扩展

检测到以下情况时，自动缩放将发出纵向扩展请求：

* 总待处理 CPU 大于总可用 CPU 的时间超过 3 分钟。
* 总待处理内存大于总可用内存的时间超过 3 分钟。

HDInsight 服务将计算需要多少个新的工作器节点才能满足当前的 CPU 和内存要求，然后发出纵向扩展请求来添加所需的节点数。

### <a name="load-based-cluster-scale-down"></a>基于负载的群集纵向缩减

检测到以下情况时，自动缩放将发出纵向缩减请求：

* 总待处理 CPU 小于总可用 CPU 的时间超过 10 分钟。
* 总待处理内存小于总可用内存的时间超过 10 分钟。

根据每个节点的 AM 容器数目以及当前的 CPU 和内存要求，自动缩放将发出请求来删除特定数量的节点。 该服务还会根据当前的作业执行来检测哪些节点可以删除。 纵向缩减操作首先会解除节点，然后从群集中删除它们。

## <a name="get-started"></a>开始使用

### <a name="create-a-cluster-with-load-based-autoscaling"></a>使用基于负载的自动缩放创建群集

若要结合基于负载的缩放启用自动缩放功能，请在创建普通群集的过程中完成以下步骤：

1. 选择“自定义(大小、设置、应用)”  而非“快速创建”  。
1. 在“自定义”安装的步骤 5（指定“群集大小”）中，选中“工作器节点自动缩放”复选框。   
1. 在“自动缩放类型”下选择“基于负载”选项。  
1. 为以下属性输入所需的值：  

    * 初始工作节点数  。  
    * 最小  工作节点数。  
    * 最大  工作节点数。  

    ![启用工作器节点的基于负载的自动缩放选项](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

工作节点的初始数量必须介于最小值和最大值之间（含最大值和最小值）。 此值定义创建群集时的群集初始大小。 最小工作节点数必须大于零。

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>使用基于计划的自动缩放创建群集

若要结合基于计划的缩放启用自动缩放功能，请在创建普通群集的过程中完成以下步骤：

1. 选择“自定义(大小、设置、应用)”  而非“快速创建”  。
1. 在“自定义”安装的步骤 5（指定“群集大小”）中，选中“工作器节点自动缩放”复选框。   
1. 输入**工作器节点的数目**，以控制纵向扩展群集的限制。
1. 在“自动缩放类型”下选择“基于计划”选项。  
1. 单击“配置”打开“自动缩放配置”窗口。  
1. 选择时区，然后单击“+ 添加条件” 
1. 选择新条件要应用到的星期日期。
1. 编辑该条件生效的时间，以及群集要缩放到的节点数。
1. 根据需要添加更多条件。

    ![启用工作器节点的基于计划的自动缩放选项](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

节点数最小为 1，最大为添加条件之前输入的工作器节点数。

### <a name="final-creation-steps"></a>最终创建步骤

对于基于负载和基于计划的缩放，请单击“工作器节点大小”和“头节点大小”选择工作器节点的 VM 类型。   为每个节点类型选择 VM 类型后，可以看到整个群集的估算成本范围。 请根据预算调整 VM 类型。

![启用工作器节点的基于计划的自动缩放选项](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-node-size-selection.png)

你的订阅具有针对每个区域的容量配额。 头节点核心总数加最大工作节点数不能超过容量配额。 但是，此配额是软性限制；始终可创建支持票证来轻松地增加此配额。

> [!Note]  
> 如果超出总核心配额限制，将收到一条错误消息，指出“最大节点数超出此区域中的可用核心数，请选择其他区域或联系客户支持以增加配额”。

有关使用 Azure 门户创建 HDInsight 群集的详细信息，请参阅[使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-portal.md)。  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>使用资源管理器模板创建群集

#### <a name="load-based-autoscaling"></a>基于负载的自动缩放

可以使用 Azure 资源管理器模板创建支持基于负载的自动缩放的 HDInsight 群集，方法是将 `autoscale` 节点添加到包含属性 `minInstanceCount` 和 `maxInstanceCount` 的 `computeProfile` > `workernode` 节，如以下 JSON 代码片段所示。

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

#### <a name="schedule-based-autoscaling"></a>基于计划的自动缩放

可以使用 Azure 资源管理器模板创建支持基于计划的自动缩放的 HDInsight 群集，方法是将 `autoscale` 节点添加到 `computeProfile` > `workernode` 节。 `autoscale` 节点包含 `recurrence`，其中的 `timezone` 和 `schedule` 描述了更改生效的时间。

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>为正在运行的群集启用和禁用自动缩放

若要在运行中的群集上启用自动缩放，请选择“设置”下的“群集大小”。   然后单击“启用自动缩放”。  选择所需的自动缩放类型，然后输入基于负载或基于计划的缩放选项。 最后，单击“保存”  。

![启用工作器节点的基于计划的自动缩放选项](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

## <a name="best-practices"></a>最佳做法

### <a name="choosing-load-based-or-schedule-based-scaling"></a>选择基于负载或基于计划的缩放

在做出决定要选择的模式之前，请考虑以下因素：

* 加载的变体： 不在群集的负载在特定时间，在特定日期遵循一致的模式。 如果没有，基于负载计划是更好的选择。
* SLA 的要求：自动缩放缩放是被动而不是预测。 将之间有足够的延迟时在加载开始增长以及当群集必须位于其目标大小？ 如果有严格的 SLA 要求的负载是固定的已知的模式，基于计划将是更好的选择。

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>缩放的延迟，请考虑向上扩展或缩减操作

可能需要 10 到 20 分钟的缩放操作完成。 在设置自定义计划，规划这种延迟。 例如，如果您需要将群集大小为 20 个在上午 9:00，设置计划触发器为较早的时间，如上午 8:30，以便在缩放操作已完成的上午 9:00。

### <a name="preparation-for-scaling-down"></a>向下缩放的准备

群集缩减进程，自动缩放将停止使用满足目标大小的节点。 如果这些节点上正在运行的任务，自动缩放将等待，直到完成任务。 由于每个工作节点还可在 HDFS 中的一个角色，临时数据将转移到剩余节点中。 因此应确保托管所有临时数据的其余节点上没有足够的空间。 

正在运行的作业将继续运行并完成。 等待挂起的作业将被排定为正常并且更少的可用的辅助角色节点。

## <a name="monitoring"></a>监视

### <a name="cluster-status"></a>群集状态

Azure 门户中列出的群集状态可帮助你监视自动缩放活动。

![启用工作器节点的基于负载的自动缩放选项](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

以下列表解释了你可能会看到的所有群集状态消息。

| 群集状态 | 说明 |
|---|---|
| 正在运行 | 群集在正常运行。 所有以前的自动缩放活动已成功完成。 |
| 正在更新  | 正在更新群集自动缩放配置。  |
| HDInsight 配置  | 某个群集纵向扩展或缩减操作正在进行。  |
| 更新时出错  | 更新自动缩放配置期间 HDInsight 遇到问题。 客户可以选择重试更新或禁用自动缩放。  |
| 错误  | 群集发生错误且不可用。 请删除此群集，然后新建一个。  |

若要查看群集中当前的节点数，请转到群集“概述”页上的“群集大小”图表，或单击“设置”下的“群集大小”。    

### <a name="operation-history"></a>操作历史记录

可查看群集指标中包含的群集增加和减少历史记录。 还可以列出过去一天、过去一周或其他时间段的所有缩放操作。

在“监视”下选择“指标”。   然后单击“添加指标”，并从“指标”下拉框中选择“活动辅助角色数”。    单击右上角的按钮更改时间范围。

![启用工作器节点的基于计划的自动缩放选项](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)


## <a name="next-steps"></a>后续步骤

* 阅读[缩放最佳做法](hdinsight-scaling-best-practices.md)，了解手动缩放群集的最佳做法
