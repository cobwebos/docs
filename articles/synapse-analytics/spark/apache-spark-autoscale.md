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
ms.openlocfilehash: a2f907384326aa887c12c293feb8c988f42bbaf1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210509"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>自动缩放 Azure Synapse Analytics Apache Spark 池

Azure Synapse Spark 池的自动缩放功能可自动缩放群集实例中的节点数。 在创建新的 Azure Synapse Spark 池的过程中，在选择 "自动缩放" 时，可以设置最小和最大节点数。 然后，自动缩放会监视负载的资源要求，并向上或向下缩放节点数。 此功能不会产生额外的费用。

## <a name="metrics-monitoring"></a>指标监视

自动缩放会持续监视 Spark 实例，并收集以下度量值：

|指标|说明|
|---|---|
|挂起 CPU 总数|开始执行所有挂起的节点所需的核心总数。|
|挂起的总内存|启动所有挂起节点的执行所需的总内存（以 MB 为单位）。|
|可用 CPU 总量|活动节点上所有未使用的内核的总和。|
|可用内存总量|活动节点上未使用的内存（以 MB 为单位）的总和。|
|每个节点使用的内存|节点上的负载。 如果某个节点在其上使用了 10 GB 的内存，则认为其负载比具有 2 GB 使用的内存的工作负荷要多。|

每隔30秒检查以上指标。 自动缩放根据这些指标做出纵向扩展和纵向缩减决策。

## <a name="load-based-scale-conditions"></a>基于负载的缩放条件

当检测到以下情况时，自动缩放将发出一个缩放请求：

|纵向扩展|缩小|
|---|---|
|总待处理 CPU 大于总可用 CPU 的时间超过 1 分钟。|挂起 CPU 总数小于可用 CPU 总数超过2分钟。|
|总待处理内存大于总可用内存的时间超过 1 分钟。|挂起的总内存小于可用内存总量超过2分钟。|

对于纵向扩展，Azure Synapse 自动缩放服务计算需要多少个新节点来满足当前的 CPU 和内存需求，然后发出向上扩展请求以添加所需数量的节点。

为了向下缩放，根据执行程序数、每个节点的应用程序主机以及当前的 CPU 和内存需求，自动缩放会发出删除一定数量节点的请求。 该服务还会根据当前的作业执行来检测哪些节点可以删除。 纵向缩减操作首先会解除节点，然后从群集中删除它们。

## <a name="get-started"></a>入门

### <a name="create-a-spark-pool-with-autoscaling"></a>使用自动缩放创建 Spark 池

若要启用自动缩放功能，请在正常的池创建过程中完成以下步骤：

1. 在 "**基本**信息" 选项卡上，选中 "**启用自动缩放**" 复选框。
1. 为以下属性输入所需的值：  

    * **最小**节点数。
    * **最大**节点数。

初始节点数将为最小值。 此值用于定义实例在创建时的初始大小。 节点的最小数量不能小于三。

## <a name="best-practices"></a>最佳做法

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>考虑纵向扩展或纵向缩减操作的延迟

缩放操作完成可能需要1到5分钟。

### <a name="preparation-for-scaling-down"></a>准备进行纵向缩减

在实例缩小过程中，自动缩放会使节点处于解除锁定状态，以便不会在该节点上启动新的执行程序。

正在运行的作业会继续运行，直至完成。 挂起的作业将等待计划为普通的可用节点。

## <a name="next-steps"></a>后续步骤

设置新 Spark 池的快速入门[创建 spark 池](..\quickstart-create-apache-spark-pool.md)
