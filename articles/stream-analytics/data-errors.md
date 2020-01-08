---
title: Azure 流分析诊断日志数据错误
description: 本文介绍使用 Azure 流分析时可能出现的不同输入和输出数据错误。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 0546464b4d1bcc9eaa4fbffe265486985d9c58f3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465031"
---
# <a name="azure-stream-analytics-data-errors"></a>Azure 流分析数据错误

数据错误是在处理数据时发生的错误。  在数据反序列化、序列化和写入操作期间，最常发生这些错误。  发生数据错误时，流分析会将详细信息和示例事件写入诊断日志。  在某些情况下，还会通过门户通知来提供此信息的摘要。

本文概述了输入和输出数据错误的不同错误类型、原因和诊断日志详细信息。

## <a name="diagnostic-log-schema"></a>诊断日志架构

请参阅[使用诊断日志对 Azure 流分析进行故障排除](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema)，查看诊断日志的架构。 下面的 JSON 是数据错误的诊断日志的 "**属性**" 字段的示例值。

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

## <a name="input-data-errors"></a>输入数据错误

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* 原因：所选的输入压缩类型与数据不匹配。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：包含任何反序列化错误的消息（包括无效压缩类型）将从输入中删除。
* 日志详细信息
   * 输入消息标识符。 对于事件中心，标识符是 PartitionId、偏移量和序列号。

**错误消息**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* 原因：输入数据的标头无效。 例如，CSV 包含具有重复名称的列。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：包含任何反序列化错误的消息（包括无效的标头）将从输入中删除。
* 日志详细信息
   * 输入消息标识符。 
   * 实际有效负载，最大为 kb。

**错误消息**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* 原因：使用 CREATE TABLE 或通过 TIMESTAMP 定义的输入列不存在。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：丢失列的事件将从输入中删除。
* 日志详细信息
   * 输入消息标识符。 
   * 缺少的列的名称。 
   * 实际有效负载到几 kb。

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
* 影响：具有类型转换错误的事件从输入中删除。
* 日志详细信息
   * 输入消息标识符。 
   * 列的名称和所需的类型。

**错误消息**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* 原因：输入数据的格式不正确。 例如，输入不是有效的 JSON。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：遇到无效数据错误后，消息中的所有事件都将从输入中删除。
* 日志详细信息
   * 输入消息标识符。 
   * 实际有效负载，最大为 kb。

**错误消息**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* 原因： TIMESTAMP BY 表达式的值不能转换为 datetime。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：输入时间戳无效的事件将从输入中删除。
* 日志详细信息
   * 输入消息标识符。 
   * 错误消息。 
   * 实际有效负载，最大为 kb。

**错误消息**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* 原因： TIMESTAMP BY timestampColumn 的值为 NULL。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：输入中包含无效输入时间戳密钥的事件将被删除。
* 日志详细信息
   * 实际有效负载，最大为 kb。

**错误消息**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* 原因：应用程序时间与到达时间之间的差异大于延迟到达容错时段。
* 提供的门户通知：否
* 诊断日志级别：信息
* 影响：根据作业配置的 "事件排序" 部分中的 "处理其他事件" 设置来处理延迟输入事件。 有关详细信息，请参阅[时间处理策略](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)。
* 日志详细信息
   * 应用程序时间和到达时间。 
   * 实际有效负载，最大为 kb。

**错误消息**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* 原因：应用程序时间与到达时间之间的差异大于5分钟。
* 提供的门户通知：否
* 诊断日志级别：信息
* 影响：根据作业配置的 "事件排序" 部分中的 "处理其他事件" 设置来处理早期输入事件。 有关详细信息，请参阅[时间处理策略](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)。
* 日志详细信息
   * 应用程序时间和到达时间。 
   * 实际有效负载，最大为 kb。

**错误消息**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* 原因：根据定义的 "有序容错" 窗口，事件被视为顺序不对。
* 提供的门户通知：否
* 诊断日志级别：信息
* 影响：根据作业配置的 "事件排序" 部分中的 "处理其他事件" 设置，处理出序事件。 有关详细信息，请参阅[时间处理策略](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)。
* 日志详细信息
   * 实际有效负载，最大为 kb。

**错误消息**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>输出数据错误

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* 原因：输出所需的列不存在。 例如，定义为 Azure 表 PartitionKey 不的列存在。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：根据[输出数据策略](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)设置处理所有输出数据转换错误，包括缺少的必需列。
* 日志详细信息
   * 列的名称以及记录标识符或部分记录。

**错误消息**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* 原因：列值不符合输出。 例如，列名称不是有效的 Azure 表列。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：根据[输出数据策略](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)设置处理所有输出数据转换错误，包括无效的列名称。
* 日志详细信息
   * 列的名称，以及记录标识符或部分记录。

**错误消息**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* 原因：无法在输出中将列转换为有效类型。 例如，列的值与 SQL 表中定义的约束或类型不兼容。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：根据[输出数据策略](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)设置处理所有输出数据转换错误，包括类型转换错误。
* 日志详细信息
   * 列的名称。
   * 记录标识符或部分记录。

**错误消息**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* 原因：消息的值大于支持的输出大小。 例如，事件中心输出的记录大于 1 MB。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：根据[输出数据策略](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)设置，处理所有输出数据转换错误，包括超过记录大小限制。
* 日志详细信息
   * 记录标识符或部分记录。

**错误消息**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* 原因：记录已包含与系统列同名的列。 例如，ID 列到其他列时，使用名为 ID 的列的 CosmosDB 输出。
* 提供的门户通知：是
* 诊断日志级别：警告
* 影响：根据[输出数据策略](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)设置处理所有输出数据转换错误，包括重复键。
* 日志详细信息
   * 列的名称。
   * 记录标识符或部分记录。

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>后续步骤

* [使用诊断日志对 Azure 流分析进行故障排除](stream-analytics-job-diagnostic-logs.md)

* [了解流分析作业监视以及如何监视查询](stream-analytics-monitoring.md)
