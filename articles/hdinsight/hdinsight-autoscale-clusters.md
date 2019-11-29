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
ms.openlocfilehash: 5cc473635543a22fd7e7223f4a5715f78457a897
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561750"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>自动缩放 Azure HDInsight 群集

> [!Important]
> 自动缩放功能仅适用于在5月 2019 8 日之后创建的 Spark、Hive 和 MapReduce 群集。 

Azure HDInsight 的群集自动缩放功能会自动增加和减少群集中的辅助角色节点数。 当前无法缩放群集中的其他类型的节点。  创建新 HDInsight 群集期间，可以设置最小和最大工作节点数。 然后，自动缩放会监视 analytics 负载的资源要求，并增加或减少辅助角色节点的数量。 此功能不收取额外费用。

## <a name="cluster-compatibility"></a>群集兼容性

下表描述了与自动缩放功能兼容的群集类型和版本。

| 版本 | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| 不带 ESP 的 HDInsight 3。6 | 是，仅2。3| 是 | No | No | No | No | No |
| 不带 ESP 的 HDInsight 4。0 | 是 | 是 | No | No | No | No | No |
| HDInsight 3.6 与 ESP | 是，仅2。3 | 是 | No | No | No | No | No |
| HDInsight 4.0 与 ESP | 是 | 是 | No | No | No | No | No |

## <a name="how-it-works"></a>如何运作

可以为 HDInsight 群集选择基于负载的缩放或基于计划的缩放。 基于负载的缩放更改群集中的节点数（在你设置的范围内），以确保 CPU 的最佳利用率并最大程度地降低运行成本。

基于计划的缩放根据特定时间生效的条件更改群集中的节点数。 这些条件将群集缩放到所需的节点数。

### <a name="metrics-monitoring"></a>指标监视

自动缩放会持续监视群集并收集以下指标：

* **挂起的总 CPU**：启动执行所有挂起的容器所需的核心总数。
* **挂起的总内存**：启动所有挂起容器的执行所需的总内存（以 MB 为单位）。
* **可用 CPU 总计**：活动工作节点上所有未使用的内核数之和。
* **总可用内存**：活动工作节点上未使用的内存（以 MB 为单位）的总和。
* **每个节点使用的内存**：辅助节点上的负载。 使用了 10 GB 内存的工作节点的负载被认为比使用了 2 GB 内存的工作节点的负载更大。
* **每个节点的应用程序主机的数量**：辅助节点上运行的应用程序主（AM）容器的数目。 托管两个 AM 容器的工作节点被视为比托管零 AM 容器的工作节点更重要。

每 60 秒检查一次上述指标。 自动缩放根据这些指标进行纵向扩展和缩减决策。

### <a name="load-based-cluster-scale-up"></a>基于负载的群集纵向扩展

当检测到以下情况时，自动缩放将发出向上扩展请求：

* 挂起 CPU 总数大于可用 CPU 总数超过3分钟。
* Total memory memory 大于可用内存总量超过3分钟。

HDInsight 服务计算需要多少个新的工作节点来满足当前的 CPU 和内存需求，然后发出向上扩展请求以添加所需数量的节点。

### <a name="load-based-cluster-scale-down"></a>基于负载的群集缩小

当检测到以下情况时，自动缩放将发出向下缩放请求：

* 总待处理 CPU 小于总可用 CPU 的时间超过 10 分钟。
* 总待处理内存小于总可用内存的时间超过 10 分钟。

根据每个节点的 AM 容器数以及当前的 CPU 和内存需求，自动缩放会发出删除一定数量节点的请求。 服务还会根据当前作业执行情况，检测哪些节点是候选删除。 缩小操作首先 add-on 节点，然后将其从群集中删除。

## <a name="get-started"></a>开始体验

### <a name="create-a-cluster-with-load-based-autoscaling"></a>使用基于负载的自动缩放创建群集

若要在群集上使用自动缩放，则在创建群集时必须启用 "**启用自动缩放**" 选项。 

