---
title: 映射数据流疑难解答
description: 了解如何排查 Azure 数据工厂中的数据流问题。
services: data-factory
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: e52432c01e649754116fcd0420fa52ae6c4e3733
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031851"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Azure 数据工厂中映射数据流的疑难解答

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文探讨了在 Azure 数据工厂中映射数据流的常见故障排除方法。

## <a name="common-errors-and-messages"></a>常见错误和消息

### <a name="error-code-df-executor-sourceinvalidpayload"></a>错误代码:DF-Executor-SourceInvalidPayload
- **消息**：数据预览、调试和管道数据流执行失败，因为容器不存在
- 原因：当数据集包含存储中不存在的容器时
- **建议**：请确保数据集中引用的容器存在或可访问。

### <a name="error-code-df-executor-systemimplicitcartesian"></a>错误代码:DF-Executor-SystemImplicitCartesian

- **消息**：用于 INNER 联接的隐式笛卡尔乘积不受支持。请改用 CROSS JOIN。 联接中使用的列应为行创建唯一键。
- 原因：用于逻辑计划之间 INNER 联接的隐式笛卡尔乘积不受支持。 如果联接中使用的列创建唯一键，则至少需要两个关系的一列。
- **建议**：对于基于不相等的联接，必须选择使用 CUSTOM CROSS JOIN。

### <a name="error-code-df-executor-systeminvalidjson"></a>错误代码:DF-Executor-SystemInvalidJson

- **消息**：JSON 分析错误、编码不受支持或多行
- 原因：JSON 文件可能存在的问题：编码不受支持、字节损坏或在多个嵌套行上使用 JSON 源作为单文档
- **建议**：请验证 JSON 文件的编码是否受支持。 在使用 JSON 数据集的源转换上，展开“JSON 设置”，然后启用“单文档”。
 
### <a name="error-code-df-executor-broadcasttimeout"></a>错误代码:DF-Executor-BroadcastTimeout

- **消息**：广播联接超时错误。请确保在调试运行中广播流在 60 秒内生成数据，在作业运行中广播流在 300 秒内生成数据
- **原因**：广播在调试运行中的默认超时为60秒，在作业运行期间为300秒。 为广播选择的流似乎太大，无法在此限制内生成数据。
- **建议**：请检查数据流转换的“优化”选项卡，以进行 Join、Exists 和 Lookup。 广播的默认选项为“自动”。 如果设置了 "自动"，或者要在 "固定" 下手动设置向左或向右广播，则可以设置较大的 Azure Integration Runtime 配置，或关闭广播。 为了获得最佳的数据流性能，推荐的方法是允许 Spark 使用“自动”进行广播，并使用内存优化的 Azure IR。

如果要从调试管道运行在调试测试执行中执行数据流，可能会更频繁地运行到此条件。 这是因为 ADF 将广播超时限制为60秒，以便保持更快的调试体验。 如果要将该时间从触发的运行延长到300秒的超时时间，则可以使用 Debug > Use 活动运行时选项来利用执行数据流管道活动中定义的 Azure IR。

### <a name="error-code-df-executor-conversion"></a>错误代码:DF-Executor-Conversion

- **消息**：无法转换为日期或时间，因为字符无效
- 原因：数据未采用预期格式
- **建议**：请使用正确的数据类型

### <a name="error-code-df-executor-invalidcolumn"></a>错误代码:DF-Executor-InvalidColumn

- **消息**：需要在查询中指定列名称。如果使用的是 SQL 函数，请设置别名
- 原因：未指定列名称
- **建议**：如果使用的是 min()/max() 等 SQL 函数，请设置别名。

 ### <a name="error-code-df-executor-drivererror"></a>错误代码： DF-执行器-DriverError
- **消息**： INT96 是 ADF 数据流不支持的旧时间戳类型。 请考虑将列类型升级到最新类型。
- **原因**：驱动程序错误
- **建议**： INT96 是旧时间戳类型，ADF 数据流不支持此类型。 请考虑将列类型升级到最新类型。

 ### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>错误代码： DF-执行器-BlockCountExceedsLimitError
- **消息**：未提交的块计数不能超过100000块的最大限制。 检查 blob 配置。
- **原因**： blob 中最多可以有100000个未提交的块。
- **建议**：有关此问题的详细信息，请与 Microsoft 产品团队联系

 ### <a name="error-code-df-executor-partitiondirectoryerror"></a>错误代码： DF-执行器-PartitionDirectoryError
