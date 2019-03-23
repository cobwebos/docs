---
title: Azure 数据工厂映射数据流接收器转换
description: Azure 数据工厂映射数据流接收器转换
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: a56f391aa76bd1216fd51d516adb836a2093bcba
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371133"
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

## <a name="field-mapping"></a>字段映射

在接收器转换的“映射”选项卡上，可将传入（左侧）列映射到目标（右侧）。 将数据流沉积到文件时，ADF 始终会将新文件写入某个文件夹。 映射到数据库数据集时，可以选择使用此架构生成新表（将“保存策略”设置为“覆盖”），或者将新行插入到现有表，并将字段映射到现有架构。

可以使用映射表中多选要链接多个列单击一次，解除链接多个列或映射到相同的列名称的多个行。

当你希望始终采用传入的字段集，并将它们映射到作为目标的，将"允许架构偏差"设置。

![字段映射](media/data-flow/multi1.png "多个选项")

若要重置列映射，请按“重新映射”按钮以重置映射。

![接收器选项](media/data-flow/sink1.png "接收器 1")

![接收器选项](media/data-flow/sink2.png "接收器")

* 在接收器中现在可以使用“允许架构偏差”和“验证架构”选项。 这样，就可以指示 ADF 完全接受灵活的架构定义（架构偏差），或者在架构发生更改时使接收器失败（验证架构）。

* 清除文件夹。 ADF 在该目标文件夹中写入目标文件之前，会截断接收器文件夹的内容。

## <a name="file-name-options"></a>文件名选项

   * 默认值：允许 Spark 根据 PART 默认值为文件命名
   * 模式：输入输出文件的一种模式。 例如，将创建"贷款 [n]"loans1.csv、 loans2.csv，...
   * 按分区：输入每个分区的文件名
   * 作为列中的数据：将输出文件设置为列的值

> [!NOTE]
> 仅当正在运行“执行数据流”活动且不处于“数据流调试”模式时，才会执行文件操作

## <a name="database-options"></a>数据库选项

* 允许插入、 更新、 删除、 更新插入。 默认值是允许插入。 如果您希望更新、 更新插入或删除行，必须首先将 alter 行转换添加到这些特定操作的标记行。 关闭"允许插入"将阻止 ADF 从您的源中插入新行。
* 截断的表 （删除所有行从您的目标表前完成流的数据）
* 重新创建的表 （删除/创建目标表之前执行完成流的数据）
* 较大数据负载的批大小。 分解成多存储桶写入到输入的数字
* 启用暂存：这将指示 ADF Polybase 加载时要使用 Azure 数据仓库作为接收器数据集

> [!NOTE]
> 在数据流中，可以让 ADF 通过接收器转换具有新的表名称中设置数据集在目标数据库中创建新的表定义。 在 SQL 数据集，表名称下面单击"编辑"并输入新的表名称。 然后，在接收器转换中，打开"允许架构偏差"。 Seth 的"导入架构"设置为无。

![源转换架构](media/data-flow/dataset2.png "SQL 架构")

![SQL 接收器选项](media/data-flow/alter-row2.png "SQL 选项")

> [!NOTE]
> 在更新或删除数据库接收器中的行，必须设置的键列。 这样一来，Alter 行是能够确定在 DML 中唯一的行。

## <a name="next-steps"></a>后续步骤

创建数据流后，请[将“执行数据流”活动添加到管道](concepts-data-flow-overview.md)。
