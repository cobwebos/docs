---
title: 自动缩放 Azure HDInsight 群集
description: 使用 Azure HDInsight 自动缩放功能自动 Apache Hadoop 缩放群集
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: fff5ad379aa11a0aae14b33f9f82f6da9c794517
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643733"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>自动缩放 Azure HDInsight 群集

> [!Important]
> 自动缩放功能仅适用于 2019 年 5 月 8 日以后创建的 Spark、Hive 和 MapReduce 群集。 

Azure HDInsight 的群集自动缩放功能可以自动增加和减少群集中的工作器节点数。 目前无法缩放群集中其他类型的节点。  创建新 HDInsight 群集期间，可以设置最小和最大工作节点数。 自动缩放功能随后监视分析负载的资源需求，并增加或减少工作器节点数。 此功能不会产生额外的费用。

## <a name="cluster-compatibility"></a>群集兼容性

下表描述了与自动缩放功能兼容的群集类型和版本。

| 版本 | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| 不包含 ESP 的 HDInsight 3.6 | 是 | 是 | 否 | 否 | 否 | 否 | 否 |
| 不包含 ESP 的 HDInsight 4.0 | 是 | 是 | 否 | 否 | 否 | 否 | 否 |
| 包含 ESP 的 HDInsight 3.6 | 是 | 是 | 否 | 否 | 否 | 否 | 否 |
| 包含 ESP 的 HDInsight 4.0 | 是 | 是 | 否 | 否 | 否 | 否 | 否 |

## <a name="how-it-works"></a>工作原理

可为 HDInsight 群集选择基于负载的缩放或基于计划的缩放。 基于负载的缩放会在设置的范围内更改群集中的节点数，以确保获得最佳的 CPU 利用率并尽量降低运行成本。

基于计划的缩放根据特定时间生效的条件更改群集中的节点数。 这些条件会将群集缩放到所需的节点数。

### <a name="metrics-monitoring"></a>指标监视

自动缩放会持续监视群集并收集以下指标：

* **挂起的总 CPU**：启动执行所有挂起的容器所需的核心总数。
* **挂起的总内存**：启动所有挂起容器的执行所需的总内存（以 MB 为单位）。
* **可用 CPU 总计**：活动工作节点上所有未使用的内核数之和。
* **总可用内存**：活动工作节点上未使用的内存（以 MB 为单位）的总和。
* **每个节点使用的内存**：辅助节点上的负载。 使用了 10 GB 内存的工作节点的负载被认为比使用了 2 GB 内存的工作节点的负载更大。
* **每个节点的应用程序主机的数量**：辅助节点上运行的应用程序主（AM）容器的数目。 托管两个 AM 容器的工作节点被认为比托管零个 AM 容器的工作节点更重要。

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

## <a name="get-started"></a>入门

### <a name="create-a-cluster-with-load-based-autoscaling"></a>使用基于负载的自动缩放创建群集

若要在群集上使用自动缩放，则在创建群集时必须启用 "**启用自动缩放**" 选项。 

若要结合基于负载的缩放启用自动缩放功能，请在创建普通群集的过程中完成以下步骤：

1. 在 "**配置 + 定价**" 选项卡上，选中 "**启用自动缩放**" 复选框。
1. 在**自动缩放类型**下选择 "**基于负载**"。
1. 为以下属性输入所需的值：  

    * **辅助角色节点** **的初始节点数**。
    * 辅助角色节点的**最小**数目。
    * 辅助角色节点的**最大**数目。

    ![启用工作器节点的基于负载的自动缩放](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

工作节点的初始数量必须介于最小值和最大值之间（含最大值和最小值）。 此值定义群集创建时的初始大小。 辅助角色节点的最小数目应设置为三个或更多。 。 将群集缩放到小于三个节点可能导致其在安全模式下停滞，因为文件复制不足。 有关详细信息，请参阅[进入安全模式停滞]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode)状态。

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>使用基于计划的自动缩放创建群集

若要结合基于计划的缩放启用自动缩放功能，请在创建普通群集的过程中完成以下步骤：

