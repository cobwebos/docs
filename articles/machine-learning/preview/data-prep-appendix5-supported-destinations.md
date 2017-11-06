---
title: "适用于 Azure 机器学习数据准备的受支持数据目标和输出 | Microsoft Docs"
description: "本文档提供适用于 Azure 机器学习数据准备的受支持目标和输出的完整列表"
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
ms.openlocfilehash: bbbee61d6cd67dd437e4fbcd7260d2f378531912
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="supported-data-exports-for-this-preview"></a>此预览支持的数据导出 
可以导出为多种不同的格式。 在集成到其余的机器学习工作流之前，可以使用这些格式来保留数据准备的中间结果。

## <a name="types"></a>类型 
### <a name="csv-file"></a>CSV 文件 
将逗号分隔值文件写入到存储。

#### <a name="options"></a>选项
- 行尾
- 将 null 替换为
- 将 error 替换为 
- 分隔符


### <a name="parquet"></a>Parquet 
将数据集作为 Parquet 写入到存储。

Parquet 格式在存储中可采用各种形式。 对于较小的数据集，有时会使用单个 .parquet 文件。 各种 Python 库均支持读取和写入单个 .parquet 文件。 

目前，Azure Machine Learning Workbench 依靠 PyArrow Python 库在本地交互使用过程中写出 Parquet。 这意味着单个文件 Parquet 当前是在本地交互使用过程中唯一支持的 Parquet 输出格式。

在横向扩展运行期间（在 Spark 上），Azure Machine Learning Workbench 依靠 Spark 的 Parquet 读取和写入功能。 Spark 用于 Parquet 的默认输出格式（目前唯一支持的格式）与 Hive 数据集的结构相似。 这意味着文件夹包含的每个“.parquet”文件都是较大数据集的较小分区。 

#### <a name="caveats"></a>注意事项 
Parquet 格式相对较新，在不同库之间存在一些实施不一致的地方。 例如，在写出到 Parquet 时，Spark 对列名中可使用的有效字符有一些限制， 而 PyArrow 不进行这些限制。 列名中不能有以下字符： 
- ，
- ;
- {}
- ()
- \\n
- \\t
- =

>[!NOTE]
>- 为了确保与 Spark 的兼容性，只要将数据写入 Parquet，列名中出现的这些字符都将替换为下划线 ( _ )。
>- 为确保本地和横向扩展运行间的一致性，通过应用、Python 或 Spark 向 Parquet 写入的任何数据都清理其列名以确保 Spark 兼容性。 为确保写入到 Spark 中的 Parquet 字符时列名如预期，应在写出之前从列中删除无效集。



## <a name="locations"></a>位置 
### <a name="local"></a>Local 
本地硬盘或映射网络存储位置。

### <a name="azure-blob-storage"></a>Azure Blob 存储
Azure Blob 存储需要 Azure 订阅。

