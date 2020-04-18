---
title: 映射数据流的多个分支
description: 复制数据流，映射具有多个分支的数据流
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: daebd6a4a00d82706ceda68f94736f21b8fe5a84
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606397"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>在映射数据流中创建新分支

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

添加新分支，以便针对同一数据流执行多组操作和转换。 如果要将同一源用于多个接收器或将数据合并在一起，则添加新分支非常有用。

可以从转换列表中添加新分支，类似于其他转换。 仅当尝试分支转换后存在现有转换时，**新分支**才可作为操作提供。

![添加新分支](media/data-flow/new-branch2.png "添加新分支")

在下面的示例中，数据流正在读取出租车行程数据。 需要按天和供应商聚合的输出。 可以添加新分支，而不是创建从同一源读取的两个单独的数据流。 这样，两个聚合都可以作为同一数据流的一部分执行。 

![添加新分支](media/data-flow/new-branch.png "添加新分支")
