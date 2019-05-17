---
title: Azure 数据工厂映射数据流 Alter 行转换
description: 如何更新数据库目标，使用 Azure 数据工厂映射数据流 Alter 行转换
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: f0ac5bb36079983b10e4d86cc776bd4e5ee6817d
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520149"
---
# <a name="azure-data-factory-alter-row-transformation"></a>Azure 数据工厂 Alter 行转换

使用 Alter 行转换行上设置 insert、 delete、 update 和 upsert 的策略。 作为表达式，可以添加一个对多条件。 这些条件的每个可能导致行 （或行） 插入、 更新、 删除，或 upsert。 Alter 行可以生成针对您的数据库 DDL 和 DML 操作。

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![更改行设置](media/data-flow/alter-row1.png "Alter 行设置")

> [!NOTE]
> Alter 行转换将仅对在数据流中的数据库接收器进行操作。 为行 （insert、 update、 delete、 upsert） 指定的操作不会在调试会话期间发生。 必须向管道添加执行数据流任务，并使用管道调试或触发器来制定对数据库表的 alter 行策略。

## <a name="view-policies"></a>查看策略

切换到在数据流动调试模式，然后查看数据预览窗格中的 alter 行策略的结果。 在数据流动调试模式下执行 alter 行不会产生针对你的目标的 DDL 或 DML 操作。 为了获得这些操作发生，请执行数据流管道中执行数据流的活动内。

![Alter 行策略](media/data-flow/alter-row3.png "Alter 行策略")

这样，您可以验证并查看每个基于你的条件的行的状态。 有图标表示每个插入，将发生的操作，该值指示在数据流中的 update、 delete 和 upsert 操作时，将会执行管道中的数据流。

## <a name="sink-settings"></a>接收器设置

必须具有接收器类型为 Alter 行，若要运行的数据库。 在接收器的设置中，必须设置以允许每个操作。

![Alter 行接收器](media/data-flow/alter-row2.png "Alter 行接收器")

数据库接收器 ADF 数据流中的默认行为是插入行。 如果你想要允许更新、 更新插入和删除也，还必须检查这些框以允许操作在接收器中。

> [!NOTE]
> 如果插入、 更新或 upsert 修改在接收器中的目标表的架构，数据的流将失败。 若要修改您的数据库中的目标架构，必须在接收器中选择"重新创建表"选项。 这将删除并重新创建使用新的架构定义表。

## <a name="next-steps"></a>后续步骤

Alter 行在转换后，你可能希望[到目标数据存储接收器数据](data-flow-sink.md)。
