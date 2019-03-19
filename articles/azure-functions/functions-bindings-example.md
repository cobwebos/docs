---
title: Azure Functions 触发器和绑定示例
description: 了解如何配置 Azure 函数绑定
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 6d5f9b171a4efc5e52d281655de143ac9d40d437
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2019
ms.locfileid: "56740009"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Azure Functions 触发器和绑定示例

本文展示了如何在 Azure 函数中配置[触发器和绑定](./functions-triggers-bindings.md)。

假设希望在 Azure 队列存储中显示一条新消息时就将一个新行写入 Azure 表存储。 使用 Azure 队列存储触发器和 Azure 表存储输出绑定即可实现此方案。 

下面是用于这种方案的 function.json 文件。 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

`bindings` 数组中的第一个元素是队列存储触发器。 `type` 和 `direction` 属性标识该触发器。 `name` 属性标识接收队列消息内容的函数参数。 要监视的队列的名称位于 `queueName` 中，连接字符串位于由 `connection` 标识的应用设置中。

`bindings` 数组中的第二个元素是 Azure 表存储输出绑定。 `type` 和 `direction` 属性标识该绑定。 `name` 属性指定函数提供新表行的方式，在此例中是使用函数返回值来提供。 表格的名称位于 `tableName` 中，连接字符串位于由 `connection` 标识的应用设置中。

若要在 Azure 门户中查看和编辑 *function.json* 的内容，请单击函数“集成”选项卡上的“高级编辑器”选项。

> [!NOTE]
> `connection` 的值是包含连接字符串的应用设置的名称，而不是连接字符串本身的名称。 绑定使用应用设置中存储的连接字符串，以强制执行 function.json 不包含服务密钥这一最佳做法。

## <a name="c-script-example"></a>C# 脚本示例

以下是适用于此触发器和绑定的 C# 脚本代码。 请注意，提供队列消息内容的参数的名称是 `order`；需使用此名称是因为 function.json 中的 `name` 属性值是 `order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>JavaScript 示例

可将同一 *function.json* 文件用于 JavaScript 函数：

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>类库示例

在类库中，由特性而不是 function.json 文件提供这些触发器和绑定信息 &mdash; 队列和表名称、存储帐户、输入和输出 &mdash; 的函数参数。 下面是一个示例：

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

你现在有一个由 Azure 表存储触发的工作函数，该函数将数据输出到队列中。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 绑定表达式模式](./functions-bindings-expressions-patterns.md)