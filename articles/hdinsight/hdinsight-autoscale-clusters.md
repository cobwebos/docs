---
title: 自动缩放 Azure HDInsight 群集
description: 使用 Azure HDInsight 自动缩放功能自动 Apache Hadoop 缩放群集
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 8354be28203f1d466df6a22159fef87c9ae6f803
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199736"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>自动缩放 Azure HDInsight 群集

Azure HDInsight 的 "免费自动缩放" 功能可根据先前设置的条件自动增加或减少群集中的辅助角色节点数。 在群集创建过程中，可以设置最小和最大节点数，使用日期时间计划或特定性能指标建立缩放条件，而 HDInsight 平台会执行其他任务。

## <a name="how-it-works"></a>工作原理

自动缩放功能使用两种类型的条件来触发缩放事件：不同群集性能指标的阈值（称为*基于负载的缩放*）和基于时间的触发器（称为*基于计划的缩放*）。 基于负载的缩放会在你设置的范围内更改群集中的节点数，以确保 CPU 使用最佳并且最大程度地降低运行成本。 基于计划的缩放根据与特定日期和时间关联的操作更改群集中的节点数。

以下视频概述了自动缩放解决的难题，以及它如何帮助你使用 HDInsight 控制成本。


> [!VIDEO https://www.youtube.com/embed/UlZcDGGFlZ0?WT.mc_id=dataexposed-c9-niner]

### <a name="choosing-load-based-or-schedule-based-scaling"></a>选择基于负载或基于计划的缩放

选择缩放类型时，请考虑以下因素：

* 负载变化：群集的负载是否在特定的时间内按特定的时间使用一致的模式？ 如果不是，则最好使用基于负载的计划。
* SLA 要求：自动缩放缩放为反应性，而不是预测。 在负载开始增加以后，是否有足够的延迟来确保将群集设置为目标大小？ 如果存在严格的 SLA 要求并且负载为固定的已知模式，则 "基于计划" 是一个更好的选择。

### <a name="cluster-metrics"></a>群集指标

自动缩放会持续监视群集并收集以下指标：

|指标|说明|
|---|---|
|挂起 CPU 总数|开始执行所有待处理容器所需的核心总数。|
|挂起的总内存|开始执行所有待处理容器所需的总内存（以 MB 为单位）。|
|可用 CPU 总量|活动工作节点上所有未使用核心的总和。|
|可用内存总量|活动工作节点上未使用内存的总和（以 MB 为单位）。|
|每个节点使用的内存|工作节点上的负载。 使用了 10 GB 内存的工作节点的负载被认为比使用了 2 GB 内存的工作节点的负载更大。|
|每个节点的应用程序主机数|在工作节点上运行的应用程序主机 (AM) 容器的数量。 托管两个 AM 容器的工作节点被认为比托管零个 AM 容器的工作节点更重要。|

每 60 秒检查一次上述指标。 可以使用任何这些指标设置群集的缩放操作。

### <a name="load-based-scale-conditions"></a>基于负载的缩放条件

当检测到以下情况时，自动缩放将发出一个缩放请求：

|纵向扩展|缩小|
|---|---|
|总待处理 CPU 大于总可用 CPU 的时间超过 3 分钟。|总待处理 CPU 小于总可用 CPU 的时间超过 10 分钟。|
|总待处理内存大于总可用内存的时间超过 3 分钟。|总待处理内存小于总可用内存的时间超过 10 分钟。|

对于纵向扩展，自动缩放会发出扩展请求，以添加所需数量的节点。 向上缩放基于所需的新工作节点数量，以满足当前的 CPU 和内存要求。

对于向下缩放，自动缩放会发出删除一定数量节点的请求。 根据每个节点的 AM 容器数向下缩放。 以及当前的 CPU 和内存要求。 该服务还会根据当前的作业执行来检测哪些节点可以删除。 纵向缩减操作首先会解除节点，然后从群集中删除它们。

### <a name="cluster-compatibility"></a>群集兼容性

> [!Important]
> Azure HDInsight 自动缩放功能已发布，适用于 Spark 和 Hadoop 群集的2019年11月7日，并随附了功能预览版本中未提供的改进。 如果你在2019年11月7日之前创建了一个 Spark 群集，并且想要在群集上使用自动缩放功能，则建议的路径是创建一个新的群集，并在新群集上启用自动缩放。
>
> 交互式查询（LLAP）和 HBase 群集的自动缩放仍处于预览阶段。 自动缩放仅适用于 Spark、Hadoop、交互式查询和 HBase 群集。

下表描述了与自动缩放功能兼容的群集类型和版本。

| 版本 | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| 不包含 ESP 的 HDInsight 3.6 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| 不包含 ESP 的 HDInsight 4.0 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| 包含 ESP 的 HDInsight 3.6 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| 包含 ESP 的 HDInsight 4.0 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |

\*只能将 HBase 群集配置为基于计划的缩放，而不能配置基于负载的群集。

## <a name="get-started"></a>入门

### <a name="create-a-cluster-with-load-based-autoscaling"></a>使用基于负载的自动缩放创建群集

若要结合基于负载的缩放启用自动缩放功能，请在创建普通群集的过程中完成以下步骤：

1. 在 "**配置 + 定价**" 选项卡上，选中 "**启用自动缩放**" 复选框。
1. 在“自动缩放类型”下选择“基于负载”。********
1. 为以下属性输入预期值：  

    * 适用于工作器节点的初始工作节点数****。****
    * 工作器节点**最小**数目。
    * 工作器节点**最大**数目。

    ![启用工作器节点的基于负载的自动缩放](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

工作节点的初始数量必须介于最小值和最大值之间（含最大值和最小值）。 此值定义创建群集时的群集初始大小。 工作器节点最小数目至少应设置为 3。 将群集缩放成少于三个节点可能导致系统停滞在安全模式下，因为没有进行充分的文件复制。  有关详细信息，请参阅[进入安全模式](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)。

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>使用基于计划的自动缩放创建群集

若要结合基于计划的缩放启用自动缩放功能，请在创建普通群集的过程中完成以下步骤：

1. 在“配置 + 定价”选项卡上，勾选“启用自动缩放”复选框。********
1. 输入**工作器节点**的**节点数**，以控制纵向扩展群集的限制。
1. 在“自动缩放类型”下选择“基于计划”选项。********
1. 选择 "**配置**" 以打开 "**自动缩放配置**" 窗口。
1. 选择时区，然后单击“+ 添加条件”****
1. 选择新条件要应用到的星期日期。
1. 编辑该条件生效的时间，以及群集要缩放到的节点数。
1. 根据需要添加更多条件。

    ![启用工作器节点的基于计划的创建](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

节点数最小为 3，最大为添加条件之前输入的最大工作器节点数。

### <a name="final-creation-steps"></a>最终创建步骤

通过从 "**节点大小**" 下的下拉列表中选择一个 vm，选择辅助角色节点的 vm 类型。 为每个节点类型选择 VM 类型后，可以看到整个群集的估算成本范围。 请根据预算调整 VM 类型。

![启用工作器节点的基于计划的自动缩放节点大小](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

你的订阅具有针对每个区域的容量配额。 头节点的内核总数和最大工作节点数不能超过容量配额。 但是，此配额是软性限制；始终可创建支持票证来轻松地增加此配额。

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

若要在运行中的群集上启用自动缩放，请选择“设置”下的“群集大小”。******** 然后选择 "**启用自动缩放**"。 选择所需的自动缩放类型，然后输入基于负载或基于计划的缩放选项。 最后，选择“保存”****。

![启用工作器节点的基于计划的自动缩放运行群集](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>使用 REST API

若要使用 REST API 在正在运行的群集上启用或禁用自动缩放，请将 POST 请求发送到自动缩放终结点：

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

请在请求有效负载中使用适当的参数。 下面的 json 有效负载可以用来启用自动缩放。 使用有效负载 `{autoscale: null}` 禁用自动缩放。

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

请参阅介绍如何[启用基于负载的自动缩放](#load-based-autoscaling)的上一部分，详尽了解所有的有效负载参数。

## <a name="monitoring-autoscale-activities"></a>监视自动缩放活动

### <a name="cluster-status"></a>群集状态

Azure 门户中列出的群集状态可帮助你监视自动缩放活动。

![启用工作器节点的基于负载的自动缩放群集状态](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

以下列表解释了你可能会看到的所有群集状态消息。

| 群集状态 | 说明 |
|---|---|
| 运行 | 群集在正常运行。 所有以前的自动缩放活动已成功完成。 |
| 更新  | 正在更新群集自动缩放配置。  |
| HDInsight 配置  | 某个群集纵向扩展或缩减操作正在进行。  |
| 更新时出错  | HDInsight 在自动缩放配置更新期间遇到问题。 客户可以选择重试更新或禁用自动缩放。  |
| 错误  | 群集出现问题，无法使用。 请删除此群集，然后新建一个。  |

若要查看群集中节点的当前数目，请在群集的 "**概述**" 页上，中转到 "**群集大小**" 图表。 或在 "**设置**" 下选择**群集大小**。

### <a name="operation-history"></a>操作历史记录

可查看群集指标中包含的群集增加和减少历史记录。 还可以列出过去一天、过去一周或其他时间段的所有缩放操作。

在“监视”下选择“指标”。******** 然后从 "**指标**" 下拉框中选择 "**添加度量值**" 和 "**活动工作线程数**"。 选择右上角的按钮以更改时间范围。

![启用工作器节点的基于计划的自动缩放指标](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="other-considerations"></a>其他注意事项

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>考虑纵向扩展或纵向缩减操作的延迟

完成一项缩放操作可能需要 10 到 20 分钟。 设置自定义计划时，请将此延迟计划在内。 例如，如果需要在早晨 9:00 将群集大小设置为 20，请将计划触发器设置为更早的某个时间（例如早晨 8:30），这样缩放操作就可以在早晨 9:00 之前完成。

### <a name="preparation-for-scaling-down"></a>准备进行纵向缩减

在群集纵向缩减过程中，自动缩放会根据目标大小解除节点的授权。 如果任务在这些节点上运行，自动缩放将等待，直到任务完成。 由于每个工作器节点也充当 HDFS 中的某个角色，因此会将临时数据转移到剩余节点中。 因此，您应该确保剩余节点上有足够的空间来承载所有临时数据。

正在运行的作业将继续。 等待作业将等待计划的可用辅助角色节点更少。

### <a name="minimum-cluster-size"></a>最小的群集大小

不要将群集缩小到少于三个节点。 将群集缩放成少于三个节点可能导致系统停滞在安全模式下，因为没有进行充分的文件复制。  有关详细信息，请参阅[进入安全模式](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)。

## <a name="next-steps"></a>后续步骤

阅读有关在[缩放准则](hdinsight-scaling-best-practices.md)中手动缩放群集的准则
