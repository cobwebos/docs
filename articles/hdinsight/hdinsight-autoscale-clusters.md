---
title: 自动缩放 Azure HDInsight 群集
description: 使用 Azure HDInsight 自动缩放功能自动使用 Apache Hadoop 缩放群集
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 4f9b43b6f800bb47942ccc00fee0fac4536d2ec0
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640579"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>自动缩放 Azure HDInsight 群集

> [!Important]
> Azure HDInsight 自动缩放功能于 2019 年 11 月 7 日发布，适用于 Spark 和 Hadoop 群集，并包含该功能预览版本中未提供的改进。 如果您在 2019 年 11 月 7 日之前创建了 Spark 群集，并希望在群集上使用自动缩放功能，则建议的路径是创建新群集，并在新群集上启用自动缩放。
>
> 交互式查询 （LLAP） 和 HBase 群集的自动缩放仍处于预览阶段。 自动缩放仅在 Spark、Hadoop、交互式查询和 HBase 群集上可用。

Azure HDInsight 的群集自动缩放功能可自动缩放群集中辅助节点的数量。 目前无法缩放群集中其他类型的节点。  创建新 HDInsight 群集期间，可以设置最小和最大工作节点数。 自动缩放功能随后监视分析负载的资源需求，并增加或减少工作器节点数。 此功能不会产生额外的费用。

## <a name="cluster-compatibility"></a>群集兼容性

下表描述了与自动缩放功能兼容的群集类型和版本。

| 版本 | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| 不包含 ESP 的 HDInsight 3.6 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| 不包含 ESP 的 HDInsight 4.0 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| 包含 ESP 的 HDInsight 3.6 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |
| 包含 ESP 的 HDInsight 4.0 | 是 | 是 | 是 | 是* | 否 | 否 | 否 |

\*HBase 群集只能配置为基于计划的缩放，不能基于负载。

## <a name="how-it-works"></a>工作原理

可为 HDInsight 群集选择基于负载的缩放或基于计划的缩放。 基于负载的扩展会更改群集中设置范围内的节点数，以确保最佳 CPU 使用率并最大限度降低运行成本。

基于计划的缩放根据特定时间生效的条件更改群集中的节点数。 这些条件将群集缩放为预期的节点数。

### <a name="metrics-monitoring"></a>指标监视

自动缩放会持续监视群集并收集以下指标：

|指标|说明|
|---|---|
|总挂起 CPU|开始执行所有待处理容器所需的核心总数。|
|总挂起内存|开始执行所有待处理容器所需的总内存（以 MB 为单位）。|
|总免费 CPU|活动工作节点上所有未使用核心的总和。|
|总可用内存|活动工作节点上未使用内存的总和（以 MB 为单位）。|
|每个节点使用的内存|工作节点上的负载。 使用了 10 GB 内存的工作节点的负载被认为比使用了 2 GB 内存的工作节点的负载更大。|
|每个节点的应用程序母版数|在工作节点上运行的应用程序主机 (AM) 容器的数量。 托管两个 AM 容器的工作节点被认为比托管零个 AM 容器的工作节点更重要。|

每 60 秒检查一次上述指标。 自动缩放根据这些指标做出决策。

### <a name="load-based-scale-conditions"></a>基于负载的缩放条件

当检测到以下条件时，自动缩放将发出缩放请求：

|纵向扩展|缩小|
|---|---|
|总待处理 CPU 大于总可用 CPU 的时间超过 3 分钟。|总待处理 CPU 小于总可用 CPU 的时间超过 10 分钟。|
|总待处理内存大于总可用内存的时间超过 3 分钟。|总待处理内存小于总可用内存的时间超过 10 分钟。|

对于放大，自动缩放发出放大请求以添加所需的节点数。 扩展基于满足当前 CPU 和内存要求所需的新工作节点数。

对于缩减，自动缩放发出删除一定数量的节点的请求。 缩减基于每个节点的 AM 容器数。 以及当前的 CPU 和内存要求。 该服务还会根据当前的作业执行来检测哪些节点可以删除。 纵向缩减操作首先会解除节点，然后从群集中删除它们。

## <a name="get-started"></a>入门

### <a name="create-a-cluster-with-load-based-autoscaling"></a>使用基于负载的自动缩放创建群集

若要结合基于负载的缩放启用自动缩放功能，请在创建普通群集的过程中完成以下步骤：

