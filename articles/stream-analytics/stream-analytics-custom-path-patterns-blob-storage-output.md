---
title: Azure 流分析自定义 blob 输出分区
description: 本文介绍了 Azure 流分析作业中 blob 存储输出的自定义 DateTime 路径模式和自定义字段或属性功能。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9cdf99884845a9cb83ac26723c3ea0e7a779ebff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771746"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Azure 流分析自定义 blob 输出分区

Azure 流分析支持包含自定义字段或属性和自定义 DateTime 路径模式的自定义 blob 输出分区。 

## <a name="custom-field-or-attributes"></a>自定义字段或属性

自定义字段或输入属性通过允许进一步控制输出，改进下游数据处理和报告工作流。

### <a name="partition-key-options"></a>分区键选项

用于分区输入数据的分区键或列名称可能包含带有连字符、下划线和空格的字母数字字符。 除非与别名一起使用，否则无法将嵌套字段用作分区键。

### <a name="example"></a>示例

假设作业从连接到外部视频游戏服务的实时用户会话获取输入数据，其中引入的数据包含用于识别会话的列 client_id。 若要按 client_id 对数据进行分区，请在创建作业时将“blob 路径模式”字段设置为，在 blob 输出属性中添加分区标记 {client_id}。 当包含各种 client_id 值的数据流经流分析作业时，输出数据根据每个文件夹的单一 client_id 值保存到单独的文件夹中。

![包含客户端 ID 的路径模式](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

同样，如果作业输入是来自数百万个传感器的传感器数据（其中每个传感器有一个 sensor_id），那么路径模式为 {sensor_id}，用于将每个传感器数据分区到不同的文件夹中。  


使用 REST API，用于相应请求的 JSON 文件的输出部分可能如下所示：  

![REST API 输出](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

在作业开始运行后，“客户端”容器可能如下所示：  

![“客户端”容器](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

每个文件夹都可能包含多个 blob，其中每个 blob 包含一个或多个记录。 在上面的示例中，标记为“06000000”的文件夹中有一个 blob，其中包含以下内容：

![blob 内容](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

请注意，blob 中的每个记录都有一个与文件夹名称匹配的 client_id 列，这是因为用于在输出路径中对输出进行分区的列是 client_id。

### <a name="limitations"></a>限制

1. “路径模式”blob 输出属性中只允许有一个自定义分区键。 以下所有路径模式都有效：

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData}  

2. 由于分区键不区分大小写，因此像“John”和“john”这样的分区键是等效的。 另外，无法使用表达式作为分区键。 例如，{columnA + columnB} 不起作用。  

3. 如果输入流由分区键基数低于 8000 的记录组成，记录会附加到现有 blob，并且仅在必要时新建 blob。 如果基数超过 8000，无法保证将写入现有 blob，并且不会为具有相同分区键的任意数量记录新建 blob。  

## <a name="custom-datetime-path-patterns"></a>自定义 DateTime 路径模式

可以使用自定义 DateTime 路径模式来指定与 Hive 流式处理约定相符的输出格式，这样 Azure 流分析就可以将数据发送到 Azure HDInsight 和 Azure Databricks 进行下游处理。 自定义 DateTime 路径模式可以轻松地实现，只需在 Blob 输出的“路径前缀”字段中使用 `datetime` 关键字并使用格式说明符即可。 例如，`{datetime:yyyy}`。

### <a name="supported-tokens"></a>支持的令牌

以下格式说明符令牌可以单独使用，也可以组合使用，以便实现自定义 DateTime 格式：

|格式说明符   |描述   |示例时间 2018-01-02T10:06:08 的结果|
|----------|-----------|------------|
|{datetime:yyyy}|年份为四位数|2018|
|{datetime:MM}|月份为 01 到 12|01|
|{datetime:M}|月份为 1 到 12|第|
|{datetime:dd}|日期为 01 到 31|02|
|{datetime:d}|日期为 1 到 12|2|
|{datetime:HH}|小时为 00 到 23，采用 24 小时格式|10|
|{datetime:mm}|分钟为 00 到 24|06|
|{datetime:m}|分钟为 0 到 24|6|
|{datetime:ss}|秒为 00 到 60|08|

如果不希望使用自定义 DateTime 模式，可以将 {date} 和/或 {time} 令牌添加到路径前缀，以便使用内置的 DateTime 格式生成一个下拉列表。

![流分析的旧 DateTime 格式](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>扩展性和限制

可以在路径模式中使用尽量多的 `{datetime:<specifier>}` 令牌，直到达到路径前缀字符限制。 在单个令牌中，格式说明符的组合不能超出日期和时间下拉列表已经列出的组合。 

对于 `logs/MM/dd` 路径分区：

|有效表达式   |无效表达式   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

可以在路径前缀中多次使用同一格式说明符。 令牌每次都必须重复。

### <a name="hive-streaming-conventions"></a>Hive 流式处理约定

Blob 存储的自定义路径模式可以与 Hive 流式处理约定配合使用，后者要求文件夹在其名称中使用 `column=` 进行标记。

例如，`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`。

有了自定义输出，就不需更改表，也不需将分区添加到 Azure 流分析和 Hive 之间的端口数据。 许多文件夹可以使用以下方式自动添加：

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>示例

根据 [Azure 流分析 Azure 门户](stream-analytics-quick-create-portal.md)快速入门指南中的说明，创建存储帐户、资源组、流分析作业和输入源。 使用在快速入门指南中使用的同一示例数据，该数据也可以在 [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) 上获取。

使用以下配置创建 Blob 输出接收器：

![流分析创建 Blob 输出接收器](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

完整路径模式如下所示：


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


启动作业时，会在 Blob 容器中创建基于路径模式的文件夹结构。 可以向下钻取到日级别。

![使用自定义路径模式的流分析 Blob 输出](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>后续步骤

* [了解 Azure 流分析的输出](stream-analytics-define-outputs.md)
