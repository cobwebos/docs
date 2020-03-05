---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8bdd8b9d900cc50fdeb34ff7d233ac4d7e17a45c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190995"
---
按以下代码所示更新 *HttpExample\\\_\_init\_\_.py*，将 `msg` 参数添加到函数定义，并将 `msg.set(name)` 添加到 `if name:` 语句下。

:::code language="python" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

`msg` 参数是 [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest) 的实例。 其 `set` 方法将字符串消息写入队列，在本例中，此消息是在 URL 查询字符串中传递给函数的名称。