1. 在 "**配置 + 定价**" 选项卡上，选中 "**启用自动缩放**" 复选框。
1. 输入**辅助角色节点**的**节点数**，该节点控制扩展群集的限制。
1. 在“自动缩放类型”下选择“基于计划”选项。
1. 单击“配置”打开“自动缩放配置”窗口。
1. 选择时区，然后单击“+ 添加条件”
1. 选择新条件要应用到的星期日期。
1. 编辑该条件生效的时间，以及群集要缩放到的节点数。
1. 根据需要添加更多条件。

    ![启用工作器节点的基于计划的创建](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

节点数必须介于3和在添加条件之前输入的辅助角色节点的最大数目之间。

### <a name="final-creation-steps"></a>最终创建步骤

对于基于负载和计划的缩放，请在 "**节点大小**" 下的下拉列表中选择 vm，以选择工作节点的 vm 类型。 为每个节点类型选择 VM 类型后，可以看到整个群集的估算成本范围。 请根据预算调整 VM 类型。

![启用工作器节点的基于计划的自动缩放节点大小](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

你的订阅具有针对每个区域的容量配额。 头节点核心总数加最大工作节点数不能超过容量配额。 但是，此配额是软性限制；始终可创建支持票证来轻松地增加此配额。

> [!Note]  
> 如果超出总核心配额限制，将收到一条错误消息，指出“最大节点数超出此区域中的可用核心数，请选择其他区域或联系客户支持以增加配额”。

有关使用 Azure 门户创建 HDInsight 群集的详细信息，请参阅[使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-portal.md)。  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>使用资源管理器模板创建群集

#### <a name="load-based-autoscaling"></a>基于负载的自动缩放

可以使用 Azure 资源管理器模板创建支持基于负载的自动缩放的 HDInsight 群集，方法是将 `autoscale` 节点添加到包含属性 `computeProfile` 和  >  的 `workernode``minInstanceCount``maxInstanceCount` 节，如以下 JSON 代码片段所示。

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
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

#### <a name="using-the-azure-portal"></a>使用 Azure 门户

若要在运行中的群集上启用自动缩放，请选择“设置”下的“群集大小”。 然后单击“启用自动缩放”。 选择所需的自动缩放类型，然后输入基于负载或基于计划的缩放选项。 最后，单击“保存”。

![启用工作器节点的基于计划的自动缩放运行群集](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

#### <a name="using-the-rest-api"></a>使用 REST API

若要使用 REST API 在运行中的群集上启用或禁用自动缩放，请向自动缩放终结点发出 POST 请求，如以下代码片段所示：

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

请在请求有效负载中使用适当的参数。 下面的 json 有效负载可以用来启用自动缩放。 使用有效负载 `{autoscale: null}` 禁用自动缩放。

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

请参阅介绍如何[启用基于负载的自动缩放](#load-based-autoscaling)的上一部分，详尽了解所有的有效负载参数。

## <a name="best-practices"></a>最佳实践

### <a name="choosing-load-based-or-schedule-based-scaling"></a>选择基于负载或基于计划的缩放

在决定选择哪个模式之前，请考虑以下因素：

* 在群集创建期间启用自动缩放。
* 最小节点数应至少为三个。
* 负载差异：群集的负载是否在特定日期的特定时间遵循一致的模式。 如果不是，则最好使用基于负载的计划。
* SLA 要求：自动缩放缩放为反应性，而不是预测。 在负载开始增加以后，是否有足够的延迟来确保将群集设置为目标大小？ 如果有严格的 SLA 要求，且负载是固定的已知模式，则“基于计划”是更好的选项。

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>考虑纵向扩展或纵向缩减操作的延迟

完成一项缩放操作可能需要 10 到 20 分钟。 设置自定义计划时，请将此延迟计划在内。 例如，如果需要在早晨 9:00 将群集大小设置为 20，请将计划触发器设置为更早的某个时间（例如早晨 8:30），这样缩放操作就可以在早晨 9:00 之前完成。

### <a name="preparation-for-scaling-down"></a>准备进行纵向缩减

在群集纵向缩减过程中，自动缩放会根据目标大小解除节点的授权。 如果这些节点上有正在运行的任务，自动缩放会等待这些任务完成。 由于每个工作器节点也充当 HDFS 中的某个角色，因此会将临时数据转移到剩余节点中。 因此，您应该确保剩余节点上有足够的空间来承载所有临时数据。

正在运行的作业会继续运行，直至完成。 在可用的工作器节点变少的情况下，挂起的作业会等待系统按正常情况进行安排。

### <a name="minimum-cluster-size"></a>最小群集大小

不要将群集缩小到少于三个节点。 将群集缩放到小于三个节点可能导致其在安全模式下停滞，因为文件复制不足。 有关详细信息，请参阅[进入安全模式停滞]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode)状态。

## <a name="monitoring"></a>监视

### <a name="cluster-status"></a>群集状态

Azure 门户中列出的群集状态可帮助你监视自动缩放活动。

![启用工作器节点的基于负载的自动缩放群集状态](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

以下列表解释了你可能会看到的所有群集状态消息。

| 群集状态 | 说明 |
|---|---|
| 正在运行 | 群集在正常运行。 所有以前的自动缩放活动已成功完成。 |
| 正在更新  | 正在更新群集自动缩放配置。  |
| HDInsight 配置  | 某个群集纵向扩展或缩减操作正在进行。  |
| 更新时出错  | 更新自动缩放配置期间 HDInsight 遇到问题。 客户可以选择重试更新或禁用自动缩放。  |
| 错误  | 群集出现问题，无法使用。 请删除此群集，然后新建一个。  |

若要查看群集中当前的节点数，请转到群集“概述”页上的“群集大小”图表，或单击“设置”下的“群集大小”。

### <a name="operation-history"></a>操作历史记录

可查看群集指标中包含的群集增加和减少历史记录。 还可以列出过去一天、过去一周或其他时间段的所有缩放操作。

在“监视”下选择“指标”。 然后单击“添加指标”，并从“指标”下拉框中选择“活动辅助角色数”。 单击右上角的按钮更改时间范围。

![启用工作器节点的基于计划的自动缩放指标](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>后续步骤

* 阅读[缩放最佳做法](hdinsight-scaling-best-practices.md)，了解手动缩放群集的最佳做法
