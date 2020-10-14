---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80673252"
---
在 Java 项目中，绑定被定义为函数方法上的绑定注释。 然后根据这些注释自动生成 *function.json* 文件。

浏览到函数代码在 _src/main/java_ 下的位置，打开 *Function.java* 项目文件，然后将以下参数添加到 `run` 方法定义：

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

`msg` 参数为 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 类型，表示函数完成时作为消息写入到输出绑定的字符串集合。 在这种情况下，输出是名为的 `outqueue` 存储队列。 存储帐户的连接字符串由 `connection` 方法设置。 请传递包含存储帐户连接字符串的应用程序设置，而不是传递连接字符串本身。

`run` 方法定义现在应如以下示例所示：  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```