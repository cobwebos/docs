---
title: 在 Azure 中使用 Visual Studio Code 创建你的第一个函数
description: 使用 Visual Studio Code 中的 Azure Functions扩展创建一个简单的 HTTP 触发函数并将其发布到 Azure。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, functions, 事件处理, 计算, 无服务器体系结构
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 09/07/2018
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: cbe4dbd2ae741f4225cfdc628c31508956cbb95c
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469720"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>使用 Visual Studio Code 创建你的第一个函数

Azure Functions 用于在[无服务器](https://azure.microsoft.com/solutions/serverless/)环境中执行代码，无需先创建 VM 或发布 Web 应用程序。

本文介绍了如何使用[适用于 Visual Studio Code 的 Azure Functions 扩展]来通过 Microsoft Visual Studio Code 在本地计算机上创建和测试“hello world”函数。 然后说明了如何将函数代码从 Visual Studio Code 发布到 Azure。

![Visual Studio 项目中的 Azure Functions 代码](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

该扩展目前完全支持 C#、JavaScript 和 Java 函数，Python 支持目前处于预览状态。 本文中的步骤可能会因你为 Azure Functions 项目所选的语言而异。 此扩展目前为预览版。 若要了解详细信息，请参阅[适用于 Visual Studio Code 的 Azure Functions 扩展]页。

## <a name="prerequisites"></a>先决条件

完成本快速入门教程需要：

* 在某一[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上安装 [Visual Studio Code](https://code.visualstudio.com/)。 本文是在运行 macOS (High Sierra) 的设备上开发并测试的。

* 安装 [Azure Functions Core Tools](functions-run-local.md#v2) 的 2.x 版（仍为预览版）。

* 针对所选语言安装特定必需组件：

    | 语言 | 分机 |
    | -------- | --------- |
    | **C#** | [适用于 Visual Studio Code 的 C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[.NET Core CLI 工具](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)*   |
    | **Java** | [用于 Java 的调试器](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3+](https://maven.apache.org/) |
    | **JavaScript** | [Node 8.0+](https://nodejs.org/)  |

    \* 也是 Core Tools 所必需的。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

## <a name="create-an-http-triggered-function"></a>创建 HTTP 触发的函数

1. 从 **Azure：Functions** 区域中，选择“创建函数”图标。

    ![创建函数](./media/functions-create-first-function-vs-code/create-function.png)

1. 选择包含函数应用项目的文件夹，然后选择“HTTP 触发器”函数模板。

    ![选择 HTTP 触发器模板](./media/functions-create-first-function-vs-code/create-function-choose-template.png)

1. 键入 `HTTPTrigger` 作为函数名称，然后按 Enter，然后选择“匿名”身份验证。

    ![选择匿名身份验证](./media/functions-create-first-function-vs-code/create-function-anonymous-auth.png)

    此时将使用 HTTP 触发的函数的模板，以所选语言创建函数。

    ![Visual Studio Code 中的 HTTP 触发的函数模板](./media/functions-create-first-function-vs-code/new-function-full.png)

可以通过修改 function.json 文件，将输入和输出绑定添加到函数。 有关详细信息，请参阅 [Azure Functions 触发器和绑定概念](functions-triggers-bindings.md)。

创建函数项目和 HTTP 触发的函数后，可以在本地计算机上对其进行测试。

## <a name="test-the-function-locally"></a>在本地测试函数

使用 Azure Functions Core Tools 可以在本地开发计算机上运行 Azure Functions 项目。 首次从 Visual Studio Code 启动某个函数时，系统会提示你安装这些工具。  

1. 若要测试函数，请在函数代码中设置断点并按 F5 启动函数应用项目。 来自 Core Tools 的输出会显示在“终端”面板中。

1. 在“终端”面板中，复制 HTTP 触发的函数的 URL 终结点。

    ![Azure 本地输出](./media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. 将 HTTP 请求的 URL 粘贴到浏览器的地址栏中。 将查询字符串 `?name=<yourname>` 追加到此 URL 并执行请求。 执行将在命中断点时暂停。

    ![Visual Studio Code 中的命中断点的函数](./media/functions-create-first-function-vs-code/function-debug-vscode-js.png)

1. 下面显示了当继续执行时浏览器中对 GET 请求的响应：

    ![浏览器中的函数 localhost 响应](./media/functions-create-first-function-vs-code/functions-test-local-browser.png)

1. 若要停止调试，请按 Shift + F5。

确认该函数可以在本地计算机上正确运行以后，即可将项目发布到 Azure。

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>在 Azure 中测试函数

1. 从“输出”面板复制 HTTP 触发器的 URL。 与前面一样，请确保将查询字符串 `?name=<yourname>` 添加到此 URL 的末尾并执行请求。

    调用 HTTP 触发的函数的 URL 应采用以下格式：

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

1. 将 HTTP 请求的这个新 URL 粘贴到浏览器的地址栏中。 下面演示浏览器中函数返回的对远程 GET 请求的响应： 

    ![浏览器中的函数响应](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>后续步骤

你已使用 Visual Studio Code 通过简单的 HTTP 触发函数创建了函数应用。 你还可能想要了解有关使用 Azure Functions Core Tools [从终端或命令提示符进行本地测试和调试](functions-run-local.md)的详细信息。

> [!div class="nextstepaction"]
> [启用 Application Insights 集成](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Functions Core Tools]: functions-run-local.md
[适用于 Visual Studio Code 的 Azure Functions 扩展]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
