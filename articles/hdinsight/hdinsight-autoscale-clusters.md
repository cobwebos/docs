---
title: 自动缩放 Azure HDInsight 群集
description: 使用 Azure HDInsight 自动缩放功能自动缩放 Apache Hadoop 分类。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperfq1
ms.date: 09/14/2020
ms.openlocfilehash: 385e910befb79daafa532fa816b96d50a46b7d8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91620080"
---
# <a name="autoscale-azure-hdinsight-clusters"></a>自动缩放 Azure HDInsight 群集

Azure HDInsight 的免费“自动缩放”功能可根据先前设置的条件自动增加或减少群集中的工作器节点数。 在群集创建过程中，你可以设置最小和最大节点数，使用日期时间计划或特定性能指标建立缩放条件，其余的事项将由 HDInsight 平台完成。

## <a name="how-it-works"></a>工作原理

自动缩放功能使用两种类型的条件来触发缩放事件：各种群集性能指标的阈值（称为“基于负载的缩放”）和基于时间的触发器（称为“基于计划的缩放”）。 基于负载的缩放会在设置的范围内更改群集中的节点数，以确保获得最佳的 CPU 使用率并尽量降低运行成本。 基于计划的缩放根据你关联到特定日期和时间的操作更改群集中的节点数。

以下视频概述了自动缩放解决的难题，以及它如何帮助你使用 HDInsight 控制成本。


