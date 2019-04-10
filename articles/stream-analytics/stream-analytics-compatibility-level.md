---
title: 了解 Azure 流分析作业的兼容性级别
description: 了解如何设置 Azure 流分析作业的兼容性级别，并了解最新兼容性级别中的重大更改
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 6fb93152263d253de983b17d25f02f4c68a172fd
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361393"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure 流分析作业的兼容性级别
 
兼容性级别是指 Azure 流分析服务特定于版本的行为。 Azure 流分析是托管的服务，会定期进行功能更新和性能改进。 通常情况下，更新自动提供给最终用户。 但是，某些新功能可能会引入重大更改，例如，更改现有作业的行为、更改使用这些作业的数据的进程，等等。兼容性级别用于表示在流分析中引入的重大更改。 重大更改始终随新的兼容性级别一起引入。 

兼容性级别可确保现有作业运行时没有任何故障。 当创建新的 Stream Analytics 作业时，它是最好使用最新的兼容性级别创建。 
 
## <a name="set-a-compatibility-level"></a>设置兼容性级别 

兼容性级别控制流分析作业的运行时行为。 可以通过门户或通过[创建作业 REST API 调用](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)来设置流分析作业的兼容性级别。 Azure 流分析目前支持两种兼容性级别 -“1.0”和“1.1”。 默认情况下，兼容性级别设置为“1.0”，这是在 Azure 流分析公开发行期间引入的。 若要更新默认值，请导航到现有的流分析作业 > 在“配置”部分选择“兼容性级别”选项，然后更改该值。 

请确保在更新兼容性级别之前停止作业。 如果作业处于运行状态，则无法更新兼容性级别。 

![Azure 门户中的流分析兼容性级别](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

 
在更新兼容性级别时，T-SQL 编译器会使用与所选兼容性级别相对应的语法来验证作业。 

## <a name="major-changes-in-the-latest-compatibility-level-12"></a>最新兼容级别 (1.2) 中的重大更改

在兼容级别 1.2 中引入了以下重大更改：

### <a name="geospatial-functions"></a>地理空间函数 

**以前的版本：** 使用 azure Stream Analytics 的地理计算。

**当前版本：** Azure Stream Analytics，可计算几何投影的地理坐标。 没有任何更改的地理空间函数签名中。 但是，它们的语义是稍有不同，从而允许比之前的更精确计算。

Azure Stream Analytics 支持地理空间引用数据编制索引。 包含地理空间元素的引用数据创建索引的速度更快的联接计算。

已更新的地理空间函数自带熟知文本 (WKT) 地理空间信息格式的完整的表现力。 您可以使用 GeoJson 指定以前不支持其他地理空间信息组件。

有关详细信息，请参阅[更新到 Azure Stream Analytics – 云和 IoT Edge 中的地理空间功能](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)。

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>具有多个分区的输入源的并行查询执行 

**以前的版本：** Azure Stream Analytics 查询需要使用 PARTITION BY 子句在输入的源分区并行查询处理的数据。

**当前版本：** 如果在输入的源分区，可以并行化查询逻辑，Azure Stream Analytics 创建单独的查询实例，并在并行运行计算。

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>CosmosDB 输出与本机大容量 API 集成

**以前的版本：** Upsert 行为*插入或合并*。

**当前版本：** CosmosDB 输出与本机大容量 API 集成最大化吞吐量，并有效地处理限制请求。

Upsert 行为*插入或替换*。

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset 时写入到 SQL 输出

**以前的版本：**[DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017)类型已调整为 UTC。

**当前版本：** DateTimeOffset 无法再进行调整。

### <a name="strict-validation-of-prefix-of-functions"></a>严格的验证函数的前缀

**以前的版本：** 没有函数前缀严格验证。

**当前版本：** Azure Stream Analytics 具有严格的验证函数的前缀。 将前缀添加到内置函数将导致错误。 例如，`myprefix.ABS(…)`不受支持。

将前缀添加到内置聚合还会导致错误。 例如，`myprefix.SUM(…)`不受支持。

使用前缀"system"的任何用户定义的函数将导致错误。

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>禁止为 Cosmos DB 输出适配器中的键属性的数组和对象

**以前的版本：** 数组和对象类型支持为键属性。

**当前版本：** 为键属性不再支持数组和对象类型。


## <a name="next-steps"></a>后续步骤
* [排查 Azure Stream Analytics 输入](stream-analytics-troubleshoot-input.md)
* [Stream Analytics 资源运行状况](stream-analytics-resource-health.md)
