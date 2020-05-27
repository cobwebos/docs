---
title: Azure Synapse Analytics 中的 Apache Spark - 核心概念
description: 本文介绍 Azure Synapse Analytics 中的 Apache Spark 以及不同的概念。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 6276d198e547efec3d2e3cb88816da5e2b593aae
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83644686"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Azure Synapse Analytics 中的 Apache Spark 的核心概念

Apache Spark 是并行处理框架，支持使用内存中处理来提升大数据分析应用程序的性能。 Azure Synapse Analytics 中的 Apache Spark 是 Apache Spark 在云中的一种 Microsoft 实现。 

使用 Azure Synapse 可在 Azure 中轻松创建和配置 Spark 功能。 Azure Synapse 提供本文档所述的这些 Spark 功能的不同实现。

## <a name="spark-pools-preview"></a>Spark 池（预览版）

Spark 池（预览版）在 Azure 门户中创建。 Spark 池的定义是，在实例化后，该池可用于创建一个 Spark 实例来处理数据。 创建 Spark 池后，它只作为元数据存在；不会消耗、运行资源，也不会产生资源费用。 Spark 池具有一系列用于控制 Spark 实例特征的属性；这些特征包括但不限于名称、大小、缩放行为和生存时间。

由于创建 Spark 池不会产生相关的费用或资源成本，因此可以使用任意数目的不同配置创建任意数目的 Spark 池。 还可以将权限应用到 Spark 池，使用户只能访问某些池。

最佳做法是先创建较小的 Spark 池进行开发和调试，然后再创建较大的 Spark 池来运行生产工作负载。

可在 [Synapse Analytics 中的 Spark 池入门](../quickstart-create-apache-spark-pool-portal.md)中了解如何创建 Spark 池及查看其所有属性

## <a name="spark-instances"></a>Spark 实例

Spark 实例在你连接到 Spark 池、创建会话和运行作业时创建。 由于可能有多个用户有权访问单个 Spark 池，因此将为连接的每个用户创建一个新的 Spark 实例。 

提交另一个作业时，如果池中有容量，并且现有的 Spark 实例也有容量，则现有的实例会处理该作业；如果不符合上述条件，但池级别有容量，则会创建一个新的 Spark 实例。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1

- 创建名为 SP1 的 Spark 池；其固定群集大小为 20 个节点。
- 提交使用 10 个节点的笔记本作业 J1，此时会创建 Spark 实例 SI1 来处理该作业。
- 现在提交使用 10 个节点的另一个作业 J2（因为池和实例中仍有容量），此时，J2 将由 SI1 处理。
- 如果 J2 请求 11 个节点，则 SP1 或 SI1 中不会有容量。 在这种情况下，如果 J2 来自笔记本，则会拒绝该作业；如果 J2 来自批处理作业，则会将其排队。

### <a name="example-2"></a>示例 2

- 创建名为 SP2 的 Spark 池；该池已启用自动缩放，并使用 10-20 个节点
- 提交使用 10 个节点的笔记本作业 J1，此时会创建 Spark 实例 SI1 来处理该作业。
- 现在提交使用 10 个节点的另一个作业 J2（因为池中仍有容量），此时实例会自动扩展到 20 个节点并处理 J2。

### <a name="example-3"></a>示例 3

- 创建名为 SP1 的 Spark 池；其固定群集大小为 20 个节点。
- 提交使用 10 个节点的笔记本作业 J1，此时会创建 Spark 实例 SI1 来处理该作业。
- 另一个用户 U2 提交使用 10 个节点的作业 J3，此时会创建新的 Spark 实例 SI2 来处理该作业。
- 现在提交使用 10 个节点的另一个作业 J2（因为池和实例中仍有容量），此时，J2 将由 SI1 处理。

## <a name="next-steps"></a>后续步骤

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 文档](https://spark.apache.org/docs/2.4.4/)
