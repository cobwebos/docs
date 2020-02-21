---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: b118da6b751bc7a1e29ceef10c91dc5e9e3659c2
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474207"
---
直接在 function.json 文件中定义绑定属性。 根据绑定类型，可能还需要其他属性。 [队列输出配置](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration)描述 Azure 存储队列绑定所需的字段。 扩展可以轻松地将绑定添加到 function.json 文件。 

要创建绑定，请右键单击（在 macOS 系统上，请按住 Ctrl 并单击）HttpTrigger 文件夹中的 `function.json` 文件，并选择“添加绑定...”  。请按照提示为新绑定定义以下绑定属性：

| Prompt | 值 | 说明 |
| -------- | ----- | ----------- |
| **选择绑定方向** | `out` | 该绑定是输出绑定。 |
| **选择具有方向的绑定...** | `Azure Queue Storage` | 该绑定是 Azure 存储队列绑定。 |
| **用于在代码中标识此绑定的名称** | `msg` | 用于标识代码中引用的绑定参数的名称。 |
| **要将消息发送到的队列** | `outqueue` | 绑定要写入到的队列的名称。 如果 *queueName* 不存在，首次使用绑定时，它会创建该属性。 |
| **从 local.setting.json 中选择设置** | `AzureWebJobsStorage` | 包含存储帐户连接字符串的应用程序设置的名称。 `AzureWebJobsStorage` 设置包含连同函数应用一起创建的存储帐户的连接字符串。 |

将绑定添加到 function.json 文件中的 `bindings` 数组，如以下示例所示：

```json
{
   ...

  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```