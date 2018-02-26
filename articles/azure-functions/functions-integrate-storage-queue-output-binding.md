---
title: "使用 Functions 将消息添加到 Azure 存储队列 | Microsoft Docs"
description: "使用 Azure Functions 创建一个无服务器函数，该函数通过 HTTP 请求调用，并在 Azure 存储队列中创建一条消息。"
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: a17897dc76b47f3fb7b6eb5076160faf6c5ae9c9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>使用 Functions 将消息添加到 Azure 存储队列

在 Azure Functions 中，可以通过输入和输出绑定以声明方式将外部服务中的数据提供给代码使用。 在本快速入门中，可以使用输出绑定，这样便可以当某个 HTTP 请求触发某个函数时，在队列中创建一条消息。 可以使用 Azure 存储资源管理器查看函数创建的队列消息：

![存储资源管理器中显示的队列消息](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)

## <a name="prerequisites"></a>先决条件 

完成本快速入门教程：

* 按[通过 Azure 门户创建第一个函数](functions-create-first-azure-function.md)中的说明操作，请勿执行“清理资源”这一步。 该快速入门创建此处所用的函数应用和函数。

* 安装 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/)。 这是一项工具，可以用来检查输出绑定创建的队列消息。

## <a name="add-binding"></a>添加输出绑定

在此部分，请使用门户 UI 将队列存储输出绑定添加到此前创建的函数。 有了此绑定，就可以在尽量减少代码编写工作的情况下在队列中创建消息。 不需为打开存储连接、创建队列、获取队列引用之类的任务编写代码。 Azure Functions 运行时和队列输出绑定为你处理这些任务。

1. 在 Azure 门户中，打开在[通过 Azure 门户创建第一个函数](functions-create-first-azure-function.md)中创建的函数应用的函数应用页。 为此，请选择“所有服务”>“Function App”，然后选择你的函数应用。

2. 选择在此前的那个快速入门中创建的函数。

1. 选择“集成”>“新建输出”>“Azure 队列存储”。

1. 单击“选择”。
    
    ![将队列存储输出绑定添加到 Azure 门户中的函数。](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding.png)

3. 在“Azure 队列存储输出”下，使用在此屏幕截图下的表中指定的设置： 

    ![将队列存储输出绑定添加到 Azure 门户中的函数。](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding-2.png)

    | 设置      |  建议的值   | 说明                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **消息参数名称** | outputQueueItem | 输出绑定参数的名称。 | 
    | **存储帐户连接** | AzureWebJobsStorage | 可以使用 Function App 已在使用的存储帐户连接，也可以创建一个新的存储帐户连接。  |
    | **队列名称**   | outqueue    | 要连接到存储帐户中的队列的名称。 |

4. 单击“保存”添加绑定。
 
现在，已定义输出绑定，需要更新代码以使用绑定将消息添加到队列。  

## <a name="add-code-that-uses-the-output-binding"></a>添加使用输出绑定的代码

在此部分，请添加将消息写入输出队列的代码。 该消息包括在查询字符串中传递到 HTTP 触发器的值。 例如，如果查询字符串包含 `name=Azure`，则队列消息将是“传递给函数的名称: Azure”。

1. 选择函数以在编辑器中显示函数代码。 

2. 对于 C# 函数，请为绑定添加一个方法参数，然后编写使用它的代码：

   向方法签名添加 **outputQueueItem** 参数，如以下示例所示。 参数名称就是创建绑定时输入的**消息参数名称**。

   ```cs   
   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, 
       ICollector<string> outputQueueItem, TraceWriter log)
   {
       ...
   }
   ```

   在 C# 函数正文中刚好在 `return` 语句之前，添加可以使用该参数创建队列消息的代码。

   ```cs
   outputQueueItem.Add("Name passed to the function: " + name);     
   ```

3. 对于 JavaScript 函数，请添加可以在 `context.bindings` 对象上使用输出绑定来创建队列消息的代码。 请在`context.done` 语句之前添加此代码。

   ```javascript
   context.bindings.outputQueueItem = "Name passed to the function: " + 
               (req.query.name || req.body.name);
   ```

4. 选择“保存”以保存更改。
 
## <a name="test-the-function"></a>测试函数 

1. 保存代码更改后，选择“运行”。 

    ![将队列存储输出绑定添加到 Azure 门户中的函数。](./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png)

   请注意，**请求正文**包含 `name` 值 *Azure*。 此值显示在队列消息中，该消息是在调用函数时创建的。

   如果不想选择此处的“运行”，也可调用该函数，方法是在浏览器中输入 URL，然后在查询字符串中指定 `name` 值。 此浏览器方法在[以前的快速入门](functions-create-first-azure-function.md#test-the-function)中演示过。

2. 检查日志以确保该函数成功。 

首次使用输出绑定时，Functions 运行时会在存储帐户中创建名为 **outqueue** 的新队列。 可以使用存储资源管理器来验证是否已创建队列以及其中的消息。

### <a name="connect-storage-explorer-to-your-account"></a>将存储资源管理器连接到帐户

如果已安装存储资源管理器并将其连接到此快速入门中使用的存储帐户，则请跳过此部分。

2. 运行 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/)工具，选择左侧的“连接”图标，选择“使用存储帐户名称和密钥”，然后选择“下一步”。

    ![运行“存储帐户资源管理器”工具。](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-1.png)

1. 在 Azure 门户的函数应用页中选择函数，然后选择“集成”。

1. 选择在前面的步骤中添加的“Azure 队列存储”输出绑定。

1. 展开页面底部的“文档”部分。 

   门户显示的凭据可以在存储资源管理器中用于连接到存储帐户。

   ![获取存储帐户连接凭据。](./media/functions-integrate-storage-queue-output-binding/function-get-storage-account-credentials.png)

1. 从门户复制“帐户名称”值，将其粘贴在存储资源管理器的“帐户名称”框中。
 
1. 单击“帐户密钥”旁边的显示/隐藏图标以显示该值，然后复制“帐户密钥”值并将其粘贴在存储资源管理器的“帐户密钥”框中。
  
3. 选择“下一步”>“连接”。

   ![粘贴存储凭据，然后进行连接。](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-2.png)

### <a name="examine-the-output-queue"></a>检查输出队列

4. 在存储资源管理器中选择用于本快速入门的存储帐户。

1. 展开“队列”节点，然后选择名为 **outqueue** 的队列。 

   此队列包含在运行 HTTP 触发的函数时队列输出绑定创建的消息。 如果使用默认的 `name` 值 *Azure* 调用了此函数，则队列消息为“传递给函数的名称: Azure”。

    ![存储资源管理器中显示的队列消息](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)

2. 再次运行函数，此时会看到新消息出现在队列中。  

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何将输出绑定添加到现有函数。 有关队列存储绑定的详细信息，请参阅 [Azure Functions 存储队列绑定](functions-bindings-storage-queue.md)。 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
