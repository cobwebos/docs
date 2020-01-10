---
title: 映射数据流中的多个分支
description: 复制包含多个分支的映射数据流中的数据流
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 71fb9f1ba9952be0e6b3910dd1079aa6d3c0482d
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834510"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>在映射数据流中创建新分支

添加新分支，对同一数据流执行多组操作和转换。 如果要为多个接收器使用相同的源，或将自联接数据一起使用，则添加新分支会很有用。

可以从转换列表添加新分支，类似于其他转换。 仅当正在尝试分支的转换之后存在转换后，**新分支**才会作为操作可用。

![添加新分支](media/data-flow/new-branch2.png "添加新分支")

在下面的示例中，数据流正在读取出租车行程数据。 由 day 和供应商聚合的输出是必需的。 可以添加一个新的分支，而不是创建两个从同一源中读取的单独数据流。 这样，就可以将这两种聚合作为同一数据流的一部分来执行。 

![添加新分支](media/data-flow/new-branch.png "添加新分支")
