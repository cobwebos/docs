---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 5e1a2622df0038141dd5cb05237f93d5e33e0bfb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190903"
---
在 C# 类库项目中，绑定被定义为函数方法上的绑定属性。 然后，基于这些属性自动生成 Functions 所需的 function.json  文件。

打开 HttpExample.cs  项目文件，并将以下参数添加到 `Run` 方法定义中：

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

`msg` 参数为 `ICollector<T>` 类型，表示函数完成时写入输出绑定的消息集合。 在这种情况下，输出是名为的 `outqueue` 存储队列。 存储帐户的连接字符串由 `StorageAccountAttribute` 设置。 此属性指示包含存储帐户连接字符串的设置，可以在类、方法或参数级别应用。 在这种情况下，可以省略 `StorageAccountAttribute`，因为你已使用默认存储帐户。

Run 方法定义如下所示：  

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="14-18":::
