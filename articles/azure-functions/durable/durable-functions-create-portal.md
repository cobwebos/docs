---
title: 使用 Azure 门户创建 Durable Functions
description: 了解如何针对门户开发安装 Azure Functions 的 Durable Functions 扩展。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: acbba991e6dcce56fad7f27c45f85214cc8fc707
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637002"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>使用 Azure 门户创建 Durable Functions

Azure Functions 的 [Durable Functions](durable-functions-overview.md) 扩展是在 NuGet 包 [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 中提供的。 此扩展必须安装在函数应用中。 本文介绍如何安装此包，以便在 Azure 门户中开发 Durable Functions。

>[!NOTE]
>
>* 如果在 C# 中开发 Durable Functions，则应改为考虑 [Visual Studio 2017 开发](durable-functions-create-first-csharp.md)。
* 如果在 JavaScript 中开发 Durable Functions，则应改为考虑 **Visual Studio Code 开发**。
>
>目前尚不支持在门户中使用 JavaScript 创建 Durable Functions。 请改用 Visual Studio Code。

## <a name="create-a-function-app"></a>创建函数应用

必须使用函数应用托管任何函数的执行。 函数应用可将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。 必须创建 C# 函数应用，因为 Durable Functions 尚不支持 JavaScript 模板。  

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

默认情况下，创建的函数应用使用 2.x 版 Azure Functions 运行时。 Durable Functions 扩展在 1.x 和 2.x 版 Azure Functions 运行时上均可使用。 但是，只有在以 2.x 版运行时为目标时，才能使用模板。

## <a name="create-an-orchestrator-function"></a>创建一个业务流程协调程序函数

1. 展开 Function App，单击“Functions”旁边的 + 按钮。 如果这是函数应用中的第一个函数，请依次选择“门户中”、“继续”。 否则，请转到第三步。

   ![Azure 门户中的 Functions 快速入门页](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. 依次选择“更多模板”、“完成并查看模板”。

    ![Functions 快速入门选择更多模板](./media/durable-functions-create-portal/add-first-function.png)

1. 在搜索字段中键入 `durable`，然后选择“Durable Functions HTTP 初学者”模板。

1. 系统提示时，请选择“安装”以在函数应用中安装 Azure DurableTask 扩展的任何依赖项。 对于给定的函数应用，只需安装该扩展一次。 安装成功后，选择“继续”。

    ![安装绑定扩展](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. 安装完成后，请将新函数命名为 `HttpStart`，然后选择“创建”。 创建的函数用于启动业务流程。

1. 在函数应用中创建另一函数，这次使用 **Durable Functions 业务流程协调程序**模板。 将新的业务流程函数命名为 `HelloSequence`。

1. 使用 **Durable Functions 活动**模板创建第三个函数，该函数名为 `Hello`。

## <a name="test-the-durable-function-orchestration"></a>测试持久函数业务流程

1. 回到 **HttpStart** 函数，选择“</> 获取函数 URL”并**复制**此 URL。 请使用此 URL 启动 **HelloSequence** 函数。

1. 使用 HTTP 工具（例如 Postman 或 cURL）将 POST 请求发送到已复制的 URL。 以下示例是一个 cURL 命令，该命令将 POST 请求发送到持久函数：

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    在此示例中，`{your-function-app-name}` 是域，该域是函数应用的名称。 响应消息包含一组 URI 终结点，这些终结点可以用来监视并管理执行，该执行如以下示例所示：

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. 调用 `statusQueryGetUri` 终结点 URI，此时会看到持久函数的当前状态，如以下示例所示：

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. 继续调用 `statusQueryGetUri` 终结点，直到状态更改为“Completed”，此时会看到一个响应，如以下示例所示： 

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

现已在 Azure 中启动并运行第一个持久函数。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解常见的持久函数模式](durable-functions-overview.md)