- **消息**：指定的源路径包含多个分区目录 (，例如 <Source Path> /<分区根目录 1>/a = 10/b = 20， <Source Path> /<分区根目录 2>/c = 10/d = 30) 或包含其他文件或非分区目录的分区目录 (例如 <Source Path> /<分区根目录 1>/a = 10/b = 20， <Source Path> /目录 2/file1) ，从源路径中删除分区根目录并通过单独的源转换进行读取。
- **原因**：源路径包含多个分区目录或分区目录，其中包含其他文件或非分区目录。
- **建议**：从源路径中删除已分区的根目录并通过单独的源转换读取该目录。

 ### <a name="error-code-df-executor-outofmemoryerror"></a>错误代码： DF-执行器-OutOfMemoryError
- **消息**：群集在执行过程中遇到内存不足问题，请使用具有更大核心计数和/或内存优化计算类型的集成运行时重试
- **原因**：群集内存不足
- **建议**：调试群集用于开发目的。 利用数据采样、适当的计算类型和大小来运行有效负载。 若要实现最佳性能，请参阅 [映射数据流性能指南](concepts-data-flow-performance.md) 。

 ### <a name="error-code-df-executor-illegalargument"></a>错误代码： DF-执行器-illegalArgument
- **消息**：请确保链接的服务中的访问密钥正确
- **原因**：帐户名称或访问密钥不正确
- **建议**：确保在链接服务中指定的帐户名称或访问密钥是正确的。 

 ### <a name="error-code-df-executor-invalidtype"></a>错误代码： DF-执行器-InvalidType
- **消息**：请确保参数类型与传入的值的类型相匹配。 当前不支持从管道传递浮点参数。
- **原因**：声明类型与实际参数值之间不兼容的数据类型
- **建议**：检查传递到数据流的参数值是否与声明的类型匹配。

 ### <a name="error-code-df-executor-columnunavailable"></a>错误代码： DF-执行器-ColumnUnavailable
- **消息**：表达式中使用的列名称不可用或无效
- **原因**：表达式中使用的列名称无效或不可用
- **建议**：检查表达式中使用的列名称 () 

 ### <a name="error-code-df-executor-parseerror"></a>错误代码： DF-执行器-ParseError
- **消息**：无法分析表达式
- **原因**：表达式由于格式设置而分析错误
- **建议**：检查表达式中的格式设置

### <a name="error-code-getcommand-outputasync-failed"></a>错误代码:GetCommand OutputAsync 失败

- **消息**：在数据流调试和数据预览期间：GetCommand OutputAsync 失败，出现...
- 原因：这是后端服务错误。 可以重试此操作，并重启调试会话。
- **建议**：如果重试操作和重启会话无法解决问题，请联系客户支持。

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>错误代码:遇到了意外异常，执行失败

- **消息**：在数据流活动执行期间：遇到了意外异常，执行失败。
- 原因：这是后端服务错误。 可以重试此操作，并重启调试会话。
- **建议**：如果重试操作和重启会话无法解决问题，请联系客户支持。

### <a name="error-code-debug-data-preview-no-output-data-on-join"></a>错误代码：调试数据预览在联接时无输出数据

- **消息**：由于采样了太少的行，导致空值太多或缺少值。 请尝试更新调试行限制并刷新数据。
- **原因**：联接条件与任何行都不匹配，或在数据预览期间导致了大量的 null 值。
- **建议**：中转到 "调试" 设置，并增加源行限制中的行数。 请确保已选择并 Azure IR 具有足够大的数据流群集来处理更多数据。


## <a name="general-troubleshooting-guidance"></a>一般故障排除指南

1. 检查数据集连接状态。 在每个源转换和接收器转换中，访问要使用的每个数据集的链接服务，然后测试连接。
1. 在数据流设计器中，检查文件和表连接的状态。 打开“调试”并单击源转换上的“数据预览”，以确保你能够访问数据。
1. 如果数据预览中的一切正常，请转到管道设计器，并将数据流置于管道活动中。 调试管道，以进行端到端测试。

## <a name="next-steps"></a>后续步骤

若要获取故障排除方面的更多帮助，请尝试参阅以下资源：
*  [数据工厂博客](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Microsoft Q&A 问题页](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
*  [ADF 映射数据流性能指南](concepts-data-flow-performance.md)
