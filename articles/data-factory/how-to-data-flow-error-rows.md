---
title: 处理 Azure 数据工厂中映射数据流的错误行
description: 了解如何使用映射数据流处理 Azure 数据工厂中的 SQL 截断错误。
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: makromer
ms.openlocfilehash: 3fe3403ad06d82ba5ccd33d2718bf0e5eff64490
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166533"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>处理数据工厂映射数据流中的 SQL 截断错误行

数据工厂中的常见方案是在使用映射数据流时，将转换的数据写入 Azure SQL 数据库。 在此方案中，您必须阻止的常见错误条件是可能的列截断。 按照这些步骤来提供不适合目标字符串列的列日志记录，以便在这些情况下数据流继续。

## <a name="scenario"></a>场景

1. 我们有一个具有名为 "name" 的 ```nvarchar(5)``` 列的目标 Azure SQL 数据库表。

2. 在我们的数据流中，我们想要将来自接收器的电影标题映射到该目标 "name" 列。

    ![电影数据流1](media/data-flow/error4.png)
    
3. 问题在于电影标题不会全部容纳在只能容纳5个字符的接收器列中。 当你执行此数据流时，你将收到如下错误： ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

## <a name="how-to-design-around-this-condition"></a>如何围绕此情况设计

1. 在这种情况下，"name" 列的最大长度为五个字符。 接下来，让我们添加一个有条件拆分转换，以便记录 "title" 长度超过五个字符的行，同时允许在该空间中容纳的其余行写入数据库。

    ![有条件拆分](media/data-flow/error1.png)

2. 此有条件拆分转换将 "title" 的最大长度定义为5。 任何小于或等于5的行都将进入 ```GoodRows``` 流中。 大于五的任何行都将进入 ```BadRows``` 流中。

3. 现在，我们需要记录失败的行。 将接收器转换添加到 ```BadRows``` 流以进行日志记录。 在这里，我们将 "自动映射" 所有字段，以便记录完整的事务记录。 这是一个以文本分隔的 CSV 文件输出到 Blob 存储中的单个文件。 我们将调用日志文件 "badrows"。

    ![错误行](media/data-flow/error3.png)
    
4. 完成的数据流如下所示。 现在，我们可以拆分错误行以避免 SQL 截断错误，并将这些条目放入日志文件中。 同时，成功的行可以继续写入我们的目标数据库。

    ![完成数据流](media/data-flow/error2.png)

## <a name="next-steps"></a>后续步骤

* 使用映射数据流[转换](concepts-data-flow-overview.md)生成数据流逻辑的其余部分。
