---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "68881331"
---
#### <a name="built-in-log-streaming"></a>内置日志流式处理

使用 `logstream` 选项开始接收在 Azure 中运行的特定函数应用的流式处理日志，如以下示例所示：

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>实时指标流

也可通过包括 `--browser` 选项在新的浏览器窗口中查看函数应用的[实时指标流](../articles/azure-monitor/app/live-stream.md)，如以下示例所示：

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
