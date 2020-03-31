---
title: 在 Azure 数据工厂中使用映射数据流处理固定长度的文本文件
description: 了解如何使用映射数据流在 Azure 数据工厂中处理固定长度的文本文件。
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: d6885e9b30cc71bda822a29574c4d574f2b020a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72387052"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>使用数据工厂映射数据流处理固定长度的文本文件

通过使用 Microsoft Azure 数据工厂中的映射数据流，可以从固定宽度文本文件转换数据。 在以下任务中，我们将为没有分隔符的文本文件定义数据集，然后根据单位位置设置子字符串拆分。

## <a name="create-a-pipeline"></a>创建管道

1. 选择 **"新建管道**"以创建新管道。

2. 添加数据流活动，该活动将用于处理固定宽度文件：

    ![固定宽度管道](media/data-flow/fwpipe.png)

3. 在数据流活动中，选择 **"新建映射数据流**"。

4. 添加源、派生列、选择和接收器转换：

    ![固定宽度数据流](media/data-flow/fw2.png)

5. 将源转换配置为使用新数据集，该数据集将属于"分隔文本"类型。

6. 不要设置任何列分隔符或标题。

   现在，我们将为此文件的内容设置字段起始点和长度：

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. 在"源"转换的 **"投影"** 选项卡上，应看到名为*Column_1*的字符串列。

8. 在"派生"列中，创建新列。

9. 我们将给列简单名称，如*col1。*

10. 在表达式生成器中，键入以下内容：

    ```substring(Column_1,1,4)```

    ![派生列 (derived column)](media/data-flow/fwderivedcol1.png)

11. 对于需要分析的所有列，重复步骤 10。

12. 选择 **"检查**"选项卡以查看将生成的新列：

    ![检查](media/data-flow/fwinspect.png)

13. 使用"选择"转换删除不需要转换的任何列：

    ![选择转换](media/data-flow/fwselect.png)

14. 使用"接收器"将数据输出到文件夹：

    ![固定宽度水槽](media/data-flow/fwsink.png)

    以下是输出的外观：

    ![固定宽度输出](media/data-flow/fxdoutput.png)

  固定宽度数据现在被拆分，每个字符有四个字符，并分配给 Col1、Col2、Col3、Col4 等。 根据前面的示例，数据分为四列。

## <a name="next-steps"></a>后续步骤

* 通过使用映射数据流[转换](concepts-data-flow-overview.md)构建数据流逻辑的其余部分。
