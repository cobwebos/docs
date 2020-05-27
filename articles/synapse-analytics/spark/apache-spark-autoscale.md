---
title: 自动缩放 Azure Synapse Apache Spark 实例
description: 使用 Azure Synapse 自动缩放功能自动缩放 Apache Spark 实例
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: be7ac79b9429d2fc72c3b6bc2b6d92666b089dfb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591958"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>自动缩放 Azure Synapse Analytics Apache Spark 池

Azure Synapse Analytics Apache Spark 池的自动缩放功能自动缩放群集实例中的节点数。 在创建新的 Azure Synapse Analytics Apache Spark 池期间，如果选择了自动缩放，则可以设置最小和最大节点数。 自动缩放功能随后监视负载的资源需求，并增加或减少节点数。 此功能不会产生额外费用。

## <a name="metrics-monitoring"></a>指标监视

自动缩放会持续监视 Spark 实例并收集以下指标：

|指标|说明|
|---|---|
|总待处理 CPU|开始执行所有待处理节点所需的核心总数。|
|总待处理内存|开始执行所有待处理节点所需的总内存（以 MB 为单位）。|
|总可用 CPU|活动节点上所有未使用核心的总和。|
|总可用内存|活动节点上未使用内存的总和（以 MB 为单位）。|
|每个节点的已使用内存|节点上的负载。 使用了 10 GB 内存的节点的负载会被认为比使用了 2 GB 内存的工作节点的负载更大。|

每 30 秒检查一次上述指标。 自动缩放将根据这些指标做出纵向扩展和纵向缩减决策。

## <a name="load-based-scale-conditions"></a>基于负载的缩放条件

检测到以下情况时，自动缩放将发出缩放请求：

|纵向扩展|纵向缩减|
|---|---|
|总待处理 CPU 大于总可用 CPU 的时间超过 1 分钟。|总待处理 CPU 小于总可用 CPU 的时间超过 2 分钟。|
|总待处理内存大于总可用内存的时间超过 1 分钟。|总待处理内存小于总可用内存的时间超过 2 分钟。|

对于纵向扩展，Azure Synapse 自动缩放服务将计算需要多少个新节点才能满足当前 CPU 和内存需求，然后发出纵向扩展请求来添加所需数量的节点。

对于纵向缩减，根据执行程序数、每个节点的应用程序主机以及当前 CPU 和内存需求，自动缩放会发出删除特定数量节点的请求。 此服务还会根据当前作业执行情况，检测待删除的节点。 纵向缩减操作首先关闭节点，然后将其从群集中删除。

## <a name="get-started"></a>入门

### <a name="create-a-spark-pool-with-autoscaling"></a>使用自动缩放创建 Spark 池

若要启用自动缩放功能，请在正常池创建过程中完成以下步骤：

1. 在“基本信息”选项卡上，选中“启用自动缩放”复选框。
1. 为以下属性输入所需的值：  

    * 最小节点数。
    * 最大节点数。

初始节点数将为最小值。 此值定义创建实例时实例的初始大小。 最小节点数不能少于三个。

## <a name="best-practices"></a>最佳做法

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>请考虑纵向扩展或纵向缩减操作的延迟

完成缩放操作可能需要 1 到 5 分钟。

### <a name="preparation-for-scaling-down"></a>准备纵向缩减

在实例纵向缩减过程中，自动缩放会使节点处于关闭状态，以便不会在该节点上启动新的执行程序。

正在运行的作业将继续运行并完成。 挂起作业将和往常一样等待调度，可用节点更少。

## <a name="next-steps"></a>后续步骤

设置新 Spark 池快速入门[创建 Spark 池](..\quickstart-create-apache-spark-pool.md)
