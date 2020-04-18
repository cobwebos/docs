---
title: 映射数据流的代理密钥转换
description: 如何使用 Azure 数据工厂的映射数据流代理密钥转换生成顺序键值
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: ade2fd6011bbcdaed4ce31ce70bfb4235429bb0d
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606296"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>映射数据流的代理密钥转换 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用代理项密钥转换向每行数据添加递增键值。 这在星形架构分析数据模型中设计维度表时非常有用。 在星形架构中，维度表中的每个成员都需要一个非业务密钥的唯一密钥。

## <a name="configuration"></a>配置

![代理密钥转换](media/data-flow/surrogate.png "代理密钥转换")

**键列：** 生成的代理项键列的名称。

**起始值：** 将生成的最低键值。

## <a name="increment-keys-from-existing-sources"></a>来自现有源的增量键

要从源中存在的值启动序列，请使用代理密钥转换之后的派生列转换将两个值一起添加：

![SK 添加最大值](media/data-flow/sk006.png "代理密钥转换添加最大值")

### <a name="increment-from-existing-maximum-value"></a>从现有最大值递增

要用前面的最大值设置键值的种子，可以使用两种技术，具体取决于源数据的位置。

#### <a name="database-sources"></a>数据库源

使用 SQL 查询选项从源中选择 MAX（）。 例如，`Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![代理密钥查询](media/data-flow/sk002.png "代理密钥转换查询")

#### <a name="file-sources"></a>文件源

如果以前的最大值位于文件中，请使用聚合转换中的`max()`函数来获取以前的最大值：

![代理密钥文件](media/data-flow/sk008.png "代理密钥文件")

在这两种情况下，都必须将传入的新数据与包含前一个最大值的源联接在一起。

![代理密钥联接](media/data-flow/sk004.png "代理密钥联接")

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>示例

![代理密钥转换](media/data-flow/surrogate.png "代理密钥转换")

上述代理项密钥配置的数据流脚本位于下面的代码段中。

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>后续步骤

这些示例使用[联接](data-flow-join.md)和[派生列](data-flow-derived-column.md)转换。
