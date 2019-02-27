---
title: Azure 数据工厂映射数据流接收器转换
description: Azure 数据工厂映射数据流接收器转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 381dc2f9f6d3a074af00ba047472719c086f5811
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408402"
---
# <a name="mapping-data-flow-sink-transformation"></a>映射数据流接收器转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![接收器选项](media/data-flow/windows1.png "接收器 1")

数据流转换完成后，可将转换的数据沉积到目标数据集中。 在接收器转换中，可以选择要用于目标输出数据的数据集定义。 可根据数据流的需要创建任意个接收器转换。

规划传入数据更改和架构偏差的一种常见做法是将输出数据沉积到某个文件夹，且不在输出数据集中定义任何架构。 此外，可以通过在源中选择“允许架构偏差”来规划源中的所有列更改，然后在接收器中自动映射所有字段。

沉积到数据集时，可以选择覆盖、追加数据流，或者使数据流失败。

还可以选择“自动映射”以沉积所有传入字段。 若要选择想要沉积到目标的字段，或者要更改目标中字段的名称，请对“自动映射”选择“关闭”，然后单击“映射”选项卡以映射输出字段：

![接收器选项](media/data-flow/sink2.png "接收器 2")

## <a name="output-to-one-file"></a>输出到一个文件
对于 Azure 存储 Blob 或 Data Lake 接收器类型，需将转换的数据输出到某个文件夹。 Spark 将会根据接收器转换中使用的分区方案生成分区的输出数据文件。 可以单击“优化”选项卡设置分区方案。如果希望 ADF 将输出合并成单个文件，请单击“单个分区”单选按钮。

![接收器选项](media/data-flow/opt001.png "接收器选项")

### <a name="output-settings"></a>输出设置

覆盖操作会截断表（如果存在），然后重新创建表并加载数据。 追加操作会插入新行。 如果“数据集表名”中的表在目标 ADW 中根本不存在，则数据流将创建该表，然后加载数据。

如果取消选择“自动映射”，可以手动将字段映射到目标表。

![接收器 ADW 选项](media/data-flow/adw2.png "ADW 接收器")

#### <a name="field-mapping"></a>字段映射

在接收器转换的“映射”选项卡上，可将传入（左侧）列映射到目标（右侧）。 将数据流沉积到文件时，ADF 始终会将新文件写入某个文件夹。 映射到数据库数据集时，可以选择使用此架构生成新表（将“保存策略”设置为“覆盖”），或者将新行插入到现有表，并将字段映射到现有架构。

可以使用映射表中的多项选择功能一键式链接多个列、取消链接多个列，或者将多个行映射到相同的列名。

![字段映射](media/data-flow/multi1.png "多个选项")

若要重置列映射，请按“重新映射”按钮以重置映射。

![连接](media/data-flow/maxcon.png "连接")

### <a name="updates-to-sink-transformation-for-adf-v2-ga-version"></a>ADF V2 正式版的接收器转换更新

![接收器选项](media/data-flow/sink1.png "接收器 1")

![接收器选项](media/data-flow/sink2.png "接收器")

* 在接收器中现在可以使用“允许架构偏差”和“验证架构”选项。 这样，就可以指示 ADF 完全接受灵活的架构定义（架构偏差），或者在架构发生更改时使接收器失败（验证架构）。

* 清除文件夹。 ADF 在该目标文件夹中写入目标文件之前，会截断接收器文件夹的内容。

* 文件名选项

   * 默认值：允许 Spark 根据 PART 默认值为文件命名
   * 模式：输入输出文件的名称
   * 按分区：输入每个分区的文件名
   * 作为列中的数据：将输出文件设置为列的值

> [!NOTE]
> 仅当正在运行“执行数据流”活动且不处于“数据流调试”模式时，才会执行文件操作

使用 SQL 接收器类型可以设置：

* 截断表
* 重新创建表（执行删除/创建）
* 较大数据负载的批大小。 输入一个数字以将写入内容装桶成区块。

![字段映射](media/data-flow/sql001.png "SQL 选项")

## <a name="next-steps"></a>后续步骤

创建数据流后，请[将“执行数据流”活动添加到管道](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-overview)。
