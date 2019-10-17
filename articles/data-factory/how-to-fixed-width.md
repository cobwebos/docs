---
title: 处理具有在 Azure 数据工厂中映射数据流的固定长度文本文件
description: 了解如何使用映射数据流处理 Azure 数据工厂中的固定长度文本文件。
services: data-factory
author: balakreshnan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: d6885e9b30cc71bda822a29574c4d574f2b020a0
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387052"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>使用数据工厂映射数据流处理固定长度文本文件

通过使用在 Microsoft Azure 数据工厂中映射数据流，可以转换固定宽度文本文件中的数据。 在下面的任务中，我们将为不带分隔符的文本文件定义一个数据集，然后基于序号位置设置子字符串拆分。

## <a name="create-a-pipeline"></a>创建管道

1. 选择 " **+ 新建管道**" 以创建新管道。

2. 添加一个数据流活动，该活动将用于处理固定宽度的文件：

    ![固定宽度管道](media/data-flow/fwpipe.png)

3. 在 "数据流" 活动中，选择 "**新建映射**数据流"。

4. 添加源、派生列、选择和接收器转换：

    ![固定宽度数据流](media/data-flow/fw2.png)

5. 将源转换配置为使用新的数据集，该数据集将为分隔文本类型。

6. 不要设置任何列分隔符或标头。

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

7. 在源转换的 "**投影**" 选项卡上，应看到一个名为 " *Column_1*" 的字符串列。

8. 在派生列中，创建一个新列。

9. 我们将为列指定简单名称，如*col1*。

10. 在 "表达式生成器" 中，键入以下内容：

    ```substring(Column_1,1,4)```

    ![派生列](media/data-flow/fwderivedcol1.png)

11. 对需要分析的所有列重复步骤10。

12. 选择 "**检查**" 选项卡以查看将生成的新列：

    ![一下](media/data-flow/fwinspect.png)

13. 使用 "选择转换" 删除不需要进行转换的任何列：

    ![选择转换](media/data-flow/fwselect.png)

14. 使用接收器将数据输出到文件夹：

    ![固定宽度接收器](media/data-flow/fwsink.png)

    输出如下所示：

    ![固定宽度输出](media/data-flow/fxdoutput.png)

  固定宽度数据现在是拆分的，其中每个字符四个字符并分配给 Col1、Col2、Col3、Col4 等等。 根据前面的示例，将数据拆分为四列。

## <a name="next-steps"></a>后续步骤

* 使用映射数据流[转换](concepts-data-flow-overview.md)生成数据流逻辑的其余部分。
