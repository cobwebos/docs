---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "68881331"
---
#### <a name="built-in-log-streaming"></a>内置日志流式处理

使用`logstream`选项开始接收在 Azure 中运行的特定函数应用的流式传输日志，如以下示例中所示：

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>实时指标流

你还可以通过包含[Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) `--browser`选项，在新的浏览器窗口中查看函数应用的实时指标流，如以下示例中所示：

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
