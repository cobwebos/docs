---
title: include 文件
description: include 文件
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 5/30/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: e1db978dbb7b63aaa4e9d189486faa0dfef520d1
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75662917"
---
Azure 数据工厂是一项多租户服务，它具有以下默认限制，可确保客户订阅免受彼此工作负荷的保护。 若要将最大限制提高到订阅的最大值，请联系支持人员。

### <a name="version-2"></a>版本 2

| 资源 | 默认限制 | 最大限制 |
| -------- | ------------- | ------------- |
| Azure 订阅中的数据工厂 | 50 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 数据工厂中的实体总数，如管道、数据集、触发器、链接服务和集成运行时 | 5,000 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 一个订阅下的 Azure SSIS 集成运行时的 CPU 内核总数 | 256 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个数据工厂在工厂中的所有管道间共享的并发管道运行 | 10,000  | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个[Azure Integration Runtime 区域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)每个订阅的并发外部活动运行<br><small>外部活动在集成运行时上进行管理，但在链接服务上执行，包括 Databricks、存储过程、HDInsights 和其他活动。</small> | 3000 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个[Azure Integration Runtime 区域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)每个订阅的并发管道活动运行 <br><small>在集成运行时上执行管道活动，包括查找、GetMetadata 和删除。</small>| 1000 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个[Azure Integration Runtime 区域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)每个订阅的并发创作操作<br><small>包括测试连接，浏览文件夹列表和表列表，预览数据。 | 200 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个[Azure Integration Runtime 区域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)每个订阅的并发数据集成单位<sup>1</sup>个消耗量| 区域组 1<sup>2</sup>：6000<br>区域组 2<sup>2</sup>：3000<br>区域组 3<sup>2</sup>：1500 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个管道的最大活动数，包括容器的内部活动 | 40 | 40 |
| 可针对单个自承载集成运行时创建的链接集成运行时的最大数目 | 100 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个管道的最大参数 | 50 | 50 |
| ForEach 项 | 100,000 | 100,000 |
| ForEach 并行度 | 20 | 50 |
| 每个管道的最大排队运行 | 100 | 100 |
| 每个表达式的字符数 | 8,192 | 8,192 |
| 最小翻转窗口触发器间隔 | 15 分钟 | 15 分钟 |
| 管道活动运行的最大超时 | 7 天 | 7 天 |
| 管道对象的每个对象字节数<sup>3</sup> | 200 KB | 200 KB |
| 数据集和链接服务对象的每个对象字节数<sup>3</sup> | 100 KB | 2000 KB |
| 每个复制活动运行的数据集成单元<sup>1</sup> | 256 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 编写 API 调用 | 1200/h<br/><br/> 此限制是由 Azure 资源管理器而不是 Azure 数据工厂所强加的。 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 读取 API 调用 | 12500/h<br/><br/> 此限制是由 Azure 资源管理器而不是 Azure 数据工厂所强加的。 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每分钟监视的查询数 | 1,000 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每分钟的实体 CRUD 操作数 | 50 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 数据流调试会话最长时间 | 8小时 | 8小时 |
| 每工厂数据流的并发数量 | 50 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个用户每个工厂的数据流调试会话并发数量 | 3 | 3 |
| 数据流 Azure IR TTL 限制 | 4小时 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |

<sup>1</sup>数据集成单元（DIU）用于云到云复制操作，详细了解[数据集成单元（版本2）](../articles/data-factory/copy-activity-performance.md#data-integration-units)。 有关计费的信息，请参阅[Azure 数据工厂定价](https://azure.microsoft.com/pricing/details/data-factory/)。

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) [全局可用](https://azure.microsoft.com/global-infrastructure/services/)，以确保数据符合性、效率并降低网络出口成本。 

| 区域组 | 区域 | 
| -------- | ------ |
| 区域组1 | 美国中部、美国东部、东2、北欧、西欧、美国西部、美国西部2 |
| 区域组2 | 澳大利亚东部、澳大利亚东南部、巴西南部、印度中部、日本东部、Northcentral、Southcentral US、东南亚、美国西部 |
| 区域组3 | 加拿大中部、东亚、法国中部、韩国中部英国南部 |

<sup>3</sup>管道、数据集和链接服务对象代表工作负荷的逻辑分组。 这些对象的限制与可通过 Azure 数据工厂移动和处理的数据量无关。 数据工厂旨在进行扩展以处理数 pb 的数据。

### <a name="version-1"></a>版本 1

| **资源** | **默认限制** | **最大限制** |
| --- | --- | --- |
| 数据工厂中的管道 |2,500 |请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 数据工厂中的数据集 |5,000 |请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个数据集的并发切片数 |10 |10 |
| 管道对象的每个对象字节数<sup>1</sup> |200 KB |200 KB |
| 数据集和链接服务对象的每个对象字节数<sup>1</sup> |100 KB |2000 KB |
| 订阅中的 Azure HDInsight 按需群集核心数<sup>2</sup> |60 |请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 云数据移动单元每个复制活动运行<sup>3</sup> |32 |请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 管道活动运行的重试次数 |1,000 |MaxInt（32 位） |

<sup>1</sup>管道、数据集和链接服务对象代表工作负荷的逻辑分组。 这些对象的限制与可通过 Azure 数据工厂移动和处理的数据量无关。 数据工厂旨在进行扩展以处理数 pb 的数据。

<sup>2</sup> 按需 HDInsight 核心数从包含数据工厂的订阅中分配。 因此，以前的限制是针对按需 HDInsight 核心的数据工厂强制核心限制。 这不同于与 Azure 订阅关联的核心限制。

<sup>3</sup>版本1的云数据移动单元（DMU）用于云到云复制操作，详细信息请参阅[云数据移动单元（版本1）](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units)。 有关计费的信息，请参阅[Azure 数据工厂定价](https://azure.microsoft.com/pricing/details/data-factory/)。

| **资源** | **默认下限** | **最小限制** |
| --- | --- | --- |
| 计划间隔 |15 分钟 |15 分钟 |
| 重试尝试之间的间隔 |1 秒 |1 秒 |
| 重试超时值 |1 秒 |1 秒 |

#### <a name="web-service-call-limits"></a>Web 服务调用限制
Azure 资源管理器限制 API 调用。 可以根据 [Azure 资源管理器 API 限制](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)中规定的频率执行 API 调用。
