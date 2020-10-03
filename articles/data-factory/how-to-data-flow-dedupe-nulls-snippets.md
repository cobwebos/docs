---
title: 使用数据流片段重复数据的行重复和查找 null
description: 了解如何使用数据流中的代码片段轻松地重复数据的行重复数据
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: makromer
ms.openlocfilehash: 841484a647d2737d621c75ebe63f65f2de829a26
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91666479"
---
# <a name="dedupe-rows-and-find-nulls-using-data-flow-snippets"></a>使用数据流片段重复数据的行重复和查找 null

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

通过使用映射数据流中的代码片段，您可以很容易地执行重复数据删除和 null 筛选等常见任务。 本操作方法指南将说明如何使用数据流脚本代码段轻松地将这些函数添加到管道。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>创建管道

1. 选择 " **+ 新建管道** " 以创建新管道。

2. 添加数据流活动。

3. 添加源转换，并将其连接到你的某个数据集

    ![源代码段2](media/data-flow/snippet-adf-2.png)

4. 重复数据的和 NULL 检查代码段使用一般模式，这些模式使用数据流架构偏移，因此它们将适用于来自数据集的任何架构，或与没有任何预定义架构的数据集配合使用。

5. [中转到 "数据流脚本" 文档页，复制不同行的代码片段。](https://docs.microsoft.com/azure/data-factory/data-flow-script#distinct-row-using-all-columns)

6. 在 "数据流设计器 UI" 中，单击右上方的 "脚本" 按钮，以打开 "数据流" 图后面的脚本编辑器。

    ![源代码段3](media/data-flow/snippet-adf-3.png)

7. ```source1```在脚本中的定义后，按 Enter，然后粘贴到代码段中。

8. 您将此粘贴的代码段连接到您在关系图中创建的以前的源转换，方法是在粘贴的代码前面键入 "source1"。

9. 或者，您可以通过从关系图中的 "新转换" 节点中选择传入流，在设计器中连接新的转换。

    ![源代码段4](media/data-flow/snippet-adf-4.png)

10. 现在，数据流将使用聚合转换从源删除源中的重复行，该转换使用跨所有列值的一般哈希对所有行进行分组。
    
11. 接下来，我们将添加一个代码片段，以便将数据拆分为包含空值的行和没有任何空值的流。

12. [返回到代码片段库，此时复制 NULL 检查的代码。](https://docs.microsoft.com/azure/data-factory/data-flow-script#check-for-nulls-in-all-columns)

13. 在数据流设计器中，再次单击 "脚本"，然后在底部粘贴这一新的转换代码，并通过在粘贴的代码段前面键入该转换的名称将其连接到以前的转换。

14. 数据流图表现在应如下所示：

    ![源代码段1](media/data-flow/snippet-adf-1.png)

  通过从数据流脚本库中提取现有代码段并将其添加到现有设计中，你现在可以使用一般的 deduping 和 NULL 检查工作流。

## <a name="next-steps"></a>后续步骤

* 使用映射数据流 [转换](concepts-data-flow-overview.md)生成数据流逻辑的其余部分。
