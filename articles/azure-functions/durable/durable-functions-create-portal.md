---
title: 使用 Azure 门户创建 Durable Functions
description: 了解如何针对门户开发安装 Azure Functions 的 Durable Functions 扩展。
ms.topic: conceptual
ms.date: 04/10/2020
ms.reviewer: azfuncdf
ms.openlocfilehash: 6416ae4aba8b045c6c4fb0fe6557bdcd1efb3a9b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83120128"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>使用 Azure 门户创建 Durable Functions

Azure Functions 的 [Durable Functions](durable-functions-overview.md) 扩展是在 NuGet 包 [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 中提供的。 此扩展必须安装在函数应用中。 本文介绍如何安装此包，以便在 Azure 门户中开发 Durable Functions。

> [!NOTE]
> 
> * 如果是在 C# 中开发 Durable Functions，则应改为考虑 [Visual Studio 2019 开发](durable-functions-create-first-csharp.md)。
> * 如果在 JavaScript 中开发 Durable Functions，则应改为考虑 [Visual Studio Code 开发](./quickstart-js-vscode.md)。

## <a name="create-a-function-app"></a>创建函数应用

必须使用函数应用托管任何函数的执行。 函数应用可让你将函数分组为逻辑单元，以便更轻松地管理、部署、缩放和共享资源。 你可以创建 .NET 或 JavaScript 应用。

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

默认情况下，创建的函数应用使用 2.x 版 Azure Functions 运行时。 Durable Functions 扩展在 C# 中能够在 1.x 和 2.x 版 Azure Functions 运行时上工作，在 JavaScript 中能够在 2.x 版上工作。 但是，无论选择哪种语言，只有在以 2.x 版运行时为目标时，才能使用模板。

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>安装 Durable Functions npm 程序包（仅限 JavaScript）

如果要创建 JavaScript Durable Functions，需要安装 [`durable-functions` npm 程序包](https://www.npmjs.com/package/durable-functions)：

1. 从函数应用的页面中，选择左侧窗格中“开发工具”下的“高级工具”。

   :::image type="content" source="./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png" alt-text="Functions 平台功能选择 Kudu":::

2. 在“高级工具”页中，选择“Go”。

3. 在 Kudu 控制台中，选择“调试控制台”，然后选择“CMD”。

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Kudu 调试控制台":::

3. 此时应当会显示你的函数应用的文件目录结构。 导航到 `site/wwwroot` 文件夹。 在这里，可以通过将 `package.json` 文件拖放到文件目录窗口中来将其上传。 下面是一个示例 `package.json`：

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Kudu 上传 package.json":::

4. 上传你的 `package.json` 后，从 Kudu 远程执行控制台中运行 `npm install` 命令。

   ![Kudu 运行 npm install](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>创建一个业务流程协调程序函数

1. 在函数应用中，从左窗格中选择“函数”，然后从顶部菜单中选择“添加”。 

1. 在“新建函数”页的搜索字段中，输入 `durable`，然后选择“Durable Functions HTTP 初学者”模板。

   :::image type="content" source="./media/durable-functions-create-portal/durable-functions-http-starter-template.png" alt-text="选择“Durable Functions HTTP 初学者”":::

1. 对于新函数名称，请输入 `HttpStart`，然后选择“创建函数”。

   创建的函数用于启动业务流程。

1. 在函数应用中创建另一函数，这次使用“Durable Functions 业务流程协调程序”模板。 将新的业务流程函数命名为 `HelloSequence`。

1. 使用“Durable Functions 活动”模板创建第三个函数（名为 `Hello`）。

## <a name="test-the-durable-function-orchestration"></a>测试持久函数业务流程

1. 返回到 HttpStart 函数，选择“获取函数 URL”，然后选择“复制到剪贴板”图标以复制该 URL。 请使用此 URL 启动 **HelloSequence** 函数。

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
> [了解常见的持久函数模式](durable-functions-overview.md#application-patterns)