1. 在"**配置 + 定价**"选项卡上，选择"**启用自动缩放**"复选框。
1. 在“自动缩放类型”下选择“基于负载”。********
1. 输入以下属性的预期值：  

    * 适用于工作器节点的初始工作节点数****。****
    * 工作器节点**最小**数目。
    * 工作器节点**最大**数目。

    ![启用工作器节点的基于负载的自动缩放](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

工作节点的初始数量必须介于最小值和最大值之间（含最大值和最小值）。 此值定义创建群集时的群集初始大小。 工作器节点最小数目至少应设置为 3。 将群集缩放成少于三个节点可能导致系统停滞在安全模式下，因为没有进行充分的文件复制。  有关详细信息，请参阅[卡在安全模式](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)。

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>使用基于计划的自动缩放创建群集

若要结合基于计划的缩放启用自动缩放功能，请在创建普通群集的过程中完成以下步骤：

1. 在“配置 + 定价”选项卡上，勾选“启用自动缩放”复选框。********
1. 输入**工作器节点**的**节点数**，以控制纵向扩展群集的限制。
1. 在“自动缩放类型”下选择“基于计划”选项。********
1. 选择 **"配置**"以打开**自动缩放配置**窗口。
1. 选择时区，然后单击“+ 添加条件”****
1. 选择新条件要应用到的星期日期。
1. 编辑该条件生效的时间，以及群集要缩放到的节点数。
1. 根据需要添加更多条件。

    ![启用工作器节点的基于计划的创建](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

节点数最小为 3，最大为添加条件之前输入的最大工作器节点数。

### <a name="final-creation-steps"></a>最终创建步骤

通过在 **"节点大小**"下的下拉列表中选择 VM，为辅助节点选择 VM 类型。 为每个节点类型选择 VM 类型后，可以看到整个群集的估算成本范围。 请根据预算调整 VM 类型。

![启用工作器节点的基于计划的自动缩放节点大小](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

你的订阅具有针对每个区域的容量配额。 头节点和最大辅助节点的内核总数不能超过容量配额。 但是，此配额是软性限制；始终可创建支持票证来轻松地增加此配额。

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

若要在运行中的群集上启用自动缩放，请选择“设置”下的“群集大小”。******** 然后选择 **"启用自动缩放**"。 选择所需的自动缩放类型，然后输入基于负载或基于计划的缩放选项。 最后，选择“保存”****。

![启用工作器节点的基于计划的自动缩放运行群集](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>使用 REST API

要使用 REST API 在正在运行的群集上启用或禁用自动缩放，请向自动缩放终结点发出 POST 请求：

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

请在请求有效负载中使用适当的参数。 下面的 json 有效负载可以用来启用自动缩放。 使用有效负载 `{autoscale: null}` 禁用自动缩放。

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

请参阅介绍如何[启用基于负载的自动缩放](#load-based-autoscaling)的上一部分，详尽了解所有的有效负载参数。

## <a name="guidelines"></a>指南

### <a name="choosing-load-based-or-schedule-based-scaling"></a>选择基于负载或基于计划的缩放

在决定选择哪个模式之前，请考虑以下因素：

* 在群集创建期间启用“自动缩放”。
* 节点最小数目至少应为 3。
* 负载差异：群集的负载是否在特定日期的特定时间遵循一致的模式。 如果不是，则最好使用基于负载的计划。
* SLA 要求：自动缩放是被动的，而不是预测性的。 在负载开始增加以后，是否有足够的延迟来确保将群集设置为目标大小？ 如果有严格的 SLA 要求，并且负载是固定的已知模式，则"基于计划"是一个更好的选择。

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>考虑纵向扩展或纵向缩减操作的延迟

完成一项缩放操作可能需要 10 到 20 分钟。 设置自定义计划时，请将此延迟计划在内。 例如，如果需要在早晨 9:00 将群集大小设置为 20，请将计划触发器设置为更早的某个时间（例如早晨 8:30），这样缩放操作就可以在早晨 9:00 之前完成。

### <a name="preparation-for-scaling-down"></a>准备进行纵向缩减

在群集纵向缩减过程中，自动缩放会根据目标大小解除节点的授权。 如果这些节点上运行任务，自动缩放将等待任务完成。 由于每个工作器节点也充当 HDFS 中的某个角色，因此会将临时数据转移到剩余节点中。 因此，应确保其余节点上有足够的空间来承载所有临时数据。

正在运行的作业将继续。 挂起的作业将等待具有较少可用辅助角色节点的计划。

### <a name="minimum-cluster-size"></a>最小的群集大小

不要将群集缩减到少于三个节点。 将群集缩放成少于三个节点可能导致系统停滞在安全模式下，因为没有进行充分的文件复制。  有关详细信息，请参阅[卡在安全模式](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode)。

## <a name="monitoring"></a>监视

### <a name="cluster-status"></a>群集状态

Azure 门户中列出的群集状态可帮助你监视自动缩放活动。

![启用工作器节点的基于负载的自动缩放群集状态](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

以下列表解释了你可能会看到的所有群集状态消息。

| 群集状态 | 说明 |
|---|---|
| 运行 | 群集在正常运行。 所有以前的自动缩放活动已成功完成。 |
| 正在更新  | 正在更新群集自动缩放配置。  |
| HDInsight 配置  | 某个群集纵向扩展或缩减操作正在进行。  |
| 更新时出错  | HDInsight 在自动缩放配置更新期间遇到了问题。 客户可以选择重试更新或禁用自动缩放。  |
| 错误  | 群集出现问题，并且不可用。 请删除此群集，然后新建一个。  |

要查看群集中的当前节点数，请转到群集**的"概述"** 页上的 **"群集大小**图表"。 或者选择 **"设置"** 下的 **"群集大小**"。

### <a name="operation-history"></a>操作历史记录

可查看群集指标中包含的群集增加和减少历史记录。 还可以列出过去一天、过去一周或其他时间段的所有缩放操作。

在“监视”下选择“指标”。******** 然后从 **"指标"** 下拉框中选择 **"添加指标**"和 **"活动工作人员数**"。 选择右上角的按钮以更改时间范围。

![启用工作器节点的基于计划的自动缩放指标](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>后续步骤

在缩放指南中阅读有关手动缩放群集[的指南](hdinsight-scaling-best-practices.md)