若要启用基于负载的缩放的自动缩放功能，请在正常的群集创建过程中完成以下步骤：

1. 在 "**配置 + 定价**" 选项卡上，选中 "**启用自动缩放**" 复选框。
1. 在**自动缩放类型**下选择 "**基于负载**"。
1. 为以下属性输入所需的值：  

    * **辅助角色节点** **的初始节点数**。
    * 辅助角色节点的**最小**数目。
    * 辅助角色节点的**最大**数目。

    ![启用辅助节点基于负载的自动缩放](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

工作节点的初始数量必须介于最小值和最大值之间（含最大值和最小值）。 此值定义群集创建时的初始大小。 辅助角色节点的最小数目应设置为三个或更多。 . 将群集缩放到小于三个节点可能导致其在安全模式下停滞，因为文件复制不足。 有关详细信息，请参阅[进入安全模式停滞]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode)状态。

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>使用基于计划的自动缩放创建群集

若要使用基于计划的缩放启用自动缩放功能，请在正常的群集创建过程中完成以下步骤：

1. 在 "**配置 + 定价**" 选项卡上，选中 "**启用自动缩放**" 复选框。
1. 输入**辅助角色节点**的**节点数**，该节点控制扩展群集的限制。
1. 在**自动缩放类型**下选择 "**基于计划**" 选项。
1. 单击 "**配置**" 以打开 "**自动缩放配置**" 窗口。
1. 选择时区，并单击 " **+ 添加条件**"
1. 选择新条件应应用于一周中的哪一天。
1. 编辑条件应生效的时间和群集应缩放到的节点数。
1. 如果需要，添加更多条件。

    ![启用基于工作节点计划的创建](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

节点数必须介于3和在添加条件之前输入的辅助角色节点的最大数目之间。

### <a name="final-creation-steps"></a>最终创建步骤

对于基于负载和计划的缩放，请在 "**节点大小**" 下的下拉列表中选择 vm，以选择工作节点的 vm 类型。 为每个节点类型选择 VM 类型后，可以看到整个群集的估计成本范围。 调整 VM 类型以适应预算。

![启用辅助节点基于计划的自动缩放节点大小](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

你的订阅具有针对每个区域的容量配额。 头节点核心总数加最大工作节点数不能超过容量配额。 但是，此配额是软性限制；始终可创建支持票证来轻松地增加此配额。

> [!Note]  
> 如果超出了总核心配额限制，则将收到一条错误消息，指出 "最大节点已超过此区域的可用内核数，请选择另一个区域，或联系支持人员以增加配额。"

有关使用 Azure 门户创建 HDInsight 群集的详细信息，请参阅[使用 Azure 门户在 HDInsight 中创建基于 Linux 的群集](hdinsight-hadoop-create-linux-clusters-portal.md)。  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>使用资源管理器模板创建群集

#### <a name="load-based-autoscaling"></a>基于负载的自动缩放

你可以使用基于负载自动缩放的 Azure 资源管理器模板创建一个 HDInsight 群集，方法是将 `autoscale` 节点添加到 `computeProfile` > `workernode` 部分，其中包含属性 `minInstanceCount` 和 `maxInstanceCount`，如下面的 json 代码段所示。

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

可以通过将 `autoscale` 节点添加到 `computeProfile` > `workernode` 部分来创建具有基于计划的自动缩放 Azure 资源管理器模板的 HDInsight 群集。 "`autoscale`" 节点包含一个 `recurrence`，该具有描述更改发生时间的 `timezone` 和 `schedule`。

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

若要在运行的群集上启用自动缩放，请在 "**设置**" 下选择 "**群集大小**" 然后单击 "**启用自动缩放**"。 选择所需的自动缩放类型，并输入基于负载或计划的缩放的选项。 最后，单击“保存”。

![启用基于工作节点计划的基于计划的自动缩放运行群集](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-enable-running-cluster.png)

#### <a name="using-the-rest-api"></a>使用 REST API

若要使用 REST API 在正在运行的群集上启用或禁用自动缩放，请将 POST 请求发送到自动缩放终结点，如下面的代码片段所示：

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

使用请求负载中的相应参数。 下面的 json 有效负载可用于启用自动缩放。 使用有效负载 `{autoscale: null}` 禁用自动缩放。

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

请参阅上一节，了解如何[启用基于负载的自动缩放](#load-based-autoscaling)以获取所有负载参数的完整说明。

## <a name="best-practices"></a>最佳做法

### <a name="choosing-load-based-or-schedule-based-scaling"></a>选择基于负载或计划的缩放

在决定要选择哪种模式之前，请考虑以下因素：

* 在群集创建期间启用自动缩放。
* 最小节点数应至少为三个。
* 负载变化：群集的负载是否在特定的时间在特定的时间遵循一致的模式。 如果不是，则基于负载的计划是更好的选择。
* SLA 要求：自动缩放缩放为反应性，而不是预测。 负载开始增加的时间和群集需要达到目标大小时之间是否有足够的延迟？ 如果存在严格的 SLA 要求并且负载为固定的已知模式，则 "基于计划" 是一个更好的选择。

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>请考虑向上扩展或向下缩放操作的延迟

完成缩放操作可能需要10到20分钟的时间。 设置自定义计划时，请规划此延迟。 例如，如果在上午9:00 需要群集大小为20，请将计划触发器设置为较早的时间（如 8:30 AM），以使缩放操作已完成 9:00 AM。

### <a name="preparation-for-scaling-down"></a>准备缩减

在群集缩小过程中，自动缩放会使节点停止运行以满足目标大小。 如果这些节点上正在运行任务，自动缩放将等待，直到任务完成。 由于每个辅助角色节点还在 HDFS 中提供角色服务，因此临时数据将移动到剩余节点。 因此，您应该确保剩余节点上有足够的空间来承载所有临时数据。

正在运行的作业将继续运行并完成。 挂起的作业将等待计划为普通的可用工作节点。

### <a name="minimum-cluster-size"></a>最小群集大小

不要将群集缩小到少于三个节点。 将群集缩放到小于三个节点可能导致其在安全模式下停滞，因为文件复制不足。 有关详细信息，请参阅[进入安全模式停滞]( https://docs.microsoft.com/ azure/hdinsight/hdinsight-scaling-best-practices#getting-stuck-in-safe-mode)状态。

## <a name="monitoring"></a>监视

### <a name="cluster-status"></a>群集状态

Azure 门户中列出的群集状态有助于监视自动缩放活动。

![启用基于工作节点负载的自动缩放群集状态](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

下面的列表中介绍了你可能会看到的所有群集状态消息。

| 群集状态 | 说明 |
|---|---|
| 正在运行 | 群集正常运行。 先前的所有自动缩放活动已成功完成。 |
| 正在更新…  | 正在更新群集自动缩放配置。  |
| HDInsight 配置  | 群集向上缩放或向下缩放操作正在进行。  |
| 正在更新错误  | HDInsight 在自动缩放配置更新过程中遇到问题。 客户可选择重试更新或禁用自动缩放。  |
| 错误  | 群集出现问题，无法使用。 删除此群集并创建一个新群集。  |

若要查看群集中节点的当前数目，请在群集的 "**概述**" 页上，单击 "**群集大小**" 图表，或在 "**设置**" 下单击 "**群集大小**"。

### <a name="operation-history"></a>操作历史记录

可以在群集度量值中查看群集的向上缩放和向下缩放历史记录。 还可以列出过去一天、一周或其他时间段内的所有缩放操作。

选择 "**监视**" 下的**度量值**。 然后单击 "**指标**" 下拉框中的 "**添加指标**" 和 "**活动工作线程数**"。 单击右上角的按钮以更改时间范围。

![启用基于工作节点计划的自动缩放指标](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>后续步骤

* 阅读[缩放最佳做法](hdinsight-scaling-best-practices.md)，了解手动缩放群集的最佳做法
