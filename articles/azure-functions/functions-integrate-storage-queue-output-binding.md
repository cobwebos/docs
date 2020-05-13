---
title: 使用 Functions 将消息添加到 Azure 存储队列
description: 使用 Azure Functions 创建一个无服务器函数，该函数通过 HTTP 请求调用，并在 Azure 存储队列中创建一条消息。
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.topic: how-to
ms.date: 04/24/2020
ms.custom: mvc
ms.openlocfilehash: 5ae282750580ed5b4e53e78c52ca285e40365fd3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121970"
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>使用 Functions 将消息添加到 Azure 存储队列

在 Azure Functions 中，可以通过输入和输出绑定以声明方式将外部服务中的数据提供给代码使用。 在本快速入门中，可以使用输出绑定，这样便可以当某个 HTTP 请求触发某个函数时，在队列中创建一条消息。 使用 Azure 存储容器可以查看函数创建的队列消息。

## <a name="prerequisites"></a>先决条件

完成本快速入门教程需要：

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 按[通过 Azure 门户创建第一个函数](functions-create-first-azure-function.md)中的说明操作，请勿执行“清理资源”**** 这一步。 该快速入门创建此处所用的函数应用和函数。

## <a name="add-an-output-binding"></a><a name="add-binding"></a>添加输出绑定

在此部分，请使用门户 UI 将队列存储输出绑定添加到此前创建的函数。 通过此绑定，可以编写最少量的代码来创建队列中的消息。 不需为打开存储连接、创建队列、获取队列引用之类的任务编写代码。 Azure Functions 运行时和队列输出绑定为你处理这些任务。

1. 在 Azure 门户中，打开在[通过 Azure 门户创建第一个函数](functions-create-first-azure-function.md)中创建的函数应用的函数应用页。 若要打开此页，请搜索并选择**Function App**。 然后选择函数应用。

1. 选择 "function app"，然后选择你在前面的快速入门中创建的函数。

1. 选择 "**集成**"，然后选择 " **+ 添加输出**"。

   :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding.png" alt-text="创建函数的输出绑定。" border="true":::

1. 选择**Azure 队列存储**绑定类型，并按以下屏幕截图所示添加表中指定的设置： 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding-details.png" alt-text="将队列存储输出绑定添加到 Azure 门户中的函数。" border="true":::
    
    | 设置      |  建议的值   | 说明                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **消息参数名称** | outputQueueItem | 输出绑定参数的名称。 | 
    | **队列名称**   | outqueue  | 要连接到存储帐户中的队列的名称。 |
    | **存储帐户连接** | AzureWebJobsStorage | 可以使用 Function App 已在使用的存储帐户连接，也可以创建一个新的存储帐户连接。  |

1. 选择 **"确定"** 以添加绑定。

现在，已定义输出绑定，需要更新代码以使用绑定将消息添加到队列。  

## <a name="add-code-that-uses-the-output-binding"></a>添加使用输出绑定的代码

在此部分，请添加将消息写入输出队列的代码。 该消息包括在查询字符串中传递到 HTTP 触发器的值。 例如，如果查询字符串包含 `name=Azure`，则队列消息将是“传递给函数的名称: Azure”。**

1. 在函数中，选择 "**代码 + 测试**" 以在编辑器中显示函数代码。

1. 根据函数语言更新函数代码：

    # <a name="c"></a>[Ansi-c\#](#tab/csharp)

    向方法签名添加 **outputQueueItem** 参数，如以下示例所示。

    ```cs
    public static async Task<IActionResult> Run(HttpRequest req,
        ICollector<string> outputQueueItem, ILogger log)
    {
        ...
    }
    ```

    在 `return` 语句之前的函数体中，添加使用参数创建队列消息的代码。

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);
    ```

    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    添加使用 `context.bindings` 对象上的输出绑定创建队列消息的代码。 请在`context.done` 语句之前添加此代码。

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ---

1. 选择“保存”**** 以保存更改。

## <a name="test-the-function"></a>测试函数

1. 保存代码更改后，请选择 "**测试**"。
1. 确认测试与下面的图像匹配，然后选择 "**运行**"。 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png" alt-text="在 Azure 门户中测试队列存储绑定。" border="true":::

    请注意，**请求正文**包含 `name` 值 *Azure*。 此值显示在队列消息中，该消息是在调用函数时创建的。
    
    如果不想选择此处的“运行”，也可调用该函数，方法是在浏览器中输入 URL，然后在查询字符串中指定 `name` 值。**** 此浏览器方法在[以前的快速入门](functions-create-first-azure-function.md#test-the-function)中演示过。

1. 检查日志以确保该函数成功。 

首次使用输出绑定时，函数运行时将在存储帐户中创建一个名为**outqueue**的新队列。 你将使用存储帐户来验证队列和其中的消息是否已创建。

### <a name="find-the-storage-account-connected-to-azurewebjobsstorage"></a>查找连接到 AzureWebJobsStorage 的存储帐户


1. 中转到 function app 并选择 "**配置**"。

1. 在 "**应用程序设置**" 下，选择**AzureWebJobsStorage**。

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-find-storage-account.png" alt-text="找到连接到 AzureWebJobsStorage 的存储帐户。" border="true":::

1. 找到帐户名称，并记下它。

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-storage-account-name.png" alt-text="找到连接到 AzureWebJobsStorage 的存储帐户。" border="true":::

### <a name="examine-the-output-queue"></a>检查输出队列

1. 在函数应用的资源组中，选择要用于本快速入门的存储帐户。

1. 在 "**队列服务**" 下，选择 "**队列**"，然后选择名为**outqueue**的队列。 

   此队列包含在运行 HTTP 触发的函数时队列输出绑定创建的消息。 如果使用 Azure 的默认 `name` 值调用了此函数，则队列消息为“传递给函数的名称：   Azure”。

1. 再次运行函数，此时会看到新消息出现在队列中。  

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何将输出绑定添加到现有函数。 有关队列存储绑定的详细信息，请参阅 [Azure Functions 存储队列绑定](functions-bindings-storage-queue.md)。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps-2.md)]
