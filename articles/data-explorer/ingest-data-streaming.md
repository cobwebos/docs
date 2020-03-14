---
title: 使用流式引入将数据引入 Azure 数据资源管理器
description: 了解如何使用流引入将数据引入（加载）到 Azure 数据资源管理器中。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: d7d2bcf487c37fbb523b648d5aa4c572add5dfa9
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297078"
---
# <a name="streaming-ingestion-preview"></a>流式引入（预览）

如果需要低延迟，并且不同的卷数据的引入时间小于10秒，请使用流式引入。 它用于在一个或多个数据库中优化多个表的操作处理，其中，每个表中的数据流相对较小（每秒少条记录），但总体数据引入量为高（每秒数千条记录）。 

当数据量增长到每个表的每秒 1 MB 以上时，使用大容量引入而不是流式引入。 阅读[数据引入概述](/azure/data-explorer/ingest-data-overview)，了解有关引入的各种方法的详细信息。

## <a name="prerequisites"></a>必备条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* 登录到[WEB UI](https://dataexplorer.azure.com/)。
* 创建[Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>在群集上启用流式引入

> [!WARNING]
> 请在启用传送视频流引入之前查看这些[限制](#limitations)。

1. 在 Azure 门户中，转到 Azure 数据资源管理器群集。 在 "**设置**" 中选择 "**配置**"。 
1. 在 "**配置**" 窗格中，选择 **"打开"** 以启用**流式引入**。
1. 选择“保存”。
 
    ![流式引入](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. 在[WEB UI](https://dataexplorer.azure.com/)中，定义将接收流式处理数据的一个或多个表的[流式处理引入策略](/azure/kusto/management/streamingingestionpolicy)。 

    > [!NOTE]
    > * 如果策略是在数据库级别定义的，则将为流式引入启用数据库中的所有表。
    > * 应用的策略只能引用新引入的数据，而不能引用数据库中的其他表。

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>使用流式引入将数据引入群集

支持两种流式处理引入类型：


* 用作数据源的[**事件中心**](/azure/data-explorer/ingest-data-event-hub)
* **自定义引入**要求你编写使用 Azure 数据资源管理器客户端库之一的应用程序。 有关示例应用程序，请参阅[流式处理引入示例](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample)。

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>选择适当的流式摄取类型

|   |事件中心  |自定义引入  |
|---------|---------|---------|
|引入启动与可用于查询的数据之间的数据延迟   |    延迟时间较长     |   缩短延迟      |
|开发开销    |   快速轻松地进行设置，无需开发开销    |   应用程序处理错误和确保数据一致性的高开发开销     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>禁用群集上的流引入

> [!WARNING]
> 禁用流引入可能需要几个小时。

1. 从所有相关的表和数据库中删除[流式处理引入策略](/azure/kusto/management/streamingingestionpolicy)。 流式处理引入策略删除触发从初始存储到列存储中的永久存储（区或分片）的流引入数据移动。 数据移动可能会持续几秒钟到几个小时，具体取决于初始存储中的数据量，以及群集如何使用 CPU 和内存。
1. 在 Azure 门户中，转到 Azure 数据资源管理器群集。 在 "**设置**" 中选择 "**配置**"。 
1. 在 "**配置**" 窗格中，选择 "**关闭**" 以禁用**流式引入**。
1. 选择“保存”。

    ![流引入关闭](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>限制

* 流式引入不支持[数据库游标](/azure/kusto/management/databasecursor)或[数据映射](/azure/kusto/management/mappings)。 仅支持[预先创建的](/azure/kusto/management/create-ingestion-mapping-command)数据映射。 
* VM 和群集大小增加时，流式处理引入性能和容量可扩展性。 并发 ingestions 限制为每个内核六个 ingestions。 例如，对于16核 Sku，如 D14 和 L16，支持的最大负载为96并发 ingestions。 对于两个核心 Sku，如 D11，最大支持负载为12个并发 ingestions。
* 每个摄取请求的数据大小限制为 4 MB。
* 对于流式引入服务，架构更新（如创建和修改表和引入映射）可能需要长达五分钟。
* 即使数据不引入通过流式处理，也要在群集上启用流式引入，使用群集计算机的部分本地 SSD 磁盘来流式处理引入数据，并减少可用于热缓存的存储空间。
* 无法在流式处理引入数据上设置[区标记](/azure/kusto/management/extents-overview#extent-tagging)。

## <a name="next-steps"></a>后续步骤

* [在 Azure 数据资源管理器中查询数据](web-query-data.md)
