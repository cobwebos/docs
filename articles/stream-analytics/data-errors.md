---
title: Azure Stream Analytics 诊断日志数据错误
description: 此文章介绍了不同的输入和输出数据错误时使用 Azure Stream Analytics 可能发生的。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: ecc7077bf208adf1ac89adcce2f2e480ce34888e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329601"
---
# <a name="azure-stream-analytics-data-errors"></a>Azure Stream Analytics 数据错误

数据错误是处理的数据时出现的错误。  这些错误通常发生数据反序列化，序列化期间和写入操作。  当数据发生错误，Stream Analytics 写入诊断日志的详细的信息和示例的事件。  在某些情况下，通过门户通知还提供了此信息的摘要。

本文概述了不同错误类型、 原因和诊断日志的输入和输出数据错误的详细信息。

## <a name="diagnostic-log-schema"></a>诊断日志架构

请参阅[通过使用诊断日志进行故障排除 Azure Stream Analytics](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema)若要查看诊断日志的架构。 以下 JSON 是示例值**属性**字段的数据错误的诊断日志。

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>输入的数据错误

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* 原因：选定的输入的压缩类型与数据不匹配。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：与任何反序列化错误包括无效的压缩类型的消息是从输入中删除。
* 日志详细信息
   * 输入消息标识符。 事件中心的标识符是 PartitionId、 偏移量和序列号。

**错误消息**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* 原因：输入数据的标头无效。 例如，CSV 还包含具有重复名称的列。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：与任何反序列化错误包括无效的标头的消息是从输入中删除。
* 日志详细信息
   * 输入消息标识符。 
   * 最多几个千字节为单位的实际负载。

**错误消息**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* 原因：使用 CREATE TABLE 或通过 TIMESTAMP BY 定义的输入的列不存在。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：从输入中删除具有缺少列的事件。
* 日志详细信息
   * 输入消息标识符。 
   * 缺少的列的名称。 
   * 最多几个千字节为单位的实际负载。

**错误消息**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* 原因：无法将输入转换为 CREATE TABLE 语句中指定的类型。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：输入中将删除与类型转换错误的事件。
* 日志详细信息
   * 输入消息标识符。 
   * 列和预期的类型的名称。

**错误消息**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* 原因：输入的数据不在正确的格式。 例如，输入不是有效的 JSON。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：从输入中删除后遇到无效的数据错误的消息中的所有事件。
* 日志详细信息
   * 输入消息标识符。 
   * 最多几个千字节为单位的实际负载。

**错误消息**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* 原因：TIMESTAMP BY 表达式的值无法转换为日期时间。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：从输入中删除具有无效输入的时间戳的事件。
* 日志详细信息
   * 输入消息标识符。 
   * 错误消息。 
   * 最多几个千字节为单位的实际负载。

**错误消息**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* 原因：TIMESTAMP BY OVER timestampColumn 的值为 NULL。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：从输入中删除具有无效输入的时间戳键事件。
* 日志详细信息
   * 多达几千实际有效负载。

**错误消息**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* 原因：应用程序时间与到达时间之间的差异大于延迟到达容错时段。
* 提供的门户通知：否
* 诊断日志级别：信息
* 影响：晚期输入的事件处理根据"处理其他事件"设置在事件排序作业配置的部分。 有关详细信息请参阅[时间处理策略](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)。
* 日志详细信息
   * 应用程序时间与到达时间。 
   * 最多几个千字节为单位的实际负载。

**错误消息**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* 原因：应用程序时间与到达时间之间的差异大于 5 分钟。
* 提供的门户通知：否
* 诊断日志级别：信息
* 影响：早期输入的事件处理根据"处理其他事件"设置在事件排序作业配置的部分。 有关详细信息请参阅[时间处理策略](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)。
* 日志详细信息
   * 应用程序时间与到达时间。 
   * 最多几个千字节为单位的实际负载。

**错误消息**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* 原因：事件被视为无序容错时段定义根据无序。
* 提供的门户通知：否
* 诊断日志级别：信息
* 影响：不按顺序处理根据"处理其他事件"事件设置在事件排序部分作业配置。 有关详细信息请参阅[时间处理策略](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)。
* 日志详细信息
   * 最多几个千字节为单位的实际负载。

**错误消息**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>输出数据错误

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* 原因：所需的输出列不存在。 例如，列定义为 Azure 表 PartitionKey 不存在。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：根据处理包括缺少所需的列的所有输出数据转换错误[输出数据策略](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)设置。
* 日志详细信息
   * 列和记录标识符或记录的一部分的名称。

**错误消息**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* 原因：列的值不符合的输出。 例如，列名称不是有效的 Azure 表的列。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：根据处理包括无效的列名称的所有输出数据转换错误[输出数据策略](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)设置。
* 日志详细信息
   * 列和记录标识符或记录的一部分的名称。

**错误消息**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* 原因：列不能转换为输出中的有效类型。 例如，列的值是与约束或 SQL 表中定义的类型不兼容。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：根据处理包括类型转换错误的所有输出数据转换错误[输出数据策略](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)设置。
* 日志详细信息
   * 列的名称。
   * 记录标识符或记录的一部分。

**错误消息**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* 原因：消息的值大于支持的输出大小。 例如，一条记录是大于 1 MB 的事件中心输出。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：根据处理包括记录已超过的大小限制的所有输出数据转换错误[输出数据策略](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)设置。
* 日志详细信息
   * 记录标识符或记录的一部分。

**错误消息**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* 原因：一条记录已包含具有相同的名称的系统列的列。 例如，某列的 CosmosDB 输出名为 ID 为不同的列 ID 列时。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：根据处理包括重复的键的所有输出数据转换错误[输出数据策略](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)设置。
* 日志详细信息
   * 列的名称。
   * 记录标识符或记录的一部分。

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>后续步骤

* [Azure Stream Analytics 通过使用诊断日志进行故障排除](stream-analytics-job-diagnostic-logs.md)

* [了解 Stream Analytics 作业监视以及如何监视查询](stream-analytics-monitoring.md)
