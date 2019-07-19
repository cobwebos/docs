---
title: 了解 Azure 流分析作业的兼容性级别
description: 了解如何设置 Azure 流分析作业的兼容性级别，并了解最新兼容性级别中的重大更改
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 5/2/2019
ms.openlocfilehash: 8e3ae84242a1a9b76fbb18a8d8164c97a62a97d9
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68003909"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure 流分析作业的兼容性级别

本文介绍 Azure 流分析中的兼容性级别选项。 流分析是一个托管服务，它会定期进行功能更新和性能改进。 该服务的大多数运行时更新会自动提供给最终用户。 

但是，该服务中的某些新功能可能会引入重大更改，例如，现有作业的行为更改，或正在运行的作业中使用数据的方式更改。 可以通过降低兼容性级别设置，使现有流分析作业保持运行状态而不会发生重大更改。 如果已准备使用最新的运行时行为，可以选择提高兼容性级别。 

## <a name="choose-a-compatibility-level"></a>选择兼容性级别

兼容性级别控制流分析作业的运行时行为。 

Azure 流分析目前支持三种兼容性级别：

* 1.0 - 以前的行为
* 1.1 - 默认行为
* 1.2（预览版）- 最新的行为，提供最新的改进措施供评估

最初的 1.0 兼容性级别是多年前在正式推出 Azure 流分析期间引入的。

创建新的流分析作业时，最佳做法是使用最新的兼容性级别来创建它。 依赖于最新的行为着手作业设计，以免将来添加更改和增大复杂性。

## <a name="set-the-compatibility-level"></a>设置兼容性级别

可以使用 Azure 门户或[创建作业 REST API 调用](/rest/api/streamanalytics/stream-analytics-job)来设置流分析作业的兼容性级别。

若要在 Azure 门户中更新作业的兼容性级别：

1. 使用 [Azure 门户](https://portal.azure.com)定位到你的流分析作业。
2. **停止**该作业，然后更新兼容性级别。 如果作业处于运行状态，则无法更新兼容性级别。
3. 在“配置”标题下，选择“兼容性级别”。  
4. 选择所需的兼容性级别值。
5. 选择页面底部的“保存”  。

![Azure 门户中的流分析兼容性级别](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

在更新兼容性级别时，T-SQL 编译器会使用与所选兼容性级别相对应的语法来验证作业。

## <a name="compatibility-level-12-preview"></a>兼容性级别 1.2 (预览版)

兼容级别1.2 中引入了以下主要更改:

### <a name="geospatial-functions"></a>地理空间函数

**之前的级别:** Azure 流分析使用地理计算。

**1.2 级别:** Azure 流分析允许计算几何投影的地理坐标。 地理空间函数的签名没有变化。 但是，其语义略有不同，与以往相比可以提高计算精度。

Azure 流分析支持地理空间参考数据索引编制。 可为包含地理空间元素的参考数据编制索引，以加快联接计算的速度。

更新的地理空间函数提供已知文本 (WKT) 地理空间格式的完整表达能力。 可以指定以前在 GeoJson 中所不支持的其他地理空间组件。

有关详细信息, 请参阅[Azure 流分析中的地理空间功能更新-云和 IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)。

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>针对使用多个分区的输入源的并行执行查询

**之前的级别:** Azure 流分析查询要求使用 PARTITION BY 子句在不同的输入源分区之间并行化查询处理。

**1.2 级别:** 如果查询逻辑可在不同的输入源分区之间并行化，则 Azure 流分析会创建独立的查询实例，并且并行运行计算。

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>与 CosmosDB 输出的本机批量 API 集成

**之前的级别:** 更新插入行为是“插入或合并”。 

**1.2 级别:** 与 CosmosDB 输出的本机批量 API 集成可以最大程度地提高吞吐量，并有效地处理限制请求。 有关详细信息, 请参阅[Azure 流分析输出到 Azure Cosmos DB 页](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)。

更新插入行为是“插入或替换”。 

### <a name="datetimeoffset-when-writing-to-sql-output"></a>写入到 SQL 输出时的 DateTimeOffset

**之前的级别:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) 类型已调整为 UTC。

**1.2 级别:** 不再调整 DateTimeOffset。

### <a name="long-when-writing-to-sql-output"></a>写入 SQL 输出时长

**之前的级别:** 根据目标类型截断值。

**1.2 级别:** 不符合目标类型的值将根据输出错误策略进行处理。

### <a name="strict-validation-of-prefix-of-functions"></a>严格验证函数的前缀

**之前的级别:** 不对函数前缀执行严格验证。

**1.2 级别:** Azure 流分析严格验证函数前缀。 将前缀添加到内置函数会导致出错。 例如，`myprefix.ABS(…)` 不受支持。

将前缀添加到内置聚合也会导致出错。 例如，`myprefix.SUM(…)` 不受支持。

对用户定义的任何函数使用前缀“system”会导致出错。

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>不允许在 Cosmos DB 输出适配器中使用数组和对象作为键属性

**之前的级别:** 支持使用数组和对象类型作为键属性。

**1.2 级别:** 不再支持使用数组和对象类型作为键属性。

## <a name="compatibility-level-11"></a>兼容性级别1。1

在兼容性级别 1.1 中引入了以下重大更改：

### <a name="service-bus-xml-format"></a>Service Bus XML 格式

**1.0 级别:** Azure 流分析使用 DataContractSerializer，因此消息内容包括 XML 标记。 例如：

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 级别:** 消息内容只包含流，没有其他的标记。 例如： `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>保持字段名称区分大小写

**1.0 级别:** 在由 Azure 流分析引擎处理时，字段名称更改为小写。

**1.1 级别:** 在由 Azure 流分析引擎处理时, 会为字段名称保留区分大小写。

> [!NOTE]
> 保留区分大小写尚不适用于使用 Edge 环境托管的流分析作业。 因此，如果在 Edge 上托管作业，所有字段名称都将转换为小写。

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1.0 级别:** CREATE TABLE 命令不使用 NaN（非数值。 例如，Infinity、-Infinity）在 FLOAT 列类型中筛选事件，因为对于这些数字来说，事件不在已记录的范围之内。

**1.1 级别:** CREATE TABLE 允许指定强架构。 流分析引擎验证数据是否符合此架构。 使用这一模型，该命令可以通过 NaN 值筛选事件。

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>对 JSON 中的 datetime 字符串禁用自动向上转换

**1.0 级别:** JSON 分析器会自动将包含日期/时间/时区信息的字符串值向上转换为日期/时间类型，然后转换为 UTC。 此行为导致丢失了时区信息。

**1.1 级别:** 不再将包含日期/时间/时区信息的字符串值自动向上转换为日期/时间类型。 因此会保留时区信息。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析输入的故障排除](stream-analytics-troubleshoot-input.md)
* [流分析资源运行状况](stream-analytics-resource-health.md)
