---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1300b15f91a6522a2c2a1c19f2999947abaac083
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "67173093"
---
在复制活动中，如果源的类型为 **FileSystemSource**，则可以在 typeProperties 节中使用以下属性：

| 属性 | 说明 | 允许的值 | 必须 |
| --- | --- | --- | --- |
| recursive |指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 |True、False（默认值） |否 |

