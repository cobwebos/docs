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
ms.openlocfilehash: e1ba09f459152616941071c23f7a6545ec2a8b73
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210671"
---
# <a name="process-fixed-length-text-files-using-data-factory-mapping-data-flows"></a>使用数据工厂映射数据流处理固定长度文本文件

数据工厂映射数据流支持固定宽度文本文件中的转换数据。 您将为不带分隔符的文本文件定义数据集, 然后基于序号位置设置子字符串拆分。

## <a name="create-a-pipeline"></a>创建管道

1. 中转到 **+ 新建管道**以启动新管道

2. 添加将用于处理固定宽度文件的数据流活动

  ![固定宽度管道](media/data-flow/fwpipe.png)

3. 在 "数据流" 活动中, 选择 "新建映射数据流"

4. 添加源转换、派生列、选择和接收器转换

  ![固定宽度数据流](media/data-flow/fw2.png)

5. 将源转换配置为使用类型分隔文本的新数据集

6. 不设置列分隔符且不设置标头

只需为此文件内容设置字段起始点和长度:

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

7. 在源转换的 "投影" 选项卡上, 应看到一个名为 "Column_1" 的字符串列

8. 现在, 在派生列中创建新列

9. 我们将为列指定简单名称, 如 col1

10. 然后, 在 "表达式生成器" 中键入:

  ```substring(Column_1,1,4)```

  ![派生列](media/data-flow/fwderivedcol1.png)

10. 对需要分析的所有列重复此操作

12. 单击 "检查" 选项卡以查看将生成的新列

  ![一下](media/data-flow/fwinspect.png)

13. 使用 "选择转换" 可以删除任何不需要进行转换的列

  ![选择转换](media/data-flow/fwselect.png)

14. 最后, 使用 Sink 将数据输出到文件夹:

  ![固定宽度接收器](media/data-flow/fwsink.png)

  输出如下所示:

  ![固定宽度输出](media/data-flow/fxdoutput.png)

  固定宽度数据现在由四个字符拆分, 并分配到 Col1、Col2、Col3、Col4 .。。根据上面的示例, 我将数据拆分为4列

## <a name="next-steps"></a>后续步骤

* 使用映射数据流[转换](concepts-data-flow-overview.md)构建数据流逻辑的其余部分
