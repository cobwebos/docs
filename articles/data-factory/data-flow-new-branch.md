---
title: 映射数据流新分支转换
description: Azure 数据工厂映射数据流新建分支转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: b4617689fe1ab14856bde9a4e8134b12aa6d815b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930303"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure 数据工厂映射数据流新建分支转换

![分支选项](media/data-flow/menu.png "菜单")

分支将获取数据流中的当前数据流并将其复制到另一个流。 使用“新建分支”对同一数据流执行多组操作和转换。

示例：数据流的源转换具有一组选定的列和数据类型转换。 然后立即将派生列放置在该源之后。 在派生列中，已创建组合了名和姓的新字段，以便创建新的“全名”字段。

可以使用一组转换和某一行上的接收器来处理该新流，并使用 New Branch 创建该流的副本，在其中可以使用一组不同转换来转换相同数据。 通过在单独分支中转换复制的数据，之后可以将该数据接收到单独的位置。

> [!NOTE]
> 试图分支到的当前位置之后还存在后续转换时，“新建分支”将只会显示为“+ 转换”菜单上的操作。 也就是说，在“选择”之后添加另一个转换后，才能在此处末尾看到“新建分支”选项

![分支](media/data-flow/branch2.png "分支2")
