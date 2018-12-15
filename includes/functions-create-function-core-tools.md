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
ms.openlocfilehash: 26789a12053fa6275b09836e706c391e181c8efd
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429918"
---
## <a name="create-a-function"></a>创建函数

以下命令创建一个名为 `MyHttpTrigger` 的 HTTP 触发的函数。

```bash
func new --name MyHttpTrigger --template "HttpTrigger"
```

执行命令后，看到的内容如以下输出所示：

```output
The function "MyHttpTrigger" was created successfully from the "HttpTrigger" template.
```
