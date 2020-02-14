---
title: Azure 流分析兼容性级别
description: 了解如何设置 Azure 流分析作业的兼容性级别，并了解最新兼容性级别中的重大更改
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 14b04f7d6068863c79c7060d29b58232be1f40cb
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201792"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure 流分析作业的兼容性级别

本文介绍 Azure 流分析中的兼容级别选项。 流分析是一项托管服务，其中包含常规功能更新和性能改进。 大多数服务的运行时更新将自动提供给最终用户。 

但是，此服务中的某些新功能可能会导致重大更改，例如，更改现有作业的行为，或在运行的作业中使用数据的方式发生变化。 你可以通过降低兼容级别设置来保持现有的流分析作业的运行，而不会发生重大更改。 如果已准备好使用最新的运行时行为，则可以通过提高兼容级别来选择加入。 

## <a name="choose-a-compatibility-level"></a>选择兼容级别

兼容性级别控制流分析作业的运行时行为。 

Azure 流分析目前支持三种兼容性级别：

* 1.0-以前的行为
* 1.1-默认行为
* 1.2-最新的行为和最新的改进

在过去几年前，Azure 流分析的公开上市期间引入了原始1.0 兼容性级别。

创建新的流分析作业时，最佳做法是使用最新的兼容性级别来创建它。 根据最新行为开始工作设计，以避免以后增加更改和复杂性。

## <a name="set-the-compatibility-level"></a>设置兼容级别

可以在 Azure 门户中或使用[create job REST API 调用](/rest/api/streamanalytics/stream-analytics-job)设置流分析作业的兼容性级别。

在 Azure 门户中更新作业的兼容级别：

1. 使用[Azure 门户](https://portal.azure.com)查找流分析作业。
2. 请在更新兼容级别之前**停止**作业。 如果作业处于运行状态，则无法更新兼容性级别。
3. 在 "**配置**" 标题下，选择 "**兼容级别**"。
4. 选择所需的兼容级别值。
5. 选择页面底部的 "**保存**"。

![Azure 门户中的流分析兼容性级别](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

当你更新兼容级别时，T-sql 编译器将用与所选兼容级别对应的语法来验证作业。

## <a name="compatibility-level-12"></a>兼容性级别1。2

兼容级别1.2 中引入了以下主要更改：

###  <a name="amqp-messaging-protocol"></a>AMQP 消息传送协议

**1.2 级别**： Azure 流分析使用[高级消息队列协议（AMQP）](../service-bus-messaging/service-bus-amqp-overview.md)消息传递协议来写入 Service Bus 队列和主题。 AMQP 让您能够使用开放标准协议构建跨平台的混合应用程序。

### <a name="geospatial-functions"></a>地理空间函数

**之前的级别：** Azure 流分析使用地理计算。

**1.2 级别：** Azure 流分析可用于计算几何投影地理坐标。 地理空间函数的签名没有变化。 但是，它们的语义略有不同，允许比以前更精确的计算。

Azure 流分析支持地理空间引用数据索引。 对于更快的联接计算，可以为包含地理空间元素的引用数据编制索引。

更新的地理空间函数会引入完全表现力的知名文本（WKT）地理空间格式。 可以指定以前不受 GeoJson 支持的其他地理空间组件。

有关详细信息，请参阅[Azure 流分析中的地理空间功能更新-云和 IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)。

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>具有多个分区的输入源的并行查询执行

**之前的级别：** Azure 流分析查询要求使用 PARTITION BY 子句跨输入源分区并行执行查询处理。

**1.2 级别：** 如果可跨输入源分区并行执行查询逻辑，Azure 流分析会创建单独的查询实例并并行运行计算。

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>与 CosmosDB 输出的本机大容量 API 集成

**之前的级别：** Upsert 行为是*insert 或 merge*。

**1.2 级别：** 本机批量 API 与 CosmosDB 输出的集成最大化了吞吐量并有效处理限制请求。 有关详细信息，请参阅[Azure 流分析输出到 Azure Cosmos DB 页](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)。

Upsert 行为是*insert 或 replace*。

### <a name="datetimeoffset-when-writing-to-sql-output"></a>写入 SQL 输出时的 DateTimeOffset

**之前的级别：** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017)类型已调整为 UTC。

**1.2 级别：** 不会再调整 DateTimeOffset。

### <a name="long-when-writing-to-sql-output"></a>写入 SQL 输出时长

**之前的级别：** 根据目标类型截断值。

**1.2 级别：** 不符合目标类型的值将根据输出错误策略进行处理。

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>写入 SQL 输出时记录和数组的序列化

**之前的级别：** 记录是以 "记录" 形式编写的，数组是以 "数组" 形式编写的。

**1.2 级别：** 记录和数组按 JSON 格式进行序列化。

### <a name="strict-validation-of-prefix-of-functions"></a>严格验证函数的前缀

**之前的级别：** 不会严格验证函数前缀。

**1.2 级别：** Azure 流分析严格验证函数前缀。 将前缀添加到内置函数会导致错误。 例如，不支持`myprefix.ABS(…)`。

将前缀添加到内置聚合还会导致错误。 例如，不支持 `myprefix.SUM(…)`。

对任何用户定义函数使用前缀 "system" 会导致错误。

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>禁用数组和对象作为 Cosmos DB 输出适配器中的键属性

**之前的级别：** 支持将数组和对象类型作为键属性。

**1.2 级别：** 不再支持数组和对象类型作为键属性。

## <a name="compatibility-level-11"></a>兼容性级别1。1

在兼容性级别 1.1 中引入了以下重大更改：

### <a name="service-bus-xml-format"></a>Service Bus XML 格式

**1.0 级别：** Azure 流分析使用 DataContractSerializer，因此消息内容包括 XML 标记。 例如：

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 级别：** 消息内容直接包含流，无任何其他标记。 例如： `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>保持字段名称区分大小写

**1.0 级别：** 当由 Azure 流分析引擎处理时，字段名称已更改为小写。

**1.1 级别：** 在由 Azure 流分析引擎处理时，会为字段名称保留区分大小写。

> [!NOTE]
> 保留区分大小写尚不适用于使用 Edge 环境托管的流分析作业。 因此，如果在 Edge 上托管作业，所有字段名称都将转换为小写。

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0 级别：** CREATE TABLE 命令未使用 NaN 筛选事件（非数字）。 例如，Infinity、-Infinity）在 FLOAT 列类型中筛选事件，因为对于这些数字来说，事件不在已记录的范围之内。

**1.1 级别：** CREATE TABLE 允许指定强架构。 流分析引擎验证数据是否符合此架构。 使用这一模型，该命令可以通过 NaN 值筛选事件。

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>对 JSON 中的 datetime 字符串禁用自动向上转换

**1.0 级别：** JSON 分析器会自动将包含日期/时间/时区信息的字符串值转换为 DateTime 类型，然后将其转换为 UTC。 此行为导致丢失了时区信息。

**1.1 级别：** 不会将包含日期/时间/时区信息的字符串值自动向上转换为日期/时间类型。 因此会保留时区信息。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析输入的故障排除](stream-analytics-troubleshoot-input.md)
* [流分析资源运行状况](stream-analytics-resource-health.md)
