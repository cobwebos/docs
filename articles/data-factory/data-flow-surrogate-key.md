---
title: 映射数据流中的代理键转换
description: 如何使用 Azure 数据工厂的映射数据流代理键转换生成顺序键值
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: ade2fd6011bbcdaed4ce31ce70bfb4235429bb0d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606296"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>映射数据流中的代理键转换 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用代理键转换向每个数据行添加递增的键值。 在星型架构分析数据模型中设计维度表时，这非常有用。 在星型架构中，维度表中的每个成员都需要唯一键，这是一个非业务键。

## <a name="configuration"></a>配置

![代理键转换](media/data-flow/surrogate.png "代理键转换")

**键列：** 生成的代理键列的名称。

**起始值：** 要生成的最小键值。

## <a name="increment-keys-from-existing-sources"></a>从现有源增加密钥

若要从源中存在的值启动序列，请使用代理键转换后的派生列转换将两个值相加：

![SK 添加 Max](media/data-flow/sk006.png "代理键转换添加 Max")

### <a name="increment-from-existing-maximum-value"></a>从现有的最大值开始递增

若要使用以前的最大值为密钥值设定种子，可以根据源数据的位置使用两种方法。

#### <a name="database-sources"></a>数据库源

使用 SQL 查询选项可选择源中的 MAX （）。 例如，`Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![代理键查询](media/data-flow/sk002.png "代理键转换查询")

#### <a name="file-sources"></a>文件源

如果以前的最大值位于文件中，则使用 `max()` 聚合转换中的函数获取上一个最大值：

![代理键文件](media/data-flow/sk008.png "代理键文件")

在这两种情况下，你都必须将传入的新数据与包含之前的最大值的源一起加入。

![代理键联接](media/data-flow/sk004.png "代理键联接")

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

![代理键转换](media/data-flow/surrogate.png "代理键转换")

上面的代码段中提供了上述代理项密钥配置的数据流脚本。

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>后续步骤

这些示例使用[联接](data-flow-join.md)和[派生列](data-flow-derived-column.md)转换。
