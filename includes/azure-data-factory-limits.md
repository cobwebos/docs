---
title: include 文件
description: include 文件
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 01/08/2020
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 217e48ab21439f03e0b52c894c9aace5b51b1502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086149"
---
Azure 数据工厂是一项多租户服务，具有以下默认限制，目的是确保客户订阅不受彼此工作负荷的影响。 若要将限制提高到订阅的最高限制，请联系支持部门。

### <a name="version-2"></a>版本 2

| 资源 | 默认限制 | 最大限制 |
| -------- | ------------- | ------------- |
| Azure 订阅中的数据工厂 | 800 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 数据工厂中的实体（例如管道、数据集、触发器、链接服务以及集成运行时）的总数 | 5,000 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 一个订阅中 Azure-SSIS Integration Runtime 的总 CPU 内核数 | 256 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个数据工厂的并行管道运行数（在工厂中的所有管道之间共享） | 10,000  | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个 [Azure Integration Runtime 区域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)的每个订阅的并发外部活动运行数<br><small>外部活动在集成运行时进行管理，但在链接的服务上执行，包括数据砖块、存储过程、HDInsights、Web 等。</small> | 3000 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个 [Azure Integration Runtime 区域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)的每个订阅的并发管道活动运行数 <br><small>管道活动在集成运行时上执行，包括 Lookup、GetMetadata 和 Delete。</small>| 1000 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个 [Azure Integration Runtime 区域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)的每个订阅的并发创作操作数<br><small>包括测试连接、浏览器文件夹列表和表列表、预览数据。 | 200 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个[Azure 集成运行时区域](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)每个订阅的并发数据集成单元<sup>1</sup>消耗| 区域组 1<sup>2</sup>：6000<br>区域组 2<sup>2</sup>：3000<br>区域组 3<sup>2</sup>： 1500 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个管道的最大活动数，包括容器的内部活动 | 40 | 40 |
| 可以针对单个自承载集成运行时创建的最大链接集成运行时数 | 100 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个管道的最大参数个数 | 50 | 50 |
| ForEach 项 | 100,000 | 100,000 |
| ForEach 并行度 | 20 | 50 |
| 每个管道的最大排队运行 | 100 | 100 |
| 每个表达式的字符数 | 8,192 | 8,192 |
| 最小翻转窗口触发间隔 | 15 分钟 | 15 分钟 |
| 管道活动运行的最大超时时间 | 7 天 | 7 天 |
| 管道对象的每个对象的字节数<sup>3</sup> | 200 KB | 200 KB |
| 数据集和链接服务对象的每个对象的字节数<sup>3</sup> | 100 KB | 2,000 KB |
| 每个复制活动运行的数据集成单元数<sup>1</sup> | 256 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 编写 API 调用 | 1,200/小时<br/><br/> 此限制是由 Azure 资源管理器而不是 Azure 数据工厂所强加的。 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 读取 API 调用 | 12,500/小时<br/><br/> 此限制是由 Azure 资源管理器而不是 Azure 数据工厂所强加的。 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每分钟监视的查询数 | 1,000 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每分钟的实体 CRUD 操作数 | 50 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 数据流调试会话的最大时间 | 8 小时 | 8 小时 |
| 每个工厂的并发数据流数 | 50 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个工厂每个用户的数据流调试会话的并发数量 | 3 | 3 |
| 数据流 Azure IR TTL 限制 | 4 小时 | 请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |

<sup>1</sup> 数据集成单元 (DIU) 用于云到云复制操作，详见[数据集成单元（版本 2）](../articles/data-factory/copy-activity-performance.md#data-integration-units)。 有关计费的信息，请参阅 [Azure 数据工厂定价](https://azure.microsoft.com/pricing/details/data-factory/)。

<sup>2</sup> [Azure 集成运行时](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime)[可在全球范围内使用](https://azure.microsoft.com/global-infrastructure/services/)，以确保数据合规性、效率和降低网络出口成本。 

| 区域组 | 区域 | 
| -------- | ------ |
| 区域组 1 | 美国中部、美国东部、美国东部、北欧、西欧、美国西部、美国西部 2 |
| 区域组 2 | 澳大利亚东部、澳大利亚东南部、巴西南部、印度中部、日本东部、美国中北部、美国中南部、东南亚、美国中西部 |
| 区域组 3 | 加拿大 中部、 东亚、 法国 中部、 韩国 中部、 英国南部 |

<sup>3</sup> 管道、数据集和链接服务对象代表工作负荷的逻辑分组。 对这些对象的限制与可以使用 Azure 数据工厂移动或处理的数据量无关。 可以缩放数据工厂以处理 PB 量级的数据。

### <a name="version-1"></a>版本 1

| **资源** | **默认限制** | **最大限度** |
| --- | --- | --- |
| 数据工厂中的管道 |2,500 |请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 数据工厂内的数据集 |5,000 |请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个数据集的并发切片 |10 |10 |
| 管道对象<sup>1</sup>的每个对象的字节数 |200 KB |200 KB |
| 数据集和链接服务对象每个对象的字节<sup>数 1</sup> |100 KB |2,000 KB |
| 订阅<sup>2</sup>中的 Azure HDInsight 按需群集内核 |60 |请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 每个复制活动运行的云数据移动单位<sup>3</sup> |32 |请[联系支持人员](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。 |
| 管道活动运行的重试次数 |1,000 |MaxInt（32 位） |

<sup>1</sup>管道、数据集和链接的服务对象表示工作负载的逻辑分组。 对这些对象的限制与可以使用 Azure 数据工厂移动或处理的数据量无关。 可以缩放数据工厂以处理 PB 量级的数据。

<sup>2</sup> 按需 HDInsight 核心数从包含数据工厂的订阅中分配。 因此，上一个限制是按需 HDInsight 内核的数据工厂强制实施的核心限制。 它与与 Azure 订阅关联的核心限制不同。

<sup>3</sup>版本 1 的云数据移动单元 （DMU） 用于云到云复制操作，从[云数据移动单元（版本 1）](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units)中了解更多信息。 有关计费的信息，请参阅 [Azure 数据工厂定价](https://azure.microsoft.com/pricing/details/data-factory/)。

| **资源** | **默认下限** | **最小限制** |
| --- | --- | --- |
| 计划间隔 |15 分钟 |15 分钟 |
| 重试尝试之间的间隔 |1 秒 |1 秒 |
| 重试超时值 |1 秒 |1 秒 |

#### <a name="web-service-call-limits"></a>Web 服务调用限制
Azure 资源管理器限制 API 调用。 可以根据 [Azure 资源管理器 API 限制](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)中规定的频率执行 API 调用。
