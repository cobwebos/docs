---
title: Azure 数据工厂映射数据流“新建分支”转换
description: Azure 数据工厂映射数据流“新建分支”转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 3f443396627a4bbaba2a3eeb0a3ac05cc1597c85
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029287"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure 数据工厂映射数据流“新建分支”转换



![分支选项](media/data-flow/menu.png "菜单")

分支将获取数据流中的当前数据流并将其复制到另一个流。 使用“新建分支”对同一数据流执行多组操作和转换。

示例：你的数据流具有源转换，其中包含一组选定的列和数据类型转换。 然后立即将派生列放置在该源之后。 在派生列中，已创建组合了名和姓的新字段，以便创建新的“全名”字段。

可以使用一组转换和某一行上的接收器来处理该新流，并使用 New Branch 创建该流的副本，在其中可以使用一组不同转换来转换相同数据。 通过在单独分支中转换复制的数据，之后可以将该数据接收到单独的位置。

> [!NOTE]
> 试图分支到的当前位置之后还存在后续转换时，“新建分支”将只会显示为“+ 转换”菜单上的操作。 也就是说，在“选择”之后添加另一个转换后，才能在此处末尾看到“新建分支”选项

![分支](media/data-flow/branch2.png "分支 2")
