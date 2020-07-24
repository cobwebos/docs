---
title: Azure 数据工厂中的增量格式
description: 使用增量格式从增量 lake 转换和移动数据
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: daperlov
ms.openlocfilehash: e9df7b00a384859fb29577be0ad05da233683f46
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044533"
---
# <a name="delta-format-in-azure-data-factory"></a>Azure 数据工厂中的增量格式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文重点介绍如何使用增量格式将数据复制到[Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md)或[Azure Blob 存储](connector-azure-blob-storage.md)中存储的增量 lake。 此连接器作为[内联数据集](data-flow-source.md#inline-datasets)提供，同时将数据流映射为源和接收器。

> [!NOTE]
> 用于映射数据流的增量格式连接器目前以公共预览版的形式提供。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4ALTs]

## <a name="mapping-data-flow-properties"></a>映射数据流属性

此连接器作为[内联数据集](data-flow-source.md#inline-datasets)提供，同时将数据流映射为源和接收器。

### <a name="source-properties"></a>源属性

下表列出了增量源支持的属性。 可以在 "**源选项**" 选项卡中编辑这些属性。

| 名称 | 说明 | 必须 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必须为`delta` | 是 | `delta` | format |
| 文件系统 | Delta lake 的容器/文件系统 | 是 | 字符串 | fileSystem |
| 文件夹路径 | 增量 lake 的直接 | 是 | 字符串 | folderPath |
| 压缩类型 | 增量表的压缩类型 | 否 | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| 压缩级别 | 选择压缩是否尽快完成，或者是否以最佳方式压缩生成的文件。 | 如果 `compressedType` 指定了，则为必需。 | `Optimal` 或 `Fastest` | compressionLevel |
| 旅行时间 | 选择是否要查询增量表的旧快照 | 否 | 按时间戳查询：时间戳 <br> 按版本查询：整数 | timestampAsOf <br> versionAsOf |

#### <a name="import-schema"></a>导入架构

Delta 仅作为内联数据集提供，默认情况下，不具有关联的架构。 若要获取列元数据，请单击 "**投影**" 选项卡中的 "**导入架构**" 按钮。这将允许你引用语料库指定的列名称和数据类型。 若要导入该架构，[数据流调试会话](concepts-data-flow-debug-mode.md)必须处于活动状态，并且您必须具有现有的 CDM 实体定义文件以指向。
 

### <a name="delta-source-script-example"></a>增量源脚本示例

```
source(output(movieId as integer,
            title as string,
            releaseDate as date,
            rated as boolean,
            screenedOn as timestamp,
            ticketPrice as decimal(10,2)
            ),
    store: 'local',
    format: 'delta',
    versionAsOf: 0,
    allowSchemaDrift: false,
    folderPath: $tempPath + '/delta'
  ) ~> movies
```

### <a name="sink-properties"></a>接收器属性

下表列出了增量接收器支持的属性。 可以在 "**设置**" 选项卡中编辑这些属性。

| 名称 | 说明 | 必须 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必须为`delta` | 是 | `delta` | format |
| 文件系统 | Delta lake 的容器/文件系统 | 是 | 字符串 | fileSystem |
| 文件夹路径 | 增量 lake 的直接 | 是 | 字符串 | folderPath |
| 压缩类型 | 增量表的压缩类型 | 否 | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| 压缩级别 | 选择压缩是否尽快完成，或者是否以最佳方式压缩生成的文件。 | 如果 `compressedType` 指定了，则为必需。 | `Optimal` 或 `Fastest` | compressionLevel |
| 清空 | 以小时为单位指定旧版本表的保留阈值。 值0或小于30天 | 是 | Integer | 度 |
| Update 方法 | 指定增量 lake 允许哪些更新操作。 对于不插入的方法，需要执行前面的更改行转换才能标记行。 | 是 | `true` 或 `false` | 删除 <br> 可插入 <br> 更新 <br> upsertable |

### <a name="delta-sink-script-example"></a>增量接收器脚本示例

关联的数据流脚本为：

```
moviesAltered sink(
          input(movieId as integer,
                title as string
            ),
           mapColumn(
                movieId,
                title
            ),
           insertable: true,
           updateable: true,
           deletable: true,
           upsertable: false,
           keys: ['movieId'],
            store: 'local',
           format: 'delta',
           vacuum: 180,
           folderPath: $tempPath + '/delta'
           ) ~> movieDB
```

### <a name="known-limitations"></a>已知的限制

写入到增量接收器时，存在一个已知的限制，其中写入的行数不会在监视输出中返回。

## <a name="next-steps"></a>后续步骤

* 在映射数据流中创建[源转换](data-flow-source.md)。
* 在映射数据流中创建[接收器转换](data-flow-sink.md)。
* 创建[更改行转换](data-flow-alter-row.md)，以将行标记为 insert、update、upsert 或 delete。
