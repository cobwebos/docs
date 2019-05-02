---
title: include 文件
description: include 文件
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 1/10/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 6d06ac6efd08c14f77fd963ddf2c67de54260959
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733755"
---
Azure 数据工厂是一个具有以下默认限制到位，以确保客户订阅不会受到彼此工作负荷的多租户服务。 若要将提升到你的订阅的最大限制，请联系支持。

### <a name="version-2"></a>版本 2

| 资源 | 默认限制 | 最大限制 |
| -------- | ------------- | ------------- |
| Azure 订阅中的数据工厂 | 50 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 实体，例如管道、 数据集、 触发器、 链接的服务和集成运行时，数据工厂中的总数 | 5,000 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| Azure SSIS 集成运行时的一个订阅下的总 CPU 内核 | 256 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每在工厂中的所有管道之间共享的数据工厂的并行管道运行 | 10,000  | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个管道，其中包括用于容器的内部活动的最大活动 | 40 | 40 |
| 可以针对单个自承载的集成运行时创建的链接的集成运行时的最大数目 | 100 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个管道的最大参数 | 50 | 50 |
| ForEach 项 | 100,000 | 100,000 |
| ForEach 并行度 | 20 | 50 |
| 每个表达式的字符数 | 8,192 | 8,192 |
| 最小的翻转窗口触发器间隔 | 15 分钟 | 15 分钟 |
| 最大超时为管道活动运行 | 7 天 | 7 天 |
| 每个管道对象的对象的字节<sup>1</sup> | 200 KB | 200 KB |
| 每个数据集的对象和链接的服务对象的字节数<sup>1</sup> | 100 KB | 2,000 KB |
| 每个复制活动运行的数据集成单位<sup>3</sup> | 256 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 编写 API 调用 | 2500/h<br/><br/> 此限制是由 Azure 资源管理器而不是 Azure 数据工厂所强加的。 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 读取 API 调用 | 12500/h<br/><br/> 此限制是由 Azure 资源管理器而不是 Azure 数据工厂所强加的。 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每分钟监视的查询数 | 1,000 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每分钟的实体 CRUD 操作数 | 50 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |


### <a name="version-1"></a>版本 1

| **资源** | **默认限制** | **最大限制** |
| --- | --- | --- |
| Azure 订阅中的数据工厂 |50 |请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 数据工厂中的管道 |2,500 |请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 数据工厂中的数据集 |5,000 |请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个数据集的并发切片数 |10 |10 |
| 每个管道对象的对象的字节<sup>1</sup> |200 KB |200 KB |
| 每个对象的数据的字节集和链接服务对象<sup>1</sup> |100 KB |2,000 KB |
| 在订阅中的 azure HDInsight 按需群集核心<sup>2</sup> |60 |请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 云数据移动单位，每个复制活动运行<sup>3</sup> |32 |请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 管道活动运行的重试次数 |1,000 |MaxInt（32 位） |

<sup>1</sup>管道、 数据集和链接的服务对象表示工作负荷的逻辑分组。 这些对象的限制不相关的使用 Azure 数据工厂可以移动和处理的数据量。 数据工厂可进行扩展以处理千万亿字节的数据。

<sup>2</sup> 按需 HDInsight 核心数从包含数据工厂的订阅中分配。 因此，以前的限制是针对按需 HDInsight 核心强制数据工厂执行核心数限制。 它是不同于与你的 Azure 订阅相关联的核心限制。

<sup>3</sup>云到云复制操作中使用的数据集成单位 (DIU) 版本 2 或版本 1 的云数据移动单位 (DMU)。 它是度量值，代表单个单位在数据工厂的能力。 它结合了 CPU、 内存和网络资源分配。 就某些方案来说，使用更多 DMU 可以提高复制吞吐量。 有关详细信息，请参阅[数据集成单位 （第 2 版）](../articles/data-factory/copy-activity-performance.md#data-integration-units)并[云数据移动单位 （版本 1）](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units)。 有关计费的更多信息，请参阅[Azure 数据工厂定价](https://azure.microsoft.com/pricing/details/data-factory/)。

<sup>4</sup>integration runtime (IR) 是 Azure 数据工厂用于在不同的网络环境中，数据移动、 调度活动来计算服务，如之间提供数据集成功能的计算基础结构和SSIS 包的执行。 有关详细信息，请参阅[集成运行时概述](../articles/data-factory/concepts-integration-runtime.md)。

| **资源** | **默认下限** | **最小限制** |
| --- | --- | --- |
| 计划间隔 |15 分钟 |15 分钟 |
| 重试尝试之间的间隔 |1 秒 |1 秒 |
| 重试超时值 |1 秒 |1 秒 |

#### <a name="web-service-call-limits"></a>Web 服务调用限制
Azure 资源管理器限制 API 调用。 可以根据 [Azure 资源管理器 API 限制](../articles/azure-subscription-service-limits.md#resource-group-limits)中规定的频率执行 API 调用。
