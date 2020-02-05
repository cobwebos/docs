---
title: 使用 Azure 流分析中的 .NET 自定义反以任意格式读取输入
description: 本文介绍了为 Azure 流分析云和边缘作业定义自定义 .NET 反的序列化格式和接口。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 270e9a31c28e7209cfe43ea8307b928ed3257a35
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845264"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>使用 .NET 自定义反以任意格式读取输入

.NET 自定义反允许 Azure 流分析作业从三种[内置数据格式](stream-analytics-parsing-json.md)之外的格式读取数据。 本文介绍了为 Azure 流分析云和边缘作业定义 .NET 自定义反的序列化格式和接口。 还提供了协议缓冲区和 CSV 格式的示例反。

## <a name="net-custom-deserializer"></a>.NET 自定义反序列化程序

下面的代码示例是定义自定义反序列化程序并实现 `StreamDeserializer<T>`的接口。

`UserDefinedOperator` 是所有自定义流式处理运算符的基类。 它会初始化 `StreamingContext`，它提供了上下文，其中包括用于发布诊断的机制，你将需要对其调试反序列化程序的任何问题。

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

以下代码片段是流式处理数据的反序列化。 

应使用通过 `UserDefinedOperator`的 Initialize 方法传递 `IStreamingDiagnostics` 来发出 Skippable 错误。 所有异常将被视为错误，将重新创建反序列化程序。 出现特定数量的错误后，该作业将会失败。

`StreamDeserializer<T>` 将流反序列化为 `T`类型的对象。 必须满足以下条件：

1. T 是一个类或结构。
1. T 中的所有公共字段都是
    1. [Sbyte、byte、short、ushort、int、uint、long、DateTime、string、float、double] 或它们可以为 null 的等效项之一。
    1. 遵循相同规则的另一个结构或类。
    1. 遵循相同规则的类型 `T2` 的数组。
    1. IList`T2` 其中 T2 遵循相同的规则。
    1. 没有任何递归类型。

参数 `stream` 是包含序列化对象的流。 `Deserialize` 返回 `T` 实例的集合。

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` 提供了上下文，其中包括用于发布用户操作员诊断的机制。

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` 是用户定义的运算符的诊断，包括序列化程序、反序列化程序和用户定义的函数。

`WriteError` 将错误消息写入诊断日志，并将错误发送到诊断。

`briefMessage` 是一条简短的错误消息。 此消息将显示在 "诊断" 中，供产品团队用于调试目的。 不要包括敏感信息，并将消息保留为小于200个字符

`detailedMessage` 是一条详细的错误消息，仅添加到存储中的诊断日志。 此消息应小于2000个字符。

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>反序列化程序示例

本部分介绍如何为 Protobuf 和 CSV 编写自定义反。 有关其他示例，如事件中心捕获的 AVRO 格式，请访问[GitHub 上的 Azure 流分析](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)。

### <a name="protocol-buffer-protobuf-format"></a>协议缓冲区（Protobuf）格式

这是一个使用协议缓冲区格式的示例。

采用以下协议缓冲区定义。

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

从**Protobuf**中运行 `protoc.exe` 时，将生成具有定义的 .cs 文件。 此处未显示生成的文件。

下面的代码片段是反序列化实现，假设生成的文件包含在项目中。 此实现只是生成的文件的精简包装器。

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

以下代码片段是一个简单的 CSV 反序列化程序，它还演示传播错误。

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>REST Api 的序列化格式

每个流分析输入都有一种**序列化格式**。 有关输入选项的详细信息，请参阅[input REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)文档。

以下 Javascript 代码是使用 REST API 时的 .NET 反序列化程序序列化格式的示例：

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName` 应是实现 `StreamDeserializer<T>`的类。 下一节将对此进行介绍。

## <a name="region-support"></a>区域支持

此功能在以下区域中提供：

* 美国中西部
* 北欧
* 美国东部
* 美国西部
* 美国东部 2
* 西欧

你可以为其他区域[请求支持](https://aka.ms/ccodereqregion)。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>此功能何时在所有 Azure 区域中可用？

此功能在6个[区域](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support)中提供。 如果你有兴趣在另一个区域中使用此功能，则可以[提交请求](https://aka.ms/ccodereqregion)。 所有 Azure 区域都支持所有 Azure 区域。

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>我是否可以从类似于 GetMetadataPropertyValue 函数的输入访问 MetadataPropertyValue？

现在不支持此功能。 如果需要此功能，可以在[UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese)上为此请求投票。

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>我是否可以与社区共享反序列化程序实现，以便其他人能够受益？

实现反序列化程序后，可以通过将它与社区共享来帮助其他人。 将代码提交到[Azure 流分析 GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)存储库。

## <a name="next-steps"></a>后续步骤

* [用于 Azure 流分析云作业的 .NET 自定义反](custom-deserializer.md)
