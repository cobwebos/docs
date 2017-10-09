---
title: "适用于 Azure 机器学习数据准备的受支持数据目标/输出 | Microsoft Docs"
description: "本文档提供了适用于 Azure ML 数据准备的受支持目标/输出的完整列表"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 415ceba02eb3c8da3de5ab3aa6980fbe5bae2db9
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="supported-data-exports-for-this-preview"></a>此预览支持的数据导出 
可以导出为多种不同的格式。 在集成到其余的机器学习工作流之前，可以使用这些格式来保留数据准备的中间结果。

## <a name="types"></a>类型 
### <a name="csv-file"></a>CSV 文件 
将逗号分隔值文件写入到存储

#### <a name="options"></a>选项
- 行尾
- 将 Nulls 替换为
- 将 Errors 替换为 
- 分隔符


### <a name="parquet"></a>Parquet ###
将数据集作为 Parquet 写入到存储。

Parquet 格式可以在存储中采用各种形式。 对于较小的数据集，有时会使用单个“.parquet”文件，各种 python 库支持读取/写入到单个“.parquet”文件。 目前，AMLWB 依靠 PyArrow python 库在本地“交互式”使用过程中写出 Parquet。 这意味着单个文件 parquet 当前是在本地交互式使用过程中唯一支持的 Parquet 输出格式。

在横向扩展运行期间（在 Spark 上），AMLWB 依靠 Spark 的 Parquet 读取/写入功能。 Spark 用于 Parquet 的默认输出格式（目前唯一支持的格式）与 HIVE 数据集的结构相似。 这意味着文件夹包含的每个“.parquet”文件都是较大数据集的较小分区。 

#### <a name="caveats"></a>注意事项 ####
Parquet 格式相对较新，在不同库之间存在一些实施不一致的地方。 例如，在写出到 Parquet 时， Spark 对“列名”中可使用的有效字符有一些限制，而 PyArrow 却没有。 集内的任何字符“ ,;{}()\\n\\t=”不能在“列名”中使用。

>[!NOTE]
>为了确保与 Spark 的兼容性，只要将数据写入 Parquet，列名中出现的这些字符都将替换为“_”（下划线）。**

>[!NOTE]
>为确保通过应用、Python 或 Spark 向 Parquet 写入的任何本地和横向扩展数据的一致性，请清理其列名以确保 Spark 兼容性。 为确保向 Parquet 写入数据时获得预期的列名，应在写出之前从列中删除 Spark 无效集中的字符。



## <a name="locations"></a>位置 
### <a name="local"></a>Local 
本地硬盘或映射的网络存储位置

### <a name="azure-blob"></a>Azure BLOB 
Azure 存储 (BLOB)，需要 Azure 订阅


