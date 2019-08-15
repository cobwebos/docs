---
title: include 文件
description: include 文件
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 83f3def8f4972f35df25dc98f4b818e703801d2e
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949986"
---
## <a name="create-a-function"></a>创建函数

以下命令创建一个名为 `MyHttpTrigger` 的 HTTP 触发的函数。

```command
func new --name MyHttpTrigger --template "HttpTrigger"
```

执行命令后，看到的内容如以下输出所示：

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```
