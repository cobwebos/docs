---
title: Azure 数据工厂映射数据流选择转换
description: Azure 数据工厂映射数据流选择转换
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 15c74637a2dc42ec44f582878b5505d94637cd7b
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314216"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Azure 数据工厂映射数据流选择转换
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

此转换用于列选择性 (减少列数)、别名列和流名称, 以及重新排序列。

## <a name="how-to-use-select-transformation"></a>如何使用选择转换
使用选择转换可为整个流或该流中的列指定别名、分配其他名称（别名），以及稍后在数据流中引用这些新名称。 此转换对于自联接方案很有用。 在 ADF 数据流中实现自联接的方法是，采用流并使用“新建分支”对其进行分支，之后立即添加“选择”转换。 该流现在具有新名称，可使用新名称重新联接到原始流，创建自联接：

![自联接](media/data-flow/selfjoin.png "自联接")

在上面的关系图中，选择转换位于顶部。 这会将原始流的别名指定为“OrigSourceBatting”。 在下面突出显示的联接转换中, 可以看到, 我们使用了 Select alias stream 作为右侧联接, 这使我们可以在内部联接的左侧 & 右侧引用相同的键。

选择转换还可用作从数据流中取消选择列的方法。 例如，如果接收器中定义了 6 列，但只想选取特定的 3 列用于转换并随后流动到接收器，则可使用选择转换仅选择这 3 列。

> [!NOTE]
> 必须关闭“全选”才能仅选择特定的列

![选择转换](media/data-flow/select001.png "选择别名")

## <a name="options"></a>选项
* “选择”的默认设置是包括所有传入列并保留其原始名称。 可以通过设置选择转换的名称来指定流的别名。
* 若要指定单个列的别名，请取消选择“全选”并使用底部的列映射。
* 选择 "跳过重复项" 以消除输入或输出元数据中的重复列。

![跳过重复项](media/data-flow/select-skip-dup.png "跳过重复项")

## <a name="next-steps"></a>后续步骤
* 使用 "选择重命名"、"重新排序" 和 "别名" 列后, 使用[接收器转换](data-flow-sink.md)将数据插入到数据存储中。
