---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: c27f3fea6d2dd8b891220ba06b375b33e6cd950b
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673481"
---
在 Java 项目中，绑定被定义为函数方法上的绑定注释。 然后根据这些注释自动生成 *function.json* 文件。

浏览到函数代码在 _src/main/java_ 下的位置，打开 *Function.java* 项目文件，然后将以下参数添加到 `run` 方法定义：

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

`msg` 参数为 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 类型，表示函数完成时作为消息写入到输出绑定的字符串集合。 在这种情况下，输出是名为的 `outqueue` 存储队列。 存储帐户的连接字符串由 `connection` 方法设置。 请传递包含存储帐户连接字符串的应用程序设置，而不是传递连接字符串本身。

`run` 方法定义现在应如以下示例所示：  

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-22":::