> [!VIDEO https://www.youtube.com/embed/UlZcDGGFlZ0?WT.mc_id=dataexposed-c9-niner]

### <a name="choosing-load-based-or-schedule-based-scaling"></a>选择基于负载或基于计划的缩放

在选择缩放类型时请考虑以下因素：

* 负载差异：群集的负载是否在特定日期的特定时间遵循一致的模式？ 如果不是，则最好使用基于负载的计划。
* SLA 要求：自动缩放型缩放是响应性的，而不是预测性的。 在负载开始增加以后，是否有足够的延迟来确保将群集设置为目标大小？ 如果有严格的 SLA 要求，且负载是固定的已知模式，则“基于计划”是更好的选项。

### <a name="cluster-metrics"></a>群集指标

自动缩放会持续监视群集并收集以下指标：

|指标|说明|
|---|---|
|总待处理 CPU|开始执行所有待处理容器所需的核心总数。|
|总待处理内存|开始执行所有待处理容器所需的总内存（以 MB 为单位）。|
|总可用 CPU|活动工作节点上所有未使用核心的总和。|
|总可用内存|活动工作节点上未使用内存的总和（以 MB 为单位）。|
|每个节点的已使用内存|工作节点上的负载。 使用了 10 GB 内存的工作节点的负载被认为比使用了 2 GB 内存的工作节点的负载更大。|
|每个节点的应用程序主机数|在工作节点上运行的应用程序主机 (AM) 容器的数量。 托管两个 AM 容器的工作节点被认为比托管零个 AM 容器的工作节点更重要。|

每 60 秒检查一次上述指标。 可以使用这些指标中的任意一个来设置群集的缩放操作。

### <a name="load-based-scale-conditions"></a>基于负载的缩放条件

检测到以下情况时，自动缩放将发出缩放请求：

|纵向扩展|纵向缩减|
|---|---|
|总待处理 CPU 大于总可用 CPU 的时间超过 3 分钟。|总待处理 CPU 小于总可用 CPU 的时间超过 10 分钟。|
|总待处理内存大于总可用内存的时间超过 3 分钟。|总待处理内存小于总可用内存的时间超过 10 分钟。|

对于纵向扩展，自动缩放会发出纵向扩展请求来添加所需数量的节点。 纵向扩展基于的条件是：需要多少新的工作器节点才能满足当前的 CPU 和内存要求。

对于纵向缩减，自动缩放会发出请求来删除一定数量的节点。 纵向缩减基于的条件是：每个节点的 AM 容器数、 当前的 CPU 和内存要求。 此服务还会根据当前作业执行情况，检测待删除的节点。 纵向缩减操作首先关闭节点，然后将其从群集中删除。

### <a name="cluster-compatibility"></a>群集兼容性

> [!Important]
> Azure HDInsight 自动缩放功能于 2019 年 11 月 7 日正式发布，适用于 Spark 和 Hadoop 群集，并包含了该功能预览版本中未提供的改进。 如果你在 2019 年 11 月 7 日之前创建了 Spark 群集，并希望在群集上使用自动缩放功能，我们建议创建新群集，并在新群集上启用自动缩放。
>
> 在8月27日，2020上发布了针对 HDI 4.0 的公开上市 (LLAP) 自动缩放。 HBase 群集仍处于预览阶段。 自动缩放仅适用于 Spark、Hadoop、交互式查询和 HBase 群集。

下表描述了与自动缩放功能兼容的群集类型和版本。

| 版本 | Spark | Hive | 交互式查询 | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| 不包含 ESP 的 HDInsight 3.6 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| 不包含 ESP 的 HDInsight 4.0 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| 包含 ESP 的 HDInsight 3.6 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| 包含 ESP 的 HDInsight 4.0 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |

\* 只能为 HBase 群集配置基于计划的缩放，不能为其配置基于负载的缩放。

## <a name="get-started"></a>入门

### <a name="create-a-cluster-with-load-based-autoscaling"></a>使用基于负载的自动缩放创建群集

若要结合基于负载的缩放启用自动缩放功能，请在创建普通群集的过程中完成以下步骤：

1. 在“配置 + 定价”选项卡上，选中“启用自动缩放”复选框。 
1. 在“自动缩放类型”下选择“基于负载”。********
1. 为以下属性输入所需的值：  

    * 适用于工作器节点的初始工作节点数****。****
    * 工作器节点**最小**数目。
    * 工作器节点**最大**数目。

    ![启用工作器节点的基于负载的自动缩放](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

工作节点的初始数量必须介于最小值和最大值之间（含最大值和最小值）。 此值定义创建群集时的群集初始大小。 工作器节点最小数目至少应设置为 3。 将群集缩放成少于三个节点可能导致系统停滞在安全模式下，因为没有进行充分的文件复制。  有关详细信息，请参阅[停滞在安全模式下](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)。

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>使用基于计划的自动缩放创建群集

若要结合基于计划的缩放启用自动缩放功能，请在创建普通群集的过程中完成以下步骤：

1. 在“配置 + 定价”选项卡上，勾选“启用自动缩放”复选框。********
1. 输入**工作器节点**的**节点数**，以控制纵向扩展群集的限制。
1. 在“自动缩放类型”下选择“基于计划”选项。********
1. 选择“配置”以打开“自动缩放配置”窗口。 
1. 选择时区，然后单击“+ 添加条件”****
1. 选择新条件要应用到的星期日期。
1. 编辑该条件生效的时间，以及群集要缩放到的节点数。
1. 根据需要添加更多条件。

    ![启用工作器节点的基于计划的创建](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

节点数最小为 3，最大为添加条件之前输入的最大工作器节点数。

### <a name="final-creation-steps"></a>最终创建步骤

请在“节点大小”下的下拉列表中选择一个 VM，通过这种方式选择工作器节点的 VM 类型。 为每个节点类型选择 VM 类型后，可以看到整个群集的估算成本范围。 请根据预算调整 VM 类型。

![启用工作器节点的基于计划的自动缩放节点大小](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

你的订阅具有针对每个区域的容量配额。 头节点核心总数加最大工作器节点数不能超过容量配额。 但是，此配额是软性限制；始终可创建支持票证来轻松地增加此配额。

> [!Note]  
> 如果超出总核心配额限制，将收到一条错误消息，指出“最大节点数超出此区域中的可用核心数，请选择其他区域或联系客户支持以增加配额”。

有关使用 Azure 门户创建 HDInsight 群集的详细信息，请参阅[使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-portal.md)。  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>使用资源管理器模板创建群集

#### <a name="load-based-autoscaling"></a>基于负载的自动缩放

可以使用 Azure 资源管理器模板创建支持基于负载的自动缩放的 HDInsight 群集，方法是将 `autoscale` 节点添加到包含属性 `minInstanceCount` 和 `maxInstanceCount` 的 `computeProfile` > `workernode` 节，如以下 JSON 代码片段所示。 有关完整的资源管理器模板，请参阅[快速入门模板：在启用了基于负载的自动缩放的情况下部署 Spark 群集](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-loadbased)。

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

可以使用 Azure 资源管理器模板创建支持基于计划的自动缩放的 HDInsight 群集，方法是将 `autoscale` 节点添加到 `computeProfile` > `workernode` 节。 `autoscale` 节点包含 `recurrence`，其中的 `timezone` 和 `schedule` 描述了更改生效的时间。 有关完整的资源管理器模板，请参阅[在启用了基于计划的自动缩放的情况下部署 Spark 群集](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-schedulebased)。

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
        }
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>为正在运行的群集启用和禁用自动缩放

#### <a name="using-the-azure-portal"></a>使用 Azure 门户

若要在运行中的群集上启用自动缩放，请选择“设置”下的“群集大小”。******** 然后选择“启用自动缩放”。 选择所需的自动缩放类型，然后输入基于负载或基于计划的缩放选项。 最后，选择“保存”****。

![启用工作器节点的基于计划的自动缩放运行群集](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>使用 REST API

若要使用 REST API 在运行中的群集上启用或禁用自动缩放，请向自动缩放终结点发出 POST 请求：

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

请在请求有效负载中使用适当的参数。 下面的 json 有效负载可以用来启用自动缩放。 使用有效负载 `{autoscale: null}` 禁用自动缩放。

```json
{ "autoscale": { "capacity": { "minInstanceCount": 3, "maxInstanceCount": 5 } } }
```

请参阅介绍如何[启用基于负载的自动缩放](#load-based-autoscaling)的上一部分，详尽了解所有的有效负载参数。

## <a name="monitoring-autoscale-activities"></a>监视自动缩放活动

### <a name="cluster-status"></a>群集状态

Azure 门户中列出的群集状态可帮助你监视自动缩放活动。

![启用工作器节点的基于负载的自动缩放群集状态](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

以下列表解释了你可能会看到的所有群集状态消息。

| 群集状态 | 说明 |
|---|---|
| 正在运行 | 群集在正常运行。 所有以前的自动缩放活动已成功完成。 |
| 更新  | 正在更新群集自动缩放配置。  |
| HDInsight 配置  | 某个群集纵向扩展或缩减操作正在进行。  |
| 更新时出错  | 更新自动缩放配置期间 HDInsight 遇到问题。 客户可以选择重试更新或禁用自动缩放。  |
| 错误  | 群集发生错误且不可用。 请删除此群集，然后新建一个。  |

若要查看群集中当前的节点数，请转到群集“概览”页上的“群集大小”图表。  或者在“设置”下选择“群集大小”。 

### <a name="operation-history"></a>操作历史记录

可查看群集指标中包含的群集增加和减少历史记录。 还可以列出过去一天、过去一周或其他时间段的所有缩放操作。

在“监视”下选择“指标”。  然后选择“添加指标”，并从“指标”下拉框中选择“活动辅助角色数”。   选择右上角的按钮来更改时间范围。

![启用工作器节点的基于计划的自动缩放指标](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="best-practices"></a>最佳做法

### <a name="consider-the-latency-of-scale-up-and-scale-down-operations"></a>请考虑向上扩展和向下缩放操作的延迟

完成一项缩放操作可能需要 10 到 20 分钟。 设置自定义计划时，请将此延迟计划在内。 例如，如果需要在早晨 9:00 将群集大小设置为 20，请将计划触发器设置为更早的某个时间（例如早晨 8:30），这样缩放操作就可以在早晨 9:00 之前完成。

### <a name="prepare-for-scaling-down"></a>准备缩小

在群集缩小过程中，自动缩放会 add-on 节点以满足目标大小。 如果任务在这些节点上运行，自动缩放会等待，直到完成 Spark 和 Hadoop 群集的任务。 由于每个辅助角色节点还在 HDFS 中提供角色服务，因此临时数据将移动到其余节点。 请确保剩余节点上有足够的空间来承载所有临时数据。

正在运行的作业将继续运行。 在可用的工作器节点变少的情况下，挂起的作业会等待安排。

### <a name="be-aware-of-the-minimum-cluster-size"></a>了解最小群集大小

请勿将群集缩减到三个节点以下。 将群集缩放成少于三个节点可能导致系统停滞在安全模式下，因为没有进行充分的文件复制。 有关详细信息，请参阅 [进入安全模式](hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)。

### <a name="increase-the-number-of-mappers-and-reducers"></a>增加映射器和化简器的数量

Hadoop 群集的自动缩放还会监视 HDFS 的使用情况。 如果 HDFS 处于繁忙状态，它将假定群集仍需要当前资源。 如果查询中涉及大量数据，则可以增加映射器和化简器的数量以增加并行度并加速 HDFS 操作。 这样，当有额外的资源时，就会触发适当的缩小。 

### <a name="set-the-hive-configuration-maximum-total-concurrent-queries-for-the-peak-usage-scenario"></a>为高峰使用方案设置 Hive 配置最大并发查询数

自动缩放事件不会在 Ambari 中更改 Hive 配置 *最大并发查询总数* 。 这意味着，在任何时间点，Hive Server 2 交互式服务只能处理给定数量的并发查询，即使交互式查询守护程序计数根据负载和计划增加和减少也是如此。 一般建议为高峰使用方案设置此配置，以避免手动干预。

但是，如果只有少量的工作节点，并且将最大并发查询数的值配置得太高，则可能会遇到 Hive 服务器2重新启动失败。 至少需要最少数量的辅助角色节点，才能容纳给定数量的 Tez Ams (等于最大总计并发查询配置) 。 

## <a name="limitations"></a>限制

### <a name="node-label-file-missing"></a>缺少节点标签文件

HDInsight 自动缩放使用节点标签文件来确定节点是否准备好执行任务。 节点标签文件存储在具有三个副本的 HDFS 中。 如果群集大小大幅缩小，并且有大量的临时数据，则可能会删除所有三个副本。 如果发生这种情况，群集将进入错误状态。

### <a name="interactive-query-daemons-count"></a>交互式查询守护程序计数

对于启用自动缩放的交互式查询群集，自动缩放启用/关闭事件还会将交互式查询守护程序的数目向上/向下扩展到活动工作节点数。 不会在 Ambari 的配置中保留守护程序数量的变化 `num_llap_nodes` 。 如果手动重新启动 Hive 服务，则会根据 Ambari 中的配置重置交互查询守护程序的数量。

如果手动重新启动了交互式查询服务，则需要手动更改 `num_llap_node` 配置 () 运行 Hive 交互查询) 后台 *程序* 所需的节点 (，以匹配当前活动的工作节点计数。

## <a name="next-steps"></a>后续步骤

阅读[缩放准则](hdinsight-scaling-best-practices.md)，了解有关手动缩放群集的准则
