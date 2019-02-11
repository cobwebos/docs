---
title: Azure 流分析 Blob 输出的 DateTime 路径模式（预览）
description: 本文介绍 Azure Stream Analytics 作业的 blob 存储输出的自定义 DateTime 路径模式功能。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: ba386539c3f3c6740b843575bbccd4b028b8a5a7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090770"
---
# <a name="custom-datetime-path-patterns-for-azure-stream-analytics-blob-storage-output-preview"></a>Azure 流分析 Blob 存储输出（预览版）的自定义 DateTime 路径模式

Azure 流分析支持在 Blob 存储输出的文件路径中使用自定义日期和时间格式说明符。 可以使用自定义 DateTime 路径模式来指定与 Hive 流式处理约定相符的输出格式，这样 Azure 流分析就可以将数据发送到 Azure HDInsight 和 Azure Databricks 进行下游处理。 自定义 DateTime 路径模式可以轻松地实现，只需在 Blob 输出的“路径前缀”字段中使用 `datetime` 关键字并使用格式说明符即可。 例如，`{datetime:yyyy}`。

使用 [Azure 门户](https://portal.azure.com/?Microsoft_Azure_StreamAnalytics_bloboutputcustomdatetimeformats=true)的此链接来切换功能标志，该标志可以为 Blob 存储输出预览版启用自定义 DateTime 路径模式。 此功能很快会在主门户中启用。

## <a name="supported-tokens"></a>支持的令牌

以下格式说明符令牌可以单独使用，也可以组合使用，以便实现自定义 DateTime 格式：

|格式说明符   |Description   |示例时间 2018-01-02T10:06:08 的结果|
|----------|-----------|------------|
|{datetime:yyyy}|年份为四位数|2018|
|{datetime:MM}|月份为 01 到 12|01|
|{datetime:M}|月份为 1 到 12|1|
|{datetime:dd}|日期为 01 到 31|02|
|{datetime:d}|日期为 1 到 12|2|
|{datetime:HH}|小时为 00 到 23，采用 24 小时格式|10|
|{datetime:mm}|分钟为 00 到 24|06|
|{datetime:m}|分钟为 0 到 24|6|
|{datetime:ss}|秒为 00 到 60|08|

如果不希望使用自定义 DateTime 模式，可以将 {date} 和/或 {time} 令牌添加到路径前缀，以便使用内置的 DateTime 格式生成一个下拉列表。

![流分析的旧 DateTime 格式](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

## <a name="extensibility-and-restrictions"></a>扩展性和限制

可以在路径模式中使用尽量多的 `{datetime:<specifier>}` 令牌，直到达到路径前缀字符限制。 在单个令牌中，格式说明符的组合不能超出日期和时间下拉列表已经列出的组合。 

对于 `logs/MM/dd` 路径分区：

|有效表达式   |无效表达式   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

可以在路径前缀中多次使用同一格式说明符。 令牌每次都必须重复。

## <a name="hive-streaming-conventions"></a>Hive 流式处理约定

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
