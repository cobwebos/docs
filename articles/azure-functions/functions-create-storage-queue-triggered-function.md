---
title: "在 Azure 中创建由队列消息触发的函数 | Microsoft Docs"
description: "使用 Azure Functions 创建由提交到 Azure 存储队列的消息调用的无服务器函数。"
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/31/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 3fd5a5b9d2e2eec485fd9ecc5380ad6adb9851d0
ms.contentlocale: zh-cn
ms.lasthandoff: 09/28/2017

---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>创建由 Azure 队列存储触发的函数

了解如何创建在将消息提交到 Azure 存储队列时触发的函数。

![在日志中查看消息。](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>先决条件

- 下载并安装 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/)。

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>创建 Azure Function App

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![已成功创建 Function App。](./media/functions-create-first-azure-function/function-app-create-success.png)

接下来，在新的 Function App 中创建一个函数。

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>创建队列触发的函数

1. 展开 Function App，单击“Functions”旁边的 + 按钮。 如果这是 Function App 中的第一个函数，请选择“自定义函数”。 此时将显示函数模板的完整集合。

    ![Azure 门户中的 Functions 快速入门页](./media/functions-create-storage-queue-triggered-function/add-first-function.png)

2. 选择适用于所需语言的“QueueTrigger”模板，然后使用表中指定的设置。

    ![创建存储队列触发的函数。](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)
    
    | 设置 | 建议的值 | 说明 |
    |---|---|---|
    | **为函数命名** | 在 Function App 中唯一 | 此队列触发函数的名称。 |
    | **队列名称**   | myqueue-items    | 要连接到存储帐户中的队列的名称。 |
    | **存储帐户连接** | AzureWebJobStorage | 可以使用 Function App 已在使用的存储帐户连接，也可以创建一个新的存储帐户连接。  |    

3. 单击“创建”以创建函数。

接下来，连接到 Azure 存储帐户并创建 **myqueue-items** 存储队列。

## <a name="create-the-queue"></a>创建队列

1. 在函数中，单击“集成”，展开“文档”，并复制**帐户名称**和**帐户密钥**。 在 Azure 存储资源管理器中使用这些凭据连接到存储帐户。 如果已连接存储帐户，请跳到步骤 4。

    ![获取存储帐户连接凭据。](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)

1. 运行 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/)工具，单击左侧的“连接”图标，选择“使用存储帐户名称和密钥”，并单击“下一步”。

    ![运行“存储帐户资源管理器”工具。](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)

1. 输入步骤 1 中的**帐户名称**和**帐户密钥**，单击“下一步”，并单击“连接”。

    ![输入存储凭据和连接。](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

1. 展开附加的存储帐户，右键单击“队列”，单击“创建队列”，键入 `myqueue-items`，并按 Enter。

    ![创建存储队列。](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

现在已有存储队列，可以通过将消息添加到队列来测试函数。

## <a name="test-the-function"></a>测试函数

1. 返回到 Azure 门户中，浏览到函数，展开页面底部的“日志”并确保日志流式处理未暂停。

1. 在存储资源管理器中，依次展开存储帐户、“队列”和 **myqueue-items**，然后单击“添加消息”。

    ![将消息添加到队列。](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

1. 将“Hello World!” 消息键入到“消息文本”中，单击“确定”。

1. 等待几秒钟，返回到函数日志并验证是否已从队列中读取新消息。

    ![在日志中查看消息。](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

1. 返回到存储资源管理器中，单击“刷新”，并验证该消息是否已处理且不再在队列中。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>后续步骤

已创建将消息添加到存储队列时运行的函数。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

有关队列存储触发器的详细信息，请参阅 [Azure Functions 存储队列绑定](functions-bindings-storage-queue.md)。

