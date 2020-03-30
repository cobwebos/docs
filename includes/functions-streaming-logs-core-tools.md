---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881331"
---
#### <a name="built-in-log-streaming"></a>内置日志流

使用`logstream`选项开始接收在 Azure 中运行的特定函数应用的流式处理日志，如以下示例所示：

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>实时指标流

您还可以通过包含`--browser`选项在新的浏览器窗口中查看函数应用[的实时指标流](../articles/azure-monitor/app/live-stream.md)，如以下示例所示：

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
