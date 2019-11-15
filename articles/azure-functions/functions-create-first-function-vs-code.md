---
title: 在 Azure 中使用 Visual Studio Code 创建你的第一个函数
description: 使用 Visual Studio Code 中的 Azure Functions扩展创建一个简单的 HTTP 触发函数并将其发布到 Azure。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, functions, 事件处理, 计算, 无服务器体系结构
ms.service: azure-functions
ms.topic: quickstart
ms.date: 06/25/2019
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: fbde3c939a23e4023c0403c27433717a11762b08
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622061"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>使用 Visual Studio Code 创建你的第一个函数

Azure Functions 用于在[无服务器](https://azure.microsoft.com/solutions/serverless/)环境中执行代码，无需先创建 VM 或发布 Web 应用程序。

本文介绍了如何使用[适用于 Visual Studio Code 的 Azure Functions 扩展]来通过 Microsoft Visual Studio Code 在本地计算机上创建和测试“hello world”函数。 然后说明了如何将函数代码从 Visual Studio Code 发布到 Azure。

![Visual Studio 项目中的 Azure Functions 代码](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

该扩展当前支持 C#、JavaScript、Java 和 Python 函数。 本文和后续文章中的步骤仅支持 JavaScript 和 C# 函数。 若要了解如何使用 Visual Studio Code 创建和发布 Python 函数，请参阅[使用 Visual Studio Code 在 Python 中创建和部署无服务器 Azure Functions](/azure/python/tutorial-vs-code-serverless-python-01)。 若要了解如何使用 Visual Studio Code 创建和发布 PowerShell 函数，请参阅[在 Azure 中创建第一个 PowerShell 函数](functions-create-first-function-powershell.md)。 

此扩展目前为预览版。 若要了解详细信息，请参阅[适用于 Visual Studio Code 的 Azure Functions 扩展]页。

## <a name="prerequisites"></a>先决条件

完成本快速入门教程需要：

* 在某一[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上安装 [Visual Studio Code](https://code.visualstudio.com/)。

* 安装 [Azure Functions Core Tools](functions-run-local.md#v2) 版本 2.x。

* 针对所选语言安装特定必需组件：

    | 语言 | 要求 |
    | -------- | --------- |
    | **C#** | [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>活动 LTS 和维护 LTS 版本（建议使用 8.11.1 和 10.14.1）。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

确认该函数可以在本地计算机上正确运行以后，即可将项目发布到 Azure。

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>在 Azure 中运行函数

1. 从“输出”  面板复制 HTTP 触发器的 URL。 此 URL 包括函数密钥，该密钥将传递给 `code` 查询参数。 与前面一样，请确保将查询字符串 `?name=<yourname>` 添加到此 URL 的末尾并执行请求。

    调用 HTTP 触发的函数的 URL 应采用以下格式：

        http://<functionappname>.azurewebsites.net/api/<functionname>?code=<function_key>&name=<yourname> 

1. 将 HTTP 请求的这个新 URL 粘贴到浏览器的地址栏中。 下面演示浏览器中函数返回的对远程 GET 请求的响应： 

    ![浏览器中的函数响应](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>后续步骤

你已使用 Visual Studio Code 通过简单的 HTTP 触发函数创建了函数应用。 在下一篇文章中，将通过添加输出绑定来扩展该函数。 此绑定将 HTTP 请求中的字符串写入 Azure 队列存储队列中的消息。 下一篇文章还演示了如何通过删除所创建的资源组来清理这些新的 Azure 资源。

> [!div class="nextstepaction"]
> [将 Azure 存储队列绑定添加到函数](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[适用于 Visual Studio Code 的 Azure Functions 扩展]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
