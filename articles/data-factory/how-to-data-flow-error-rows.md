---
title: 使用 Azure 数据工厂中的映射数据流处理错误行
description: 了解如何使用映射数据流处理 Azure 数据工厂中的 SQL 截断错误。
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: makromer
ms.openlocfilehash: 8225143bb75118620b45c2520bb62ea30501a617
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732689"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>处理数据工厂映射数据流中的 SQL 截断错误行

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在数据工厂中使用映射数据流时的常见方案是将数据转换后的数据写入 Azure SQL 数据库。 在这种情况下，必须防止的常见错误条件是可能的列截断。 按照以下步骤提供不适合目标字符串列的列的日志记录，从而允许数据流在这些方案中继续。

## <a name="scenario"></a>方案

1. 我们有一个目标 Azure SQL 数据库表，```nvarchar(5)```该表具有一个名为"name"的列。

2. 在数据流中，我们希望将影片标题从接收器映射到目标"名称"列。

    ![影片数据流 1](media/data-flow/error4.png)
    
3. 问题是，影片标题不能全部适合只能容纳 5 个字符的接收器列。 执行此数据流时，您将收到如下所示的错误：```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

本视频介绍数据流中设置错误行处理逻辑的示例：
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>如何围绕此条件进行设计

1. 在这种情况下，"名称"列的最大长度为五个字符。 因此，让我们添加一个条件拆分转换，允许我们记录具有"标题"超过五个字符的行，同时允许可以容纳该空间的其他行写入数据库。

    ![有条件拆分 (conditional split)](media/data-flow/error1.png)

2. 此条件拆分转换将"标题"的最大长度定义为 5。 任何小于或等于五行都将进入```GoodRows```流。 任何大于 5 行都将进入```BadRows```流中。

3. 现在，我们需要记录失败的行。 向```BadRows```流添加接收器转换以进行日志记录。 在这里，我们将"自动映射"所有字段，以便记录完整的事务记录。 这是文本分隔 CSV 文件输出到 Blob 存储中的单个文件。 我们将将日志文件称为"坏行.csv"。

    ![坏行](media/data-flow/error3.png)
    
4. 已完成的数据流如下所示。 现在，我们能够拆分错误行以避免 SQL 截断错误，并将这些条目放入日志文件中。 同时，成功的行可以继续写入我们的目标数据库。

    ![完整的数据流](media/data-flow/error2.png)

## <a name="next-steps"></a>后续步骤

* 通过使用映射数据流[转换](concepts-data-flow-overview.md)构建数据流逻辑的其余部分。
