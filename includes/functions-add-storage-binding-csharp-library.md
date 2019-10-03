---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 4db460a5dcefb49de3ad2b594d3957cbcf7445c3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592803"
---
在 C# 类库项目中，绑定被定义为函数方法上的绑定属性。 然后根据这些属性自动生成 function.json  文件。

打开 HttpTrigger.cs  项目文件并添加以下 `using` 语句：

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

将以下参数添加到 `Run` 方法定义：

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

`msg` 参数为 `ICollector<T>` 类型，表示函数完成时写入输出绑定的消息集合。 在这种情况下，输出是名为的 `outqueue` 存储队列。 存储帐户的连接字符串由 `StorageAccountAttribute` 设置。 此属性指示包含存储帐户连接字符串的设置，可以在类、方法或参数级别应用。 在这种情况下，可以省略 `StorageAccountAttribute`，因为你已使用默认存储帐户。

Run 方法定义如下所示：  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```
