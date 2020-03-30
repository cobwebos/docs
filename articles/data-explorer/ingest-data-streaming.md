---
title: 使用流式引入将数据引入 Azure 数据资源管理器
description: 了解如何使用流式引入将数据引入（载入）Azure 数据资源管理器。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: d7d2bcf487c37fbb523b648d5aa4c572add5dfa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297078"
---
# <a name="streaming-ingestion-preview"></a>流式引入（预览版）

当您需要低延迟且摄入时间小于 10 秒时，对于不同的卷数据，请使用流式处理。 它用于优化许多表的操作处理，在一个或多个数据库中，其中数据流到每个表相对较小（每秒记录很少），但总体数据引入量很高（每秒数千条记录）。 

当数据量增长到每表超过每秒 1 MB 时，请使用批量引入，而不是流式处理。 请阅读[数据引入概述](/azure/data-explorer/ingest-data-overview)来详细了解各种引入方法。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* 登录到 [Web UI](https://dataexplorer.azure.com/)。
* [创建 Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>在群集上启用流式引入

> [!WARNING]
> 在启用蒸汽摄入之前，请查看[限制](#limitations)。

1. 在 Azure 门户中，转到 Azure 数据资源管理器群集。 在“设置”中选择“配置”。******** 
1. 在“配置”窗格中，选择“打开”以启用“流式引入”。************
1. 选择“保存”。****
 
    ![打开流式引入](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. 在 [Web UI](https://dataexplorer.azure.com/) 中，针对要接收流数据的表或数据库定义[流引入策略](/azure/kusto/management/streamingingestionpolicy)。 

    > [!NOTE]
    > * 如果在数据库级别定义了该策略，将为该数据库中的所有表启用流式引入。
    > * 应用的策略只能引用新引入的数据，而不能引用数据库中的其他表。

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>使用流式引入将数据引入到群集

支持两种流式引入类型：


* [**事件中心**](/azure/data-explorer/ingest-data-event-hub)， 用作数据源
* **自定义引入**要求您编写使用 Azure 数据资源管理器客户端库之一的应用程序。 有关示例应用程序，请参阅[流式引入示例](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample)。

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>选择适当的流式引入类型

|   |事件中心  |自定义引入  |
|---------|---------|---------|
|启动引入之后、数据可供查询之前的数据延迟   |    更长的延迟     |   更短的延迟      |
|开发开销    |   快速、简便的设置，无需开发开销    |   应用程序处理错误并确保数据一致性的高开发开销     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>在群集上禁用流式引入

> [!WARNING]
> 禁用流式处理可能需要几个小时。

1. 从所有相关表和数据库中删除[流式引入策略](/azure/kusto/management/streamingingestionpolicy)。 删除流式引入策略会触发将流式引入数据从初始存储移到列存储中的永久存储（盘区或分片）的行为。 数据移动可以持续几秒钟到几个小时，具体取决于初始存储中的数据量以及群集使用 CPU 和内存的方式。
1. 在 Azure 门户中，转到 Azure 数据资源管理器群集。 在“设置”中选择“配置”。******** 
1. 在“配置”窗格中，选择“关闭”以禁用“流式引入”。************
1. 选择“保存”。****

    ![关闭流式引入](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>限制

* 流式引入不支持[数据库游标](/azure/kusto/management/databasecursor)或[数据映射](/azure/kusto/management/mappings)。 仅支持[预先创建](/azure/kusto/management/create-ingestion-mapping-command)的数据映射。 
* 增大 VM 和群集大小可以提高流式引入的性能和容量。 并发引入仅限于每个核心六个引入。 例如，对于 16 核 SKU（如 D14 和 L16），支持的最大负载为 96 个并发引入。 对于两个核心 SKU（如 D11），最大支持的负载为 12 个并发引入。
* 每个引入请求的数据大小限制为 4 MB。
* 架构更新（如创建和修改表和引入映射）可能需要长达五分钟的时间进行流式处理服务。
* 即使数据不是流式引入的，在群集上启用流式引入也会占用群集计算机的一部分本地 SSD 磁盘用于存储流式引入数据，因此会减少热缓存的可用存储。
* 无法在流式处理数据上设置[扩展盘区标记](/azure/kusto/management/extents-overview#extent-tagging)。

## <a name="next-steps"></a>后续步骤

* [Azure 数据资源管理器中的查询数据](web-query-data.md)
